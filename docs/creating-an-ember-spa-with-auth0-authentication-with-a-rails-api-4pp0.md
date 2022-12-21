# 使用 Rails api 创建带 Auth0 身份验证的 Ember SPA

> 原文：<https://dev.to/dschlauderaff/creating-an-ember-spa-with-auth0-authentication-with-a-rails-api-4pp0>

我正在创建一个食谱组织/膳食计划应用程序。我已经与 Ember 合作了一段时间，但我专业工作的应用程序的后端主要是一个黑盒。我用已经存在的授权过程调用 api。我想使用 Rails 建立自己的 graphql api。让 Auth0 与这两个应用程序对话是一件非常棘手的事情。我找不到太多的(任何)教程能给你提供操作步骤，让它正常工作。

在过去几个晚上的工作中，我打开了太多不同的文档、博客和堆栈溢出问题的标签，以至于我的浏览器经常崩溃。以下是我所做的一切。

### 设置认证 0

设置 Auth0 相对容易:注册/登录，点击仪表板上的创建应用程序按钮，选择单页面应用程序。不幸的是，Ember 没有快速入门。命名应用程序，设置允许的回调 URL:[http://localhost:4200](http://localhost:4200)(目前这都是开发模式)和允许的注销 URL:[http://localhost:4200](http://localhost:4200)

创建应用程序后，应用程序的域、客户端 Id 和客户端密码将出现在应用程序的设置页面中。

接下来，设置 api 应用程序。同样，非常简单，只需提供一个名称和一个标识符。该标识符将在应用程序中用作 API 受众密钥。

### [T1】配置余烬](#configuring-ember)

新建一个 app:
`$ ember new no-stories`

删除余烬-欢迎页面。

安装 ember-simple-auth-auth0 附加组件:
`$ ember install ember-simple-auth-auth0`

配置 auth0 加载项:

*   auth0 配置变量:

```
 #config/auth0-variables.js

  module.exports = {
    clientID: "your Auth0 client id",
    domain: "your Auth0 domain" 
```

*   向 gitignore 添加授权变量
    *   in 环境. js

```
#config/environment.js
+  const AUTH_CONFIG = require('./auth0-variables')

  module.exports = function(environment) {
   let ENV = {
     ...
+   'ember-simple-auth: {
+     authenticationRoute: 'login',
+     auth0: {
+       clientId: AUTH_CONFIG.clientID, 
+       domain: AUTH_CONFIG.domain,
+       logoutReturnToURL: '/',
+       audience: 'your API Audience key',
+       enableImpersonation: false,
+       silentAuth: {}
+     }
+   },
    ... 
```

*   应用程序路由和控制器

```
 #routes/application.js
 import  Route  from  '@ember/routing/route'
import  RSVP  from  'rsvp'
import  ApplicationRouteMixin  from  'ember-simple-auth-auth0/mixins/application-route-mixin'

export  default  Route.extend(ApplicationRouteMixin, {
  beforeSessionExpired() {
    // Do custom async logic here, e.g. notify
    // the user that they are about to be logged out.

    return  RSVP.resolve()
}

// Do other application route stuff here. All hooks provided by
// ember-simple-auth's ApplicationRouteMixin, e.g. sessionInvalidated(),
// are supported and work just as they do in basic ember-simple-auth.
}) 
```

```
#controllers/application.js

import  Controller  from  '@ember/controller'
import { inject  as  service } from  '@ember/service'

export  default  Controller.extend({
  session:  service(),

  actions: {
    login() {
      const  authOptions  = {
        responseType:  'token id_token',
        scope:  'openid email profile',
        audience:  'API audience key'
      }

      this.session.authenticate(
        'authenticator:auth0-universal',
        authOptions,
        (err, email) => {
          alert(`Email link sent to ${email}`)
        }
      )
    },

    logout() {
      this.session.invalidate()
    }
  }
}) 
```

接下来，创建一个简单的导航组件来显示登录/注销按钮。这些风格来自余烬超光速粒子。

```
#app/templates/navigation.hbs
<header  class="bg-black-90 fixed w-100 ph3 pv3 pv4-ns ph4-m ph5-l">
  <nav  class="f6 fw6 ttu tracked">
    {{#if  session.isAuthenticated}}
      <a  href="#"  class="link dim white dib mr3"  {{action  "logout"}}>
        Logout
      </a>
    {{else}}
      <a  href="#"  class="link dim white dib mr3"  {{action  "login"}}>
        Login
      </a>
    {{/if}}
    <a  class="link dim white dib mr3"  href="#"  title="Home">
      Placeholder
    </a>
    <a  class="link dim white dib"  href="#"  title="Contact">
      Contact
    </a>
  </nav>
</header> 
```

```
#app/components/navigation.js
import  Component  from  '@ember/component'
import { inject  as  service } from  '@ember/service'

export  default  Component.extend({
  session:  service(),

  actions: {
    login() {
      this.login()
    },

    logout() {
      this.logout()
    }
  }
}) 
```

将导航组件插入应用程序模板:

```
#app/templates/application.hbs
<Navigation @login={{action  "login"}} @logout={{action  "logout"}} /> <div  class="main">
  {{outlet}}
</div> 
```

此时，应用程序可以通过单击 login 按钮通过 Auth0 进行身份验证，并且能够记录 this . session . data . authenticated，其中应该包含很多信息，尤其是两个 json web 令牌:accessToken 和 idToken。

### 设置 Rails api

设置 rails 应用程序相对简单。因为我使用的是 Rails 6，所以我能够遵循 Auth0 的 [rails 文档](https://auth0.com/docs/quickstart/backend/rails/01-authorization)只做了一些调整。另外，rack-cors gem 需要配置，这在我看到的 Auth0 文档中根本没有提到。以下是步骤:

`$ rails new my-api --api`

将 Auth0 配置值添加到`credentials.yml.enc` :
`$ EDITOR="code --wait" rails credentials:edit`将在 VS 代码中打开一个标签，指向解密的凭证文件

```
# Auth0
auth0:
  clientID: auth0 client id
  domain: auth0 domain
  secret: auth0 secret
  audience: api identifier 
```

```
# lib/json_web_token.rb

# frozen_string_literal: true
require 'net/http'
require 'uri'

class JsonWebToken
  def self.verify(token)
    JWT.decode(token, nil,
               true, # Verify the signature of this token
               algorithm: 'RS256',
               iss: 'https://YOUR_DOMAIN/',
               verify_iss: true,
               aud: Rails.application.secrets.auth0_api_audience,
               verify_aud: true) do |header|
      jwks_hash[header['kid']]
    end
  end

  def self.jwks_hash
    jwks_raw = Net::HTTP.get URI("https://YOUR_DOMAIN/.well-known/jwks.json")
    jwks_keys = Array(JSON.parse(jwks_raw)['keys'])
    Hash[
      jwks_keys
      .map do |k|
        [
          k['kid'],
          OpenSSL::X509::Certificate.new(
            Base64.decode64(k['x5c'].first)
          ).public_key
        ]
      end
    ]
  end
end 
```

在我的版本中，我将 jwks_raw 赋值从直接请求改为缓存，以减少发送到 auth0 服务器的请求数量:

```
def self.jwks_hash - jwks_raw - Net::HTTP.get URI("https//YOUR_DOMAIN/.well-known/jwks.json") + jwks_raw = Rails.cache.fetch("JWKS_HASH", exires_in: 10.hours) do
+   Net::HTTP.get URI("https://#{Rails.application.credentials[:auth0][:domain]}.well-known/jwks.json")
+ end 
  jwks_keys = Array(JSON.parse(jwks_raw)['keys'])
  ... 
```

这样做需要更新`config/environments/development.rb`以在内存中存储项目:

```
#config/environments/development.rb

...
# Run rails dev:cache to toggle caching.
if  Rails.root.join('tmp', 'caching-dev.txt').exist?
  config.cache_store = :memory_store 
  config.public_file_server.headers = {
    'Cache-Control' => "public, max-age=#{2.days.to_i}"
  }
else
  config.action_controller.perform_caching =  false

- config.cache_store = :null_store 
+ config.cache_store = :memory_store end ... 
```

接下来，我定义了一个安全问题:

```
# app/controllers/concerns/secured.rb

# frozen_string_literal: true
module Secured
  extend ActiveSupport::Concern

  included do
    before_action :authenticate_request!
  end

  private

  def authenticate_request!
    auth_token
  rescue JWT::VerificationError, JWT::DecodeError
    render json: { errors: ['Not Authenticated'] }, status: :unauthorized
  end

  def http_token
    if request.headers['Authorization'].present?
      request.headers['Authorization'].split(' ').last
    end
  end

  def auth_token
    JsonWebToken.verify(http_token)
  end
end 
```

Auth0 文档的下一部分是关于验证作用域的。我包括这个是因为我打算最终使用它，但是对于项目的这个阶段，我只关心`/private`路线，没有相关的范围。

```
 SCOPES = {
    '/private' => nil,
    '/private-scoped'    => ['read:messages']
  }

  private

  def authenticate_request!
    @auth_payload, @auth_header = auth_token

    render json: { errors: ['Insufficient scope'] }, status: :unauthorized unless scope_included
  rescue JWT::VerificationError, JWT::DecodeError
    render json: { errors: ['Not Authenticated'] }, status: :unauthorized
  end

  def scope_included
    if SCOPES[request.env['PATH_INFO']] == nil
      true
    else
      # The intersection of the scopes included in the given JWT and the ones in the SCOPES hash needed to access
      # the PATH_INFO, should contain at least one element
      (String(@auth_payload['scope']).split(' ') & (SCOPES[request.env['PATH_INFO']])).any?
    end
  end 
```

为了测试它是否真的如预期的那样工作，我在`app/config/routes.rb`
中添加了一条`/private`路线

```
#app/config/routes.rb
Rails.application.routes.draw do
+ get "/private", to: "private#private" ... 
```

并创建一个控制器:

```
# app/controllers/private_controller.rb

# frozen_string_literal: true
class PrivateController < ActionController::API
  include Secured

  def private
    render json: 'Hello from a private endpoint! You need to be authenticated to see this.'
  end
end 
```

最后，rack-cors gem 需要配置为允许来自 ember 应用程序的请求:
在 gem 文件中，取消 rack-cors gem 的注释并运行`bundle install`。然后在`app/config/application.rb` :

```
...

config.middleware.insert_before 0, Rack::Cors  do
  allow do
    origins '*'
    resource '*', :headers => :any, :methods => [:get, :post, :options]
  end
end 
```

在这一点上,`origins`过于宽松，我以后会想收紧它，但是现在我只关心如何让它启动和运行。

### 真相的时刻

在 Ember app 中，我生成一条烟测路线:
`$ ember g route private-test`

并导入`ember-fetch`插件:
`$ ember install ember-fetch`

我在`app/routes/private-test.js`文件中设置了我的测试:

```
import  Route  from  '@ember/routing/route'
import  ApplicationRouteMixin  from  'ember-simple-auth-auth0/mixins/application-route-mixin'
import { inject  as  service } from  '@ember/service'
import  fetch  from  'fetch'

export  default  Route.extend(ApplicationRouteMixin, {
  session:  service(),

  model() {
    return  fetch('http://localhost:3000/private', {
      method:  'GET',
      cache:  false,
      headers: {
        Authorization:  `Bearer ${this.session.data.authenticated.accessToken}`,
        'Access-Control-Allow-Origin':  '*'
      }
    }).then(response  => {
      console.log(response)
    })
  }
}) 
```

一切就绪后，启动两台服务器，流程应该如下所示:

1.  `localhost:4200/` -点击“登录”按钮
2.  已重定向至“Auth0 登录”页面
3.  输入凭据
4.  返回到`localhost:4200/`
5.  导航至`localhost:4200/private-test`
6.  在开发人员工具中，api 响应将被注销。

响应不是很好，您需要打开 network 选项卡才能看到“Hello from a private endpoint！”string，但是身份验证正在进行，ember 和 rails 应用程序可以通过 Auth0 相互通信。

这个应用程序的最终目标是将 api 设置为 graphql api。在这个概念验证代码中，有很多东西可以更好地组织，比如报头可能应该添加在各个路由之外的某个地方。当我最终得到认证的回应时，我觉得我需要在忘记我所做的一切之前尽快把它写下来。