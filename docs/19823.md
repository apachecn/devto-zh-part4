# Android 中简单的重复任务调度器

> 原文：<https://dev.to/alialp/easy-reoccurring-task-scheduler-in-android-76m>

在移动开发中，经常需要多次运行一个方法，或者按照固定的时间表运行，比如每隔几秒钟播放一次动画或获取数据，根据 Android 的最佳实践，建议使用 Handler 对象，当您有很多重复出现的任务时，下面的代码片段可以派上用场

```
import android.os.Bundle;

public interface IReoccurringTask {
    void run(Bundle bundle);
} 
```

Enter fullscreen mode Exit fullscreen mode

```
import android.os.Bundle;
import android.os.Handler;

public class ReoccurringTaskScheduler {

    private Handler handler = new Handler();
    private Runnable runnable;
    private IReoccurringTask reoccurringTask;

    private ReoccurringTaskScheduler(){}

    public ReoccurringTaskScheduler(IReoccurringTask reoccurringTask) {
        this.reoccurringTask=reoccurringTask;
    }

    public void Schedule(long initDelay, final long interval) {

        runnable = new Runnable() {

            @Override
            public void run() {
                try {
                    reoccurringTask.run(null);
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    handler.postDelayed(this, interval);
                }
            }
        };
        handler.postDelayed(runnable, initDelay);
    }

    public void Schedule(final Bundle bundle, long initDelay, final long interval) {

        runnable = new Runnable() {

            @Override
            public void run() {
                try {
                    reoccurringTask.run(bundle);
                } catch (Exception e) {
                    e.printStackTrace();
                } finally {
                    handler.postDelayed(this, interval);
                }
            }
        };
        handler.postDelayed(runnable, initDelay);
    }

    public void cancel() {
        if (runnable != null)
            handler.removeCallbacks(runnable);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样定义你的定制逻辑

```
private class ReoccurringLogicClass implements IReoccurringTask{

    @Override
    public void run(Bundle bundle) {

        //reoccurring logic
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

然后这样用

```
ReoccurringTaskScheduler rTask=
    new ReoccurringTaskScheduler(new ReoccurringLogicClass()).Schedule(1000,2000); 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样取消它

```
rTask.cancel(); 
```

Enter fullscreen mode Exit fullscreen mode

快乐编码:)