# 嵌套 Viewpager 的“找不到片段 id 的视图”错误

> 原文：<https://dev.to/fevziomurtekin/no-view-found-for-id-for-fragment-error-of-nested-viewpager-1hgl>

<header>

# ![](img/01c67cd39e9a8e551fcb75e1091225e9.png) [“未找到片段 id 的视图”嵌套 Viewpager 错误](https://stackoverflow.com/questions/57919749/no-view-found-for-id-for-fragment-error-of-nested-viewpager)

Sep 13 '19 Comments: 1 Answers: 0[![](img/e3f0373ec76330150a340eacd410b600.png)1![](img/f7bb704c8c93dfae05d2b57012ed2754.png)](https://stackoverflow.com/questions/57919749/no-view-found-for-id-for-fragment-error-of-nested-viewpager) </header>

我在标题中得到一个错误。应用程序 I 也有一个混合结构，并使用一些嵌套结构。

有一个类似**" category fragment->view pager->recycler view inside category details fragment->view pager->HeadlinePagerFragment "**的结构

当更改为 CategoryFragment 时，返回到后面将会出现此错误。

*   类别划分

```
val
```

…<button class="ltag__stackexchange--btn" type="button">[Open Full Question](https://stackoverflow.com/questions/57919749/no-view-found-for-id-for-fragment-error-of-nested-viewpager)</button>

我在标题中得到一个错误。应用程序 I 也有一个混合结构，并使用一些嵌套结构。

有一个类似**" category fragment->view pager->recycler view inside category details fragment->view pager->HeadlinePagerFragment "**的结构

当更改为 CategoryFragment 时，返回到后面将会出现此错误。

*   类别划分

```
val adapter = CategoriesPagerAdapter(activity?.supportFragmentManager!!,list as List<MutableMap<String,String>>)
vp_categories_pager.adapter = adapter
tl_categories.setupWithViewPager(vp_categories_pager) 
```

Enter fullscreen mode Exit fullscreen mode

*   CategoryDetailsFragment 此类在 childview viewpager 中使用 recyclerview 和 usage。

```
rv_category_details.adapter = ContentAdapter(
            context!!,this@CategoryDetailsFragment(usage as fragment),
            this@CategoryDetailsFragment) 
```

Enter fullscreen mode Exit fullscreen mode

->适配器

```
 vp_headline.adapter =HeadlineAdapter(
                            fragment?.childFragmentManager!!,
                            items, itemClickListener)
                    tl_headline_indicator.setupWithViewPager(vp_headline) 
```

Enter fullscreen mode Exit fullscreen mode

*   HeadlineAdapter

```
class HeadlineAdapter(
    fragment: FragmentManager,
    var list:MutableList<MutableMap<*,*>>,
    val itemclickListener:ContentAdapter.Itemclicklistener)
    :FragmentPagerAdapter(fragment){
    override fun getItem(position: Int): Fragment =
        HeadlinePagerFragment(PutData(mutableMapOf(
        'm' to list[position],
        'l' to itemclickListener
    )))

    override fun getCount(): Int = list.size

    override fun getItemPosition(`object`: Any): Int {
        return PagerAdapter.POSITION_NONE
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

返回此错误

```
No view found for id 0x7f0901de (com.bursadabugun.android:id/vp_headline) for fragment HeadlinePagerFragment{17872d5 (85c9643d-c164-417e-a428-7105105970a1) id=0x7f0901de android:switcher:2131296734:0}
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:875)
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238)
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:1303)
        at androidx.fragment.app.FragmentManagerImpl.dispatchStateChange(FragmentManagerImpl.java:2656)
        at androidx.fragment.app.FragmentManagerImpl.dispatchActivityCreated(FragmentManagerImpl.java:2610)
        at androidx.fragment.app.Fragment.performActivityCreated(Fragment.java:2619)
        at androidx.fragment.app.FragmentManagerImpl.moveToState(FragmentManagerImpl.java:904)
        at androidx.fragment.app.FragmentManagerImpl.moveFragmentToExpectedState(FragmentManagerImpl.java:1238)
        at androidx.fragment.app.BackStackRecord.executeOps(BackStackRecord.java:434)
        at androidx.fragment.app.FragmentManagerImpl.executeOps(FragmentManagerImpl.java:2076)
        at androidx.fragment.app.FragmentManagerImpl.executeOpsTogether(FragmentManagerImpl.java:1866)
        at androidx.fragment.app.FragmentManagerImpl.removeRedundantOperationsAndExecute(FragmentManagerImpl.java:1821)
        at androidx.fragment.app.FragmentManagerImpl.execSingleAction(FragmentManagerImpl.java:1696)
        at androidx.fragment.app.BackStackRecord.commitNowAllowingStateLoss(BackStackRecord.java:299)
        at androidx.fragment.app.FragmentPagerAdapter.finishUpdate(FragmentPagerAdapter.java:230)
        at androidx.viewpager.widget.ViewPager.populate(ViewPager.java:1244)
        at androidx.viewpager.widget.ViewPager.populate(ViewPager.java:1092)
        at androidx.viewpager.widget.ViewPager.onMeasure(ViewPager.java:1622)
        at android.view.View.measure(View.java:22094)
        at androidx.constraintlayout.widget.ConstraintLayout.internalMeasureChildren(ConstraintLayout.java:1227)
        at androidx.constraintlayout.widget.ConstraintLayout.onMeasure(ConstraintLayout.java:1572)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.FrameLayout.onMeasure(FrameLayout.java:185)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.FrameLayout.onMeasure(FrameLayout.java:185)
        at android.view.View.measure(View.java:22094)
        at androidx.constraintlayout.widget.ConstraintLayout.internalMeasureChildren(ConstraintLayout.java:1227)
        at androidx.constraintlayout.widget.ConstraintLayout.onMeasure(ConstraintLayout.java:1572)
        at android.view.View.measure(View.java:22094)
        at androidx.drawerlayout.widget.DrawerLayout.onMeasure(DrawerLayout.java:1119)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.FrameLayout.onMeasure(FrameLayout.java:185)
        at androidx.appcompat.widget.ContentFrameLayout.onMeasure(ContentFrameLayout.java:143)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.LinearLayout.measureChildBeforeLayout(LinearLayout.java:1514)
        at android.widget.LinearLayout.measureVertical(LinearLayout.java:806)
        at android.widget.LinearLayout.onMeasure(LinearLayout.java:685)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.FrameLayout.onMeasure(FrameLayout.java:185)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.LinearLayout.measureChildBeforeLayout(LinearLayout.java:1514)
        at android.widget.LinearLayout.measureVertical(LinearLayout.java:806)
