# HowTo:在 Ruby 中高效地处理大文件。

> 原文：<https://dev.to/netguru/howto-working-with-large-files-in-ruby-efficiently-189e>

## 如何在 Ruby 中读取文件？

### *运行以下命令生成测试文件:

```
❯ openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem 
```

它有一个明确定义的文件的开始和结束，这在阅读文件时很有用。

### 1。`File.read()`其实就是 [`IO.read()`](https://ruby-doc.org/core-2.6.4/IO.html#method-c-read) :

```
> file = File.read("cert.pem")
=> "-----BEGIN CERTIFICATE-----\nMIICljCCAX4CCQD5x/0DnI1UazANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ\nTDAeFw0xOTA4MzExOTQ0NDdaFw0yOTA4MjgxOTQ0NDdaMA0xCzAJBgNVBAYTAlBM\nMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2qJrZayMFRE7zIeUL8CZ\nzqsOcwEv6flF41EjIvVf6h164i+NGkRu9E0wo1LHYsoF5tutYKKpRLJoY9xGq+Jr\n1SPOJYGBaFqKyQye+lnSzJdpnCAklXObfJpGtBmKCm4OTcb8eC4nm2q4x3mNkP5Z\nTgzdfIhALCwtD6wsHcyy5qmqGfPWAaGUDHqAQRu7QV/vu5VzJXgN0c6Zj+bOWw4H\n7Zu+FxtpUACQk4lnqt9CUzp6GX3dIETTfA3cpTFvoxwqBZGnrjsgZA5HzbyKRUYi\naigbkyzc701sJaS8gcjIKDy2s8L8MfqaJkMu+N52e5tXoj4oQT9wPzxOou+GpYM/\n4QIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQDDrOrN+asQjkOwjPcNLkycy4TJ/6QE\nraNDVZ1N5h+70vIQwmmCS+hBN7SSM0f0OxgEggvK0etNQb6LXWXAIa7pMuzhqmHR\n9Q/NBizj+GOIvH7EoCTVKYUkRLxEq5i63cm0ZvFu9qwr8v7IGM4HkLo3A0F6+Vcp\nGNuOBNcGqAtCXNhgcpzu/6zWT2kAj1M82IC4aCIiTGovDidnp2ZO4bV5PTCy7ecd\naeJxt9LIlt/FVk29sjdtutPMZgtQwKKp2gWyY9D7/x8Dxpf2DCkjAtqEdN3/GER6\nlybIrvAtYW7MNmu9MLkxionOak9CoZGsVg0kiXliHrhfxrDc8qLe8rqV\n-----END CERTIFICATE-----\n"
> file.bytesize
=> 956
> file.class
=> String 
```

方法读取整个文件的内容，并将其作为单个字符串赋给变量。

### [2](#2-raw-filenew-endraw-and-its-synonym-raw-fileopen-endraw-)。 [`File.new()`](https://ruby-doc.org/core-2.6.4/File.html#method-c-new) 及其同义词 [`File.open()`](https://ruby-doc.org/core-2.6.4/File.html#method-c-open) :

```
> file = File.new("cert.pem")
=> #<File:cert.pem>
> lines = file.readlines
=> ["-----BEGIN CERTIFICATE-----\n",
 "MIICljCCAX4CCQD5x/0DnI1UazANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ\n",
 "TDAeFw0xOTA4MzExOTQ0NDdaFw0yOTA4MjgxOTQ0NDdaMA0xCzAJBgNVBAYTAlBM\n",
 "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2qJrZayMFRE7zIeUL8CZ\n",
 "zqsOcwEv6flF41EjIvVf6h164i+NGkRu9E0wo1LHYsoF5tutYKKpRLJoY9xGq+Jr\n",
 "1SPOJYGBaFqKyQye+lnSzJdpnCAklXObfJpGtBmKCm4OTcb8eC4nm2q4x3mNkP5Z\n",
 "TgzdfIhALCwtD6wsHcyy5qmqGfPWAaGUDHqAQRu7QV/vu5VzJXgN0c6Zj+bOWw4H\n",
 "7Zu+FxtpUACQk4lnqt9CUzp6GX3dIETTfA3cpTFvoxwqBZGnrjsgZA5HzbyKRUYi\n",
 "aigbkyzc701sJaS8gcjIKDy2s8L8MfqaJkMu+N52e5tXoj4oQT9wPzxOou+GpYM/\n",
 "4QIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQDDrOrN+asQjkOwjPcNLkycy4TJ/6QE\n",
 "raNDVZ1N5h+70vIQwmmCS+hBN7SSM0f0OxgEggvK0etNQb6LXWXAIa7pMuzhqmHR\n",
 "9Q/NBizj+GOIvH7EoCTVKYUkRLxEq5i63cm0ZvFu9qwr8v7IGM4HkLo3A0F6+Vcp\n",
 "GNuOBNcGqAtCXNhgcpzu/6zWT2kAj1M82IC4aCIiTGovDidnp2ZO4bV5PTCy7ecd\n",
 "aeJxt9LIlt/FVk29sjdtutPMZgtQwKKp2gWyY9D7/x8Dxpf2DCkjAtqEdN3/GER6\n",
 "lybIrvAtYW7MNmu9MLkxionOak9CoZGsVg0kiXliHrhfxrDc8qLe8rqV\n",
 "-----END CERTIFICATE-----\n"]
> lines.class
=> Array 
```

