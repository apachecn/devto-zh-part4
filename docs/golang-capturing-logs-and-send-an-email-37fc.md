# Golang:捕获日志并发送电子邮件

> 原文：<https://dev.to/douglasmakey/golang-capturing-logs-and-send-an-email-37fc>

在我的公司，我们有一个用 Golang 编写的 ETL 来处理与我们合作伙伴的集成，每个集成都在一个使用 cronjob k8s 的独特且隔离的 POD 中执行，每个集成都使用标准库中的`log`包打印一堆数据和每个执行步骤的指标，所有这些日志都有助于监控与不同工具的集成。

在我的团队中，现在我们希望在流程日志的某些集成失败时收到一封电子邮件，因此，我们使用了一个特性`log`来更改名为`SetOutput`的标准日志记录器的输出目的地。

使用`io.MultiWriter`我们可以创建一个写入器组合多个写入器，在这种情况下，我们将使用标准的`os.Stderr`组合一个缓冲区，将日志保存到缓冲区中，并将日志保存在`stderr`中以供监控。

*包`log`默认使用`os.Stderr`。*

```
 buf := new(bytes.Buffer)
        w := io.MultiWriter(buf, os.Stderr)
        log.SetOutput(w) 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们所有的日志都被放入缓冲区，因此我们可以创建一个文件，将它保存在一个临时文件中，并将其附加到电子邮件中。

```
 f, err := os.Create("/path/log.txt")
    if err != nil {
        // Handler ....
    }

    defer f.Close()

    // We copy the buffer into the file.
    if _, err := io.Copy(f, buf); err != nil {
        // Handler ....
    } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用日志文件发送电子邮件了，我发现一些实现只用标准库来发送带附件的电子邮件，但是它们不起作用，所以我结合了不同的方法，得到了这个实现。

```
package main

import (
    "bytes"
    "encoding/base64"
    "fmt"
    "io/ioutil"
    "mime/multipart"
    "net/smtp"
    "os"
    "path/filepath"
)

var (
    host       = os.Getenv("EMAIL_HOST")
    username   = os.Getenv("EMAiL_USERNAME")
    password   = os.Getenv("EMAIL_PASSWORD")
    portNumber = os.Getenv("EMAIL_PORT")
)

type Sender struct {
    auth smtp.Auth
}

type Message struct {
    To          []string
    Subject     string
    Body        string
    Attachments map[string][]byte
}

func New() *Sender {
    auth := smtp.PlainAuth("", username, password, host)
    return &Sender{auth}, nil
}

func (s *Sender) Send(m *Message) error {
    return smtp.SendMail(fmt.Sprintf("%s:%s", host, portNumber), s.auth, username, m.To, m.ToBytes())
}

func NewMessage(s, b string) *Message {
    return &Message{Subject: s, Body: b, Attachments: make(map[string][]byte)}
}

func (m *Message) AttachFile(src string) error {
    b, err := ioutil.ReadFile(src)
    if err != nil {
        return err
    }

    _, fileName := filepath.Split(src)
    m.Attachments[fileName] = b
    return nil
}

func (m *Message) ToBytes() []byte {
    buf := bytes.NewBuffer(nil)
    withAttachments := len(m.Attachments) > 0

    buf.WriteString(fmt.Sprintf("Subject: %s\n", m.Subject))
    buf.WriteString("MIME-Version: 1.0\n")
    writer := multipart.NewWriter(buf)
    boundary := writer.Boundary()

    if withAttachments {
        buf.WriteString(fmt.Sprintf("Content-Type: multipart/mixed; boundary=%s\n", boundary))
        buf.WriteString(fmt.Sprintf("--%s\n", boundary))
    }

    buf.WriteString("Content-Type: text/plain; charset=utf-8\n")
    buf.WriteString(m.Body)

    if withAttachments {
        for k, v := range m.Attachments {
            buf.WriteString(fmt.Sprintf("\n\n--%s\n", boundary))
            buf.WriteString("Content-Type: application/octet-stream\n")
            buf.WriteString("Content-Transfer-Encoding: base64\n")
            buf.WriteString(fmt.Sprintf("Content-Disposition: attachment; filename=%s\n", k))

            b := make([]byte, base64.StdEncoding.EncodedLen(len(v)))
            base64.StdEncoding.Encode(b, v)
            buf.Write(b)
            buf.WriteString(fmt.Sprintf("\n--%s", boundary))
        }

        buf.WriteString("--")
    }

    return buf.Bytes()
}

func main() {
    sender := New()
    m := NewMessage("Test", "Body message.")
    m.To = []string{"to@gmail.com"}
    m.AttachFile("/path/to/file")
    fmt.Println(s.Send(m))
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一些改善这个实现的技巧或者一个做得更好的方法，那将会很棒...T3】

[Github](https://gist.github.com/douglasmakey/90753ecf37ac10c25873825097f46300) 。