# OAuth 请求、API、Diesel 和会话

> 原文：<https://dev.to/jculverhouse/oauth-requests-apis-diesel-and-sessions-5lb>

<figure>[![Intertwined woven basket material.. much like Oauth, accounts, APIs, diesel, and sessions are in this project (probably any project)](img/c16d880d0295e9d5187d7687baa0c134.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HUKEh8uh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/rust.graystorm.com/wp-content/uploads/2019/08/wheel-spiral-purple-line-pasture-metal-969319-pxhere.com_.jpg%3Fresize%3D400%252C400%26ssl%3D1) 

<figcaption>事情开始纠结…</figcaption>

</figure>

最近[库](https://github.com/jculverhouse/pinpoint_shooting)发生了一些大的变化。我添加了`Google Signin`和`Facebook Signin`T5】OAuth 连接。我想我可能甚至不会在网站上为用户配置内部密码，而是只需要这些选项中的一个。考虑到网站的目的，我可能会添加更多，比如 500px.com[和/或 Flickr](https://500px.com/) 。一个`password`字段仍然在我的数据库中，所以我还没有完全放弃这个想法。此外，OAuth 请求使用 Diesel 创建帐户。

`Users`(现在被确定为`shooters`——我们摄影师还没有放弃这个术语)——现在被写到 db。我真的用了一个 Diesel 特性，所以这一点在代码中还是被注释掉了。此外，我在`POST`中添加了我的第一个 API 这也是`Rocket`机箱的另一个步骤！我想用自己的方式来玩一个 [GraphQL](https://graphql.org/) 端点，这样我也可以玩它了！！(无论如何，项目中机箱依赖的限制是什么？！)我开始认为我无法在一篇文章中解决所有这些问题——但是让我们开始吧！

## OAuth vs 新旧账户

当用户到达站点时，我会检查带有`session id`的`cookie`(见我的[前一篇文章](https://rust.graystorm.com/2019/08/04/rust-web-app-session-management-with-aws/))。我决定，现在，我将使用`User Agent`(加上一些随机字符)作为创建会话 id 的指纹。因此，当我能够从 cookie 中获得一个`session_id`时，我想验证`User Agent`是相同的，并且会话没有过期。如果用户是全新的，没有 cookie，我会立即为他们创建一个空的、非用户的会话。所有这些都完成了，现在，就在我的`index() route`顶端。

```
<src/routes.rs>

...
#[get("/")]
pub fn index(mut cookies: Cookies, nginx: Nginx) -> 
    rocket_contrib::templates::Template
{
    let session = get_or_setup_session(&mut cookies, &nginx);
... 
```

在`index`页面加载后，它显示了谷歌和脸书的登录按钮。点击其中的一个，他们进行验证并获得用户的许可。当这被授予时，我的应用程序得到一个令牌，我通过一个`POST`到`/api/v1/tokensignin`把它发送到服务器。

```
<src/api.rs>

use rocket::{http::Cookies, post, request::Form, FromForm};
use crate::oauth::*;
use crate::routes::Nginx;
use crate::session::*;
#[derive(FromForm)]
pub struct OAuthReq {
    pub g_token: Option<String>,  // google login req
    pub fb_token: Option<String>, // facebook login req`
    pub name: String,
    pub email: String,
}
#[post("/api/v1/tokensignin", data = "<oauth_req>")]
pub fn tokensignin(mut cookies: Cookies, nginx: Nginx,
        oauth_req: Form<OAuthReq>) -> String
{
    let mut session = get_or_setup_session(&mut cookies, &nginx);
    if let Some(token) = &oauth_req.g_token {
        match verify_google_oauth(&mut session, &token,
            &oauth_req.name, &oauth_req.email)
        {
            true => {
                session.google_oauth = true;
                save_session_to_ddb(&mut session);
                "success".to_string()
            }
            false => {
                session.google_oauth = false;
                save_session_to_ddb(&mut session);
                "failed".to_string()
            }
        }
    } else if let Some(token) = &oauth_req.fb_token {
        match verify_facebook_oauth(&mut session, &token,
            &oauth_req.name, &oauth_req.email)
        {
            true => {
                session.facebook_oauth = true;
                save_session_to_ddb(&mut session);
                "success".to_string()
            }
            false => {
                session.facebook_oauth = false;
                save_session_to_ddb(&mut session);
                "failed".to_string()
            }
        }
    } else {
        "no token sent".to_string()
    }
} 
```

## 通过 HTTP POSTs 的 OAuth 请求

这就是你允许一个`POST`和`form data`进入的方式——你设置一个你期望的`struct`(在我的例子中是`OAuthReq`),并把它作为一个输入参数带入。另外，我还引入了所有随请求到达的`cookies`和一些 Nginx 头，这样我就可以访问`UserAgent`。在到目前为止的代码中，我要么验证一个谷歌或脸书令牌。让我们看看谷歌的例子(脸书的几乎是一样的)。这里是相关的部分，但我会分解一些部分来浏览:

```
<src/oauth.rs>

...
pub fn verify_google_oauth(
    session: &mut Session,
    token: &String,
    name: &String,
    email: &String,
) -> bool {
    let mut google = google_signin::Client::new();
    google.audiences.push(CONFIG.google_api_client_id.clone());
    let id_info = google.verify(&token).expect("Expected token to be valid");
    let token = id_info.sub.clone();
    verify_token(session, "google".to_string(), &token, &name, &email)
} 
```

这直接导致了一个大的`match` :

```
fn verify_token(
    session: &mut Session,
    vendor: String,
    token: &String,
    name: &String,
    email: &String,
) -> bool {
    use crate::schema::oauth::dsl::*;
    use crate::schema::shooter::dsl::*;
    let connection = connect_pgsql();
    match oauth
        .filter(oauth_vendor.eq(&vendor))
        .filter(oauth_user.eq(&token))
        .first::<Oauth>(&connection)
    { 
```

用 OK 臂:

```
 // token WAS found in oauth table
        Ok(o) => {
            if let Some(id) = session.shooter_id {
                if id == o.shooter_id {
                    return true;
                } else {
                    return false;
                }
            } else {
                // log in user - what IS the problem with BelongsTo!?
                //if let Ok(s) = Shooter::belonging_to(&o)
                //    .load::<Shooter>(&connection)
                //{
                //    session.shooter_id = Some(shooter.shooter_id);
                //    session.shooter_name = Some(shooter.shooter_name);
                //    session.email_address = Some(shooter.email);
                return true;
                //} else {
                //    return false;
                //}
            }
        } 
```

和错误武器:

```
 // token not found in oauth table
        Err(diesel::NotFound) => match session.shooter_id {
            Some(id) => {
                create_oauth(&connection, &vendor, token, id);
                true
            }
            None => match shooter
                .filter(shooter_email.eq(&email))
                .first::<Shooter>(&connection)
            {
                // email address WAS found in shooter table
                Ok(s) => {
                    create_oauth(&connection, &vendor, token, s.shooter_id);
                    true
                }
                // email address not found in shooter table
                Err(diesel::NotFound) => {
                    let this_shooter =
                        create_shooter(&connection, name, None,
                            email, &"active".to_string());
                    session.shooter_id = Some(this_shooter.shooter_id);
                    create_oauth(&connection, &vendor, token,
                        this_shooter.shooter_id);
                    true
                }
                Err(e) => {
                    panic!("Database error {}", e);
                }
            },
        },
        Err(e) => {
            panic!("Database error {}", e);
        }
    }
} 
```

## 简单查询用柴油

将所有代码分解成更小的部分:首先，我查询 PgSQL 数据库中给定的`oauth user` :

```
match oauth
    .filter(oauth_vendor.eq(&vendor))
    .filter(oauth_user.eq(&token))
    .first::<Oauth>(&connection) {
        Ok(o) => { ... }
        Err(diesel::NotFound) => { ... }
        Err(e) => { ... }
} 
```

检查`oauth`表中的记录，其中(`filter`)是`oauth_vendor`(Google 或 facebook)，我已经存储了相同的验证过的`oauth_user`。我将返回`Ok(o)`或`Err(diesel::NotFound)`……(或一些更坏的错误信息)，所以我用这三条手臂做了一个图案。

如果我们确实从数据库中找到了匹配项，那么这个`session id`已经被绑定到了一个`shooter_id`(用户 id)，除非出现了非常严重的问题。因此，如果我们在当前的`session`中也定义了一个`shooter_id`，我只需要验证它们是否匹配并返回 true 或 false。但是，如果我们的`session`中没有`shooter_id`，我们知道`oauth`被绑定到 db 中的`shooter`，所以这将记录它们。`Diesel`有一个简单方法来获取父记录，这就是它应该做的:

```
// if let Ok(s) = Shooter::belonging\_to(&o).load::\<Shooter\>(&connection) {
... 
```

我奋斗了又奋斗才得到这个作品，但是你可以看到它仍然被注释掉了。从网上的帖子和聊天中，我相信它可以工作——我想我要么是`scope`有问题，要么是我的`models`设置不正确……它们看起来是这样的:

```
<src/model.rs>

...
#[derive(Identifiable, Queryable, Debug, PartialEq)]
#[table_name = "shooter"]
#[primary_key("shooter_id")]
pub struct Shooter {
    pub shooter_id: i32,
    pub shooter_name: String,
    pub shooter_password: String,
    pub shooter_status: String,
    pub shooter_email: String,
    pub shooter_real_name: String,
    pub shooter_create_time: chrono::NaiveDateTime,
    pub shooter_active_time: Option<chrono::NaiveDateTime>,
    pub shooter_inactive_time: Option<chrono::NaiveDateTime>,
    pub shooter_remove_time: Option<chrono::NaiveDateTime>,
    pub shooter_modify_time: chrono::NaiveDateTime,
}
...
#[derive(Identifiable, Associations, Queryable, Debug, PartialEq)]
#[belongs_to(Shooter, foreign_key = "shooter_id")]
#[table_name = "oauth"]
#[primary_key("oauth_id")]
pub struct Oauth {
    pub oauth_id: i32,
    pub oauth_vendor: String,
    pub oauth_user: String,
    pub shooter_id: i32,
    pub oauth_status: String,
    pub oauth_create_time: chrono::NaiveDateTime,
    pub oauth_last_use_time: chrono::NaiveDateTime,
    pub oauth_modify_time: chrono::NaiveDateTime,
} 
```

我最终会让它工作的——我真的希望它不会失败，因为我没有像指南中的例子那样只给我的主要字段命名。似乎在表`oauth`中命名`shooter_id`来匹配`shooter`表中的`shooter_id`会使事情变得明显。希望我们不会被迫总是使用`id`作为主要字段…不，不可能。

总之，回到验证。另一个主要情况是在表中没有找到带有这个标记的`oauth`记录。这意味着这是一种我们从未见过的新联系。如果`session`已经登录，我们只需要将这个`oauth`令牌附加到登录的用户并返回 true！

```
Some(id) => {
    create_oauth(&connection, &vendor, token, id);
    true
} 
```

否则，有两个选择——我们将尝试通过`email address`匹配现有的`shooter`。如果找到匹配，我们就让他们登录，并再次将这个 oauth 令牌附加到他们的`shooter`记录中。

```
 None => match shooter
     .filter(shooter_email.eq(&email))
     .first::<Shooter>(&connection)
 {
     // email address WAS found in shooter table
     Ok(s) => {
         create_oauth(&connection, &vendor, token, s.shooter_id);
         true
     } 
```

否则，我们不会成功；也就是说，我们以前没有见过这个`oauth`令牌，也没有见过这个经过验证的`email address`。我们称之为全新的`shooter`账户。我提到过我们使用 Diesel 从 OAuth 请求创建帐户——这就是发生的地方。在本例中，我们创建了`shooter`记录和`oauth`记录，并将它们链接在一起。

```
// email address not found in shooter table
Err(diesel::NotFound) => {
    let this_shooter =
        create_shooter(&connection, name, None, email,
            &"active".to_string());
    session.shooter_id = Some(this_shooter.shooter_id);
    create_oauth(&connection, &vendor, token, this_shooter.shooter_id);
    true
} 
```

## 使用柴油插入记录

当我们退出调用过的函数堆栈时，因为我们在这里返回了`true`,`session`将会被`shooter_id`更新——它们现在已经登录了。此外，`shooter`和`oauth`记录被保存，所以如果他们回来，他们可以验证并再次登录到他们相同的帐户。下面是创建这些记录的两种方法:

```
<src/shooter.rs>

...
pub fn create_shooter<'a>(
    connection: &PgConnection,
    name: &'a String,
    password: Option<&'a String>,
    email: &'a String,
    status: &'a String,
) -> Shooter {
    use crate::schema::shooter::dsl::*;
    let new_shooter = NewShooter {
        shooter_name: name.to_string(),
        shooter_password: match password {
            Some(p) => p.to_string(),
            None => thread_rng()
                .sample_iter(&Alphanumeric)
                .take(64)
                .collect::<String>(),
        },
        shooter_status: status.to_string(),
        shooter_email: email.to_string(),
        shooter_real_name: name.to_string(),
    };
    diesel::insert_into(shooter)
        .values(&new_shooter)
        .get_result(connection)
        .expect("Error saving new Shooter")
} 
```

```
<src/oauth.rs>

...
pub fn create_oauth<'a>(
    connection: &PgConnection,
    vendor: &'a String,
    user_id: &'a String,
    shooterid: i32,
) -> Oauth {
    use crate::schema::oauth::dsl::*;
    let new_oauth = NewOauth {
        oauth_vendor: vendor.to_string(),
        oauth_user: user_id.to_string(),
        shooter_id: shooterid,
    };
    diesel::insert_into(oauth)
        .values(&new_oauth)
        .get_result(connection)
        .expect("Error saving new Oauth")
} 
```

至于将这些新记录写入 PgSQL——在这两种情况下，我们有`NewShooter`和`NewOauth structs`允许我们设置最少的字段，而不必担心 PgSQL 将为我们默认的字段(如`create_date`字段)。我们设置适当的`struct`，并将其传递给`insert_into()`。添加`.get_result()`会将新创建的记录返回给我们，因此我们可以访问全新的`shooter_id`或`oauth_id`。

## 复杂程度

如果一个用户来到站点，用一个 oauth 登录(它创建他们的射手记录并附加那个 oauth 令牌),然后用另一个登录，这个逻辑认为他们被验证为同一个人，所以只创建一个射手记录和两个 OAuth 记录，并且都指向一个用户。如果他们回来，他们可以通过任何第三方认证，并被允许回来。

好吧，更多的来，因为我发现其他问题。我还没有足够严密地检查过这个逻辑，以确保我没有任何漏洞——如果发现一些，我也不会感到惊讶。这真的没关系——这无疑是在教我生锈！在[PinpointShooting.com](https://pinpointshooting.com/)试试吧——但是如果你的`shooter`账户不断被删除，不要惊讶。

OAuth 请求、API、Diesel 和会话的帖子首先出现在[学习 Rust](https://rust.graystorm.com) 上。