`new`或`open`方法返回一个`File`类的实例，我们可以调用`readlines`方法读取整个文件的内容，逐行分割，并返回一个字符串数组，其中一个元素是文件中的一行。

### 3。`File.readlines()`其实就是 [`IO.readlines()`](https://ruby-doc.org/core-2.6.4/IO.html#method-c-readlines) :

```
> lines = File.readlines("cert.pem")
=> ["-----BEGIN CERTIFICATE-----\n",
 "MIICljCCAX4CCQD5x/0DnI1UazANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ\n",
 "TDAeFw0xOTA4MzExOTQ0NDdaFw0yOTA4MjgxOTQ0NDdaMA0xCzAJBgNVBAYTAlBM\n",
 "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2qJrZayMFRE7zIeUL8CZ\n",
 "zqsOcwEv6flF41EjIvVf6h164i+NGkRu9E0wo1LHYsoF5tutYKKpRLJoY9xGq+Jr\n",
 "1SPOJYGBaFqKyQye+lnSzJdpnCAklXObfJpGtBmKCm4OTcb8eC4nm2q4x3mNkP5Z\n",
 "TgzdfIhALCwtD6wsHcyy5qmqGfPWAaGUDHqAQRu7QV/vu5VzJXgN0c6Zj+bOWw4H\n",
 "7Zu+FxtpUACQk4lnqt9CUzp6GX3dIETTfA3cpTFvoxwqBZGnrjsgZA5HzbyKRUYi\n",
 "aigbkyzc701sJaS8gcjIKDy2s8L8MfqaJkMu+N52e5tXoj4oQT9wPzxOou+GpYM/\n",
 "4QIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQDDrOrN+asQjkOwjPcNLkycy4TJ/6QE\n",
 "raNDVZ1N5h+70vIQwmmCS+hBN7SSM0f0OxgEggvK0etNQb6LXWXAIa7pMuzhqmHR\n",
 "9Q/NBizj+GOIvH7EoCTVKYUkRLxEq5i63cm0ZvFu9qwr8v7IGM4HkLo3A0F6+Vcp\n",
 "GNuOBNcGqAtCXNhgcpzu/6zWT2kAj1M82IC4aCIiTGovDidnp2ZO4bV5PTCy7ecd\n",
 "aeJxt9LIlt/FVk29sjdtutPMZgtQwKKp2gWyY9D7/x8Dxpf2DCkjAtqEdN3/GER6\n",
 "lybIrvAtYW7MNmu9MLkxionOak9CoZGsVg0kiXliHrhfxrDc8qLe8rqV\n",
 "-----END CERTIFICATE-----\n"]
> lines.class
=> Array 
```

在这里，我们通过在`File`类上调用类方法`readlines`，得到了与上一个例子相同的输出。

### [4](#4-raw-fileforeach-endraw-which-is-actually-raw-ioforeach-endraw-)。`File.foreach()`实际上是`IO.foreach()`:

