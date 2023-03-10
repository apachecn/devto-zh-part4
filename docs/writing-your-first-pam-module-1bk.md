# 编写您的第一个 PAM 模块

> 原文：<https://dev.to/rkeene/writing-your-first-pam-module-1bk>

## 可插拔的群众认证

最近我和一个人聊天，他表示有兴趣让他们的用户指定他们的帐户只能使用 SSH 密钥登录，因此不需要密码。他们希望允许用户这样做——类似于 SSH 的“authorized_keys”的做法。

我建议他们编写一个简单的 PAM 模块来实现这一点，但是他们表示对这种前景感到害怕。所以我来了，带你们完成这个相对简单的过程。

通常我以这样一种方式迭代地开发一个应用程序，我总是有一个可以编译并且不会做任何不利事情的应用程序。因此，我将首先介绍构建任何 PAM 模块的步骤，然后逐个功能部分地添加特定的功能。

首先，我们将从一个基本的 PAM 模块开始，它对所有内容都返回“ignore”:

```
/* Define which PAM interfaces we provide */
#define PAM_SM_ACCOUNT
#define PAM_SM_AUTH
#define PAM_SM_PASSWORD
#define PAM_SM_SESSION 
/* Include PAM headers */
#include <security/pam_appl.h> #include <security/pam_modules.h>  
/* PAM entry point for session creation */
int pam_sm_open_session(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
}

/* PAM entry point for session cleanup */
int pam_sm_close_session(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
}

/* PAM entry point for accounting */
int pam_sm_acct_mgmt(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
}

/* PAM entry point for authentication verification */
int pam_sm_authenticate(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
}

/*
   PAM entry point for setting user credentials (that is, to actually
   establish the authenticated user's credentials to the service provider)
 */
int pam_sm_setcred(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
}

/* PAM entry point for authentication token (password) changes */
int pam_sm_chauthtok(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        return(PAM_IGNORE);
} 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们有了 PAM 模块，它什么也不做。让我们继续编译它(指定 Linux 编译):

```
user@build$ gcc -fPIC -DPIC -shared -rdynamic -o pam_ignore.so pam_ignore.c 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您正在运行一个多库系统，您将需要为您的系统具有“libpam”的每个体系结构编译 PAM 模块，例如 Linux/x86_64 和 Linux/i386:

```
user@build$ gcc -m32 -fPIC -DPIC -shared -rdynamic -o pam_ignore_32.so pam_ignore.c
user@build$ gcc -m64 -fPIC -DPIC -shared -rdynamic -o pam_ignore_64.so pam_ignore.c 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将把 PAM 模块安装到它应该存在的路径中。在大多数 Linux 系统上，这是“/lib/security”(或者在 32/64 多库系统上，对于 32 位库是“/lib/security”，对于 64 位库是“/lib64/security”)

```
root@test# cp pam_ignore_32.so /lib/security/pam_ignore.so
root@test# cp pam_ignore_64.so /lib64/security/pam_ignore.so
root@test# chown root:root /lib/security/pam_ignore.so /lib64/security/pam_ignore.so
root@test# chmod 755 /lib/security/pam_ignore.so /lib64/security/pam_ignore.so 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们应该配置我们的 PAM 实现来实际使用这个模块。这是首先必须开始决定我们的系统应该如何与 PAM 模块交互的地方。为了确定这一点，我们回到我们的问题，回到讨论 PAM 实际上是如何工作的。

我们希望 SSH 允许所有用户使用他们的 SSH 密钥登录，但是有选择地拒绝使用密码的访问。通常，这种决策是由模块中的“帐户”接口做出的。“account”接口用于确定一个帐户对于这次登录是否有效，因此如果用户这样指定，它将返回 PAM_PERM_DENIED。但是当我们处理“account”接口时，用户已经通过 SSH 密钥或密码进行了身份验证——我们不知道发生了哪种情况！

沿着两条基本的推理路线，有几种方法可以解决这个难题。第一条理由是，我们可以使用 PAM 模块的“身份验证”接口来存储发生了什么，然后再从 PAM 模块的“帐户”接口中检索该信息。第二条理由是，PAM 模块的“认证”接口实际上只在基于非 SSH-key 的认证期间从 SSH 调用，所以我们可以在那里返回 failure。

每种方法都有好处，但为了简单起见，我们将使用后一种方法。

因此，考虑到这一点，我们现在准备将 PAM 模块插入到 PAM 配置中。我们已经确定我们只关心它的“认证”接口，而且只针对 SSH。我们如何插入取决于您的 PAM 配置，但是在 Linux 上通常是通过编辑“/etc/pam.d/sshd”来完成的。对于我们的模块，我们将在所有其他的“auth”模块之上插入类似这样的内容(只添加了第一行，第二行是可能已经存在的内容的示例):

