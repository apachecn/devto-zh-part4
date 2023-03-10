# 处理溢出时消失的“位置:固定”？

> 原文：<https://dev.to/stephencweiss/dealing-with-disappearing-position-fixed-on-overflow-465m>

当一个元素的子元素相对于它处于固定位置时，你有没有尝试过让这个元素溢出？

有趣的事实:它不起作用。固定元素被隐藏。不是以“离开屏幕”的方式。不是以另一种元素的方式。正如 StackOverflow 上描述的那样，它只是被隐藏或“剪切”。 <sup>1</sup>

唯一要做的事情是重新定位元素，使*是相对定位的父元素*的元素现在是兄弟元素——这在很大程度上违背了目的。

[![Disappearing Button On Overflow](img/8c22712db6a538998469b5a8b110290f.png)](https://i.giphy.com/media/IeFqUB7QcCHEQFHVKE/giphy.gif)

这是我在处理一个模态元素时遇到的问题。我想要一个模态，它的主体太长，屏幕无法向内滚动，但在右上角仍有一个关闭图标，用户可以点击它来关闭窗口。

原始的模态对话框(模态中有内容的部分——与覆盖原始文档的“遮罩”相对)看起来像这样:

```
<ModalStyled {...props}>
  {onClose && <CloseButton onClick={onClose}>X</CloseButton>}
  {children}
</ModalStyled> 
```

这个`ModalStyled`只是一个样式的`div`，但关键是它有`position: relative`。

这意味着按钮可能类似于(使用`styled-components` ):

```
import styled from ‘styled-components’
export const CloseButton = styled.button`
  position: absolute;
  top: -18px;
  right: -24px;
`; 
```

如果你看看。但是，你会注意到，当`ModalStyled`变成`overflow:auto`时，按钮就消失了！

我能想到的最好的解决方案是将整个东西包装在一个 div 中，并使用 flex box 来定位它，这样我的`CloseButton`仍然会出现在上方的*处，如果不是像最初那样稍微偏离右上角的话。* 

```
<Wrapper>
  <MiniWrapper>
    {onClose && <InternalScrollCloseButton name={closeIcon} onClick={onClose} />}
  </MiniWrapper>
  <ModalStyled {...props}>
    {children}
  </ModalStyled> </Wrapper 
Import styled from ‘styled-components’;

export const Wrapper = styled.div`
  display: flex;
  flex-direction: column;
  max-height: 100%;
  margin: auto;
  overflow: auto;
`;

Export const MiniWrapper = styled.div`
  display: flex;
  Justify-content: flex-end;
`; 
```

一旦我有了这个——我可以用一个道具在两者之间翻转。

基本变成了:

```
export const Dialog = props => {

const { internalScroll, …rest } = props;

return ( internalScroll
  ? (
    <Wrapper>
      <MiniWrapper>
        {onClose && <InternalScrollCloseButton name={closeIcon} onClick={onClose} />}
      </MiniWrapper>
      <ModalStyled {...props}>
        {children}
      </ModalStyled>
    </Wrapper)
  : (
    <ModalStyled {...props}>
      {onClose && <CloseButton onClick={onClose}>X</CloseButton>}
      {children}
    </ModalStyled>) )}; 
```

完美吗？几乎没有。它是否有效，能让我不需要付出大量额外的努力就能到达目的地？除非一个慷慨的网络灵魂给我预测答案…绝对是。

[![Modal With Internal Scroll](img/1712d97727b6517d97376d0b2befe75d.png)](https://i.giphy.com/media/mCav2dQLzz8izi3jrZ/giphy.gif)

我告诉一个朋友我做了一个有内部卷轴的模型。他的回应？“畏缩。也许这不应该是一个模态？”说得好。尽管如此，这是我第一次因固定位置元素和溢出的有趣怪癖而脸红，我永远不会为有机会学习而道歉。

# 脚注

*   <sup>1</sup> [css -绝对定位 div，溢出自动导致子绝对 div 被切断|堆栈溢出](https://stackoverflow.com/questions/7590772/absolute-positioned-div-with-overflow-auto-causing-child-absolute-div-to-be-cut)