# 活动快捷方式不在体内装置中创建

> 原文：<https://dev.to/imrankhanluhar/activity-shortcut-is-not-create-in-vivo-devices-3m29>

我想在主屏幕上创建活动快捷方式，这样用户可以直接打开活动屏幕。

我用过这个代码

if(shortcutmanagercompat . isrequestpinshortcutsupported
(getApplicationContext())
{

```
 ShortcutInfoCompat shortcut = new ShortcutInfoCompat.Builder(getApplicationContext(), id)
     .setIntent(new Intent(getApplicationContext(), 
       TranslateActivity.class).setAction(Intent.ACTION_MAIN))
     .setShortLabel(label)
     .setIcon(icon)
     .build();
     return 
```

Enter fullscreen mode Exit fullscreen mode

shortcutmanagercompat . requestpin shortcut(getApplicationContext()，
快捷方式，start home screen())；
}

```
 else
     {
    // Shortcut is not supported by your launcher

    Intent shortcutIntent = new Intent(getApplicationContext(), TranslateActivity.class);
    shortcutIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    shortcutIntent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

    Intent addIntent = new Intent();
    addIntent.putExtra(Intent.EXTRA_SHORTCUT_INTENT, shortcutIntent);
    addIntent.putExtra(Intent.EXTRA_SHORTCUT_NAME, "Test");
    addIntent.putExtra(Intent.EXTRA_SHORTCUT_ICON_RESOURCE, Intent.ShortcutIconResource.fromContext(getApplicationContext(), R.mipmap.ic_launcher));
    addIntent.setAction("com.android.launcher.action.INSTALL_SHORTCUT");
    getApplicationContext().sendBroadcast(addIntent);
} 
```

Enter fullscreen mode Exit fullscreen mode