E/AndroidRuntime:     at android.widget.LinearLayout.onMeasure(LinearLayout.java:685)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:6613)
        at android.widget.FrameLayout.onMeasure(FrameLayout.java:185)
        at com.android.internal.policy.DecorView.onMeasure(DecorView.java:724)
        at android.view.View.measure(View.java:22094)
        at android.view.ViewRootImpl.performMeasure(ViewRootImpl.java:2483)
        at android.view.ViewRootImpl.measureHierarchy(ViewRootImpl.java:1549)
        at android.view.ViewRootImpl.performTraversals(ViewRootImpl.java:1806)
        at android.view.ViewRootImpl.doTraversal(ViewRootImpl.java:1432)
        at android.view.ViewRootImpl$TraversalRunnable.run(ViewRootImpl.java:6826)
        at android.view.Choreographer$CallbackRecord.run(Choreographer.java:911)
        at android.view.Choreographer.doCallbacks(Choreographer.java:723)
        at android.view.Choreographer.doFrame(Choreographer.java:658)
        at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:897)
        at android.os.Handler.handleCallback(Handler.java:790)
        at android.os.Handler.dispatchMessage(Handler.java:99)
        at android.os.Looper.loop(Looper.java:164)
        at android.app.ActivityThread.main(ActivityThread.java:6543)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:440)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:810) 
```

Enter fullscreen mode Exit fullscreen mode