```
auth       requisite         pam_ignore.so
auth       include           system-auth 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些关于 PAM 的重要细节:

1.  PAM 指令是按顺序执行的
2.  PAM 模块有一个“结果”的概念(成功、失败、忽略、错误)
3.  “行动”(必要的、必需的、充分的等等)表明了对结果究竟做了什么

这里有一个关于我们上面使用的“action”的非常重要的注意事项，“requisite”，它意味着如果我们的 PAM 模块返回失败，该失败将立即返回给应用程序以返回给用户。我们这样做是因为我们不希望像“pam_tally”这样的东西将此视为身份验证尝试和失败。如果返回成功，如果“PAM 堆栈”中没有任何其他内容返回任何失败，则可以认为认证成功。我们不想这样做，因为我们实际上不检查任何人的认证令牌(密码)，所以我们将只返回 PAM_IGNORE 或 PAM_AUTH_ERR。

好了，现在我们已经有了 PAM 模块并正在使用。我们应该确保此时我们仍然可以登录到我们的测试系统。我们应该用一个新的连接来完成这项工作，并保留现有的根会话，以防我们需要撤销更改。

在我们验证了我们的 PAM 模块是可操作的并且我们仍然可以对系统进行认证之后，我们可以继续让我们的 PAM 模块实际做一些事情。最后。

因为我们只提供了一个“认证”接口，所以我们将只修改 *pam_sm_authenticate* 函数，我不会重复我们包含的其他函数或 C 头文件。然而，你的源代码应该是这个修改和上面的原始代码的集合。

我们希望身份验证系统做的第一件事是识别试图进行身份验证的用户。方便的是，PAM 为我们提供了这样一个函数- [pam_get_user()](http://pubs.opengroup.org/onlinepubs/8329799/pam_get_user.htm) 。由于我们将在运行时被任何使用我们的应用程序链接到 PAM，所以我们不需要在编译时表明我们依赖于它。

*pam_get_user()* 采用三(3)个参数:

1.  PAM 句柄(pamh)；
2.  存储用户名(用户)的地方；和
3.  可选提示，如果尚未收集用户名(提示)

这些都比较容易满足。我们在中操作的函数已经为我们提供了 PAM 句柄(pamh)。我们应该已经有用户名了，所以不太在意提示。最困难的事情是提供一个存储用户名的地方。文档表明这个参数是一个指向 C 风格字符串的指针(指向一个字符的指针)，并且我们不需要在以后释放它。这对我们来说很容易。

好吧，让我们从这里开始:

```
#include <unistd.h>  int pam_sm_authenticate(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        const char *user = NULL;
        int pgu_ret;

        pgu_ret = pam_get_user(pamh, &user, NULL);
        if (pgu_ret != PAM_SUCCESS || user == NULL) {
                return(PAM_IGNORE);
        }

        return(PAM_IGNORE);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了用户名。下一步是实际检查我们想要使用的文件，以确定我们应该成功还是失败。我们把这个定义为“ *<用户 _ 主页 _ 目录>* /。ssh/nopasswd”。这意味着我们需要找到用户的主目录。

为了从我们所知道的用户信息(他们的用户名)中获取用户的主目录，我们需要使用 getpwnam 函数族中的一个函数。具体来说，我们将使用 [getpwnam_r()](http://pubs.opengroup.org/onlinepubs/009696699/functions/getpwnam.html) ，因为 PAM 模块需要可重入，因为它们链接到可能有多个线程做不相关事情的应用程序中。

所以我们添加了 *getpwnam_r()* 的代码看起来像这样:

```
#include <unistd.h> #include <pwd.h>  int pam_sm_authenticate(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        struct passwd *pw = NULL, pw_s;
        const char *user = NULL;
        char buffer[1024];
        int pgu_ret, gpn_ret;

        pgu_ret = pam_get_user(pamh, &user, NULL);
        if (pgu_ret != PAM_SUCCESS || user == NULL) {
                return(PAM_IGNORE);
        }

        gpn_ret = getpwnam_r(user, &pw_s, buffer, sizeof(buffer), &pw);
        if (gpn_ret != 0 || pw == NULL || pw->pw_dir == NULL || pw->pw_dir[0] != '/') {
                return(PAM_IGNORE);
        }

        return(PAM_IGNORE);
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用 *snprintf()* 构造要检查的文件的路径，并使用 *access()* :
实际检查它

```
#include <unistd.h> #include <stdio.h> #include <pwd.h>  int pam_sm_authenticate(pam_handle_t *pamh, int flags, int argc, const char **argv) {
        struct passwd *pw = NULL, pw_s;
        const char *user = NULL;
        char buffer[1024], checkfile[1024];
        int pgu_ret, gpn_ret, snp_ret, a_ret;

        pgu_ret = pam_get_user(pamh, &user, NULL);
        if (pgu_ret != PAM_SUCCESS || user == NULL) {
                return(PAM_IGNORE);
        }

        gpn_ret = getpwnam_r(user, &pw_s, buffer, sizeof(buffer), &pw);
        if (gpn_ret != 0 || pw == NULL || pw->pw_dir == NULL || pw->pw_dir[0] != '/') {
                return(PAM_IGNORE);
        }

        snp_ret = snprintf(checkfile, sizeof(checkfile), "%s/.ssh/nopasswd", pw->pw_dir);
        if (snp_ret >= sizeof(checkfile)) {
                return(PAM_IGNORE);
        }

        a_ret = access(checkfile, F_OK);
        if (a_ret == 0) {
                /* The user's file exists, return authentication failure */
                return(PAM_AUTH_ERR);
        }

        return(PAM_IGNORE);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我们或许应该将它命名为“pam_ignore”之外的名称，比如“pam_ssh_denypasswd”或类似的名称。

然后我们如上编译、安装和测试，并宣布胜利。

## 万岁。

后记:

如果您希望进一步开发 PAM 模块，那么在这一点上开始整合 GNU autoconf 来支持多平台是一个好主意。我有一个类似于“pam_ignore”的框架，名为“ [pam_success](http://www.rkeene.org/projects/info/resources/diatribes/bloglog/pam/pam_success-0.1.tar.gz) ”，可以用于这个目的。