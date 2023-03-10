# 拿着我的表情符号

> 原文：<https://dev.to/zex/hold-my-emoji-acc>

让我们在`package emoji`中创建一个随机表情生成器。

准备一份有趣表情代码的清单

🚲 🐸 🐡 🎉 😆 👏 🏄 🔞 🍫 🏖️ 🐳 💯 🤓 🌝 🤘 🤣 🍪 🌼 🔔 🎅 🍄 🚗 🦄 🎃 👈:trollface:🐒 🌞:octocat:👇 🐽 🛍️ 🍼:感觉良好:🍻 🙀 👀 ☃️ 🐣 🚀 🐮 💅 ‼️ 🚁

```
var (
  AvailEmojies = []string{
    ":bike:",
    ":frog:",
    ":blowfish:",
    ":tada:",
    ":laughing:",
    ":clap:",
    ":surfer:",
    ":underage:",
    ":chocolate_bar:",
    ":beach_umbrella:",
    ":whale:",
    ":100:",
    ":nerd_face:",
    ":full_moon_with_face:",
    ":metal:",
    ":rofl:",
    ":cookie:",
    ":blossom:",
    ":bell:",
    ":santa:",
    ":mushroom:",
    ":red_car:",
    ":unicorn:",
    ":jack_o_lantern:",
    ":point_left:",
    ":trollface:",
    ":monkey:",
    ":sun_with_face:",
    ":octocat:",
    ":point_down:",
    ":pig_nose:",
    ":shopping:",
    ":baby_bottle:",
    ":feelsgood:",
    ":beers:",
    ":scream_cat:",
    ":eyes:",
    ":snowman_with_snow:",
    ":hatching_chick:",
    ":rocket:",
    ":cow:",
    ":nail_care:",
    ":bangbang:",
    ":helicopter:",
  }
) 
```

准备一个助手函数，每次被调用时生成一个代码。使用`math/rand`中的随机整数生成函数

```
func RndEmoji() string {
  return AvailEmojies[rand.New(rand.NewSource(time.Now().UnixNano())).Intn(len(AvailEmojies))]
} 
```

差不多完成了，现在，你可以把它用在别的地方取乐。这里我想使用由`go`提供的测试特性。
现在，在与 emoji.go 相同的位置创建一个后缀为`_test`的测试文件，名为`emoji_test.go`

```
import (
  "testing"
)

func TestEmoji(t *testing.T) {
  for i := 0; i < 10; i++ {
    t.Log(RndEmoji())
  }
} 
```

简单。不是吗？
运行测试只需`go test`它，使用详细模式允许打印`Log`级信息。
`go test -v <GOPATH>/holdmyemoji`

```
=== RUN   TestEmoji
--- PASS: TestEmoji (0.01s)
    emoji_test.go:9: :whale:
    emoji_test.go:9: :unicorn:
    emoji_test.go:9: :metal:
    emoji_test.go:9: :octocat:
    emoji_test.go:9: :monkey:
    emoji_test.go:9: :sun_with_face:
    emoji_test.go:9: :nail_care:
    emoji_test.go:9: :helicopter:
    emoji_test.go:9: :unicorn:
    emoji_test.go:9: :bell:
PASS 
```

测试也可以首先由`go test <GOPATH>/rndemoji -c -o emoji`构建，然后您可以多次运行`./emoji -test.v`。

```
=== RUN   TestEmoji
--- PASS: TestEmoji (0.01s)
    emoji_test.go:9: :santa:
    emoji_test.go:9: :tada:
    emoji_test.go:9: :pig_nose:
    emoji_test.go:9: :rocket:
    emoji_test.go:9: :eyes:
    emoji_test.go:9: :full_moon_with_face:
    emoji_test.go:9: :100:
    emoji_test.go:9: :trollface:
    emoji_test.go:9: :pig_nose:
    emoji_test.go:9: :bangbang:
PASS 
```

此外，为了获得更多的乐趣，每次在生成之前都要打乱列表，列表会不时发生变化。当然，这需要更多的计算。

```
 for i := range AvailEmojies {
    j := rand.New(rand.NewSource(time.Now().UnixNano())).Intn(i+1)
    AvailEmojies[j], AvailEmojies[i] = AvailEmojies[i], AvailEmojies[j]
  } 
```

* * *

使用你的搞笑列表，玩它🚀