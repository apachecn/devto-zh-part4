# 如何显示使用 Javascript 打字的建议？

> 原文：<https://dev.to/supunkavinda/how-to-show-suggestions-on-typing-using-javascript-4nde>

你好，这是一个关于当用户在文本框中键入特定关键字时如何显示建议的简短教程。

大多数应用程序都会显示打字建议。在这里，我说的不是搜索文本框，它总是给你显示建议。(谷歌搜索框)

让我们假设用户正在键入一个`<textarea>`，当他们键入一个以@ ( `@Supun`)开始的用户名时，我们需要显示一些建议

这需要做一些工作。这里有一个我发现有效的好方法。

*   我们有一个接受用户输入的`<textarea>`元素。
*   我们监听`keyup`事件来检查用户是否输入了我们需要的内容。

这篇文章是用普通的 JS 编写的。您可以使用一个库来操作 DOM。但是，我不知道任何流行的 JS 库提供获取和设置光标的函数。因此，使用普通的 JS 就可以了。开始吧！

在开始之前，我们需要助手函数来处理**光标位置**。

```
function getCursorPosition(el) {
    var start = 0, end = 0, normalizedValue, range, textInputRange, len, endRange;
    if (typeof el.selectionStart == "number" && typeof el.selectionEnd == "number") {
        start = el.selectionStart;
        end = el.selectionEnd;
    } else {
        range = document.selection.createRange();

        if (range && range.parentElement() == el) {
            len = el.value.length;
            normalizedValue = el.value.replace(/\r\n/g, "\n");

            // Create a working TextRange that lives only in the input
            textInputRange = el.createTextRange();
            textInputRange.moveToBookmark(range.getBookmark());

            // Check if the start and end of the selection are at the very end
            // of the input, since moveStart/moveEnd doesn't return what we want
            // in those cases
            endRange = el.createTextRange();
            endRange.collapse(false);

            if (textInputRange.compareEndPoints("StartToEnd", endRange) > -1) {
                start = end = len;
            } else {
                start = -textInputRange.moveStart("character", -len);
                start += normalizedValue.slice(0, start).split("\n").length - 1;

                if (textInputRange.compareEndPoints("EndToEnd", endRange) > -1) {
                    end = len;
                } else {
                    end = -textInputRange.moveEnd("character", -len);
                    end += normalizedValue.slice(0, end).split("\n").length - 1;
                }
            }
        }
    }
    return {
        start: start,
        end: end
    };
}

function setCursorPosition(input, start, end) {
    if (arguments.length < 3) end = start;
    if ("selectionStart" in input) {
        setTimeout(function() {
            input.selectionStart = start;
            input.selectionEnd = end;
        }, 1);
    }
    else if (input.createTextRange) {
        var rng = input.createTextRange();
        rng.moveStart("character", start);
        rng.collapse();
        rng.moveEnd("character", end - start);
        rng.select();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们要去的 textarea 元素**听**。如果是`input`元素就没有问题。我们将在建议视图中显示建议。

```
<textarea id="txt"></textarea>
<div id="suggestions"></div> 
```

Enter fullscreen mode Exit fullscreen mode

现在，一些有用的变量。

```
var txt = document.getElementById("txt"),
    suggestions = document.getElementById("suggestions"),
    regex = /@([a-zA-Z0-9]*)$/; // the regex we are going to match

// some fake data
var userData = [
  {
    name: "Supun Kavinda",
    username: "SupunKavinda"
  },
  {
    name: "John Doe",
    username: "JohnDoe"
  },
  {
    name: "Anonymous",
    username: "Anonymous"
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

将 keyup 事件侦听器添加到 textarea。

```
// listen for @...
txt.addEventListener('keyup', handleKeyUp); 
```

Enter fullscreen mode Exit fullscreen mode

然后我们的负责人。这里我们将光标前的字符串与`/@[a-zA-Z0-9]*$/`匹配。如果匹配，我们可以从数据库中提取用户的建议。(这里我会用一些假数据给用户)

```
function handleKeyUp() {
   closeSuggestions();
   var cursor = getCursorPosition(txt),
      val = txt.value,
      strLeft = val.substring(0, cursor.start);

  var match = val.match(regex);

  if (match) {
     // fetch suggestions
     var username = match[1];
     findSuggestions(username);     
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

查找、显示和结束建议...

```
function findSuggestions(username) {

  var matched = [];

  userData.forEach(function(data) {
    var dataUsername = data.username,
        pos = dataUsername.indexOf(username);

    if (pos !== -1) {
        matched.push(data);
    }
  });

  // you can also sort the matches from the index (Best Match)

  if (matched.length > 0) {
    showSuggestions(matched);
  }

}

function showSuggestions(matched) {
  // DOM creation is not that hard if you use a library ;
  suggestions.style.display = "block";
  suggestions.innerHTML = "";

  matched.forEach(function(data) {
    var wrap = document.createElement("div");
    suggestions.appendChild(wrap);

    var nameView = document.createElement("span");
    nameView.innerHTML = data.name;
    nameView.className = "name-view";

    var usernameView = document.createElement("span");
    usernameView.innerHTML = "@" + data.username;
    usernameView.className = "username-view";

    wrap.appendChild(nameView);
    wrap.appendChild(usernameView);

    // add the suggested username to the textarea
    wrap.onclick = function() {
        addToTextarea("@" + data.username + "  ");
    }
  });
}

function closeSuggestions() {
    suggestions.style.display = "none";
} 
```

Enter fullscreen mode Exit fullscreen mode

将用户名添加到文本区域的函数。

```
function addToTextarea(valueToAdd) {

   var cursor = getCursorPosition(txt),
      val = txt.value,
      strLeft = val.substring(0, cursor.start),
      strRight = val.substring(cursor.start);

    // remove the matched part
    strLeft = strLeft.replace(regex, "");

    txt.value = strLeft + valueToAdd + strRight;

    // (textarea, positionToAdd)
    setCursorPosition(txt, strLeft.length + valueToAdd.length);

    txt.focus();

    closeSuggestions();

} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们来添加一些 CSS。

```
#txt {
  resize:none;
  width:450px;
  height:100px;
  border-radius:5px;
  border:1px solid #eee;
}
#suggestions {
  /* It's better to contain textarea
  and suggestions in a div and make this suggestions view
  absolute positioned */
  box-shadow:0 0 25px rgba(0,0,0,0.05);
  width:450px;
}
#suggestions > div {
  padding:10px;
}
.name-view {
  margin-right:5px;
}
.username-view {
  color: #aaa;
} 
```

Enter fullscreen mode Exit fullscreen mode

你完了！

## 可能的改进

*   您可以添加键盘导航来选择建议。

下面是 [JSFiddle 演示](https://jsfiddle.net/4w2n1f6z/1/)。并且，我将把这篇文章作为[Java 脚本团队](https://groups.hyvor.com/JS)的第一篇文章发布在我的网站上。

希望有帮助。