```
> file = File.foreach("./cert.pem")
=> #<Enumerator: ...>
> file.entries
=> ["-----BEGIN CERTIFICATE-----\n",
 "MIICljCCAX4CCQD5x/0DnI1UazANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ\n",
 "TDAeFw0xOTA4MzExOTQ0NDdaFw0yOTA4MjgxOTQ0NDdaMA0xCzAJBgNVBAYTAlBM\n",
 "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2qJrZayMFRE7zIeUL8CZ\n",
 "zqsOcwEv6flF41EjIvVf6h164i+NGkRu9E0wo1LHYsoF5tutYKKpRLJoY9xGq+Jr\n",
 "1SPOJYGBaFqKyQye+lnSzJdpnCAklXObfJpGtBmKCm4OTcb8eC4nm2q4x3mNkP5Z\n",
 "TgzdfIhALCwtD6wsHcyy5qmqGfPWAaGUDHqAQRu7QV/vu5VzJXgN0c6Zj+bOWw4H\n",
 "7Zu+FxtpUACQk4lnqt9CUzp6GX3dIETTfA3cpTFvoxwqBZGnrjsgZA5HzbyKRUYi\n",
 "aigbkyzc701sJaS8gcjIKDy2s8L8MfqaJkMu+N52e5tXoj4oQT9wPzxOou+GpYM/\n",
 "4QIDAQABMA0GCSqGSIb3DQEBCwUAA4IBAQDDrOrN+asQjkOwjPcNLkycy4TJ/6QE\n",
 "raNDVZ1N5h+70vIQwmmCS+hBN7SSM0f0OxgEggvK0etNQb6LXWXAIa7pMuzhqmHR\n",
 "9Q/NBizj+GOIvH7EoCTVKYUkRLxEq5i63cm0ZvFu9qwr8v7IGM4HkLo3A0F6+Vcp\n",
 "GNuOBNcGqAtCXNhgcpzu/6zWT2kAj1M82IC4aCIiTGovDidnp2ZO4bV5PTCy7ecd\n",
 "aeJxt9LIlt/FVk29sjdtutPMZgtQwKKp2gWyY9D7/x8Dxpf2DCkjAtqEdN3/GER6\n",
 "lybIrvAtYW7MNmu9MLkxionOak9CoZGsVg0kiXliHrhfxrDc8qLe8rqV\n",
 "-----END CERTIFICATE-----\n"]
> lines.class
=> Array 
```

`foreach`方法返回一个枚举器实例，我们调用它返回一个字符串数组，同样，每个元素是文件中的一行。

正如我们在上面看到的，有许多方法可以让我们读取文件。然而，我们应该使用哪一个，为什么？让我们创建一个大文件并再次检查这些方法！

## 读取大文件应该用哪些方法？

### 生成我们的测试文件

首先，让我们生成一个包含随机数据的大文件:

```
require 'securerandom'
one_megabyte = 1024 * 1024

name = "large_1G"
size = 1000

File.open("./#{name}.txt", 'wb') do |file|
  size.times do
    file.write(SecureRandom.random_bytes(one_megabyte))
  end
end 
```

*   `w` -只写，将现有文件截短为零长度或创建一个新文件进行写入。
*   `b` -二进制文件模式。取消 Windows 上的 EOL < - > CRLF 转换。并将外部编码设置为 ASCII 位，除非明确指定。

结果我们生成了 1GB 的文件:

```
❯ ls -lah
...
-rw-r--r--   1 user  user   1.0G Aug 31 22:10 large_1G.txt 
```

### 定义我们的指标和分析器

在我们的实验中，我们可能希望跟踪两个最重要的指标:

*   **时间** -打开并读取文件需要多长时间？
*   **内存** -打开并读取文件需要多少内存？

还有一个额外的指标描述了垃圾收集器释放了多少对象。

我们可以准备简单的剖析方法:

```
# ./helpers.rb
require 'benchmark'

def profile_memory
  memory_usage_before = `ps -o rss= -p #{Process.pid}`.to_i
  yield
  memory_usage_after = `ps -o rss= -p #{Process.pid}`.to_i

  used_memory = ((memory_usage_after - memory_usage_before) / 1024.0).round(2)
  puts "Memory usage: #{used_memory} MB"
end

def profile_time
  time_elapsed = Benchmark.realtime do
    yield
  end

  puts "Time: #{time_elapsed.round(2)} seconds"
end

