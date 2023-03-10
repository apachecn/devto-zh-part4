# 解析 JSON

> 原文：<https://dev.to/dropconfig/parsing-json-d2h>

朋友们好，

上次我和你谈到解析 INI 文件。现在让我们解析一些 JSON。

在 JavaScript 中没有什么好的理由这样做，因为它是内置的，而且速度更快。有一个很好的理由。解析器超级好玩。不管怎样，让我们开始吧。

### 雷克塞

我们要做的第一件事是获取文件字符串，并将其转换为一个令牌数组。

```
//Make an enum it's just easier to type this way.
const _types = [
  "LEFT_BRACKET",
  "RIGHT_BRACKET",
  "LEFT_BRACE",
  "RIGHT_BRACE",
  "TRUE",
  "FALSE",
  "NULL",
  "COMMA",
  "STRING",
  "COLON",
  "QUOTE"
];

const TYPES = {};
_types.forEach((type, index)=>{
    TYPES[type] = index;
});
function getString(text, index){
  let str = "";
  let len = 0;
  for(let i = index + 1; i<text.length; i++){
    if(text[i] === `"`){
        return [str, len];
    } else if(text[i] === "\\" && text[i+1] === `"`){
        str += `"`;
      len +=2;
      i++;
    } else{
        len++;
        str += text[i];
    }
  }

  return null;
}

function lex(text) {
  let index = 0;
  const result = [];
  while(index < text.length){
    const at = text[index];
    switch(at){
    case "{":
        result.push({type: TYPES.LEFT_BRACE});
      index++;
      break;
    case "}":
        result.push({type: TYPES.RIGHT_BRACE});
      index++
      break;
    case ":":
        result.push({type: TYPES.COLON});
      index++;
      break;
    case ",":
        result.push({type: TYPES.COMMA});
      index++;
      break;
    case "n":
        if(text.substring(index, index+4) === "null"){
        index+=4
        result.push({type: TYPES.NULL});
      } else {
        throw new Error("unexpected character parsing null");
      } 
      break;
    case "f":
      if(text.substring(index, index+5) === "false"){
        index+=5
        result.push({type: TYPES.FALSE});
      } else {
        throw new Error("unexpected character parsing false");
      } 
      break;
    case "t":
        if(text.substring(index, index+4) === "true"){
        index+=4
        result.push({type: TYPES.TRUE});
      } else {
        console.log(text.substring(index, index+4));
        throw new Error("unexpected character parsing true");
      } 
      break;
    case "\"":
        const str = getString(text, index);
        if(str === null){
        throw new Error("Expected end of string");
      }
        result.push({type: TYPES.STRING, value: str[0]});
      index += str[1] + 2;
      break;
    case "[":
        result.push({type: TYPES.LEFT_BRACKET});
      index++
      break;
    case "]":
        result.push({type: TYPES.RIGHT_BRACKET});
      index++
      break;
    default: 
      index++ 
    }
  }
  return result
} 
```

所以这很简单。
我们正在检查字符串，当我们遇到某些字符时，我们将它转换成一个令牌，并将其推入一个数组。
我们也验证字符串和`true, false, null`

对字符串进行标记使得编写解析器的其余部分变得非常简单。

附注:我刚刚意识到这个解析器不处理数字。我要声明我把它留给读者作为练习。那么做吧。

您还将看到我们处理转义引用的情况。我们可以逃避其他一些事情。但那太费事了。这对读者来说也是一个练习...

### 解析

现在我们开始享受解析令牌的乐趣。

```
function parse(tokens, index){
    const t = tokens[index];

  switch(t.type){
    case TYPES.STRING:
        return [t.value, index+1];
    case TYPES.FALSE:
        return [false, index+1];
    case TYPES.TRUE:
        return [true, index+1];
    case TYPES.NULL:
        return [null, index+1];
    case TYPES.LEFT_BRACKET:
        return parse_array(tokens, index+1);
    case TYPES.LEFT_BRACE:
        return parse_obj(tokens, index+1);
    default:
     console.log(index, t, tokens)
        throw new Error("unexpected type " + Object.keys(TYPES)[t.type] + "  " + t.type); 
  }

} 
```

我们这里什么都不循环。因为我们会在`parse_array`和`parse_obj`中递归调用自己。

请注意，我们返回了一个数组`[value, nextIndex]`，因为我们希望解析的值以及`tokens`数组中的下一个索引成为下一步要查看的内容。

#### 解析数组

```
function parse_array(tokens, index){
    const result = [];

  let idx = index;

  while(true){
    const token = tokens[idx];
    if(token.type === TYPES.RIGHT_BRACKET){
        return [result, idx+1]
    }

    const [value, newIdx] = parse(tokens, idx);

    result.push(value);
    idx = newIdx;

    if(tokens[idx].type === TYPES.RIGHT_BRACKET){
        return [result, idx+1]
    }

    if(tokens[idx].type !== TYPES.COMMA){
        throw new Error("Expected a comma");
    }
    idx++
  }

  return [result, idx]
} 
```

因此，如果我们有一个空数组，下一个令牌将是`TYPES.RIGHT_BRACKET`，我们可以返回一个空数组。

否则，我们将遍历令牌列表，直到找到一个右括号。或者是个错误。
调用`parse`将返回列表中下一个令牌的值。这才是真正的神奇。如果我们有一个嵌套数组，我们将再次递归调用我们自己的函数。

如果是最后一项，我们会对值之间的逗号或右括号进行错误检查。

好了，现在我们如何解析一个对象？

#### 解析一个对象

```
function parse_obj(tokens, index){
    const result = {};
  let idx = index;
  while(true){
    const token = tokens[idx];
    if(token.type === TYPES.RIGHT_BRACE){
        return [result, idx+1];
    }

    if(token.type === TYPES.STRING){
        if(tokens[idx+1].type !== TYPES.COLON){
        throw new Error("Expected colon after key");
      }
      const [value, newIdx] = parse(tokens, idx+2);

      result[token.value] = value;
      idx = newIdx;
      if(tokens[idx].type === TYPES.RIGHT_BRACE){
        return [result, idx+1];
      }
      if(tokens[idx].type !== TYPES.COMMA){
        throw new Error("Expected a comma"); 
      }
    } else {
        throw new Error("expected key");
    }
    idx++;
  }

  return [result, idx];
} 
```

如你所见，它的*真的*类似于一个数组。但是我们需要一个键值对。我们还检查键和值之间是否有冒号`:`。(`"key":"value"`)。其他的都和数组差不多。

### 裹足不前。

好的，那么 parse 最后会返回什么？

它将是一个数组，第一个值是我们从 JSON 解析的对象，第二个值应该是 tokens 数组中的最后一个索引。

例如`[{}, 2]`

为了调用 parse，我们需要向它传递我们的 lex
的结果

```
const tokens = lex(inputString);

const value = parse(tokens, 0); 
```

这不是最友好的用户界面，所以让我们用一个简单的函数来包装这些混乱，这个函数将返回解析过的对象。

```
function parseJSON(text){
    const tokens = lex(inputString);
    const value = parse(tokens, 0);

    return value[0]
} 
```

轻松点。

以下是 JSFiddle 的完整源代码:[https://jsfiddle.net/pebj04zv/1/](https://jsfiddle.net/pebj04zv/1/)

我确实从一篇为 python 写的文章中得到一些帮助:[http://notes . eaton Phil . com/writing-a-simple-JSON-parser . html](http://notes.eatonphil.com/writing-a-simple-json-parser.html)

如果你愿意，请去 https://dropconfig.com 支持我。