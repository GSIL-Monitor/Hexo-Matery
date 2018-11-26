---
title: Android应用软件设计-4
tags: [Android,Java]
categories: [Android]
copyright: true
comments: true
top: false
date: 2018-11-13 10:56:56
updated: 2018-11-13 10:56:56
keywords: Android
description: Multi-thread Programming
passwords:
author: XQ
---

<center>Android应⽤软件设计</center>

<!--<center>E 4 Multi-thread Programming 学号：SA18225428 姓名：许强</center>-->



#### 1.主题概述

##### 1.

> 修改E 3中的LoginOrRegister代码， 当导航到该页面时， 使用SharedPreferences保存或读取用户登录状态。

##### 2.

> 修改LoginOrRegister代码，当用户点击登录&注册时，按E3中登录规则判断是否通过， 如果登录成功则将当前登录名输入框的值写入到SharedPreferences中的userName记录中，另外再写入loginState值为1。当用户点击返回时，判断SharedPreferences中是否有用户名记录，如无，则保持原功能不变；否则在SharedPreferences写入loginState值为0。
>

##### 3.

> 用户是否登录的判断逻辑改成使用现SharedPreferences包中，es.source.code.activity的loginState值进行功能实。
>

##### 4.

> 新建SCOSHelper.java类，设置RelativeLayout布局，显示，用户使用协议，关于系统， 电话人工帮助，短信帮助，邮件帮助，当用户点击“电话人工帮助”时，使用 ImplicitIntent 调用系统 app 实现自动拨号功能，拨打目标号码为“5554”；当用户点击“短信帮助”时，使用 SmsManager 自动发送短信只目 标号码“5554”，内容为“test scos helper”。当短信发送完毕后，使用 Toast 提示“求助短信发送成功”；当用户点击“邮件帮助”时，启动自定义多线程 MailSender 完成 邮件发送，邮件内容和标题自定义。当邮件发送完毕后，使用 Handler 接收消息，当消息值为 1 时，使用 Toast 提示“求助邮件已发送成功”。

##### 5.

> 修改E3中FoodOrderView用户点击结账按钮时，启动代码，使用AsyncTask模拟结账功能。当AsyncTask，并使用ProgressBar实时显示结账进度，要求AsyncTask运行6秒后模拟完成结账功能。
>

#### 2.步骤

修改LigionOrRegister->修改MainScreen和SCOSEntry->添加SCOSHelper->修改FoodOrderView。

