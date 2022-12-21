# 在 hyper-ts 中构建类型安全和状态快速 APP 应用程序

> 原文：<https://dev.to/e_ntyo/hyper-ts-type-safe-stateless-express-2c64>

# 在 hyper-ts 中构建类型安全&stateless 的快速 APP 应用程序

## TL；博士；医生

使用 TypeScript 和 Express 开发 APP 应用程序时，存在以下课题。

1.  目前，`@types/express`中的类型定义并不严格
2.  在请求处理程序中杂乱无章地做，容易有状态

对于这样的课题，`gcanti/hyper-ts`是有效的。

## 1\. `@types/express`中的类型定义不是严格的

例如，我们来看一下[`Application`界面](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express-serve-static-core/index.d.ts#L472)的[`render`方法](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express-serve-static-core/index.d.ts#L980)的类型定义。

```
render(view: string, options?: object, callback?: (err: Error, html: string) => void): void; 
```

这个类型定义缺乏严密性。 首先，第二自变量的`options`的类型是`object`。 (`cookie`方法的选项被定义为`CookieOption`。 ）

另外，这与其说是严密性的错误，不如说`callback`的类型是`(err: Error | null, html: string) => void`是正确的(因为准备验证环境很麻烦，所以要各自进行必要的验证)。

不仅仅是 Express，在 JavaScript 库后面制作类型定义的情况下，经常会停留在这样模糊的状态(主要出处)。 但是，像[`export type Send = (body?: any) => Response;`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/express-serve-static-core/index.d.ts#L470)这样使用`any`的地方也很多，对于我们这些想尽量努力定义模型的人们来说，情况很痛苦。

## 2 .在请求处理程序中杂乱无章地做，容易有状态

以下代码是用于拙作[https://anygma.site](https://anygma.site) 的 rest 风格的 http API 的端点的实现。 用于受理用户的文本投稿。

```
app.post("/posts", async (req, res) => {
  const { title, body, createdById, password } = req.body as PostOption;
  if (!body) {
    return res.status(400).send("本文を入力してください");
  }
  if (body.length > 1200) {
    return res.status(400).send("本文は1200 字までです");
  }
  if (title && title.length > 50) {
    return res.status(400).send("タイトルは50 字までです");
  }
  if (!createdById) {
    return res.status(400).send("ユーザidが提供されませんでした");
  }
  const userRecord = await admin
    .auth()
    .getUser(createdById)
    .catch(error => {
      console.error({ error });
      return null;
    });
  if (!userRecord) {
    return res.status(400).send(`ユーザ: ${createdById}は存在しません`);
  }
  if (!password) {
    return res.status(400).send("パスワードが設定されていません");
  }
  if (password.length < 8) {
    return res
      .status(400)
      .send("パスワードが短すぎます。8 文字以上にしてください。");
  }
  const passwordHash = await bcryptjs.hash(password, 10);

  const doc = await db.collection("/posts").add({
    title,
    body,
    createdById,
    passwordHash
  } as FSPost);
  const snapshot = await doc.get();
  const data = snapshot.data();
  if (!data) {
    return res.status(500).send("投稿の作成に失敗しました");
  }
  const { id } = doc;

  const bucket = admin.storage().bucket();
  const bucketName = bucket.name;
  // 投稿を編集したらプレビュー画像も変更されるようにしたいので、プレビュー画像は投稿のidから一意に決まるようにする
  const fileName = `${id}.png`;
  const previewUrl = `https://storage.googleapis.com/${bucketName}/${fileName}`;
  const bufferOrError = await generatePreview(body).catch(err => {
    console.error({ err });
    return new Error("プレビュー画像の生成に失敗しました");
  });
  if (bufferOrError instanceof Error) {
    return res.status(500).send(bufferOrError.message);
  }
  const tmpPath = join(tmpdir(), `${id}.png`);
  writeFileSync(tmpPath, bufferOrError);
  await bucket
    .upload(tmpPath, {
      metadata: { contentType: "image/png" },
      public: true
    })
    .catch(err => {
      console.log({ err });
      return res.status(500).send("プレビュー画像のアップロードに失敗しました");
    });
  unlinkSync(tmpPath);
  await doc.set({ previewUrl } as Partial<Post>, { merge: true });
  return res.send({
    id,
    title,
    body,
    createdAt: snapshot.createTime ? snapshot.createTime.seconds : undefined,
    createdBy: extractDataFromUserRecord(userRecord),
    previewUrl
  } as Post);
}); 
```

实现细节无关紧要，同步/异步/纯粹/有副作用的各种处理，例如访问外部数据存储器和数据验证，都被描述在一个函数中。 这样既不会有可测试性，也不会气馁。 幸运的是，Express 中有 Middleware 这个结构，使用它可以在一定程度上分割处理并连接起来。 在之后介绍的`hyper-ts`库中，以比公式的类型定义更强的类型定义为武器，提供了以该 Middleware 为中心记述无状态 APP 的结构。

## hyper-tsの紹介

[根据公式](https://github.com/gcanti/hyper-ts/tree/0.5.0)的自述，

> hyper-ts 是一个用 TypeScript 编写的用于 HTTP 服务器的实验性中间件架构。它的主要焦点是正确性和类型安全，使用类型级信息来加强 web 服务器的正确组合和抽象。

hyper-ts 是指重视**type-safety** 的**middleware architecture** 。 以下是拙作[https://tweet2img.web.app](https://tweet2img.web.app) 中，发送推文的 URL 时返回 png 格式图像的 API 端点的实现。

```
// 前略

function badRequest<E = never>(
message: string
): H.Middleware<H.StatusOpen, H.ResponseEnded, E, void> {
return pipe(
  H.status(H.Status.BadRequest),
  H.ichain(() => H.closeHeaders()),
  H.ichain(() => H.send(message))
);
}

function notFound<E = never>(
message: string
): H.Middleware<H.StatusOpen, H.ResponseEnded, E, void> {
return pipe(
  H.status(H.Status.NotFound),
  H.ichain(() => H.closeHeaders()),
  H.ichain(() => H.send(message))
);
}

function serverError<E = never>(
message: string
): H.Middleware<H.StatusOpen, H.ResponseEnded, E, void> {
return pipe(
  H.status(H.Status.ServerError),
  H.ichain(() => H.closeHeaders()),
  H.ichain(() => H.send(message))
);
}

const sendError = (
err: TweetURLError | typeof FailedToGenerateImage
): H.Middleware<H.StatusOpen, H.ResponseEnded, never, void> => {
switch (err) {
  case 'TweetNotFound':
    return notFound(TweetNotFoundMessage);
  case 'InvalidArguments':
    return badRequest(InvalidRequestMessage);
  case 'FailedToGenerateImage':
    return serverError(InternalServerErrorMessage);
}
};

// returns a middleware validating `req.param.tweetURL`
const m = t.interface({
  tweetURL: TweetURL
});
const decoded = H.decodeQuery(m.decode);

const parseTweetURLFromQueryMiddleware = pipe(
  decoded,
  H.mapLeft<t.Errors, ServerError | TweetURLError>(() => InvalidArguments)
);

const checkIfTheTweetExistsMiddleware = (query: {
  tweetURL: TweetURL;
}): H.Middleware<
  H.StatusOpen,
  H.StatusOpen,
  ServerError | TweetURLError,
  ValidatedTweetURL
> =>
  H.fromTaskEither(
    pipe(
      checkIfTheTweetExists(query.tweetURL, {
        TWITTER_BEARER_TOKEN
      }),
      TE.chain(exists =>
        exists
          ? TE.right(query.tweetURL as ValidatedTweetURL)
          : TE.left(TweetNotFound)
      )
    )
  );

const mapTweetURLToImage = (
  url: ValidatedTweetURL
): H.Middleware<
  H.StatusOpen,
  H.StatusOpen,
  ServerError | TweetURLError,
  Buffer
> =>
  H.fromTaskEither(
    pipe(
      generateImage(url),
      TE.mapLeft(e => FailedToGenerateImage)
    )
  );

const respondWithImage = (
  imgBuffer: Buffer
): H.Middleware<
  H.StatusOpen,
  H.ResponseEnded,
  ServerError | TweetURLError,
  void
> =>
  pipe(
    H.status(H.Status.OK),
    H.ichain(() =>
      H.header('Content-disposition', `inline; filename="${new Date()}.png"`)
    ),
    H.ichain(() => H.contentType(H.MediaType.imagePNG)),
    H.ichain(() => H.closeHeaders()),
    H.ichain(() => H.send(imgBuffer))
  );

const getImage = pipe(
  parseTweetURLFromQueryMiddleware,
  H.ichain(checkIfTheTweetExistsMiddleware),
  H.ichain(mapTweetURLToImage),
  H.ichain(respondWithImage)
);

app.get(
  '/img',
  toRequestHandler(
    pipe(
      getImage,
      H.orElse(sendError)
    )
  )
); 
```

### `Middleware<I, O, L, A>`

在第 3~44 行中，将平时作为`res.status(HTTP_ERROR_STATUS_CODE).send(ERROR_BODY)`等返回错误的处理定义为 Middleware。 这里，Middleware 的类型`H.Middleware<I, O, L, A>`具有以下语义。

*   `I`:使用表示用户主体和 HTTP 服务器的通信状态的[`Connection`](https://github.com/gcanti/hyper-ts/tree/0.5.0#connection)表现，表现了该中间件被调用时的通信状态的类型。 在该示例中使用`H.StatusOpen`，示出响应的状态行可以发送的状态。
*   `O`:对`I`的**Input** 表示**Output** (中间件处理结束时的通信状态)的类型。 在本示例中，使用`H.ResponseEnded`，表示响应头可以发送的状态( i.e .响应体的流传输未开始的状态)。
*   `L`: Either 莫纳德的左边值类型。 在本例子中，指定了`never`，表示错误表现。
*   `A`: Either 摩多右边值的类型。 在本例中，指定了`void`，表示在没有错误的情况下通过下一个中间件的值类型。

在此，希望您能注意到，从第 36 行到第 44 行，像模式匹配一样，针对中间件可能返回的每种错误类型，将错误信息设为`send`。 通过用中间件的合成来表示请求处理程序，可以返回的错误变得明确，这样的记述成为可能。 如果在客户端采用 TypeScript 的话，也可以使用这种类型，根据不同的错误种类分别发出错误信息。

另外，通过预先定义`I`和`O`，编译关闭请求报头后试图设置值，或者在最终`send`的 Middleware 之后连接其他 Middleware

### 使用 io-ts 对请求 body 和查询参数进行运行时型检查

第 46 行到第 55 行中，使用了用于执行时型检查的程序库[`io-ts`](https://github.com/gcanti/io-ts)，定义了解析 URL 的查询参数的中间件。 通过使用`io-ts`，即使收到了与程序运行时期待的类型(在这次的例子中是`string`的亚种`NonEmptyString`)不同类型的值，也可以返回`left(ERROR_INSTANCE)`等进行恰当的处理。

## 最后

*   hyper-ts 运行在 fp-ts 的生态系统上，但请注意，截至本文发布时，hyper-ts 的最新版本不支持 fp-ts 的新主版本 2.x。

    *   2 .在 x 中，API 中有几个 breaking changes，如果现在开始使用的话应该采用 2.x。
    *   [`0.5.0`](https://github.com/gcanti/hyper-ts/tree/0.5.0#connection)分支支持 2.x (当前最新版本为`0.4.0`)。
        *   **因为这篇文章写的代码使用了 0.5.0 的 hyper-ts 的 API**，所以请一并注意。
*   即使不太清楚这次登场的`Either`等莫纳德是什么，也可以向想努力静态定型的 TS 程序员推荐 hyper-ts。

    *   在 TypeScript 中，现在在作为纯粹函数处理的函数中，即使调用`console.log`等不纯粹的函数也不会发生编译错误( Haskell 和 PureScript 等，在作为语言规格设想了摩纳德的静态定型语言的一部分中，为埃 使用 fp-ts 也是如此，使用 fp-ts 并不意味着完全不能通过编译就很痛苦(明显对在纯函数链接的地方写有副作用的处理会产生一些罪恶感)。 希望能将莫纳德作为“方便的道具”，以轻松的心情开始使用。