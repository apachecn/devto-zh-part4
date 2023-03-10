# 优化全文搜索引擎-压缩

> 原文：<https://dev.to/fprime/optimizing-a-full-text-search-engine-compression-49jm>

大约三个月前，我遇到了一个问题。问题是，我希望能够根据一集的脚本而不是一集的标题、标签等来搜索播客。然而，搜索大量的文本变得非常困难非常快。普通的数据库引擎(MySQL、MongoDB、Postgresql 等)没有提供我所寻求的性能水平。我想要一个非常快速和容易部署的东西，而不需要写很多代码。

所以，为了解决我的困境，我决定自己动手。我决定构建自己的解决方案。一个全文索引和搜索引擎服务器，可以处理大量信息并提供近乎即时的搜索结果。

自从第一个项目开始开发以来，一些人已经对此产生了兴趣，并极大地帮助这个项目进入下一个阶段。我们正在朝着创建一个轻量级且易于部署的生产就绪型全文搜索引擎的目标快速前进。

问题是它还没有出现。有问题，相当多的问题。使 Fist 不适合任何生产用途的问题。为了让 Fist 更上一层楼，我们需要让它更轻、更快、更高效。

Fist 目前面临三个紧迫的问题。首先是保存到磁盘的索引文件变得太大太快。第二个问题是大量的并发请求处理得不够快。第三个问题是索引算法太慢。

在这个由三部分组成的博客系列中，我们将实施必要的改变来帮助缓解这些问题。

我们将:

1.  实现索引文件压缩
2.  优化请求处理
3.  优化索引

