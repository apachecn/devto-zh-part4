# 答:如何将值从 RecycleAdapter 传递给 MainActivity 或其他活动

> 原文：<https://dev.to/selmantsn/answer-how-to-pass-values-from-recycleadapter-to-mainactivity-or-other-activities-89c>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [ 回答 re:如何将值从 RecycleAdapter 传递给 MainActivity 或其他活动](https://stackoverflow.com/questions/35008860/how-to-pass-values-from-recycleadapter-to-mainactivity-or-other-activities/35009073#35009073)

Jan 26 '16[![](img/e3f0373ec76330150a340eacd410b600.png)40![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/35008860/how-to-pass-values-from-recycleadapter-to-mainactivity-or-other-activities/35009073#35009073) </header>

您应该创建接口，并且 activity 实现这个接口。

```
public interface OnItemClick {
    void onClick (String value);
} 
```

创建适配器时(最后一个参数是这个接口)

```
public class MainActivity extends AppCompatActivity implements OnItemClick {
 recycleAdapter = new RecycleAdapter(MainActivity.this,onlineData, this);
            recyclerView.setAdapter(recycleAdapter);

 @Override
 void onClick (String value){
// value this data
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Answer](https://stackoverflow.com/questions/35008860/how-to-pass-values-from-recycleadapter-to-mainactivity-or-other-activities/35009073#35009073)</button>