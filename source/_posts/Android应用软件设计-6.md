---
title: Android应用软件设计-6
tags: [Android,Java]
categories: [Android]
copyright: true
comments: true
top: false
date: 2018-11-13 10:57:07
updated: 2018-11-13 10:57:07
keywords: Android
description: XML or JSON
passwords:
author: XQ
---

<center>Android应⽤软件设计</center>

<!--<center>E 6 XML or JSON 学号：SA18225428 姓名：许强</center>-->

#### 1.主题概述

##### 1.

> 使用Tomcat web server，并安装Eclipse新建Java web工程SCOSServer，在该工程下定义源码包“HttpServletesd.scos.servlet；
>
> 在SCOSServer”包esd.scos.servlet下新建类LoginValidator继承HttpServlet，要求如下：
>
> 1）实现 doPost()方法完成 SCOS 客户端登录请求传入的用户名与密码 验证， 当验证成功， 返回 JSON 串 “ {RESULTCODE:1} ”；否 则返回 “{RESULTCODE:0}”；
>
> 2）实现 doGet()方法，并在该方法中调用 doPost()；
>
> 在SCOSServer包esd.scos.servlet下新建类FoodUpdateService继承HttpServlet，要求如下：
>
> 1）实现 doGet（）方法，当 SCOS 客户端请求菜品信息更新时，实现 菜品更新信息发送，详细信息如下；
>
> 2）使用 JSON 封装菜品更新信息内容：更新菜品数量，每个菜品名 称，每个菜品价格，每个菜品类型；
>
> 3）将菜品更新信息以流的形式发送至 SCOS 客户端；
>
> 4）实现 doPost（）方法，并在该方法中调用 doGet（）；



##### 2.

>修改E5中SCOS的LoginOrRegister代码，当用户点击“登录”或“注册”按钮时，使用HttpURLConnection访问SCOSServer，并传入用户输入的用户名和密码；
>
>接收的Servlet类LoginValidator返回信息，当示登录失败，保持原有提示功能不变SCOSServerRESULTCODE为“1”时表示登录成功，为“0”时表示登录失败；
>
>修改E 5中SCOS的UpdateService代码，在该类中使用HttpURLConnection访问SCOSServer中FoodUpdateService的doGet()方法；解析返回结果json信息；

##### 3.

> UpdateService代码，当有菜品更新时，使用MediaPlayer播放更新提示音，并使用NotificationManager在状态栏提示用户“新品上架：菜品数量”，通知中含有“清除”按钮，当点击清除按钮时，通知消除；当点击通知其他区域时，页面跳转至的MainScreen屏幕
>

#### 2.步骤

Tomcat web server环境搭建->服务端LoginValidator->服务端FoodUpdateService->客户端LoginOrRegister->客户端UpdateService