[代码Github托管](https://github.com/xq773939719/USTC/tree/master/Android-SCOS)

[报告博客托管](https://xq773939719.github.io/)

#### 3.实现

##### 1.添加SharedPreferences代码

```java
//LoginOrRegister
/*
使用SharedPreferences进行用户登录状态判断，当状态为0，设置登录按钮不可见，当为1，设置注册按钮不可见。
*/
protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.loginorregister);
        mButton_Enter = (Button)findViewById(R.id.enterButton);
        mButton_Login = (Button)findViewById(R.id.loginButton);
        mButton_Return = (Button)findViewById(R.id.returnButton);
        etName = (EditText)findViewById(R.id.userName);
        etPassword = (EditText)findViewById(R.id.userPassword);
        setListeners();

        SharedPreferences sharedPreferences = this.getSharedPreferences("User",MODE_PRIVATE);
        int loginState = sharedPreferences.getInt("loginState",0);
        if(loginState == 0){
            mButton_Enter.setVisibility(View.INVISIBLE);
        }
        else{
            mButton_Login.setVisibility(View.VISIBLE);
        }

    }
```

##### 2.修改LoginOrRegister代码

> 当用户点击登录时，如果登录&注册成功则将当前登录名输入框的值写入到SharedPreferences中的userName记录中，另外再写入loginState值为1。当用户点击返回时，判断SharedPreferences中是否有用户名记录，如无，则保持原功能不变；否则在SharedPreferences写入loginState值为0。

```java
//LoginOrRegister
private class OnClick implements View.OnClickListener{

        @Override
        public void onClick(View view){

            final User loginUser = new User();
            Intent intent = null;
            final SharedPreferences.Editor editor = getSharedPreferences("User",MODE_PRIVATE).edit();
            Thread thread = null;
            
            switch (view.getId()){
                    
                case R.id.loginButton:
                    showProgress(R.string.registering);

                    thread = new Thread(new Runnable() {
                        @Override
                        public void run() {

                            //sendJson();
                            String url = MyPOSTHttpURLConnection.BASE_URL + "/LoginValidator";
                            Map<String,String > parms = new HashMap<String, String>();
                            String name = etName.getText().toString();
                            String password = etPassword.getText().toString();
                            parms.put("name",name);
                            parms.put("password",password);


                            String result = MyPOSTHttpURLConnection.getContextByHttp(url,parms);
                            Log.d("result",result);


                            Message msg = new Message();
                            msg.what = 20;
                            Bundle data = new Bundle();

                            data.putString("result",result);
                            msg.setData(data);

                            handler.sendMessage(msg);
                        }


                        Handler handler = new Handler(){
                            @Override
                            public void handleMessage(Message msg) {
                                if(msg.what==20){
                                    Bundle data = msg.getData();
                                    String key = data.getString("result");
                                    Log.d("key",key);
                                    try{
                                        JSONObject json = new JSONObject(key);
                                        String result = (String)json.get("result");
                                        if(result.equals("success")){
                                            mButton_Enter.setVisibility(View.VISIBLE);
                                            mButton_Login.setVisibility(View.INVISIBLE);
                                            loginUser.setOldUser(false);
                                            loginUser.setUserName(etName.getText().toString());
                                            loginUser.setPassword(etPassword.getText().toString());
                                            Toast.makeText(LoginOrRegister.this,"欢迎您成为 SCOS 新用户",Toast.LENGTH_SHORT).show();
                                            
											//设置SharedPreferences
                                            String string = mButton_Enter.getText().toString();
                                            editor.putString("userName",string);
                                            editor.putInt("loginState",1);
                                            editor.commit();
                                            Toast.makeText(LoginOrRegister.this,"注册成功",Toast.LENGTH_LONG).show();
                                        }
                                        else{
                                            Toast.makeText(LoginOrRegister.this,"输入内容不符合规则",Toast.LENGTH_SHORT).show();
                                            
                                        }


                                    }
                                    catch (Exception e){
                                        e.printStackTrace();
                                    }
                                }
                            }
                        };


                    });

                    thread.start();
                  
                    dismissProgress();
                    break;
                case R.id.enterButton:
                    showProgress(R.string.logining);

                    thread = new Thread(new Runnable() {
                        @Override
                        public void run() {

                            //sendJson();
                            String url = MyPOSTHttpURLConnection.BASE_URL + "/LoginValidator";
                            Map<String,String > parms = new HashMap<String, String>();
                            String name = etName.getText().toString();
                            String password = etPassword.getText().toString();
                            parms.put("name",name);
                            parms.put("password",password);
                            String result = MyPOSTHttpURLConnection.getContextByHttp(url,parms);
                            Log.d("result",result);
                            Message msg = new Message();
                            msg.what = 21;
                            Bundle data = new Bundle();
                            data.putString("result",result);
                            msg.setData(data);

                            handler.sendMessage(msg);
                        }
						//使用Handler处理消息
                        Handler handler = new Handler(){
                            @Override
                            public void handleMessage(Message msg) {
                                if(msg.what==21){
                                    Bundle data = msg.getData();
                                    String key = data.getString("result");
                                    Log.d("key",key);
                                    try{
                                        JSONObject json = new JSONObject(key);
                                        String result = (String)json.get("result");
                                        if(result.equals("success")){
                                            loginUser.setOldUser(true);
                                            loginUser.setUserName(etName.getText().toString());
                                            loginUser.setPassword(etPassword.getText().toString());
                                            Toast.makeText(LoginOrRegister.this,"已登录",Toast.LENGTH_SHORT).show();
                                            //设置SharedPreferences
                                            String string = mButton_Enter.getText().toString();
                                            editor.putString("userName",string);
                                            editor.putInt("loginState",1);
                                            editor.commit();

                                            Intent intent = new Intent(LoginOrRegister.this,MainScreen.class);
                                           
                                            startActivity(intent);
                                            Toast.makeText(LoginOrRegister.this,"登录成功",Toast.LENGTH_LONG).show();
                                        }
                                        else{
                                            Toast.makeText(LoginOrRegister.this,"输入内容不符合规则",Toast.LENGTH_SHORT).show();                                   
                                        }
                                    }
                                    catch (Exception e){
                                        e.printStackTrace();
                                    }
                                }
                            }
                        };
                    });
                    thread.start();
                    
                    dismissProgress();
                    break;

                case R.id.returnButton:
                    String string = mButton_Enter.getText().toString();
                    editor.putString("userName",string);
                    editor.putInt("loginState",0);
                    editor.commit();
                    intent = new Intent(LoginOrRegister.this,MainScreen.class);
                    startActivity(intent);
                    break;

            }
        }
    }
```

##### 3.修改MainScreen和SCOSEntry代码

``` java
//SCOSEntry.java
@Override
public boolean onTouchEvent(MotionEvent event) {
        return mGestureDetector.onTouchEvent(event);
    }

GestureDetector.SimpleOnGestureListener mGestureListener = new GestureDetector.SimpleOnGestureListener() 	{
        public boolean onFling(MotionEvent e1, MotionEvent e2, float velocityX, float velocityY) {
            float x = e1.getX() - e2.getX();

            if (x > MIN_DISTANCE && Math.abs(velocityX) > MIN_VELOCITY) {
                showToast(getResources().getString(R.string.welcome));//显示欢迎

                SharedPreferences.Editor editor = getSharedPreferences("User",MODE_PRIVATE).edit();
                editor.putInt("loginState",0);
                editor.commit();

                Intent intent = new Intent(SCOSEntry.this,MainScreen.class);
                intent.putExtra("from","FromEntry");
                startActivity(intent);

                finish();
            }
            return false;
        }
    };//
```

``` java
//MainScreen
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.mainscreen);
        mLoginButton = (Button) findViewById(R.id.HomeLoginButton);
        mOrderButton = (Button) findViewById(R.id.HomeOrderButton);
        mMenuButton = (Button) findViewById(R.id.HomeMenuButton);
        mHelpButton = (Button) findViewById(R.id.HomeHelpButton);
        mHomeImage = (ImageView) findViewById(R.id.HomeLogo);

        Intent intent = getIntent();
        String string = intent.getStringExtra("from");
        SharedPreferences sharedPreferences = this.getSharedPreferences("User",MODE_PRIVATE);
        int loginState = sharedPreferences.getInt("loginState",0);
        if(loginState == 0){
            mOrderButton.setVisibility(View.INVISIBLE);
            mMenuButton.setVisibility(View.INVISIBLE);
        }
        if(loginState == 1){
            if (mOrderButton.getVisibility() == View.INVISIBLE) {
                mOrderButton.setVisibility(View.VISIBLE);
            }
            if (mMenuButton.getVisibility() == View.INVISIBLE) {
                mMenuButton.setVisibility(View.VISIBLE);
            }
        }

        setListeners();
       
```

#### 4.新建SCOSHelper类

> 实现用户使用协议，关于系统， 电话人工帮助，短信帮助，邮件帮助。

``` java
//Help
/*
使用隐式消息打电话，发送短信，发送邮件时使用SMTP协议
*/
public class Help extends AppCompatActivity {
    private GridView gridView;
    private List<Map<String, Object>> dataList;
    private SimpleAdapter adapter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.help);

        gridView = (GridView) findViewById(R.id.gridview);
        initData();

        String[] from={"img","text"};

        int[] to={R.id.grid_view_image,R.id.grid_view_textview};

        adapter=new SimpleAdapter(this, dataList, R.layout.layout_grid_view_item, from, to);

        gridView.setAdapter(adapter);
        gridView.setNumColumns(2);

        gridView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            Intent intent = null;
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long arg3) {
                switch(position){
                    case 0:
                        //协议
                        Toast.makeText(Help.this,"by冰水鉴心",Toast.LENGTH_SHORT).show();
                        break;
                        
                    case 1:
                        //关于
                        Toast.makeText(Help.this,"SCOS点餐系统",Toast.LENGTH_SHORT).show();
                        break;
                        
                    case 2:
                        //电话
                        intent = new Intent(Intent.ACTION_DIAL,Uri.parse("tel:"+5554));
                        startActivity(intent);
                        break;
                        
                    case 3:
                        //短信
                        String tel = "5554";
                        String content = "test scos help";
                        if (PhoneNumberUtils.isGlobalPhoneNumber(tel)) {

                            Intent intent = new Intent(Intent.ACTION_SENDTO, Uri.parse("smsto:" + tel));
                            intent.putExtra("sms_body", content);
                            startActivity(intent);
                        }
                        Toast.makeText(getApplicationContext(), "求助短信发送成功", Toast.LENGTH_SHORT).show();
                        break;
                        
                    case 4:
                        //邮件
                        new Thread(new Runnable() {
                            @Override
                            public void run() {
                                sendEmail();
                            }

                        }).start();
                        Toast.makeText(Help.this,"求助邮件已发送成功",Toast.LENGTH_SHORT).show();
                        break;

                }  
            }
        });

    }


    public void helpToMainscreen(View view){
        Intent intent = new Intent(this,MainScreen.class);
        startActivity(intent);
    }


    void initData() {
        //图标
        int icno[] = { R.drawable.free04,R.drawable.free08,R.drawable.free21,R.drawable.free28 ,R.drawable.free38};
        //图标下的文字
        String name[]={"用户使用协议","关于系统", "电话人工帮助","短信帮助","邮件帮助"};
        dataList = new ArrayList<Map<String, Object>>();
        for (int i = 0; i <icno.length; i++) {
            Map<String, Object> map=new HashMap<String, Object>();
            map.put("img", icno[i]);
            map.put("text",name[i]);
            dataList.add(map);
        }
    }

    private void sendEmail() {
        try {
            MailSenderInfo mailInfo = new MailSenderInfo();
            mailInfo.setMailServerHost("smtp.163.com");
            mailInfo.setMailServerPort("25");
            mailInfo.setValidate(true);
            mailInfo.setUserName("18207162619@163.com");  //邮箱地址
            mailInfo.setPassword("xxxxxxxx");//邮箱密码
            mailInfo.setFromAddress("18207162619@163.com");//和username的邮箱地址一致
            mailInfo.setToAddress("773939719@qq.com");
            mailInfo.setSubject("邮件主题");
            mailInfo.setContent("邮件内容");

            //这个类主要来发送邮件
            SimpleMailSender sms = new SimpleMailSender();
            boolean b = sms.sendTextMail(mailInfo);//发送文体格式,返回是否发送成功的boolean类型

            Log.e("MainActivity", "MainActivity sendEmail()"+b);//sms.sendHtmlMail(mailInfo);//发送html格式

        } catch (Exception e) {
            Log.e("SendMail", e.getMessage(), e);
        }
    }
}
```

##### 5.修改FoodOrderView，实现模拟结账功能，结账功能放进了片段中

``` java
//OrderedViewFragment
public class OrderedViewFragment extends Fragment {
    private ListView mListView;
    private static OrderedListViewAdapter orderedListViewAdapter;
    private Activity activity;
    private List<Food> foods = new ArrayList<>();
    private ProgressDialog progressDialog;
    private Button button = null;
    private Button button_cancle = null;
    private ProgressBar progressBar = null;
    private TextView textView = null;
    private View view;
    private int count=0;
    private final static int STOP=0x10000;
    private final static int NEXT=0x10001;


    public OrderedViewFragment(){

    }

    public static OrderedListViewAdapter getOrderListViewAdapter(){
        return  orderedListViewAdapter;

    }
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, Bundle savedInstanceState) {
        view = inflater.inflate(R.layout.layout_fragment_ordered_view, container,false);

        mListView = (ListView)view.findViewById(R.id.ordered_view_fragment);
        orderedListViewAdapter = new OrderedListViewAdapter(getActivity(),foods);
        mListView.setAdapter(orderedListViewAdapter);
        button = (Button) view.findViewById(R.id.ordered_view_button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //Toast.makeText(getContext(),"请结账",Toast.LENGTH_SHORT);
                MyAsyncTask myAsyncTask = new MyAsyncTask();
                myAsyncTask.execute();
            }
        });
        button_cancle = (Button) view.findViewById(R.id.ordered_view_button_cancle);
        button_cancle.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //
                for(int i = 0; i < ordered.size();i++){
                    ordered.get(i).setInlist(false);
                    ordered.get(i).setCount(0);
                    ordered.get(i).setState(true);
                }
                ordered.clear();
                Toast.makeText(getActivity(),"已取消",Toast.LENGTH_SHORT).show();
                orderedListViewAdapter.notifyDataSetChanged();
                //Intent intent = new Intent(getContext(),SCOSEntry.class);
                //startActivity(intent);
            }
        });
        return view;
    }
    
    public void initFoods(List<Food> foods){
        this.foods = foods;
        //foods.add(new Food("",0,R.drawable.wine));
    }
    private class MyAsyncTask extends AsyncTask<String, Object, Boolean>{
        @Override
        protected void onPreExecute() {
            //showProgress("结账中...");
            progressBar = view.findViewById(R.id.progressBar);
            progressBar.setVisibility(View.VISIBLE);
            progressBar.setMax(100);
            progressBar.setProgress(0);
            textView = view.findViewById(R.id.progress_text);
            textView.setVisibility(View.VISIBLE);
        }

        @Override
        protected Boolean doInBackground(String... strings) {
            Thread t = new Thread(new Runnable(){
                @Override
                public void run() {
                    // TODO Auto-generated method stub
                    for(int i=0;i<20;i++){
                        try {
                            count=(i+1)*5;
                            Thread.sleep(300);
                            textView.setText(count+"%");
                            if(count==100){
                                Message msg=new Message();
                                msg.what=STOP;

                                handler.sendMessage(msg);
                                break;
                            }
                            else{
                                Message msg=new Message();
                                msg.what=NEXT;
                                handler.sendMessage(msg);
                            }
                        } catch (InterruptedException e) {
                            // TODO Auto-generated catch block
                            e.printStackTrace();
                        }
                    }

                }
            });
            t.start();

            return null;
        }

        @Override
        protected void onPostExecute(Boolean aBoolean) {
            ordered.clear();
            orderedListViewAdapter.notifyDataSetChanged();
            button.setClickable(false);
            super.onPostExecute(aBoolean);

        }
        @Override
        protected void onProgressUpdate(Object... values) {
            super.onProgressUpdate(values);
        }
    }

    public void showProgress(String messageRes) {
        progressDialog = new ProgressDialog(getActivity());
        progressDialog.setTitle(getResources().getString(R.string.app_name));
        progressDialog.setMessage(messageRes);
        progressDialog.setCancelable(false);
        progressDialog.show();

    }
    private Handler handler=new Handler(){
        @SuppressWarnings("static-access")
        public void handleMessage(Message msg){
            switch(msg.what){
                case STOP:
                    progressBar.setVisibility(View.GONE);
                    Thread.currentThread().interrupt();
                    progressBar = view.findViewById(R.id.progressBar);
                    progressBar.setVisibility(View.INVISIBLE);
                    textView = view.findViewById(R.id.progress_text);
                    textView.setVisibility(View.INVISIBLE);
                    Toast.makeText(getContext(),"已结账",Toast.LENGTH_SHORT).show();
                    break;
                case NEXT:
                    if(!Thread.currentThread().interrupted()){
                        progressBar.setProgress(count);
                    }
                    break;
            }
        }
    };
}
```



#### 4.结论



本次练习：

1. 使用SharedPreference来实现数据读写，熟悉了这个轻量级存储工具的使用。

2. 再次使用了GridView，熟练掌握adapter view的用法。
3. 同时掌握了多线程处理方法。

##### 1.效果图

![](http://phmdesag2.bkt.clouddn.com/image/A-4/1.png)

<center>注册页面</center>



![](http://phmdesag2.bkt.clouddn.com/image/A-4/2.png)

<center>注册成功后显示登录</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/3.png)

<center>帮助页面</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/4.png)

<center>拨打求助电话</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/5.png)

<center>短信页面</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/6.png)

