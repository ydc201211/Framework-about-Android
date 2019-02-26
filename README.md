## otto框架
一个Android中使用得比较多的android 事件总线 EventBus模式的一个框架Otto。
### 一、Android Studio中配置Otto (Eclipse中直接下载jar包导入)

跟之前介绍的其他的框架一样，它只需要简单地在build.gradle中配置下面一行红色字体即可

dependencies { 
    compile fileTree(dir: 'libs', include: ['*.jar']) 
    compile 'com.android.support:appcompat-v7:19.+' 
    / /otto 所需要依赖的包 
    compile 'com.squareup:otto:+' 
}

### 二、Otto事件总线框架浅析

#### 1、为什么要用Otto框架？

Otto框架的主要功能是帮助我们来降低多个类之间的耦合度的（解耦）。

譬如：一个类A和另一个类B之间，如果A要操作B中的某个方法。

传统的方法：A直接调用B对象的该方法（耦合在一起）

事件总线机制：A不需要调用B类的方法，而仅仅需要产生并发出 一个“事件通知”， 如果B订阅了该“事件”

那么它将会接受到这个事件，做出相应的操作。这样就被解耦了。

#### 2. Otto框架使用(结合代码介绍)

Otto框架其实相对之前的几个Android开源框架来说，更容易理解。

它主要运用到一个类： Bus类（用于注册类，注销类，发布事件）

两个注解： @Subscribe (订阅)    @Produce (生产)    【都是针对“事件”的注解】


<table>
  <tr>
    <td>
    <img src="http://img.blog.csdn.net/20140712170558796?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGpwaGhq/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="216"  height="382"/>
    </td>
  </tr>
   
</table>

为了方便，我这边没有使用两个类来做这个Demo， 不过如果大家要试的话也可以，不过记住一点，无论是
发布者类  还是 订阅者类， 都需要用Bus进行类的注册，还有注销。否则将没法被Bus识别，这样就无法生效了。
下面的Demo， 仅为了让大家知道“事件”被产生了之后，post出来，所有订阅了该事件的类都会接到该事件，接受的先后顺序，不由我们控制！

public class MyActivity extends ActionBarActivity {  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_my);  
        findViewById(R.id.button_change).setOnClickListener(new View.OnClickListener() {  
            @Override  
            public void onClick(View view) {  
                BusProvider.getBusInstance().post(new DataChangedEvent("this is changed String"));//发布事件  
            }  
        });  
    }  
  
    @Override  
    protected void onResume() {  
        super.onResume();  
        BusProvider.getBusInstance().register(this);//注册  
          
    }  
  
    @Override  
    protected void onPause() {  
        super.onPause();  
        BusProvider.getBusInstance().unregister(this);//注销  
    }  
  
    @Subscribe   //订阅事件DataChangedEvent  
    public void sayGoodOnEvent(DataChangedEvent event){  
        Log.e("event", "good");  
    }  
  
    @Subscribe  //订阅事件  
    public void sayBadOnEvent(DataChangedEvent event){  
        Log.e("event", "bad");  
    }  
  
    @Produce    //产生事件  
    public DataChangedEvent produceDataChangedEvent(){  
        return new DataChangedEvent("this is changed String");  
    }  
  
}  