[代码Github托管](https://github.com/xq773939719/USTC/tree/master/Android-SCOS)

[报告博客托管](https://xq773939719.github.io/)

#### 3.实现

##### 1.在SCOSServer”包esd.scos.servlet下新建类LoginValidator继承HttpServlet

``` java
//LoginValidator
@WebServlet(name = "LoginValidator")
public class LoginValidator extends HttpServlet {
    private static final Pattern pattern = Pattern.compile("^[A-Za-z1-9_-]+$");

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {


        response.setContentType("text/html");
        request.setCharacterEncoding("UTF-8");
        String name="";
        String password = "";
        name = request.getParameter("name");
        password = request.getParameter("password");

        String result = "";
        
        if (startValid(name,password)) {
            result="success";
        }else{
            result = "error";
        }
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("result", result);
        response.getWriter().print(jsonObject);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doPost(request,response);
    }
    private boolean startValid(String name,String password) {
        return pattern.matcher(name.trim()).matches() && pattern.matcher(password.trim()).matches();
    }
}

```

##### 2.在SCOSServer包esd.scos.servlet下新建类FoodUpdateService继承HttpServlet

``` java
//FoodUpdateService
@WebServlet(name = "FoodUpdateService")
public class FoodUpdateService extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request,response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=utf-8");
        request.setCharacterEncoding("UTF-8");

        JSONArray jsonArray = new JSONArray();

        jsonArray.add(updateFoods("东北家拌凉菜",10,10,"冷菜"));
        jsonArray.add(updateFoods("椒油素鸡",20,10,"冷菜"));
        jsonArray.add(updateFoods("浇汁豆腐",30,10,"冷菜"));
        jsonArray.add(updateFoods("开胃泡菜",40,10,"冷菜"));
        jsonArray.add(updateFoods("凉拌海带丝",50,10,"冷菜"));
        jsonArray.add(updateFoods("凉拌黄瓜",60,10,"冷菜"));
        jsonArray.add(updateFoods("卤牛肉",70,10,"冷菜"));
        jsonArray.add(updateFoods("青椒拌干丝",80,10,"冷菜"));
        jsonArray.add(updateFoods("干煸豆角",0,10,"热菜"));
        
        response.getWriter().print(jsonArray);
    }
    private JSONObject updateFoods(String name,int last,int price, String category){
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("name", name);
        jsonObject.put("last",last);
        jsonObject.put("price",price);
        jsonObject.put("category",category);
        return jsonObject;
    }
}

```



##### 3.客户端LoginOrRegister修改

> 使用HttpURLConnection访问SCOSServer，并传入用户输入的用户名和密码；

``` java
//登录，注册，返回，按钮功能
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
 

                                            String string = mButton_Enter.getText().toString();
                                            editor.putString("userName",string);
                                            editor.putInt("loginState",1);
                                            editor.commit();
                                            Toast.makeText(LoginOrRegister.this,"注册成功",Toast.LENGTH_LONG).show();
                                        }
                                        else{
                                            Toast.makeText(LoginOrRegister.this,"输入内容不符合规则",Toast.LENGTH_SHORT).show();
                                            //Toast.makeText(LoginOrRegister.this,"注册失败",Toast.LENGTH_LONG).show();
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
                                            //
                                            String string = mButton_Enter.getText().toString();
                                            editor.putString("userName",string);
                                            editor.putInt("loginState",1);
                                            editor.commit();

                                            Intent intent = new Intent(LoginOrRegister.this,MainScreen.class);
                                            //intent.putExtra("from","LoginSuccess");
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

``` java
private void sendJson(){
        //boolean loginValidate = false;
        Log.d("url","成功");
        try{
            JSONObject jsonObject = new JSONObject();
            jsonObject.put("name",etName.getText().toString());
            jsonObject.put("password",etPassword.getText().toString());
            URL url = new URL("http://192.168.1.144:8080");
            HttpURLConnection conn = (HttpURLConnection)url.openConnection();
            conn.setDoOutput(true);
            conn.setDoInput(true);
            conn.setUseCaches(false);
            conn.setRequestMethod("POST");
            conn.setRequestProperty("Connection", "Keep-Alive");
            conn.setRequestProperty("Charset", "UTF-8");
            conn.setRequestProperty("contentType", "application/json");
            conn.connect();
            OutputStreamWriter writer = new OutputStreamWriter(conn.getOutputStream());
            // 发送给服务器
            writer.write(jsonObject.toString());
            writer.flush();
            writer.close();
            //接收服务器返回信息
            BufferedReader br = new BufferedReader(new InputStreamReader(conn.getInputStream(),"UTF-8")) ;
            String line;
            StringBuilder sb = new StringBuilder();
            while ((line = br.readLine()) != null) {
                sb.append(line);
            }

            int RESULTCODE = 0;
                RESULTCODE = sb.toString().indexOf("RESULTCODE");

            if(RESULTCODE == 1){
                Log.d("RESULTCODE",RESULTCODE+"");
            }
            else if(RESULTCODE == 0){
                Log.d("RESULTCODE",RESULTCODE+"");
            }
            else{
                Log.d("RESULTCODE","no");
            }
            conn.disconnect();
        }catch(Exception exception){
            exception.printStackTrace();
        }
    }
```

##### 4.UpdateService代码

> 当有菜品更新时，使用MediaPlayer播放更新提示音，并使用NotificationManager在状态栏提示用户“新品上架：菜品数量”，通知中含有“清除”按钮，当点击清除按钮时，通知消除；当点击通知其他区域时，页面跳转至的屏幕

``` java
 	@TargetApi(Build.VERSION_CODES.O)
    @Override
    protected void onHandleIntent(Intent intent) {
        Log.d(TAG, "onHandleIntent");
        NotificationChannel channel = new NotificationChannel("channel_1","123",NotificationManager.IMPORTANCE_LOW);
        manager=(NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        manager.createNotificationChannel(channel);
        Notification.Builder builder = new Notification.Builder(this,"channel_1");
        builder.setAutoCancel(true);
        builder.setSmallIcon(R.drawable.food_cold_dbjlbc);//设置图标
        builder.setTicker("!新品上架!");//手机状态栏的提示
        builder.setContentTitle("新品上架");//设置标题
        builder.setContentText("东北家拌凉菜,10,冷菜");//设置通知内容
        builder.setWhen(System.currentTimeMillis());//设置通知时间
        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
        builder.setSound(defaultSoundUri);

        Intent intentCancel = new Intent(this, DeviceStartedListener.class);
        intentCancel.setAction("scos.intent.action.CLOSE_NOTIFICATION");
        PendingIntent pendingIntentCancel = PendingIntent.getBroadcast(this, 0, intentCancel, PendingIntent.FLAG_ONE_SHOT);
        builder.setDeleteIntent(pendingIntentCancel);

        Intent intent_to = new Intent(this, MainScreen.class);
        PendingIntent pendingIntent = PendingIntent.getActivity(this, 0, intent_to, 0);
        builder.setContentIntent(pendingIntent);//点击后的意图
        builder.setDefaults(Notification.DEFAULT_LIGHTS);//设置指示灯
        builder.setDefaults(Notification.DEFAULT_SOUND);//设置提示声音
        builder.setDefaults(Notification.DEFAULT_VIBRATE);//设置震动    
        MediaPlayer mediaPlayer = MediaPlayer.create(this,Settings.System.DEFAULT_NOTIFICATION_URI);
        mediaPlayer.start();//通知铃声
        mediaPlayer.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
            @Override
            public void onCompletion(MediaPlayer mp) {
                mp.release();
            }
        });
        Notification notification = builder.build();//4.1以上，以下要用getNotification()
        startForeground(Notification_ID,notification);//开启前台服务
        manager.notify(Notification_ID, notification);

    }
    public static void collapseStatusBar(Context context) {
        try {
            Object statusBarManager = context.getSystemService("statusbar");
            Method collapse;
            if (Build.VERSION.SDK_INT <= 16) {
                collapse = statusBarManager.getClass().getMethod("collapse");
            } else {
                collapse = statusBarManager.getClass().getMethod("collapsePanels");
            }
            collapse.invoke(statusBarManager);
        } catch (Exception localException) {
            localException.printStackTrace();
        }
    }
}
```



#### 4.结论

本次练习：

1. 学会了Servlet的搭建方法，如何构造response，如何接收request，post和get的区别。并使用tomcat进行部署和调试。

2. 学会了Json的数据构造方法，并可以通过Servlet接收和返回。

3. 熟悉了HttpUrlConnection的使用，熟练了在客户端进行get和post请求并接收数据的操作。

4. 学会了Json的数据解析方法，并可以在客户端接收到数据之后进行相应的解析。

5. 掌握了notification的特殊用法：添加按钮，并使用广播关闭该通知。

6. 初步掌握了MediaPlayer的用法，播放一个简单的资源。



##### 1.效果图

![](http://phmdesag2.bkt.clouddn.com/image/A-6/1.png)

<center>服务器发送的Json数据</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-6/2.png)

<center>接收到数据，并更新，显示通知</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-6/3.png)

<center>接收到的Json数据，并解析加入log</center>

![](http://phmdesag2.bkt.clouddn.com/image/A-6/4.png)

<center>通知</center>





##### 2.说明 XML 不同解析方式，比较各种方式的优缺点

> 1.DOM是用与平台和语言无关的方式表示XML文档的官方W3C标准。DOM是以层次结构组织的节点或信息片断的集合。这个层次结构允许开发人员在树中寻找特定信息。分析该结构通常需要加载整个文档和构造层次结构，然后才能做任何工作。由于它是基于信息层次的，因而DOM被认为是基于树或基于对象的。

> 【优点】
> ​      ①允许应用程序对数据和结构做出更改。
> ​      ②访问是双向的，可以在任何时候在树中上下导航，获取和操作任意部分的数据。
> 【缺点】
> ​      ①通常需要加载整个XML文档来构造层次结构，消耗资源大。

> 2.SAX处理的优点非常类似于流媒体的优点。分析能够立即开始，而不是等待所有的数据被处理。而且，由于应用程序只是在读取数据时检查数据，因此不需要将数据存储在内存中。这对于大型文档来说是个巨大的优点。事实上，应用程序甚至不必解析整个文档；它可以在某个条件得到满足时停止解析。一般来说，SAX还比它的替代者DOM快许多。
>
> 【优点】
> ​     ①不需要等待所有数据都被处理，分析就能立即开始。
> ​     ②只在读取数据时检查数据，不需要保存在内存中。
> ​     ③可以在某个条件得到满足时停止解析，不必解析整个文档。
> ​     ④效率和性能较高，能解析大于系统内存的文档。
>
> 【缺点】
> ​     ①需要应用程序自己负责TAG的处理逻辑（例如维护父/子关系等），文档越复杂程序就越复杂。
> ​     ②单向导航，无法定位文档层次，很难同时访问同一文档的不同部分数据，不支持XPath。

> 3.JDOM与DOM主要有两方面不同。首先，JDOM仅使用具体类而不使用接口。这在某些方面简化了API，但是也限制了灵活性。第二，API大量使用了Collections类，简化了那些已经熟悉这些类的Java开发者的使用。
>
> 【优点】
> ​     ①使用具体类而不是接口，简化了DOM的API。
> ​     ②大量使用了Java集合类，方便了Java开发人员。
>
> 【缺点】
> ​     ①没有较好的灵活性。
> ​     ②性能较差。

> 4.DOM4J使用接口和抽象基本类方法。DOM4J大量使用了API中的Collections类，但是在许多情况下，它还提供一些替代方法以允许更好的性能或更直接的编码方法。直接好处是，虽然DOM4J付出了更复杂的API的代价，但是它提供了比JDOM大得多的灵活性。
>
> 【优点】
> ​     ①大量使用了Java集合类，方便Java开发人员，同时提供一些提高性能的替代方法。
> ​     ②支持XPath。
> ​     ③有很好的性能。
>
> 【缺点】
> ​     ①大量使用了接口，API较为复杂。

##### 3.对比 JSON 与 XML 作为数据封装方式的优缺点

- 可读性　：　JSON和XML的可读性可谓不相上下，一边是建议的语法，一边是规范的标签形式，很难分出胜负。
- 可扩展性　：　XML天生有很好的扩展性，JSON当然也有，没有什么是XML能扩展，JSON不能的。
- 编码难度　：　XML有丰富的编码工具，比如Dom4j、JDom等，JSON也有json.org提供的工具，但是JSON的编码明显比XML容易许多，即使不借助工具也能写出JSON的代码，可是要写好XML就不太容易了。
- 解码难度　：　XML的解析得考虑子节点父节点，让人头昏眼花，而JSON的解析难度几乎为0。
- 流行度　：　XML已经被业界广泛的使用，而JSON才刚刚开始，但是在Ajax这个特定的领域，未来的发展一定是XML让位于JSON。

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

【7】[Android后台service限制][7]

[1]: https://www.bilibili.com/video/av18008899
[2]: https://www.jianshu.com/
[3]: https://blog.csdn.net/jltxgcy/article/details/48288467
[4]: https://blog.csdn.net/jltxgcy
[5]: https://blog.csdn.net/barryhappy
[6]: https://blog.csdn.net/lmj623565791
[7]:https://blog.csdn.net/code_shen/article/details/78422358