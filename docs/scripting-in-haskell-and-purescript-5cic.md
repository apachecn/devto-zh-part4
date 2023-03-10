# Haskell 和 PureScript 中的脚本

> 原文：<https://dev.to/riccardoodone/scripting-in-haskell-and-purescript-5cic>

你可以继续在这里阅读或者[跳转到我的博客](https://odone.io/posts/2019-07-08-scripting-in-haskell-and-purescript.html)来获得完整的体验，包括美妙的粉色、蓝色和白色调色板。

* * *

当学习如何用滑雪板跳跃时，我的教练常说:“你需要大量的空气”。换句话说，如果你想学习如何跳跃，你需要跳很多次。

函数式编程和其他许多技能一样。因此，我们需要做大量的工作来熟悉这个范例。这很难，因为我们大多数人都在做面向对象的编程。

最近，在 [Monadic Party](http://monadic.party) 上，有人告诉我，在工作中做功能性工作的一个好方法是写脚本。我从来没有想过 Haskell 或 PureScript 是脚本语言。事实证明，这不仅容易做到，而且是一个伟大的想法，做更多的 FP。

在这篇文章中，我们将看到用 Haskell 和 PureScript 编写脚本所需的设置。另外，我们要看看我在 Monadic Party 上写的 Haskell 脚本来测试这个想法。

## Haskell 堆栈脚本

正如该网站所说

> Stack 是一个用于开发 Haskell 项目的跨平台程序

最棒的是，Stack 为脚本提供了一个[解释器](https://docs.haskellstack.org/en/stable/GUIDE/#script-interpreter)。

“你好，世界！”会是

```
#!/usr/bin/env stack
{- stack
   script
   --resolver lts-13.27
-}

main :: IO ()
main = putStrLn "Hello, World!" 
```

Enter fullscreen mode Exit fullscreen mode

可以用
运行

```
chmod +x MY_SCRIPT
./MY_SCRIPT

# Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

可以像其他 Haskell 程序一样导入包。例如，下面的脚本使用 [`directory`](http://hackage.haskell.org/package/directory-1.3.4.0) 做类似于 Bash 中`ls`的事情:

```
#!/usr/bin/env stack
{- stack
   script
   --resolver lts-13.27
   --package directory
-}

import Data.List (intercalate)
import System.Directory (listDirectory)

main :: IO ()
main = do
  entries <- listDirectory "."
  putStrLn $ intercalate " " entries 
```

Enter fullscreen mode Exit fullscreen mode

## 纯脚本脚本

由于 PureScript 编译成 JavaScript，我们可以使用 Node 作为我们的解释器。首先，我们用`pulp init`
搭建一个 PureScript 项目

```
mkdir MY_SCRIPT_DIR && cd MY_SCRIPT_DIR
pulp init 
```

Enter fullscreen mode Exit fullscreen mode

这就产生了一个看起来像 T1 的 T0

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (log)

main :: Effect Unit
main = do
  log "Hello sailor!" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以用
编译成一个节点文件

```
pulp build --to output/index.js 
```

Enter fullscreen mode Exit fullscreen mode

并用`node`
解释文件

```
node output/index.js
# Hello sailor! 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以创建一个小垫片并用`node`解释器
解释它

```
echo '#!/usr/bin/env node\n\nrequire("./output/index.js");' > MY_SCRIPT
chmod +x MY_SCRIPT
./MY_SCRIPT
# Hello sailor! 
```

Enter fullscreen mode Exit fullscreen mode

PureScript 中的`ls`脚本使用了两个额外的包，我们可以用
来安装它们

```
bower install --save purescript-node-fs
bower install --save purescript-strings 
```

Enter fullscreen mode Exit fullscreen mode

而且看起来像

```
module Main where

import Prelude
import Effect (Effect)
import Effect.Console (log)
import Node.FS.Sync (readdir)
import Data.String.Common (joinWith)

main :: Effect Unit
main = do
  entries <- readdir "."
  log $ joinWith " " entries 
```

Enter fullscreen mode Exit fullscreen mode

使用 PureScript 编写脚本的缺点是我们需要保留整个“项目”目录。使用 Stack，只需要处理脚本文件。

## 一个非平凡的 Haskell 堆栈脚本

```
#!/usr/bin/env stack
{- stack
  script
  --resolver nightly-2019-06-20
  --package directory
  --package req
  --package text
  --package aeson
  --package process
  --package parsec
  --package filepath
  --package unix
-}

-- This script creates a Brewfile using `brew bundle dump`
-- and adds to that all the apps installed in `/Applications`
-- that can be installed via HomeBrew as casks.
-- Later you can use `brew bundle` to install or upgrade
-- all dependencies listed in the Brewfile.
-- It can be useful to restore the same packages and apps
-- on a different Mac.

{-# LANGUAGE DeriveGeneric #-}
{-# LANGUAGE FlexibleContexts #-}

import System.Directory
import Data.Char
import Network.HTTP.Req
import qualified Data.Text as T
import Control.Monad.IO.Class
import GHC.Generics
import Data.Aeson
import Data.List
import System.Process
import Text.Parsec
import System.FilePath.Posix
import System.Posix.Files
import System.Exit
import Control.Monad

data Package =
  Package { name :: [String] } deriving (Generic, Show)

data BrewfileLine
  = Tap String
  | Brew String
  | Cask String
  deriving (Eq)

instance Show BrewfileLine where
  show (Tap s) = "tap \"" <> s <> "\""
  show (Brew s) = "brew \"" <> s <> "\""
  show (Cask s) = "cask \"" <> s <> "\""

instance Ord BrewfileLine where
  (<=) (Tap s1) (Tap s2) = fmap toLower s1 <= fmap toLower s2
  (<=) (Tap _) _ = True
  (<=) (Brew s1) (Brew s2) = fmap toLower s1 <= fmap toLower s2
  (<=) (Brew _) _ = True
  (<=) (Cask s1) (Cask s2) = fmap toLower s1 <= fmap toLower s2
  (<=) (Cask _) _ = False

instance ToJSON Package where
    toEncoding = genericToEncoding defaultOptions
instance FromJSON Package

data Response =
  Response [Package] deriving (Generic, Show)

instance ToJSON Response where
    toEncoding = genericToEncoding defaultOptions
instance FromJSON Response

main :: IO ()
main = do
  doesBrewfileExist <- fileExist "Brewfile"
  when doesBrewfileExist $ die "Brewfile already exists! Aborted."
  installed <- getInstalledApps
  installable <- fetchInstallableAppsWithBrew
  let casks = installed `intersect` installable
  lines <- getBrewDumpLines
  let all = union casks <$> lines
  either
    (die . show)
    (writeBrewfile >=> \_ -> putStrLn "Brewfile generated!")
    all

getInstalledApps :: IO [BrewfileLine]
getInstalledApps = do
  filePaths <- listDirectory "/Applications"
  let names = takeBaseName <$> filePaths
  pure $ Cask <$> names

fetchInstallableAppsWithBrew :: IO [BrewfileLine]
fetchInstallableAppsWithBrew = runReq defaultHttpConfig $ do
  res <- req
    GET
    (https (T.pack "formulae.brew.sh") /: (T.pack "api") /: (T.pack "cask.json"))
    NoReqBody
    jsonResponse
    mempty
  pure $ fmap Cask $ unNames $ (responseBody res :: Response)

unNames :: Response -> [String]
unNames (Response xs) = unName <$> xs

unName :: Package -> String
unName (Package name) = head name

writeBrewfile :: [BrewfileLine] -> IO ()
writeBrewfile lines = do
  let lines' = unlines $ fmap show $ sort $ nub lines
  writeFile "Brewfile" lines'

getBrewDumpLines :: IO (Either ParseError [BrewfileLine])
getBrewDumpLines = do
  out <- readProcess "brew" ["bundle", "dump", "--file=/dev/stdout"] []
  pure $ parse brewfileParser "" out

brewfileParser :: Stream s m Char => ParsecT s u m [BrewfileLine]
brewfileParser = endBy1 brewfileLine $ char '\n'

brewfileLine :: Stream s m Char => ParsecT s u m BrewfileLine
brewfileLine = brewfileLine' "tap" Tap <|> brewfileLine' "brew" Brew <|> brewfileLine' "cask" Cask

brewfileLine' :: Stream s m Char => String -> (String -> BrewfileLine) -> ParsecT s u m BrewfileLine
brewfileLine' prefix constructor = do
  string $ prefix <> " "
  name <- quoted
  pure $ constructor name

quote :: Stream s m Char => ParsecT s u m Char
quote = char '"'

quoted :: Stream s m Char => ParsecT s u m String
quoted = between quote quote (many1 $ noneOf "\"") 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

特别感谢贾斯汀，我公然从他那里复制了这篇文章的一半。一定要检查他的(用 PureScript 写一个简单的 CLI)[【https://qiita.com/kimagure/items/39b26642b89bd87bf177】T2。

* * *

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！