def profile_gc
  GC.start
  before = GC.stat(:total_freed_objects)
  yield
  GC.start
  after = GC.stat(:total_freed_objects)

  puts "Objects Freed: #{after - before}"
end

def profile
  profile_memory do 
    profile_time do 
      profile_gc do
        yield
      end
    end 
  end 
end 
```

### 测试我们读取文件的方法

*   `.read`

```
file = nil
profile do
  file = File.read("large_1G.txt")
end

Objects Freed: 39
Time: 0.52 seconds
Memory usage: 1000.05 MB 
```

*   `.new` + `#readlines`

```
file = nil
profile do
  file = File.new("large_1G.txt").readlines
end

Objects Freed: 39
Time: 4.19 seconds
Memory usage: 1298.4 MB 
```

*   `.readlines`

```
file = nil
profile do
  file = File.readlines("large_1G.txt")
end

Objects Freed: 39
Time: 4.24 seconds
Memory usage: 1284.61 MB 
```

*   `.foreach`

```
file = nil
profile do
  file = File.foreach("large_1G.txt").to_a
end

Objects Freed: 40
Time: 4.42 seconds
Memory usage: 1284.31 MB 
```

上面的例子允许我们读取整个文件，并将其作为一个字符串或字符串数组(文件中的每一行作为数组中的一个元素)存储在本地内存中。

正如我们所看到的，它要求**至少与文件**的大小一样大的内存:

*   一个**字符串** - 1GB 文件需要 1GB 内存。
*   一个**字符串数组** - 1GB 内存用于文件内容+一个数组的额外内存(+- 300MB)。这种方法有一个优点，我们可以访问我们想要的文件的任何一行，只要我们知道是哪一行。

在这一点上，我们可以看到，我们测试的方法并不真正有效。文件越大，我们需要的内存就越多。从长远来看，这种方法可能会导致一些严重的后果，甚至会扼杀应用程序。

现在，我们需要问自己一个问题。我们可以逐行处理我们的文件吗？如果是这样，那么我们可以用不同的方式读取我们的文件:

*   `.new` + `#each`

```
file = nil

profile do
  file = File.new("large_1G.txt")
  file.each { |line| line }
end

Objects Freed: 4100808
Time: 2.08 seconds
Memory usage: 57.68 MB 
```

*   `.new` + `#advise` + `#each`

```
file = nil

profile do
  file = File.new("large_1G.txt")
  file.advise(:sequential)
  file.each { |line| line }
end

Objects Freed: 4100808
Time: 2.22 seconds
Memory usage: 55.71 MB 
```

调用 [`#advise`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-advise) 方法宣告以特定模式从当前文件中访问数据的意图。这里使用`#advise`方法没有大的改进。

*   `.new` + `#read` -逐块读取

```
file = nil
chunk_size = 4096
buf = ""

profile do
  file = File.new("large_1G.txt")
  while buf = file.read(chunk_size)
    buf.tap { |buf| buf }
  end
end

Objects Freed: 256037
Time: 1.27 seconds
Memory usage: 131.64 MB 
```

我们将`chunk`定义为 4096 字节，并逐块读取文件。根据文件的结构，这种方法可能有用。

*   `.foreach` + `#each_entry`

```
file = nil

profile do
  file = File.foreach("large_1G.txt")
  file.each_entry { |line| line }
end

Objects Freed: 4100809
Time: 2.22 seconds
Memory usage: 53.02 MB 
```

创建一个枚举器实例作为`file`并使用`each_entry`方法逐行读取文件。

我们可以注意到的第一件事是，内存使用率非常低。主要原因是，我们逐行读取文件，当这一行被处理时，它就被垃圾收集了。我们可以看到，从释放的对象的大小来看，这是相当高的。

我们还试图在这里使用一个`#advise`方法，我们可以告诉如何处理我们的文件。更多关于 [`IO#advise`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-advise) 的信息可以在文档中找到。不幸的是，这对我们没有帮助。

除了 [`IO#each`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-each) 方法，我们还有类似的方法如 [`IO#each_byte`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-each_byte) (逐字节读取) [`IO#each_char`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-each_char) (逐字符读取) [`IO#each_codepoint`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-each_codepoint) 。

