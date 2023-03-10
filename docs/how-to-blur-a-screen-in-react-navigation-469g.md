# 如何在 React 导航中模糊屏幕

> 原文：<https://dev.to/jetrockets/how-to-blur-a-screen-in-react-navigation-469g>

在 stackNavigator 中，屏幕相互重叠。React 导航不仅可以改变这些屏幕的背景颜色，还可以控制它们的透明度。

为了使屏幕背景模糊，我们首先需要使屏幕透明。

```
import { createStackNavigator } from 'react-navigation';

export default createStackNavigator(
  {
    HomeStack,
    BlurModal,
  },
  {
    ...NAVIGATOR_CONFIG,
    mode: 'modal',
    cardStyle: { opacity: 1 },
    transparentCard: true,
  },
); 
```

然后用 blurView 做背景。

```
import React from 'react';
import { BlurView } from '@react-native-community/blur';
import Styled from 'styled-components';

function BlurModal() {
  return (
    <BlurWrapper blurType="light" blurAmount={20}>
      <Text>Modal with blur background</Text>
    </BlurWrapper>
  );
}

const BlurWrapper = Styled(BlurView)`
  position: absolute;
  top: 0;
  left: 0;
  bottom: 0;
  right: 0;
`; 
```

[![](img/89ed4f32c37c51eaee6f9ed3cb99b82c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V5lmuEMg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2qzpzquumbelqrrp2c5l.png)