这篇文章是这个系列的第一篇，将介绍我们如何使用 [liblzf](http://software.schmorp.de/pkg/liblzf.html) 在索引文件保存到磁盘之前对其进行压缩。这大大增加了我们可以存储在系统上的索引的大小，而不会占用不必要的存储空间。

liblzf 是一个非常小的数据压缩库。整个库由 4 个文件组成。2 .c 文件和 2。h 文件。这使得它很容易嵌入到代码库中，同时仍然保持轻量级。差不多，这正是我们在寻找的。

LZF 压缩是一种非常快速有效的数据压缩算法。Redis 和 OpenVPN 都使用它来满足他们的数据压缩需求。它已经被证明在真实的生产环境中工作良好，如果需要保持轻量级，它是一个很好的选择。

Fist 目前在将数据库文件保存到磁盘时不使用任何类型的数据压缩。这使得在开始时实现索引序列化并将其写入磁盘变得更加容易。不过，我们现在已经过了那个阶段，指数的增长速度开始成为一个问题。

由于我是 Joe Rogan Experience 播客的热心听众，我认为这将是开始向 Fist 提供真实数据的好地方。每集也超过三个小时，所以有很多对话可以编入索引。对我来说幸运的是，亚伦·亨德里克很友好地公开了 JRE 播客一系列不同剧集的脚本供我们使用[https://github.com/achendrick/jrescribe-transcripts](https://github.com/achendrick/jrescribe-transcripts)

我拼凑了一个 Python 脚本，它解析了脚本文件，只提取了对话，并使用 [fistpy](https://github.com/dyne/fistpy) 将其编入索引。每个包含该集脚本的文件(有些没有脚本)大约有 120KB - 170KB 大小。

我使用的脚本如下。它要求`jrescribe-transcripts`文件夹和它运行的目录在同一个目录下，并且依赖于 [fistpy](https://github.com/dyne/fistpy) 客户端库。它还要求第一个在本地端口 5575 上运行，这是默认端口。

```
import os
import json
import re
import string
from fist.client import Fist

f = Fist("localhost", 5575)
files = os.listdir("jrescribe-transcripts")

bytes_indexed = 0

for fon, fname in enumerate(files):
    fpath = "jrescribe-transcripts/{}".format(fname)
    if os.path.isfile(fpath):
        with open(fpath, 'r') as script:
            data = script.read()
            data = data.replace("---json", '')
            data = data.split("---")
            try:
                json_data = json.loads(data[0])
            except Exception as e:
                print(f"Failed to read JSON data from {fname}")

            script = data[1].lower()
            remove_strings = ["<episode-header />", "<episode-footer />", "<transcribe-call-to-action />", '\n']

            for string_to_remove in remove_strings:
                script = script.replace(string_to_remove, '')
            for i in re.findall("<timemark seconds=\"[0-9]+\" />", script):
                script = script.replace(i, '')
            for p in string.punctuation:
                script = script.replace(p, '')

            bytes_indexed += len(script)
            f.index(f"{fname} {script}") 
```

在索引了 1395 个可用文件中的 358 个之后，索引已经达到了惊人的 607MB。这并不令人惊讶，幸运的是还有很大的改进空间。由于我们的索引自然包含许多重复的单词，LZF 应该能够很好地压缩信息。

下面是序列化函数在实现压缩之前的样子。

```
void sdump(hashmap *hmap) {
    // Write size of hashmap to file. (# keys)

    FILE *dump = fopen("fist.db", "wb");

    uint32_t num_indices = 0;

    for(int i = 0; i < HMAP_SIZE; i++) {
        // Get number of indices that have values
        hashmap on = hmap[i];
        if(on.length > 0)
            num_indices++;
    }

    fwrite(&num_indices, sizeof(num_indices), 1, dump);
    // Iterate through hashmap and write key and array of values to file

    for(int i = 0; i < HMAP_SIZE; i++) {
        hashmap on = hmap[i];
        if(on.length > 0) {
            for(int key = 0; key < on.length; key++) {
                keyval object = on.maps[key];
                uint32_t length = object.key.length;
                // Writes key length and key name to db file
                fwrite(&length, sizeof(length), 1, dump);
                fwrite(dtext(object.key), object.key.length, 1, dump);

                uint32_t num_values = object.values.length;
                // Writes number of values associated with key to db file
                fwrite(&num_values, sizeof(num_values), 1, dump);
                for(int value = 0; value < object.values.length; value++) {
                    // Writes value to db file
                    dstring value_on = object.values.values[value];
                    uint32_t val_length = value_on.length;
                    fwrite(&val_length, sizeof(val_length), 1, dump);
                    fwrite(dtext(value_on), value_on.length, 1, dump);
                }
            }
        }
    }
    fclose(dump);
} 
```

这个名为`sdump()`的函数非常简单。它接收一个指向`hashmap`的指针，并将数据序列化为定制的二进制格式，最终将被写入磁盘上一个名为`fist.db`的文件中。这里的目标是获取将要写入的数据，并在写入之前对其进行压缩。

为了做到这一点，我们必须对`sdump()`做一些更改，并实现一个名为`sdump_compress()`的新函数，它允许我们首先将二进制数据写入一个临时文件，压缩该数据，然后将信息写入磁盘。这方面的代码如下。

```
void sdump_compress(unsigned char *data, uint64_t original_size) {
    FILE *compressed = fopen("fist.db", "wb");
    fwrite(&original_size, sizeof(original_size), 1, compressed);

    char *buffer;
    if((buffer = malloc(original_size * 3)) == NULL) {
        perror("Could not allocate memory durring compression. DB file will not be saved.");
        fclose(compressed);
        return;
    }
    long size;
    if(!(size = lzf_compress(data, original_size, buffer, original_size * 3))) {
        printf("Compression error\n");
    }
    fwrite(buffer, size, 1, compressed);
    fclose(compressed);
    free(buffer);
}

void sdump(hashmap *hmap) {
    // Write binary data to a temporary file, load the temp file into memory, compress it, save it
    // to disk.

    FILE *dump = tmpfile();

    if(dump == NULL) {
        perror("Could not create tmpfile during sdump. DB file will not be saved.");
        return;
    }

    uint32_t num_indices = 0;

    for(int i = 0; i < HMAP_SIZE; i++) {
        // Get number of indices that have values
        hashmap on = hmap[i];
        if(on.length > 0)
            num_indices++;
    }

    fwrite(&num_indices, sizeof(num_indices), 1, dump);
    // Iterate through hashmap and write key and array of values to file

    for(int i = 0; i < HMAP_SIZE; i++) {
        hashmap on = hmap[i];
        if(on.length > 0) {
            for(int key = 0; key < on.length; key++) {
                keyval object = on.maps[key];
                uint32_t length = object.key.length;
                // Writes key length and key name to db file

                fwrite(&length, sizeof(length), 1, dump);
                fwrite(dtext(object.key), object.key.length, 1, dump);

                uint32_t num_values = object.values.length;
                // Writes number of values associated with key to db file
                fwrite(&num_values, sizeof(num_values), 1, dump);
                for(int value = 0; value < object.values.length; value++) {
                    // Writes value to db file
                    dstring value_on = object.values.values[value];
                    uint32_t val_length = value_on.length;

                    fwrite(&val_length, sizeof(val_length), 1, dump);
                    fwrite(dtext(value_on), value_on.length, 1, dump);
                }
            }
        }
    }

    fseek(dump, 0, SEEK_END);
    uint64_t len = ftell(dump);
    fseek(dump, 0, SEEK_SET);
    unsigned char *buffer;
    if((buffer = malloc(len)) == NULL) {
        perror("Could not allocate memory during sdump. DB file will not be saved.");
        fclose(dump);
        return;
    }
    fread(buffer, 1, len, dump);

    sdump_compress(buffer, len);
    fclose(dump);
    free(buffer);
} 
```

只需对`sdump()`做一些改动。首先，我们现在打开一个新的`tmpfile()`而不是`fist.db`来写。然后，我们将二进制数据写入这个临时文件，并分配一个文件大小的缓冲区。然后，这些数据被传递给一个新函数`sdump_compress()`，该函数使用`lzf_compress()`压缩二进制数据，并将该信息写入一个名为`fist.db`的文件。

注意在`sdump_compress()`中，在将压缩的二进制信息写入`fist.db`之前，我们将解压缩信息的原始大小写入输出文件的前 8 个字节。这在解压过程中是需要的。

解压是反过来的同一个过程。如果你有兴趣了解它是如何工作的，你可以在这里查看整个`serializer.c`文件

最重要的是这有多大帮助。回到我们最初对 JRE podcast 文件的测试，在相同数量的脚本文件上运行相同的 Python 脚本创建了大约 300MB 的`fist.db`文件，而不是最初测试的 600MB 以上的文件。文件大小实际上减少了一半。这并不奇怪，因为有很多重复的信息被压缩。

计时显示，新的压缩算法比旧算法大约慢 45%。但是，保存到磁盘只是偶尔在服务器停止时以 2 分钟的间隔发生。这对索引或搜索速度没有任何影响，所以现在可以接受。文件大小的显著减小也弥补了速度的降低。

速度问题可以通过不使用临时来解决，但是为了节省时间和代码，选择了这个实现。