<center>邮件接收</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/7.png)

<center>订单结账进度</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-4/8.png)

<center>结账完毕后，结账按钮不可点击</center>



##### 2.如何提高程序性能和避免ANR异常的出现？

<u>Android程序不可能无限制地使用内存和CPU资源，过多地使用内存会导致程序内存溢出，即OOM(Out of Memory)；而过多地使用CPU资源，一般是指做大量的耗时任务，会导致手机变得卡顿甚至程序无法响应的情况，即ANR(Application not Responding)。</u>

性能优化：

`1.线程优化：线程优化的思想是采用线程池，避免程序中存在大量的Thread。线程池可以重用内部的线程，从而避免了线程的创建和销毁所带来的性能开销，同时线程池还能有效地控制线程池的最大并发数，避免大量的线程因互相抢占系统资源从而导致阻塞现象的发生。因此，在实际开发中，我们要尽量采用线程池，而不是每次都要创建一个Thread对象。`

`2.布局优化：布局优化的思想很简单，就是尽量减少布局文件的层级。有选择地使用性能较低的ViewGroup，比如RelativeLayout。如果布局中既可以使用LinearLayout也可以使用RelativeLayout，那么就使用LinearLayout，因为RelativeLayout比较复杂，它的布局过程需要花费更多的CPU时间。FrameLayout和LinearLayout一样都是一种简单高效的ViewGroup，因此也可以多使用FrameLayout。`