在按块读取的示例( [`IO#read`](https://ruby-doc.org/core-2.6.4/IO.html#method-i-read) )中，内存使用量将根据块的大小而变化。如果您发现这种方法有用，您可以试验一下块的大小。

当使用 [`IO.foreach`](https://ruby-doc.org/core-2.6.4/IO.html#method-c-foreach) 时，我们对枚举器进行操作，这给了我们更多的方法，如: [`IO#each_entry`](https://ruby-doc.org/core-2.6.4/Enumerable.html#method-i-each_entry) ， [`IO#each_slice`](https://ruby-doc.org/core-2.6.4/Enumerable.html#method-i-each_slice) ， [`IO#each_cons`](https://ruby-doc.org/core-2.6.4/Enumerable.html#method-i-each_cons) 。还有一个 [`lazy`](https://ruby-doc.org/core-2.6.4/Enumerable.html#method-i-lazy) 方法，返回一个[枚举器::Lazy](https://ruby-doc.org/core-2.6.4/Enumerator/Lazy.html) 。惰性枚举器有几个额外的方法，它们只在需要的时候枚举值。如果您不需要读取整个文件，但是，例如，查找包含给定表达式的特定行，那么可能值得检查一下。

我可以在这一点上完成这篇文章，但是如果在我们开始阅读我们需要解密的文件之前呢？让我们进一步看这个例子。

### 解密大文件，逐行处理

#### 先决条件

在我们解密文件之前，我们需要加密我们生成的大文件。我们将使用 256 位密钥长度的 AES，密码块链接(CBC)作为模式。

```
cipher = OpenSSL::Cipher::AES256.new(:CBC)
cipher.encrypt
KEY = cipher.random_key
IV = cipher.random_iv 
```

现在，让我们加密出文件:

```
cipher = OpenSSL::Cipher::AES256.new(:CBC)
cipher.encrypt
cipher.key = KEY
cipher.iv = IV

file = nil
enc_file = nil

profile do
  file = File.read("large_1G.txt")
  enc_file = File.open("large_1G.txt.enc", "wb")
  enc_file << cipher.update(file)
  enc_file << cipher.final
end

file.close
enc_file.close

Objects Freed: 12
Time: 3.6 seconds
Memory usage: 1000.02 MB 
```

似乎加密也是一个相当消耗内存的任务。让我们稍微调整一下算法:

```
cipher = OpenSSL::Cipher::AES256.new(:CBC)
cipher.encrypt
cipher.key = KEY
cipher.iv = IV

file = nil
enc_file = nil

profile do 
  buf = ""
  file = File.open("large_1G.txt", "rb")
  enc_file = File.open("large_1G.txt.enc", "wb")
  while buf = file.read(4096)
    enc_file << cipher.update(buf)
  end
  enc_file << cipher.final
end

file.close
enc_file.close

Objects Freed: 768048
Time: 5.05 seconds
Memory usage: 145.93 MB 
```

通过改变算法来按块读取和加密文件，使得任务消耗的内存更少。

#### 解密

好了，现在我们来尝试解密:

```
decipher = OpenSSL::Cipher::AES256.new(:CBC)
decipher.decrypt
decipher.key = KEY
decipher.iv = IV

dec_file = nil
enc_file = nil

profile do 
  buf = ""
  enc_file = File.open("large_1G.txt.enc", "rb")
  dec_file = File.open("large_1G.txt.dec", "wb")
  while buf = enc_file.read(4096)
    dec_file << decipher.update(buf)
  end
  dec_file << decipher.final
end

dec_file.close
enc_file.close

Objects Freed: 768050
Time: 3.5 seconds
Memory usage: 152.12 MB 
```

现在，让我们比较一下我们的文件是否正确加密和解密:

```
❯ diff large_1G.txt large_1G.txt.dec 
```

未发现任何差异。我们在这里很好！

我们设法大大降低了内存使用量。太好了！

将本文视为您可以在特定情况下使用的工具集。

**这篇文章最初发表在我的个人开发博客上:[https://tjay.dev/](https://tjay.dev/howto-working-efficiently-with-large-files-in-ruby/?utm_source=dev.to&utm_medium=footer-link&utm_campaign=reblogging)T3】**

*照片由[尔万·赫斯里](https://unsplash.com/@erwanhesry)在[Unsplash](https://unsplash.com/search/photos/luggage)T5 拍摄*