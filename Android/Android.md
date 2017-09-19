## 1.如下在activity的oncreat里开一个子线程更新UI，可以运行吗？

```
public class MainActivity extends Activity {  
    private TextView tvText;  
    @Override  
    public void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        tvText = (TextView) findViewById(R.id.main_tv);  
        new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    Thread.sleep(200);  
                } catch (InterruptedException e) {  
                    e.printStackTrace();  
                }  
                tvText.setText("OtherThread");  
            }  
        }).start();  
    }  
}
```

结果：可以运行。

原因如下
```
public final class ViewRootImpl implements ViewParent,  
        View.AttachInfo.Callbacks, HardwareRenderer.HardwareDrawCallbacks {  
    // 省去海量代码…………………………  
  
    void checkThread() {  
        if (mThread != Thread.currentThread()) {  
            throw new CalledFromWrongThreadException(  
                    "Only the original thread that created a view hierarchy can touch its views.");  
        }  
    }  
  
    // 省去巨量代码……………………  
} 
```

不能更新ui是因为ViewRootImpl的checkThread()会检查，而ViewRootImpl是在onResume()方法里创建。在哦你Creat()里还没有创建ViewRootImpl，所以不会抛出异常。如果耗时的话，才会抛出异常。