避免ANR出现：

`1.避免在主线程上进行复杂耗时的操作，比如说发送接收网络数据/进行大量计算/操作数据库/读写文件等。这个可以通过使用AsyncTask或者使用多线程来实现。`

`2.broadCastReceiver 要进行复杂操作的的时候，可以在onReceive()方法中启动一个Service来处理。`

`3.在设计及代码编写阶段避免出现出现同步/死锁或者错误处理不恰当等情况。`



##### 3.在多线程编程中，怎样保证线程安全？

保证线程安全：

`1.竞争与原子操作 
多个线程同时访问和修改一个数据，可能造成很严重的后果。出现严重后果的原因是很多操作被操作系统编译为汇编代码之后不止一条指令，因此在执行的时候可能执行了一半就被调度系统打断了而去执行别的代码了。一般将单指令的操作称为原子的(Atomic)，因为不管怎样，单条指令的执行是不会被打断的。因此，为了避免出现多线程操作数据的出现异常，Linux系统提供了一些常用操作的原子指令，确保了线程的安全。但是，它们只适用于比较简单的场合，在复杂的情况下就要选用其他的方法了。`

`2.同步与锁 
为了避免多个线程同时读写一个数据而产生不可预料的后果，开发人员要将各个线程对同一个数据的访问同步，也就是说，在一个线程访问数据未结束的时候，其他线程不得对同一个数据进行访问。同步的最常用的方法是使用锁(Lock)，它是一种非强制机制，每个线程在访问数据或资源之前首先试图获取锁，并在访问结束之后释放锁；在锁已经被占用的时候试图获取锁时，线程会等待，直到锁重新可用。二元信号量是最简单的一种锁，它只有两种状态：占用与非占用，它适合只能被唯一一个线程独占访问的资源。对于允许多个线程并发访问的资源，要使用多元信号量(简称信号量)。`

