# Haskell -用 Aeson 编码和解码 JSON

> 原文：<https://dev.to/piq9117/haskell-encoding-and-decoding-json-with-aeson-5d7n>

有很多关于用 [aeson](https://hackage.haskell.org/package/aeson) 编码/解码 JSON 的博文和教程。甚至图书馆也很擅长教授如何做到这一点。我经常回去看的教程是 [Artyom Kazak 的](https://artyom.me/aeson)教程。他们讨论了很多不同的技术，如何在不同的情况下解码和编码 json。

让我们从基础开始，手动派生`FromJSON`和`ToJSON`的实例。

```
data Book = Book
  { bookTitle     :: Text
  , bookISBN      :: Text
  , bookPublisher :: Text
  , bookLanguage  :: Text
  } deriving Show

instance FromJSON Book where
  parseJSON = withObject "Book" $ \b ->
    Book <$> b .: "title"
         <*> b .: "ISBN"
         <*> b .: "publisher"
         <*> b .: "language"

instance ToJSON Book where
  toJSON Book {..} = object
    [ "title"     .= bookTitle
    , "ISBN"      .= bookISBN
    , "publisher" .= bookPublisher
    , "language"  .= bookLanguage
    ] 
```

有了`FromJSON`和`ToJSON`实例，我们现在可以使用以下形式的 JSON:

```
{  "title":  ".."  ,  "ISBN":  ".."  ,  "publisher":  ".."  ,  "language":  ".."  } 
```

正如你所看到的，当类型有更多的字段时，这也意味着我们必须把所有的字段都打出来。当你完成你的应用程序时，你的手指会掉下来。

最小化样板文件的一个解决方案是使用`Generics`

```
{-# LANGUAGE DeriveGeneric #-}

import GHC.Generics

data Book = Book
  { bookTitle     :: Text
  , bookISBN      :: Text
  , bookPublisher :: Text
  , bookLanguage  :: Text
  } deriving (Generic, Show)

instance FromJSON Book
instance ToJSON Book 
```

如果我们使用`{-# LANGUAGE DeriveAnyClass #-}` pragma，我们可以做到这一点。

```
{-# LANGUAGE DeriveAnyClass #-}
{-# LANGUAGE DeriveGeneric #-}

data Book = Book
  { bookTitle     :: Text
  , bookISBN      :: Text
  , bookPublisher :: Text
  , bookLanguage  :: Text
  } deriving (Generic, Show, FromJSON, ToJSON) 
```

## DeriveAnyClass 和 GenerlizedNewTypeDeriving

如果我们启用了这两种语言扩展，ghc 将会抱怨派生含糊不清。要解决这个问题，请使用 `{-# LANGUAGE DerivingStrategies #-}`语言扩展。

```
{-# LANGUAGE DerivingStrategies #-}
{-# LANGUAGE DeriveGeneric #-}

data Book = Book
  { bookTitle     :: Text
  , bookISBN      :: Text
  , bookPublisher :: Text
  , bookLanguage  :: Text
  } 
  deriving (Generic, Show)
  deriving anyclass (FromJSON, ToJSON) 
```

如果我们不需要对字段名做任何事情，这就足够了。然而，我们的字段名是`title`而不是`bookTitle`，所以我们必须通过下面的
对字段名做一点修改

```
import Text.Casing (camel)
import Data.Aeson

instance FromJSON Book where
  parseJSON = genericParseJSON 
    defaultOptions { fieldLabelModifier = camel . drop 4 }

instance ToJSON Book where
  toJSON = genericToJSON 
    defaultOptions { fieldLabelModifier = camel . drop 4 } 
```

这里有一个对[默认选项](https://www.stackage.org/haddock/lts-14.2/aeson-1.4.4.0/Data-Aeson.html#v:defaultOptions)的引用。在上面的代码中，我们正在进行记录更新。这意味着它将从开始的 4 个字符开始，然后对其进行大小写。

## 可空字段

当涉及到可空字段时，`Generics` }会自动使用这个运算符[(。:?)](https://www.stackage.org/haddock/lts-14.2/aeson-1.4.4.0/Data-Aeson.html#v:.:-63-)在为`Maybe` s 的字段上，如果
字段为`null`或缺失，将使用`Nothing`。

## 可选字段

对于可选字段，我们必须返回手动导出`ToJSON`
和`FromJSON`。

```
data Book = Book
  { bookTitle     :: Text
  , bookISBN      :: Text
  , bookPublisher :: Text
  , bookLanguage  :: Text
  , bookPrice     :: Maybe (Fixed E2)
  } deriving Show

instance FromJSON Book where
  parseJSON = withObject "Book" $ \b ->
    Book <$> b .: "title"
         <*> b .: "ISBN"
         <*> b .: "publisher"
         <*> b .: "language"
         <*> optional (b .: "price") 
```

### 求和类型

```
{-# LANGUAGE RecordWildCards #-}

data BookFormat 
 = Ebook { price :: Fixed E2 }
 | PhysicalBook { price :: Fixed E2, coverType :: Text }
  deriving Show

instance FromJSON BookFormat where
  parseJSON = withObject "BookFormat" $ \b -> asum
    [ Ebook <$> b .: "price"
    , PhysicalBook <$> b .: "price"
                   <*> b .: "coverType"
    ]

instance ToJSON BookFormat where
  toJSON = \case 
    Ebook {..} -> object [ "price" .= price ]
    PhysicalBook {..} -> object [ "price" .= price, "coverType" .= coverType] 
```

或者我们是否可以根据`format`
来决定

```
instance FromJSON BookFormat where
  parseJSON = withObject "BookFormat" $ \b -> do
    format <- b .: "format"
    case (format :: Text) of
      "ebook" -> Ebook <$> b .: "price"
      "physicalBook" ->
        PhysicalBook <$> b .: "price"
                     <*> b .: "coverType" 
```

同样对于产品类型，我们也可以使用`Generics`和一些
语言扩展来派生`FromJSON`和`ToJSON`实例

```
{-# LANGUAGE DeriveGeneric #-}
{-# LANGUAGE DeriveAnyClass #-}
import GHC.Generics

data BookFormat
 = Ebook { price :: Fixed E2 }
 | PhysicalBook { price :: Fixed E2, coverType :: Text }
  deriving (Show, Generic, ToJSON, FromJSON) 
```

这些是我日常使用的编码/解码 json 数据的技术。