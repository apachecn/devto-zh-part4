# 轻松发送邮件！springboot、sendgrid 和 greenmail

> 原文：<https://dev.to/georgeoikawa/send-mail-easily-springboot-sendgrid-and-greenmail-9g>

这是一个关于使用 sendgrid 电子邮件提供商的 api 的简短演示。
**克隆 github 库以获得更多细节！**
我正在通过 sendgrid java 客户端和 springboot 微服务内部使用该 api，像所有好代码一样，它是经过测试的代码，让我们使用 greenmail 库来模拟 smtp 服务器接收电子邮件。

首先，您需要在 [Sendgrid](https://sendgrid.com) 中创建一个帐户，并启用一个 api 密匙以便在客户端使用。

有一些方法可以使用这个 sendgrid sdk，在这种情况下，将通过 http 连接以 json 格式发送电子邮件。还有另一种发送电子邮件的方法，那就是在 sendgrid 站点的管理面板中注册一个模板，并使用注册模板的密钥通过客户端发送。要调用 sendgrid，必须发送密钥，该密钥可在 [sendgrid 管理区](https://app.sendgrid.com/settings/api_keys)获得。

要调用 sendgrid api，需要像下面这样构建请求对象。

```
 public class RequestSendGridBuilder {
        private static final String MIME_TYPE_HTML = "text/html";
        private static final String ENDPOINT_MAIL_SEND = "mail/send";
        private Mail mail = new Mail();
        private Content content = new Content();
        public static RequestSendGridBuilder of() {
            return new RequestSendGridBuilder();
        }

        public RequestSendGridBuilder from(String from) {
            mail.setFrom(new Email(from));
            return this;
        }

        public RequestSendGridBuilder key(String key) {
            mail.addCustomArg("customerAccountNumber", key);
            return this;
        }

        public RequestSendGridBuilder content(String template) {
            content.setType(MIME_TYPE_HTML);
            content.setValue(template);
            return this;
        }

        public RequestSendGridBuilder subject(String subject) {
            mail.setSubject(subject);
            return this;
        }

        public RequestSendGridBuilder to(String to) {
            Personalization personalization = new Personalization();
            personalization.addTo(new Email(to));
            mail.addPersonalization(personalization);
            return this;
        }

        public Request build() throws IOException {
            mail.addContent(content);
            Request request = new Request();
            request.setMethod(Method.POST);
            request.setEndpoint(ENDPOINT_MAIL_SEND);
            request.setBody(mail.build());
            return request;
        }
    } 
```

在这之后调用服务:

```
 @Override
    public EmailResponseDTO send(EmailRequestDTO request) throws EmailConnectionException {
        try {
            Request sendGridRequest = RequestSendGridBuilder.of()
                    .content(request.getContent())
                    .subject(request.getSubject())
                    .to(request.getTo())
                    .from(request.getFrom())
                    .key(sendGridKey)
                    .build();
            Response sendGridResponse = sendGrid.api(sendGridRequest);
            return getResponse(sendGridResponse, sendGridRequest);
        } catch (IOException e) {
            throw new EmailConnectionException(e);
        }
    } 
```

是的，这很简单！但是不要忘了设置 sendgrid API_KEY，否则无法工作。

# 模板

只要发送一个包含 html 内容 json，就可以使用自己的模板引擎。在这种情况下，您的项目中会有您的模板(例如 html ),要更新它，需要一个新的部署。

如果您不想因为一封非常大的电子邮件而在网络上传输大量字节，如前所述，可以在[管理模板面板](https://sendgrid.com/solutions/transactional-email-templates/)中编写您的模板，创建模板后，将生成一个密钥来标识它，该密钥将由客户端发送给服务，因此您不必传输电子邮件的所有内容。此外，您可以在运行时直接在 https://sendgrid.com
网站上更新您的模板

```
 mail.setTemplateId(templateId);

    Request request = new Request();
    request.setMethod(Method.POST);
    request.setEndpoint(ENDPOINT_MAIL_SEND);
    request.setBody(mail.build());
    Response sendGridResponse = sendGrid.api(request); 
```

# 模板解析器参数

在这个例子中，我使用 mustache 为模板创建解析参数。这是一个使用非常简单而且非常高效的库。要更改模板变量的值，只需将字符串映射传递给 mustache 引擎，如下所示:

```
 public class TemplateService {
        private MustacheAutoConfiguration mustacheAutoConfiguration;

        public TemplateService(MustacheAutoConfiguration mustacheAutoConfiguration) {
            this.mustacheAutoConfiguration = mustacheAutoConfiguration;
        }

        public @NonNull String parseTemplateParams(EmailRequestDTO email) throws TemplateException {
            try {
                MustacheResourceTemplateLoader templateLoader = mustacheAutoConfiguration.mustacheTemplateLoader();
                Reader template = templateLoader.getTemplate(email.getTemplateName());
                return mustacheAutoConfiguration.mustacheCompiler(templateLoader)
                        .compile(template)
                        .execute(email.getTemplateParams());
            } catch (FileNotFoundException e) {
                throw new TemplateException("TEMPLATE_NOT_FOUND");
            } catch (MustacheException e) {
                throw new TemplateException("TEMPLATE_PARSE_ERROR", e);
            } catch (Exception e) {
                throw new TemplateException("GENERIC_ERROR", e);
            }
        }
    } 
```

# 用假冒的 smtp 邮件服务器测试发送

为了创建电子邮件集成测试，我使用了 greenmail 库。这个库允许您创建一个假的 smtp 服务器来接收客户端发送的电子邮件，这样就有可能保证您的实现有更好的资格和效率。

```
 public static GreenMail getGreenMail() {
        if (greenMail == null) {
            ServerSetup serverSetup = new ServerSetup(
                    SocketUtils.findAvailableTcpPort(
                            INIT_RANGE_EMAIL_PORT,
                            END_RANGE_EMAIL_PORT),
                    ServerSetup.getLocalHostAddress(),
                    ServerSetup.PROTOCOL_SMTP);
            greenMail = new GreenMail(serverSetup);
            greenMail.setUser(
                    EMAIL_USER_ADDRESS,
                    USER_NAME,
                    USER_PASSWORD);
            greenMail.start();
        }
        return greenMail;
    } 
```

# 源代码

要查看客户端工作，您可以使用完整的代码[这里](https://github.com/georgeoikawa/sendgrid-greenmail-client)
享受！！！

### 参考文献

[http://www.icegreen.com/greenmail/](http://www.icegreen.com/greenmail/)
T3】https://sendgrid.com/docs/api-reference/