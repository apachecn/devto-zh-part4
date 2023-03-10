# 开源库-付费视图

> 原文：<https://dev.to/fevziomurtekin/open-source-library-payview-3fi6>

[![](img/1eca376a8954109b231567520f89c48c.png)](https://jitpack.io/#fevziomurtekin/PayView)[![Android Arsenal](img/2a30f244836c5028cfaaf2a4907ba0a3.png)](https://android-arsenal.com/details/1/7724)[![Twitter Follow](img/9f3d2d21acd7b070b89fdbb1ef17d9e6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--epFRFcBC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://img.shields.io/twitter/follow/fevziomurtekin.svg%3Flabel%3Dfevziomurtekin%26style%3Dsocial)

[![](img/1702ea58f41bb49fc2a197ed3261cc7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bnxvF4y5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/record.gif)T3】💳信用卡和借记卡的支付视图库。

[![](img/e324da482d3a32093d1632c592d2aa06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F_nh6Pvy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/visa.svg%3Fsanitize%3Dtrue)[![](img/466f765c8a28969d429963d2e04a6d68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D7CrRyQ8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/mastercard.svg%3Fsanitize%3Dtrue)[![](img/a889056f961a384bff948b700e561abc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sE2JGYhD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/americanexpress.svg%3Fsanitize%3Dtrue)[![](img/59745b78a211403629b947aa3be87f1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NfP8jXEc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/jcb.svg%3Fsanitize%3Dtrue)[![](img/879151a6b778cd57aec1c23beb43955b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--30OA8Ba7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/discover.svg%3Fsanitize%3Dtrue)[![](img/43bfe9a4fc30c1b2ee0e6b230bd706a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1WTAwLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/dinnersclub.svg%3Fsanitize%3Dtrue)[![](img/3ce761f1467735c52d644ef77b5e38d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DwCKQOEz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/fevziomurtekin/PayView/master/art/undefined.svg%3Fsanitize%3Dtrue)

# 演示

[![](img/d0262a9349330f34bc61eb92155042a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LXTQswo4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://github.com/fevziomurtekin/PayView/blob/master/art/record.gif)

# 设置

## [度](#gradle)

```
allprojects {
  repositories {
    ...
    maven { url 'https://jitpack.io' }
  }
}

  .....

dependencies {
      implementation 'com.github.fevziomurtekin:PayView:1.0.1'
      }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 布局

```
 <com.fevziomurtekin.payview.Payview
        android:id="@+id/payview"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:cardBgColor="@android:color/holo_blue_light"
        app:cardFgColor="@android:color/white"
        app:cardTextColor="@color/black"
        app:cardNameHelperText="Enter to card name. Max 25 characters."
        app:cardCvTextSize="14"
        app:cardNoTextSize="14"
        app:cardNumberHelperText="You must enter your 16-digit card number."
        app:cardYearTextSize="13"
        app:cardNameTextSize="15"
        app:cardMonthTextSize="13"
        app:cardAnimationType="vertical"
    /> 
```

Enter fullscreen mode Exit fullscreen mode

## 听众

```
 payview.setOnDataChangedListener(object : Payview.OnChangelistener{
            override fun onChangelistener(payModel: PayModel?) {
                Log.d("payView", "data : ${payModel?.cardOwnerName}")

            }

        })

    payview.setPayOnclickListener(View.OnClickListener {
        Log.d("payView "," clicked.")

    }) 
```

Enter fullscreen mode Exit fullscreen mode

#属性

|属性|描述|
| - | - |
| `cardBgColor` |卡片背景颜色的 int 中的颜色(默认为 android。r . color . holo _ blue _ light)|
|`cardFgColor`|卡片前景色的 int 中的颜色(默认安卓。R.color.white)|
| `cardTextColor` |卡片文字颜色 int 中的颜色(默认为 android。R.color.black)|
| `cardAnimationType` |动画中的动画付费视图类型(默认为水平)|
| `cardNameTextSize` |搜索文本大小的 sp 大小(默认为 15sp) |
| `cardNoTextSize` |搜索文本大小的 sp 大小(默认为 14sp)|
|`cardYearTextSize`|搜索文本大小的 sp 大小(默认为 13sp)|
|`cardMonthTextSize`|搜索文本的 sp 大小 `cardCvTextSize`|结果文本大小的大小(默认为 14sp) |
| `cardNumberHelperText` |默认文本，“您必须输入您的 16 位卡号。” |
| `cardNameHelperText` |默认文本“输入到卡名。您最多可以输入 25 个字符" |

## 执照

阿帕奇许可证 2.0 -更多详情见 [`LICENSE`](https://dev.toLICENSE)

## Github 链接

[![](img/f6e9abc1d1b6ca1b019b40510444ebf3.png)](https://github.com/fevziomurtekin/PayView)