`3.可重入 
一个函数被重入，表示这个函数没有执行完成，但由于外部因素或内部因素，又一次进入该函数执行。一个函数称为可重入的，表明该函数被重入之后不会产生任何不良后果。可重入是并发安全的强力保障，一个可重入的函数可以在多线程环境下放心使用。`

`4.防止过度优化 
在很多情况下，即使我们合理地使用了锁，也不一定能够保证线程安全，因此，我们可能对代码进行防止过度的优化以确保线程安全。我们可以使用volatile关键字试图阻止过度优化，它可以做两件事：第一，阻止编译器为了提高速度将一个变量缓存到寄存器而不写回；第二，阻止编译器调整操作volatile变量的指令顺序。`



#### 5.参考

##### 参考书籍

```
【1】《Head First Android开发》
【2】《Android第一行代码》
【3】《Head First Java》
【4】《Java编程思想》
【5】《Java核心技术》
【6】《Android编程权威指南》
```

##### 参考链接

【1】[Android的视频教程][1]

【2】[提供帮助的社区][2]

【3】[技术博客CSDN][3]

【4】[技术博客开源中国][4]

【5】[张新强的CSDN博客][5]

【6】[Hongyang][6]

[1]: https://www.bilibili.com/video/av18008899
[2]: https://www.jianshu.com/
[3]: https://blog.csdn.net/jltxgcy/article/details/48288467
[4]: https://blog.csdn.net/jltxgcy
[5]: https://blog.csdn.net/barryhappy
[6]:https://blog.csdn.net/lmj623565791