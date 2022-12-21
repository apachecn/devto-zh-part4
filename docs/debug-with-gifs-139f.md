# 用 gif 调试！

> 原文：<https://dev.to/joshnuss/debug-with-gifs-139f>

你曾经希望你能看到你的程序运行的视频或动画吗？帮助调试问题或解释其工作原理？

有一个简单的方法可以做到这一点——这有点麻烦——但是它工作得很好，并且易于设置。

你所需要做的就是为每个动画帧生成一个 SVG 文件，然后将它们拼接成一个可播放的文件。

## 生成图形

对于您想要可视化的每个状态，编写一个带有填充零的连续命名的`.svg`文件。比如:`frames/00001.svg`、`frames/00002.svg`...等等。

下面是一个生成线条系列的示例。它是用 Elixir 编写的，但是这种技术适用于任何语言。

```
defmodule SVG do 
  def export(frame_index, data) do
    # filename is padded with zeros
    filename = frame_index |> to_string |> String.pad_leading(5, "0")
    path = "frames/#{filename}.svg"

    data = """
    

      #{points(data)}

    
    """

    # write to disk
    File.write(path, data)
  end

  # outputs a list of points
  defp points(data) do
    data |> Enum.map(&point/1) |> Enum.join("\n")    
  end

  # outputs a circle for each point
  defp point({x, y}) do
    ~s|<circle cx="#{x}" cy="#{y}" r="1" fill="blue"/>| 
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以从你想诊断的代码中调用`SVG.export()`，只需要提供一个`frame_index`和`series`的点:

```
frame_index = 1
series = [{0, 1}, {1, 10}]

# writes SVG to "frames/00001.svg"
SVG.export(frame_index, series) 
```

Enter fullscreen mode Exit fullscreen mode

## 将图形拼接在一起

现在你有一堆帧，让我们把它们变成一个动画。

### GIF 格式

要生成一个`.gif`，只需使用`convert` :

```
convert -delay 2 -loop 0 frames/*.svg animated.gif 
```

Enter fullscreen mode Exit fullscreen mode

### 视频格式

对于`.mp4`和其他视频格式，使用不可或缺的`ffmpeg`实用程序。

**注意**认为`ffmpeg`不理解`.svg`文件，所以你需要先把它们转换成`.png`:

```
mogrify -format png frames/*.svg
ffmpeg -r 10 -i frames/%05d.png -pix_fmt yuv420p video.mp4 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

从任何数据生成动画和视频都很容易。对于调试程序或帮助他人在工作中可视化您的程序来说，这是一项非常有用的技术。

你所需要的只是`.svg`文件和实用程序`convert` & `ffmpeg`。

快乐观想！

你可以在这里找到一个完整的工作示例:[https://gist . github . com/Josh Nuss/353 abbd 564167 e 69 EEC dadd 9d 590 F9 e 6](https://gist.github.com/joshnuss/353abbd564167e69eecdadd9d590f9e6)