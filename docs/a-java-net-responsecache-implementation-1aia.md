# java.net.ResponseCache 实现

> 原文：<https://dev.to/allenball/a-java-net-responsecache-implementation-1aia>

Java [`URLConnection`](https://docs.oracle.com/javase/8/docs/api/java/net/URLConnection.html?is-external=true) 机制可以配置成使用 [`ResponseCache`](https://docs.oracle.com/javase/8/docs/api/java/net/ResponseCache.html?is-external=true) 。本文描述了一个`ResponseCache`实现。

## 实施大纲

实现需要子类化`ResponseCache`，提供 [`get(URI,String,Map<String,List<String>>)`](https://docs.oracle.com/javase/8/docs/api/java/net/ResponseCache.html#get-java.net.URI-java.lang.String-java.util.Map-) 和 [`put(URI,URLConnection)`](https://docs.oracle.com/javase/8/docs/api/java/net/ResponseCache.html#put-java.net.URI-java.net.URLConnection-) 的实现。每个方法的非平凡实现需要提供 [`CacheResponse`](https://docs.oracle.com/javase/8/docs/api/java/net/CacheResponse.html) 和 [`CacheRequest`](https://docs.oracle.com/javase/8/docs/api/java/net/CacheRequest.html) 的具体实现。实施的概要是:

```
import java.net.CacheRequest;
import java.net.CacheResponse;
import java.net.ResponseCache;
import java.net.URI;
import java.net.URLConnection;

public class ResponseCacheImpl extends ResponseCache {

    /**
     * Default {@link ResponseCacheImpl}.
     */
    public static final ResponseCacheImpl DEFAULT = new ResponseCacheImpl();

    private ResponseCacheImpl() {
        super();
        /*
         * ...
         */
    }

    @Override
    public CacheResponse get(URI uri, String method,
                             Map<String,List<String>> headers) {
        CacheResponseImpl response = null;
        /*
         * ...
         */
        return response;
    }

    @Override
    public CacheRequest put(URI uri, URLConnection connection) {
        CacheRequestImpl request = null;
        /*
         * ...
         */
        return request;
    }

    public class CacheRequestImpl extends CacheRequest {
        /*
         * ...
         */
    }

    public class CacheResponseImpl extends CacheResponse {
        /*
         * ...
         */
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:`get()`和`put()`方法可能会返回`null`,表明对于那个 [`URI`](https://docs.oracle.com/javase/8/docs/api/java/net/URI.html) 没有可用的缓存设施。

## 缓存设计

缓存将是一个简单的文件系统层次结构，位于`${user.home}/.config/java/cache/`下。一个缓存的 [`URI`](https://docs.oracle.com/javase/8/docs/api/java/net/URI.html) 将映射到一个目录，如果对象被缓存，这个目录将存在并包含两个文件:`BODY`和`HEADERS`。

```
 private Path cache(URI uri) {
        Path path = cache.resolve(uri.getScheme().toLowerCase());
        String host = uri.getHost().toLowerCase();
        int port = uri.getPort();

        if (port > 0) {
            host += ":" + String.valueOf(port);
        }

        path = path.resolve(host);

        String string = uri.getPath();

        if (string != null) {
            for (String substring : string.split("[/]+")) {
                if (isNotEmpty(substring)) {
                    path = path.resolve(substring);
                }
            }
        }

        return path.normalize();
    } 
```

Enter fullscreen mode Exit fullscreen mode

不会试图缓存“复杂”[`URI`](https://docs.oracle.com/javase/8/docs/api/java/net/URI.html)`isCacheable`被定义为:

```
 private boolean isCacheable(URI uri) {
        return (uri.isAbsolute()
                && (! uri.isOpaque())
                && uri.getUserInfo() == null
                && uri.getQuery() == null
                && uri.getFragment() == null);
    } 
```

Enter fullscreen mode Exit fullscreen mode

一个 [`URI`](https://docs.oracle.com/javase/8/docs/api/java/net/URI.html) 被缓存如果它`isCacheable()`和它的主体存在于缓存:

```
 private boolean isCached(URI uri) {
        return isCacheable(uri) && Files.exists(cache(uri).resolve(BODY));
    } 
```

Enter fullscreen mode Exit fullscreen mode

## ResponseCache.put(URI，URLConnection)实现

此方法允许调用方将对象放入缓存中。 [`URLConnection.getHeaderFields()`](https://docs.oracle.com/javase/8/docs/api/java/net/URLConnection.html#getHeaderFields--) 是用一个 [`XMLEncoder`](https://docs.oracle.com/javase/8/docs/api/java/beans/XMLEncoder.html) 连同“正文”一起保存的。

```
 @Override
    public CacheRequest put(URI uri, URLConnection connection) {
        CacheRequestImpl request = null;

        if (isCacheable(uri)) {
            if (! connection.getAllowUserInteraction()) {
                request =
                    new CacheRequestImpl(cache(uri),
                                         connection.getHeaderFields());
            }
        }

        return request;
    }

    public class CacheRequestImpl extends CacheRequest {
        private final Path path;
        private final Map<String,List<String>> headers;

        private CacheRequestImpl(Path path, Map<String,List<String>> headers) {
            super();

            this.path = Objects.requireNonNull(path);
            this.headers = Objects.requireNonNull(headers);
        }

        @Override
        public OutputStream getBody() throws IOException {
            Files.createDirectories(path);

            XMLEncoder encoder =
                new XMLEncoder(Files.newOutputStream(path.resolve(HEADERS)));

            encoder.writeObject(headers);
            encoder.close();

            return Files.newOutputStream(path.resolve(BODY));
        } 
```

Enter fullscreen mode Exit fullscreen mode

## ResponseCache.get(URI，字符串，地图>)实现

此方法尝试从缓存中检索对象。如果被缓存，`CacheResponseImpl`提供先前保存的头和来自缓存文件的 [`InputStream`](https://docs.oracle.com/javase/8/docs/api/java/io/InputStream.html) 。头用一个 [`XMLDecoder`](https://docs.oracle.com/javase/8/docs/api/java/beans/XMLDecoder.html) 反序列化。

```
 @Override
    public CacheResponse get(URI uri, String method,
                             Map<String,List<String>> headers) {
        CacheResponseImpl response = null;

        if (isCached(uri)) {
            response = new CacheResponseImpl(cache(uri));
        }

        return response;
    }

    public class CacheResponseImpl extends CacheResponse {
        private final Path path;

        private CacheResponseImpl(Path path) {
            super();

            this.path = Objects.requireNonNull(path);
        }

        @Override
        public Map<String,List<String>> getHeaders() throws IOException {
            XMLDecoder decoder =
                new XMLDecoder(Files.newInputStream(path.resolve(HEADERS)));
            @SuppressWarnings("unchecked")
            Map<String,List<String>> headers =
                (Map<String,List<String>>) decoder.readObject();

            decoder.close();

            return headers;
        }

        @Override
        public InputStream getBody() throws IOException {
            return Files.newInputStream(path.resolve(BODY));
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 安装

[`ResponseCache`](https://docs.oracle.com/javase/8/docs/api/java/net/ResponseCache.html?is-external=true) 实现必须配置 [`ResponseCache.setDefault(ResponseCache)`](https://docs.oracle.com/javase/8/docs/api/java/net/ResponseCache.html#setDefault-java.net.ResponseCache-) 静态方法。下面的代码片段在安装目标之前检查是否安装了其他的`ResponseCache`。

```
 if (ResponseCache.getDefault() == null) {
            ResponseCache.setDefault(ResponseCacheImpl.DEFAULT);
        } 
```

Enter fullscreen mode Exit fullscreen mode

## 蚂蚁任务

使用 [`URLConnection`](https://docs.oracle.com/javase/8/docs/api/java/net/URLConnection.html?is-external=true) 和`ResponseCacheImpl`配置下载的 Ant 任务的关键部分如下所示。

```
@AntTask("download")
@NoArgsConstructor @ToString
public class DownloadTask extends Task {
    static {
        if (ResponseCache.getDefault() == null) {
            ResponseCache.setDefault(ResponseCacheImpl.DEFAULT);
        }
    }

    @NotNull @Getter @Setter
    private URI uri = null;
    @NotNull @Getter @Setter
    private File toFile = null;

    @Override
    public void execute() throws BuildException {
        try {
            URLConnection connection = getUri().toURL().openConnection();

            IOUtils.copy(connection.getInputStream(),
                         new FileOutputStream(getToFile()));

            log(getUri() + " --> " + getToFile());
        } catch (BuildException exception) {
            throw exception;
        } catch (RuntimeException exception) {
            throw exception;
        } catch (Exception exception) {
            throw new BuildException(exception);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## ResponseCacheImpl.java

完整实现:

```
/*
 * $Id: README.md 6485 2020-07-18 03:45:11Z ball $
 *
 * Copyright 2019 Allen D. Ball.  All rights reserved.
 */
package ball.net;

import java.beans.XMLDecoder;
import java.beans.XMLEncoder;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.CacheRequest;
import java.net.CacheResponse;
import java.net.ResponseCache;
import java.net.URI;
import java.net.URLConnection;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.attribute.PosixFilePermission;
import java.util.List;
import java.util.Map;
import java.util.Objects;
import java.util.Set;

import static java.nio.file.attribute.PosixFilePermissions.asFileAttribute;
import static java.nio.file.attribute.PosixFilePermissions.fromString;
import static org.apache.commons.lang3.StringUtils.isNotEmpty;

/**
 * {@link ResponseCache} implementation.
 *
 * @author {@link.uri mailto:ball@iprotium.com Allen D. Ball}
 * @version $Revision: 6485 $
 */
public class ResponseCacheImpl extends ResponseCache {

    /**
     * Default {@link ResponseCacheImpl}.
     */
    public static final ResponseCacheImpl DEFAULT = new ResponseCacheImpl();

    private static final String BODY = "BODY";
    private static final String HEADERS = "HEADERS";

    private final Path cache;

    private ResponseCacheImpl() {
        super();

        try {
            cache =
                Paths.get(System.getProperty("user.home"),
                          ".config", "java", "cache");

            Files.createDirectories(cache,
                                    asFileAttribute(fromString("rwx------")));
        } catch (Exception exception) {
            throw new ExceptionInInitializerError(exception);
        }
    }

    @Override
    public CacheResponse get(URI uri, String method,
                             Map<String,List<String>> headers) {
        CacheResponseImpl response = null;

        if (isCached(uri)) {
            response = new CacheResponseImpl(cache(uri));
        }

        return response;
    }

    @Override
    public CacheRequest put(URI uri, URLConnection connection) {
        CacheRequestImpl request = null;

        if (isCacheable(uri)) {
            if (! connection.getAllowUserInteraction()) {
                request =
                    new CacheRequestImpl(cache(uri),
                                         connection.getHeaderFields());
            }
        }

        return request;
    }

    private Path cache(URI uri) {
        Path path = cache.resolve(uri.getScheme().toLowerCase());
        String host = uri.getHost().toLowerCase();
        int port = uri.getPort();

        if (port > 0) {
            host += ":" + String.valueOf(port);
        }

        path = path.resolve(host);

        String string = uri.getPath();

        if (string != null) {
            for (String substring : string.split("[/]+")) {
                if (isNotEmpty(substring)) {
                    path = path.resolve(substring);
                }
            }
        }

        return path.normalize();
    }

    private boolean isCached(URI uri) {
        return isCacheable(uri) && Files.exists(cache(uri).resolve(BODY));
    }

    private boolean isCacheable(URI uri) {
        return (uri.isAbsolute()
                && (! uri.isOpaque())
                && uri.getUserInfo() == null
                && uri.getQuery() == null
                && uri.getFragment() == null);
    }

    private void delete(Path path) throws IOException {
        Files.deleteIfExists(path.resolve(HEADERS));
        Files.deleteIfExists(path.resolve(BODY));
        Files.deleteIfExists(path);
    }

    public class CacheRequestImpl extends CacheRequest {
        private final Path path;
        private final Map<String,List<String>> headers;

        private CacheRequestImpl(Path path, Map<String,List<String>> headers) {
            super();

            this.path = Objects.requireNonNull(path);
            this.headers = Objects.requireNonNull(headers);
        }

        @Override
        public OutputStream getBody() throws IOException {
            Files.createDirectories(path);

            XMLEncoder encoder =
                new XMLEncoder(Files.newOutputStream(path.resolve(HEADERS)));

            encoder.writeObject(headers);
            encoder.close();

            return Files.newOutputStream(path.resolve(BODY));
        }

        @Override
        public void abort() {
            try {
                delete(path);
            } catch (Exception exception) {
                throw new IllegalStateException(exception);
            }
        }
    }

    public class CacheResponseImpl extends CacheResponse {
        private final Path path;

        private CacheResponseImpl(Path path) {
            super();

            this.path = Objects.requireNonNull(path);
        }

        @Override
        public Map<String,List<String>> getHeaders() throws IOException {
            XMLDecoder decoder =
                new XMLDecoder(Files.newInputStream(path.resolve(HEADERS)));
            @SuppressWarnings("unchecked")
            Map<String,List<String>> headers =
                (Map<String,List<String>>) decoder.readObject();

            decoder.close();

            return headers;
        }

        @Override
        public InputStream getBody() throws IOException {
            return Files.newInputStream(path.resolve(BODY));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode