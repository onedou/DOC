source: http://www.jb51.net/article/84957.htm

这篇文章主要为大家详细介绍了Android WebView使用方法详解，文中附js交互调用方法，感兴趣的小伙伴们可以参考一下

目前很多Android app都内置了可以显示web页面的界面，会发现这个界面一般都是由一个叫做WebView的组件渲染出来的，学习该组件可以为你的app开发提升扩展性。

先说下WebView的一些优点：

--可以直接显示和渲染web页面，直接显示网页
--webview可以直接用html文件（网络上或本地assets中）作布局
--和JavaScript交互调用 

一、基本使用

首先layout中即为一个基本的简单控件：

?
1
2
3
4
5
<WebView
 android:id="@+id/webView1"
 android:layout_width="fill_parent"
 android:layout_height="fill_parent"
 android:layout_marginTop="10dp" />
同时，因为要房访问网络，所以manifest中必须要加uses-permission：

<uses-permission android:name="android.permission.INTERNET"/>
在activity中即可获得webview的引用，同时load一个网址：

?
1
2
3
webview = (WebView) findViewById(R.id.webView1);
webview.loadUrl("http://www.baidu.com/");
//webview.reload();// reload page
这个时候发现一个问题，启动应用后，自动的打开了系统内置的浏览器，解决这个问题需要为webview设置 WebViewClient，并重写方法：

?
1
2
3
4
5
6
7
webview.setWebViewClient(new WebViewClient(){
 @Override
 public boolean shouldOverrideUrlLoading(WebView view, String url) {
 view.loadUrl(url);
 return true;
 }
 });
若自己定义了一个页面加载进度的progressbar，需要展示给用户的时候，可以通过如下方式获取webview内页面的加载进度：

?
1
2
3
4
5
6
webview.setWebChromeClient(new WebChromeClient(){
 @Override
 public void onProgressChanged(WebView view, int newProgress) {
 //get the newProgress and refresh progress bar
 }
 });
每个页面，都有一个标题，比如www.baidu.com这个页面的title即“百度一下，你就知道”，那么如何知道当前webview正在加载的页面的title呢：

?
1
2
3
4
5
6
webview.setWebChromeClient(new WebChromeClient(){
 @Override
 public void onReceivedTitle(WebView view, String title) {
 titleview.setText(title);//a textview
 }
 });
二、通过webview控件下载文件

通常webview渲染的界面中含有可以下载文件的链接，点击该链接后，应该开始执行下载的操作并保存文件到本地中。webview来下载页面中的文件通常有两种方式：

1. 自己通过一个线程写java io的代码来下载和保存文件（可控性好）

2. 调用系统download的模块（代码简单）

 方法一：

首先要写一个下载并保存文件的线程类

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
public class HttpThread extends Thread {
 
 
 private String mUrl;
 
 public HttpThread(String mUrl) {
 this.mUrl = mUrl;
 }
  
 @Override
 public void run() {
 URL url;
 try {
 url = new URL(mUrl);
 HttpURLConnection conn = (HttpURLConnection) url.openConnection();
 conn.setDoInput(true);
 conn.setDoOutput(true);
 InputStream in = conn.getInputStream();
  
 File downloadFile;
 File sdFile;
 FileOutputStream out = null;
 if(Environment.getExternalStorageState().equals(Environment.MEDIA_UNMOUNTED)){
 downloadFile = Environment.getExternalStorageDirectory();
 sdFile = new File(downloadFile, "test.file");
 out = new FileOutputStream(sdFile);
 }
  
 //buffer 4k
 byte[] buffer = new byte[1024 * 4];
 int len = 0;
 while((len = in.read(buffer)) != -1){
 if(out != null)
 out.write(buffer, 0, len);
 }
  
 //close resource
 if(out != null)
 out.close();
  
 if(in != null){
 in.close();
 }
  
  
  
 } catch (IOException e) {
 // TODO Auto-generated catch block
 e.printStackTrace();
 }
 }
  
}
随后要实现一个DownloadListener接口，这个接口实现方法OnDownloadStart()，当用户点击一个可以下载的链接时，该回调方法被调用同时传进来该链接的URL，随后即可以对该URL塞入HttpThread进行下载操作：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
//创建DownloadListener (webkit包)
class MyDownloadListenter implements DownloadListener{
 
 @Override
 public void onDownloadStart(String url, String userAgent,
 String contentDisposition, String mimetype, long contentLength) {
 System.out.println("url ==== >" + url);
 new HttpThread(url).start();
 }
  
 }
 
//给webview加入监听
webview.setDownloadListener(new MyDownloadListenter());
方法二：

直接发送一个action_view的intent即可：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
class MyDownloadListenter implements DownloadListener{
 
 @Override
 public void onDownloadStart(String url, String userAgent,
 String contentDisposition, String mimetype, long contentLength) {
 System.out.println("url ==== >" + url);
 //new HttpThread(url).start();
  
 Uri uri = Uri.parse(url);
 Intent intent = new Intent(Intent.ACTION_VIEW, uri);
 startActivity(intent);
 }
  
 }
三、错误处理

当我们使用浏览器的时候，通常因为加载的页面的服务器的各种原因导致各种出错的情况，最平常的比如404错误，通常情况下浏览器会提示一个错误提示页面。事实上这个错误提示页面是浏览器在加载了本地的一个页面，用来提示用户目前已经出错了。

但是当我们的app里面使用webview控件的时候遇到了诸如404这类的错误的时候，若也显示浏览器里面的那种错误提示页面就显得很丑陋了，那么这个时候我们的app就需要加载一个本地的错误提示页面，即webview如何加载一个本地的页面。

1. 首先我们需要些一个html文件，比如error_handle.html，这个文件里面就是当出错的时候需要展示给用户看的一个错误提示页面，尽量做的精美一些。然后将该文件放置到代码根目录的assets文件夹下。

2. 随后我们需要复写WebViewClient的onRecievedError方法，该方法传回了错误码，根据错误类型可以进行不同的错误分类处理

?
1
2
3
4
5
6
7
8
9
10
11
12
13
webview.setWebViewClient(new WebViewClient(){
  
 @Override
 public void onReceivedError(WebView view, int errorCode,
 String description, String failingUrl) {
 switch(errorCode)
 {
 case HttpStatus.SC_NOT_FOUND:
 view.loadUrl("file:///android_assets/error_handle.html");
 break;
 }
 }
 });
其实，当出错的时候，我们可以选择隐藏掉webview，而显示native的错误处理控件，这个时候只需要在onReceivedError里面显示出错误处理的native控件同时隐藏掉webview即可。

四、webview同步cookies

cookies是服务器用来保存每个客户的常用信息的，下次客户进入一个诸如登陆的页面时服务器会检测cookie信息，如果通过则直接进入登陆后的页面。

在webview中，如果之前已经登陆过了，那么下次再进入同样的登陆界面时，若需要再次登陆的话，一定会很恼人，所以这里提供一个webview同步cookies的方法。 

1.首先，我们假设某个网站的登陆界面需要提供两个参数，一个是name，一个是pwd，那么要是对这个页面进行登陆，那么必须给与这两个信息。我们假设服务器已经注册了name为jason，pwd为123456这个账号。

2.下面，写一个Thread用来将name和pwd自动的登入，在服务器返回的response中获得cookie信息，稍后对这个cookie进行保存，这里先给出这个Thread的代码：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
public class HttpCookie extends Thread {
 
 private Handler mHandler;
 
 public HttpCookie(Handler mHandler) {
 this.mHandler = mHandler;
 }
  
 @Override
 public void run() {
 HttpClient client = new DefaultHttpClient();
 HttpPost post = new HttpPost("");//this place should add the login address
  
 List<NameValuePair> list = new ArrayList<NameValuePair>();
 list.add(new BasicNameValuePair("name", "jason"));
 list.add(new BasicNameValuePair("pwd", "123456"));
  
 try {
 post.setEntity(new UrlEncodedFormEntity(list));
 HttpResponse reponse = client.execute(post);
 if(reponse.getStatusLine().getStatusCode() == HttpStatus.SC_OK){
 AbstractHttpClient absClient = (AbstractHttpClient) client;
 List<Cookie> cookies = absClient.getCookieStore().getCookies();
  
 for(Cookie cookie:cookies){
 if(cookie != null){
 //TODO
 //this place would get the cookies
 }
 }
 }
  
 } catch (UnsupportedEncodingException e) {
 // TODO Auto-generated catch block
 e.printStackTrace();
 } catch (ClientProtocolException e) {
 // TODO Auto-generated catch block
 e.printStackTrace();
 } catch (IOException e) {
 // TODO Auto-generated catch block
 e.printStackTrace();
 }
 }
  
}
由于这是一个子线程，所以需要在主线程中创建并执行。

同时，因为其实子线程，那么里面必须含有一个handler的元素，用来当成功获取cookie后通知主线程进行同步和保存。初始化这个子线程的时候需要将主线程上的handler给传过来，随后在以上代码的TODO中发送消息，让主线程记录cookie，发送的这个消息需要将cookie信息包含进去：

?
1
2
3
4
5
6
7
8
9
10
if(cookie != null){
 //TODO
 //this place would get the cookies
 Message msg = new Message();
 msg.obj = cookie;
 if(mHandler != null){
 mHandler.sendMessage(msg);
 return;
 }
}
随后在主线程中（webview加载登陆界面前），在handler中将会获取到cookie信息，下面将对该cookie进行保存和同步：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
private Handler mHandler = new Handler(){
public void handleMessage(android.os.Message msg) 
{
 
CookieSyncManager.createInstance(MainActivity.this);
CookieManager cookieMgr = CookieManager.getInstance();
cookieMgr.setAcceptCookie(true);
cookieMgr.setCookie("", msg.obj.toString());// this place should add the login host address(not the login index address)
CookieSyncManager.getInstance().sync();
 
webview.loadUrl("");// login index address
};
};
这个时候发现webview加载的login index页面中可以自动的登陆了并显示登陆后的界面。 

五、 WebView与JavaScript的交互

1. webview调用js

mWebView.loadUrl("javascript:do()");
以上是webview在调用js中的一个叫做do的方法，该js所在的html文件大致如下：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
<html>
 <script language="javascript">
 /* This function is invoked by the webview*/
 function do() {
 alert("1");
 }
 </script>
 <body>
 <a onClick="window.demo.clickOnAndroid()"><div style="width:80px;
 margin:0px auto;
 padding:10px;
 text-align:center;
 border:2px solid #111111;" >
 <img id="droid" src="xx.png"/><br>
 Click me!
 </div></a>
 </body>
</html>
2. js调用webview

我们假设下列的本地类是要给js调用的：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
package com.test.webview;
class DemoJavaScriptInterface {
 
 DemoJavaScriptInterface() {
 }
 
 /**
 * This is not called on the UI thread. Post a runnable to invoke
 * loadUrl on the UI thread.
 */
 public void clickOnAndroid() {
 mHandler.post(new Runnable() {
 public void run() {
 //TODO
 }
 });
 
 }
 }
首先给webview设置：

mWebview.setJavaScriptEnabled(true);
随后将本地的类（被js调用的）映射出去：

mWebView.addJavascriptInterface(new DemoJavaScriptInterface(), "demo");
“demo”这个名字就是公布出去给JS调用的，那么js久可以直接用下列代码调用本地的DemoJavaScriptInterface类中的方法了：

<body onload="javascript:demo.clickOnAndroid()">  
    ...
</body> 

六、WebView与JavaScript相互调用混淆问题

若webview中的js调用了本地的方法，正常情况下发布的debug包js调用的时候是没有问题的，但是通常发布商业版本的apk都是要经过混淆的步骤，这个时候会发现之前调用正常的js却无法正常调用本地方法了。

这是因为混淆的时候已经把本地的代码的引用给打乱了，导致js中的代码找不到本地的方法的地址。

解决这个问题很简单，即在proguard.cfg文件中加上一些代码，声明本地中被js调用的代码不被混淆。下面举例说明：

第五节中被js调用的那个类DemoJavaScriptInterface的包名为com.test.webview，那么就要在proguard.cfg文件中加入：

-keep public class com.test.webview.DemoJavaScriptInterface{
    public <methods>;
}
若是内部类，则大致写成如下形式：

-keep public class com.test.webview.DemoJavaScriptInterface$InnerClass{
    public <methods>;
}
若android版本比较新，可能还需要添加上下列代码：

-keepattributes *Annotation*  
-keepattributes *JavascriptInterface*

另一种讲解：

一、基本用法
1、加载在线URL
void loadUrl(String url)  
这个函数主要加载url所对应的网页地址，或者用于调用网页中的指定的JS方法（调用js方法的用法，后面会讲）,但有一点必须注意的是：loadUrl()必须在主线程中执行！！！否则就会报错！！！。
注意：加载在线网页地址是会用到联网permission权限的，所以需要在AndroidManifest.xml中写入下面代码申请权限：
<uses-permission android:name="android.permission.INTERNET" />  
本示例效果为：



从效果图中可以明显看出本示例的布局： 
main.xml
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
<?xml version="1.0" encoding="utf-8"?> 
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
 android:orientation="vertical"
 android:layout_width="fill_parent"
 android:layout_height="fill_parent"
 > 
 <Button
 android:id="@+id/btn"
 android:layout_width="match_parent"
 android:layout_height="wrap_content"
 android:text="加载URL"/> 
  
 <WebView
 android:id="@+id/webview"
 android:layout_width="match_parent"
 android:layout_height="match_parent"/> 
</LinearLayout> 
对应的处理代码如下
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView)findViewById(R.id.webview); 
 mBtn = (Button)findViewById(R.id.btn); 
  
 mBtn.setOnClickListener(new View.OnClickListener() { 
 @Override
 public void onClick(View v) { 
 mWebView.loadUrl("http://www.baidu.com"); 
 } 
 }); 
 } 
} 
代码很简单，就是在点击按钮的时候加载网址，但需要注意的是：网址必须完整即以http://或者ftp://等协议开头，不能省略！不然将加载不出来，这是因为webview是没有自动补全协议功能的，所以如果我们不加，它将识别不出来网址类型，也就加载不出来了。 
但如果我们运行上面的代码，效果却是利用浏览器来打开网址，却不是使用webview打开网址：



如果我们想实现像示例一样在webview中打开网址需要怎么做呢？ 
我们需要设置WebViewClient： 
修改后的代码为：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView)findViewById(R.id.webview); 
 mBtn = (Button)findViewById(R.id.btn); 
  
 mWebView.setWebViewClient(new WebViewClient()); 
  
 mBtn.setOnClickListener(new View.OnClickListener() { 
 @Override
 public void onClick(View v) { 
 mWebView.loadUrl("http://www.baidu.com"); 
 } 
 }); 
 } 
} 
在上面的基础上，我们添加了下面一段代码：
mWebView.setWebViewClient(new WebViewClient());  
在这里我们利用mWebView.setWebViewClient()函数仅仅设置了一个WebViewClient实例，就可以实现在WebView中打开链接了，至于原因我们下篇会讲到，这里就先忽略了，大家只需要知道要在WebView中打开链接，就必须要设置WebViewClient; 
最终的效果图就与开篇时一样的了，这里就不再帖效果图了，下面我们来看看如何加载本地html网页。 
2、加载本地URL
一般而言，我们会将本地html文件放在assets文件夹下，比如：

web.html的内容为：
?
1
2
3
4
5
6
7
8
9
10
<!DOCTYPE html> 
<html lang="en"> 
<head> 
 <meta charset="UTF-8"> 
 <title>Title</title> 
 <h1>欢迎光临启舰的blog</h1> 
</head> 
<body> 
</body> 
</html> 
即大标题显示一段文字 
我们同样在上面的示例的基础上加以改造，在点击按钮的时候加载本地web.html文件
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView)findViewById(R.id.webview); 
 mBtn = (Button)findViewById(R.id.btn); 
  
 mBtn.setOnClickListener(new View.OnClickListener() { 
 @Override
 public void onClick(View v) { 
 mWebView.loadUrl("file:///android_asset/web.html"); 
 } 
 }); 
 } 
} 
从这里可以看到与加载在线URL有两点不同： 
1）、URL类型不一样 
在加载本地URL时，是以“file:///”开头的，而assets目录所对应的路径名为anroid_asset，写成其它的将识别不了，这是assets目录的以file开头的url形式的固定访问形式。 
2）、不需要设置WebViewClient 
这里很明显没有设置WebViewClient函数，但仍然是在webview中打开的本地文件。具体原因下篇文章讲到WebViewClient时我们会具体解释。 
本例效果图如下：



所以对于加载URL的总结就是： 
1、如果是在线网址记得添加网络访问权限 
2、在线网址中，如果要使用webview打开，记得设置WebViewClient 
3、打开本地html文件时，是不需要设置WebViewClient，对应的asstes目录的url为：file:///android_asset/xxxxx
3、WebView基本设置
如果我们需要设置WebView的属性，是通过WebView.getSettings()获取设置WebView的WebSettings对象,然后调用WebSettings中的方法来实现的。 
WebSettings的方法及说明如下：(这里先列出来所有的方法及解释，大家可以先忽略，看后面的举例中所使用的几个常用方法即可，用到哪个函数的时候再回来查查就可以了)
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
32
33
34
35
36
37
38
39
40
41
42
43
44
45
46
47
48
49
50
51
52
53
54
55
56
57
58
59
60
61
62
63
64
65
66
67
68
69
70
71
72
73
74
75
76
77
78
79
80
81
82
83
84
85
86
87
88
89
90
91
92
93
94
95
96
97
98
99
100
101
102
103
104
105
106
107
108
109
110
111
112
113
114
115
116
117
118
119
120
121
122
123
124
125
126
127
128
129
130
131
132
133
134
135
136
137
138
139
140
141
142
143
144
145
146
147
148
149
150
151
152
153
154
155
156
157
158
159
160
161
162
163
164
165
166
167
168
169
170
171
172
173
174
175
176
177
178
179
180
181
182
183
184
185
186
187
188
189
190
191
192
193
194
195
196
197
198
199
200
201
202
203
204
205
206
207
208
209
210
211
212
213
214
215
216
217
218
219
220
221
222
223
224
225
226
227
228
229
230
231
232
/** 
 * 是否支持缩放，配合方法setBuiltInZoomControls使用，默认true 
 */
setSupportZoom(boolean support) 
  
/** 
 * 是否需要用户手势来播放Media，默认true 
 */
setMediaPlaybackRequiresUserGesture(boolean require) 
  
/** 
 * 是否使用WebView内置的缩放组件，由浮动在窗口上的缩放控制和手势缩放控制组成，默认false 
 */
setBuiltInZoomControls(boolean enabled) 
  
/** 
 * 是否显示窗口悬浮的缩放控制，默认true 
 */
setDisplayZoomControls(boolean enabled) 
  
/** 
 * 是否允许访问WebView内部文件，默认true 
 */
setAllowFileAccess(boolean allow) 
  
/** 
 * 是否允许获取WebView的内容URL ，可以让WebView访问ContentPrivider存储的内容。 默认true 
 */
setAllowContentAccess(boolean allow) 
  
/** 
 * 是否启动概述模式浏览界面，当页面宽度超过WebView显示宽度时，缩小页面适应WebView。默认false 
 */
setLoadWithOverviewMode(boolean overview) 
  
/** 
 * 是否保存表单数据，默认false 
 */
setSaveFormData(boolean save) 
  
/** 
 * 设置页面文字缩放百分比，默认100% 
 */
setTextZoom(int textZoom) 
  
/** 
 * 是否支持ViewPort的meta tag属性，如果页面有ViewPort meta tag 指定的宽度，则使用meta tag指定的值，否则默认使用宽屏的视图窗口 
 */
setUseWideViewPort(boolean use) 
  
  
/** 
 * 是否支持多窗口，如果设置为true ，WebChromeClient#onCreateWindow方法必须被主程序实现，默认false 
 */
setSupportMultipleWindows(boolean support) 
  
/** 
 * 指定WebView的页面布局显示形式，调用该方法会引起页面重绘。默认LayoutAlgorithm#NARROW_COLUMNS 
 */
setLayoutAlgorithm(LayoutAlgorithm l) 
  
/** 
 * 设置标准的字体族，默认”sans-serif”。font-family 规定元素的字体系列。 
 * font-family 可以把多个字体名称作为一个“回退”系统来保存。如果浏览器不支持第一个字体， 
 * 则会尝试下一个。也就是说，font-family 属性的值是用于某个元素的字体族名称或/及类族名称的一个 
 * 优先表。浏览器会使用它可识别的第一个值。 
 */
setStandardFontFamily(String font) 
  
/** 
 * 设置混合字体族。默认”monospace” 
 */
setFixedFontFamily(String font) 
  
/** 
 * 设置SansSerif字体族。默认”sans-serif” 
 */
setSansSerifFontFamily(String font) 
  
/** 
 * 设置SerifFont字体族，默认”sans-serif” 
 */
setSerifFontFamily(String font) 
  
/** 
 * 设置CursiveFont字体族，默认”cursive” 
 */
setCursiveFontFamily(String font) 
  
/** 
 * 设置FantasyFont字体族，默认”fantasy” 
 */
setFantasyFontFamily(String font) 
  
/** 
 * 设置最小字体，默认8. 取值区间[1-72]，超过范围，使用其上限值。 
 */
setMinimumFontSize(int size) 
  
/** 
 * 设置最小逻辑字体，默认8. 取值区间[1-72]，超过范围，使用其上限值。 
 */
setMinimumLogicalFontSize(int size) 
  
/** 
 * 设置默认字体大小，默认16，取值区间[1-72]，超过范围，使用其上限值。 
 */
setDefaultFontSize(int size) 
  
/** 
 * 设置默认填充字体大小，默认16，取值区间[1-72]，超过范围，使用其上限值。 
 */
setDefaultFixedFontSize(int size) 
  
/** 
 * 设置是否加载图片资源，注意：方法控制所有的资源图片显示，包括嵌入的本地图片资源。 
 * 使用方法setBlockNetworkImage则只限制网络资源图片的显示。值设置为true后， 
 * webview会自动加载网络图片。默认true 
 */
setLoadsImagesAutomatically(boolean flag) 
  
/** 
 * 是否加载网络图片资源。注意如果getLoadsImagesAutomatically返回false，则该方法没有效果。 
 * 如果使用setBlockNetworkLoads设置为false，该方法设置为false，也不会显示网络图片。 
 * 当值从true改为false时。WebView会自动加载网络图片。 
 */
setBlockNetworkImage(boolean flag) 
  
/** 
 * 设置是否加载网络资源。注意如果值从true切换为false后，WebView不会自动加载， 
 * 除非调用WebView#reload().如果没有android.Manifest.permission#INTERNET权限， 
 * 值设为false，则会抛出java.lang.SecurityException异常。 
 * 默认值：有android.Manifest.permission#INTERNET权限时为false，其他为true。 
 */
setBlockNetworkLoads(boolean flag) 
  
/** 
 * 设置是否允许执行JS。 
 */
setJavaScriptEnabled(boolean flag) 
  
/** 
 * 是否允许Js访问任何来源的内容。包括访问file scheme的URLs。考虑到安全性， 
 * 限制Js访问范围默认禁用。注意：该方法只影响file scheme类型的资源，其他类型资源如图片类型的， 
 * 不会受到影响。ICE_CREAM_SANDWICH_MR1版本以及以下默认为true，JELLY_BEAN版本 
 * 以上默认为false 
 */
setAllowUniversalAccessFromFileURLs(boolean flag) 
  
  
/** 
 * 是否允许Js访问其他file scheme的URLs。包括访问file scheme的资源。考虑到安全性， 
 * 限制Js访问范围默认禁用。注意：该方法只影响file scheme类型的资源，其他类型资源如图片类型的， 
 * 不会受到影响。如果getAllowUniversalAccessFromFileURLs为true，则该方法被忽略。 
 * ICE_CREAM_SANDWICH_MR1版本以及以下默认为true，JELLY_BEAN版本以上默认为false 
 */
setAllowFileAccessFromFileURLs(boolean flag) 
  
/** 
 * 设置存储定位数据库的位置，考虑到位置权限和持久化Cache缓存，Application需要拥有指定路径的 
 * write权限 
 */
setGeolocationDatabasePath(String databasePath) 
  
/** 
 * 是否允许Cache，默认false。考虑需要存储缓存，应该为缓存指定存储路径setAppCachePath 
 */
setAppCacheEnabled(boolean flag) 
  
/** 
 * 设置Cache API缓存路径。为了保证可以访问Cache，Application需要拥有指定路径的write权限。 
 * 该方法应该只调用一次，多次调用自动忽略。 
 */
setAppCachePath(String appCachePath) 
  
/** 
 * 是否允许数据库存储。默认false。查看setDatabasePath API 如何正确设置数据库存储。 
 * 该设置拥有全局特性，同一进程所有WebView实例共用同一配置。注意：保证在同一进程的任一WebView 
 * 加载页面之前修改该属性，因为在这之后设置WebView可能会忽略该配置 
 */
setDatabaseEnabled(boolean flag) 
  
/** 
 * 是否存储页面DOM结构，默认false。 
 */
setDomStorageEnabled(boolean flag) 
  
/** 
 * 是否允许定位，默认true。注意：为了保证定位可以使用，要保证以下几点： 
 * Application 需要有android.Manifest.permission#ACCESS_COARSE_LOCATION的权限 
 * Application 需要实现WebChromeClient#onGeolocationPermissionsShowPrompt的回调， 
 * 接收Js定位请求访问地理位置的通知 
 */
setGeolocationEnabled(boolean flag) 
  
/** 
 * 是否允许JS自动打开窗口。默认false 
 */
setJavaScriptCanOpenWindowsAutomatically(boolean flag) 
  
/** 
 * 设置页面的编码格式，默认UTF-8 
 */
setDefaultTextEncodingName(String encoding) 
  
/** 
 * 设置WebView代理，默认使用默认值 
 */
setUserAgentString(String ua) 
  
/** 
 * 通知WebView是否需要设置一个节点获取焦点当 
 * WebView#requestFocus(int,android.graphics.Rect)被调用的时候，默认true 
 */
setNeedInitialFocus(boolean flag) 
  
/** 
 * 基于WebView导航的类型使用缓存：正常页面加载会加载缓存并按需判断内容是否需要重新验证。 
 * 如果是页面返回，页面内容不会重新加载，直接从缓存中恢复。setCacheMode允许客户端根据指定的模式来 
 * 使用缓存。 
 * LOAD_DEFAULT 默认加载方式 
 * LOAD_CACHE_ELSE_NETWORK 按网络情况使用缓存 
 * LOAD_NO_CACHE 不使用缓存 
 * LOAD_CACHE_ONLY 只使用缓存 
 */
setCacheMode(int mode) 
  
/** 
 * 设置加载不安全资源的WebView加载行为。KITKAT版本以及以下默认为MIXED_CONTENT_ALWAYS_ALLOW方 
 * 式，LOLLIPOP默认MIXED_CONTENT_NEVER_ALLOW。强烈建议：使用MIXED_CONTENT_NEVER_ALLOW 
 */
setMixedContentMode(int mode) 
下面我们就举个例子来看下用法 
示例1：在WebView中启用JavaScript：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
  
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView) findViewById(R.id.webview); 
 mBtn = (Button) findViewById(R.id.btn); 
  
 WebSettings webSettings = mWebView.getSettings(); 
 webSettings.setJavaScriptEnabled(true); 
 } 
} 
示例2：设置缓存
优先使用缓存
webView.getSettings().setCacheMode(WebSettings.LOAD_CACHE_ELSE_NETWORK);  
不使用缓存：
webView.getSettings().setCacheMode(WebSettings.LOAD_NO_CACHE);  
示例3：打开页面时，自适应屏幕：
?
1
2
3
WebSettings webSettings = mWebView .getSettings(); 
webSettings.setUseWideViewPort(true);//设置此属性，可任意比例缩放 
webSettings.setLoadWithOverviewMode(true); 
效果图如下：



示例4：使页面支持缩放：
?
1
2
3
4
5
6
7
WebSettings webSettings = mWebView.getSettings(); 
//开启javascript支持 
webSettings.setJavaScriptEnabled(true); 
// 设置可以支持缩放 
webSettings.setSupportZoom(true); 
// 设置出现缩放工具 
webSettings.setBuiltInZoomControls(true); 
示例5：.如果webView中需要用户手动输入用户名、密码或其他，则webview必须设置支持获取手势焦点
webview.requestFocusFromTouch();  
其它的设置就靠大家自己去尝试啦，这里就不再一一缀述了，大家只需要记着，如果需要设置webview就从WebSettings里面去找就可以啦。 
二、JS调用Java代码
在看了如何设置webview以后，我们来看下如何让Webview与网页中的JS代码交互的问题。
1、概述
更多时候，网页中需要通过JS代码来调用本地的Android代码，比如H5页面需要判断当前用户是否登录等。 
利用JS代码调用JAVA代码，主要是用到WebView下面的一个函数：
public void addJavascriptInterface(Object obj, String interfaceName)  
这个函数有两个参数：
--Object obj：interfaceName所绑定的对象
--String interfaceName：所绑定的对象所对应的名称
它有意义就是向WebView注入一个interfaceName的对象，这个对象绑定的是obj对象，通过interfaceName就可以调用obj对象中的方法，这个表述可能大家不太理解，因为interfaceName是一个String，怎么被你说成对象了，理解不了没关系，下面有具体示例
2、示例
下面同样是上面的示例，我们对它加以更改，效果图如下：


在原来html上面添加了一个按钮，当点击按钮时调用Android的Toast函数弹出一个toast消息。 
先看Android代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
  
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView) findViewById(R.id.webview); 
  
 WebSettings webSettings = mWebView.getSettings(); 
 webSettings.setJavaScriptEnabled(true); 
 mWebView.addJavascriptInterface(this, "android"); 
 mWebView.loadUrl("file:///android_asset/web.html"); 
 } 
  
 public void toastMessage(String message) { 
 Toast.makeText(getApplicationContext(), "通过Natvie传递的Toast:"+message, Toast.LENGTH_LONG).show(); 
 } 
} 
这里最主要是的下面这句：
mWebView.addJavascriptInterface(this, "android");  
这句的意思是把MyActivity对象注入到WebView中，在WebView中的对象别名叫android；另外，我们还在MyActivity中额外写了一个函数toastMessage(String message)，用于弹出MSG 
下面我们看看html代码：

?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
<!DOCTYPE html> 
<html lang="en"> 
<head> 
 <meta charset="UTF-8"> 
 <title>Title</title> 
 <h1>欢迎光临启舰的blog</h1> 
 <input type="button" value="js调native" onclick="ok()"> 
</head> 
<body> 
<script type="text/javascript"> 
function ok() { 
 android.toastMessage("哈哈,i m webview msg"); 
} 
</script> 
</body> 
</html> 
在这个html中，我添加了一个button按钮，当点击时调用ok函数：
?
1
2
3
function ok() { 
 android.toastMessage("哈哈,i m webview msg"); 
} 
它的意义就是调用android对象里的toastMessage方法，这个android就是我们利用mWebView.addJavascriptInterface(this, “android”)注入到WebView的android，它所对应的对象就将MyActivity；所以在JS中，我们就可以通过android这个别名来调用MyActivity对象中的任何public方法。 
3、addJavascriptInterface自定义作用对象
在上面的示例中mWebView.addJavascriptInterface(this, “android”);我们直接通过this，把当前整个类作为对象传给WebView了，但这会有很大风险，因为我们这个类中可能会有各种函数，而这些函数是只有本地Native代码才会用到，WebView是根本用不到的。所以如果通过全部注入给WebView的话，那么一些存心不良的同学就可以任意调用我们这个类中的方法，给我们APP带来危害。 
所以，一般而言，我们很少直接会传this，把整个类注入给WebView，而是单独写一个类专门用于JS交互，比如：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
  
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView) findViewById(R.id.webview); 
 mBtn = (Button) findViewById(R.id.btn); 
  
 WebSettings webSettings = mWebView.getSettings(); 
 webSettings.setJavaScriptEnabled(true); 
 mWebView.addJavascriptInterface(new JSBridge(), "android"); 
 mWebView.loadUrl("file:///android_asset/web.html"); 
 } 
  
 public class JSBridge{ 
 public void toastMessage(String message) { 
 Toast.makeText(getApplicationContext(), "通过Natvie传递的Toast:"+message, Toast.LENGTH_LONG).show(); 
 } 
 } 
} 
在这段代码中，在通过addJavascriptInterface注入时：

mWebView.addJavascriptInterface(new JSBridge(), "android");  
指定android对象绑定的是JSBridge对象！所以在WebView中，通过JS只能访问JSBridge中所定义的对象，如果访问其它类的函数，比如MyActivity中的函数，就会报下面的错误（即方法找不到）



大家可以自己尝试下； 
然后对应的html代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
<!DOCTYPE html> 
<html lang="en"> 
<head> 
 <meta charset="UTF-8"> 
 <title>Title</title> 
 <h1>欢迎光临启舰的blog</h1> 
 <input type="button" value="js调native" onclick="ok()"> 
</head> 
<body> 
<script type="text/javascript"> 
function ok() { 
 android.toastMessage("哈哈,i m webview msg"); 
 } 
</script> 
</body> 
</html> 
由于在注入时的对象别名和所调用的函数名都没有变，所以HTML中的JS代码是无需更改的。效果图与上节一样，就不再帖出了。 
源码在文章底部给出
4、addJavascriptInterface注入漏洞
上面我们说了在addJavascriptInterface注入时，为了防止WebView调用我们不想被它调用的函数，所以我们需要单独为WebView交互定义一个类，让它只执行这个类里面的函数 
但……这真的能挡住黑客的攻击吗？ 
当然是NO……，不然我就不会写这一段了……
mWebView.addJavascriptInterface(new JSBridge(), "android");  
在注入时，我们已经把对象传给了JS，在JS中当然可以通过反射得到APP中的各种类的实例！现在反编译Android代码可不是什么难事（本文结尾附jadx反编译方法），很容易拿到你有哪些类，有哪些函数，通过这些就可以想执行哪个执行哪个了，有没有细思极恐…… 
具体的细节我就不讲了，不在本篇范围，给大家找了篇文章，有兴趣的同学可以参考下：《Android WebView的Js对象注入漏洞解决方案》
5、JavascriptInterface注解
为了解决addJavascriptInterface()函数的安全问题，在android:targetSdkVersion数值为17（Android4.2）及以上的APP中，JS只能访问带有 @JavascriptInterface注解的Java函数，所以如果你的android:targetSdkVersion是17+，与JS交互的Native函数中，必须添加JavascriptInterface注解，不然无效，比如：
?
1
2
3
4
5
6
public class JSBridge { 
 @JavascriptInterface
 public void toastMessage(String message) { 
 Toast.makeText(getApplicationContext(), "通过Natvie传递的Toast:" + message, Toast.LENGTH_LONG).show(); 
 } 
} 
这也就是很多同学在高target上，addJavascriptInterface()函数无效的主要原因。
注意：虽然在target 17以后，已经修复了这个安全问题，但目前大多数APP都还是target 17以前的，所以大家可以尝试着找一些APP来演示下这个漏洞哦……
三、JAVA调用JS代码
1、JAVA调用JS代码
前面给大家演示了如何通过JS调用Java代码，这里就反过来看看，如何在Native中调用JS的代码 
本例的效果图如下：



在点击“加载URL”按钮时，调用webview中的JavaScript求和函数，将结果显示在标题中。 
先看html代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
<!DOCTYPE html> 
<html lang="en"> 
<head> 
 <meta charset="UTF-8"> 
 <title>Title</title> 
 <h1 id="h">欢迎光临启舰的blog</h1> 
 <input type="button" value="js调native" onclick="ok()"> 
</head> 
<body> 
<script type="text/javascript"> 
function sum(i,m) 
{ 
 document.getElementById("h").innerHTML= (i+m); 
} 
</script> 
</body> 
</html> 
在这里，我们写了一个求和函数sum(i,m) 
结果中就是把h1标签的内容改为求和后的结果值。 
再来看看JAVA的调用代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
  
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView) findViewById(R.id.webview); 
 mBtn = (Button) findViewById(R.id.btn); 
  
 WebSettings webSettings = mWebView.getSettings(); 
 webSettings.setJavaScriptEnabled(true); 
 mWebView.loadUrl("file:///android_asset/web.html"); 
  
 mBtn.setOnClickListener(new View.OnClickListener() { 
 @Override
 public void onClick(View v) { 
 mWebView.loadUrl("javascript:sum(3,8)"); 
 } 
 }); 
 } 
} 
在点击按钮时调用JS函数：
mWebView.loadUrl("javascript:sum(3,8)");  
这里也就是在JAVA中调用JS函数的方法：
String url = "javascript:methodName(params……);"  
webView.loadUrl(url);  
javascript:伪协议让我们可以通过一个链接来调用JavaScript函数 
中间methodName是JavaScript中实现的函数 
jsonParams是传入的参数列表 
使用起来难度不大，就不再多讲了 
源码在文章底部给出
2、JAVA中如何得到JS中的返回值(Android4.4以前)
现在我们再考虑一下，如果我们要在JAVA中需要得到JS的结果返回值要怎么办？比如在上面的例子中，我们需要在JAVA中得到在计算后的结果值 
Android在4.4之前并没有提供直接调用js函数并获取值的方法，也就是说，我们只能调用JS中的函数，并不能得到该函数的返回值，想得到返回值我们就得想其它办法，所以在此之前，常用的思路是 java调用js方法，js方法执行完毕，再次调用java代码将值返回。 
1）.Java调用js代码
?
1
2
webView.addJavascriptInterface(this, "android"); 
mWebView.loadUrl("javascript:sum(3,8)");
注意，这里通过addJavascriptInterface将MyActiviy所对应的对象注入到WebView中了。 
2）.js函数处理，并将结果通过调用java方法返回

?
1
2
3
4
5
function sum(i,m){ 
 var result = i+m; 
 document.getElementById("h").innerHTML= result; 
 android.onSumResult(result) 
} 
3）..Java在回调方法中获取js函数返回值

?
1
2
3
public void onSumResult(int result) { 
 Log.i(LOGTAG, "onSumResult result=" + result); 
} 
先看下效果图：



下面我们就完整地看一下代码： 
JS代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
<!DOCTYPE html> 
<html lang="en"> 
<head> 
 <meta charset="UTF-8"> 
 <title>Title</title> 
 <h1 id="h">欢迎光临启舰的blog</h1> 
 <input type="button" value="js调native" onclick="ok()"> 
</head> 
<body> 
<script type="text/javascript"> 
function sum(i,m){ 
 var result = i+m; 
 document.getElementById("h").innerHTML= result; 
 android.onSumResult(result); 
} 
</script> 
</body> 
</html> 
在function sum(i,m)中，先通过result得到结果，最后通过android.onSumResult(result);将结果传给Native 
然后再来看看JAVA代码：
?
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
public class MyActivity extends Activity { 
  
 private WebView mWebView; 
 private Button mBtn; 
  
 @Override
 public void onCreate(Bundle savedInstanceState) { 
 super.onCreate(savedInstanceState); 
 setContentView(R.layout.main); 
  
 mWebView = (WebView) findViewById(R.id.webview); 
 mBtn = (Button) findViewById(R.id.btn); 
  
 WebSettings webSettings = mWebView.getSettings(); 
 webSettings.setJavaScriptEnabled(true); 
 mWebView.addJavascriptInterface(this, "android"); 
 mWebView.loadUrl("file:///android_asset/web.html"); 
  
 mBtn.setOnClickListener(new View.OnClickListener() { 
  @Override
  public void onClick(View v) { 
  mWebView.loadUrl("javascript:sum(3,8)"); 
  } 
 }); 
 } 
  
 public void onSumResult(int result) { 
 Toast.makeText(this,"received result:"+result,Toast.LENGTH_SHORT).show(); 
 } 
} 
这里主要做了两件事： 
第一：通过addJavascriptInterface注入MyActivity对象，以便JS访问其中的函数
mWebView.addJavascriptInterface(this, "android");  
第二：供JS调用，以返回结果的函数onSumResult():
?
1
2
3
public void onSumResult(int result) { 
 Toast.makeText(this,"received result:"+result,Toast.LENGTH_SHORT).show(); 
}
  3、JAVA中如何得到JS中的返回值(Android4.4之后)
Android 4.4之后使用evaluateJavascript即可。这里展示一个简单的交互示例
先写一个具有返回值的js方法
?
1
2
3
function getGreetings() { 
 return 1; 
} 
java代码时用evaluateJavascript方法调用:
?
1
2
3
4
5
6
7
8
private void testEvaluateJavascript(WebView webView) { 
 webView.evaluateJavascript("getGreetings()", new ValueCallback() { 
 @Override
 public void onReceiveValue(String value) { 
  Log.i(LOGTAG, "onReceiveValue value=" + value); 
 } 
 }); 
} 
从上面的用法中很明显看到，通过evaluateJavascript调用JS中的方法，可以向其中添加结果回调，来接收JS的return值。 
注意：
上面限定了结果返回结果为String，对于简单的类型会尝试转换成字符串返回，对于复杂的数据类型，建议以字符串形式的json返回。
evaluateJavascript方法必须在UI线程（主线程）调用，因此onReceiveValue也执行在主线程。
好了，这篇文章中有关WebView的知识就先到这，下篇继续讲。下面给大家讲讲有关jadx-gui反编译的知识。
四、jadx-gui反编译
在遇到jadx-gui反编译之前，都是使用apktools进行反编译的，apktools有些是反编译不出来的，而且……难用…… 
想知道jadx-gui有多强？它都可以反编译淘宝、微信代码，是不是够强了。下面我们就来看看它是如何反编译的;
1）、下载、配置
jadx-gui是开源的，项目地址：《https://github.com/skylot/jadx》 
mac电脑： 
打开终端，切到某个路径下，输入以下命令：
?
1
2
3
git clone https://github.com/skylot/jadx.git 
cd jadx 
./gradlew dist 
其实这里只是做了两个动作：
第一，使用git命令将 项目clone下来（这里需要配置git环境，如果没有，请先搜资料配置git环境，然后再来）
然后，执行jadx目录 下gradlew脚本，这个是shell脚本
windows电脑：
?
1
2
3
git clone https://github.com/skylot/jadx.git 
cd jadx 
gradlew.bat dist
在windows电脑中，步骤与mac是一样的，只是最后一步中，已经不再是./gradlew所对应的shell脚本了，而是windows平台上的bat脚本。 
可见作者有多牛X，shell脚本和bat脚本都会写，真是屌的不能直视

整个编译过程是比较慢的，这里需要耐心等待下。因为目前会使用gradle 2.7来编译项目，如果没有在环境变量中环境gradle 2.7的环境变量，会自己下载gradle 2.7 
编译成功后会打出BUILD SUCCESS字样，如下图所示：



在编译成功后，在jadx目录下，会生成一个build目录，其中包含jadx目录和一个jadx-xxx-dev.zip的打包文件。在build/jadx目录下，就是源码编译出的jadx工具及所用jar包。jadx-xxx-dev.zip解压后的内容与build/jadx内容一样，只是将其打包了一下而已，方便移值，可见作者有多用心。build目录结构如下图所示：



2）、开始反编译
等完毕后,可以开始了,我就介绍个最简单最常用的用法 
（1）、把apk改成zip 
（2）、解压zip获取class.dex文件 
（3）、将class.dex文件放到jadx目录下

?
1
2
3
4
cd build/jadx/ 
bin/jadx -d out classes.dex # 反编译后放入out文件夹下(如果out不存在它会自动创建) 
#or 
bin/jadx-gui classes.dex # 会反编译,并且使用gui打开
目录结构图如下：



在使用jadx-gui反编译时，左下角会显示当前反编译的进度：



在反编译完成后，在左侧就可以看到目录结构和对应的代码，目录结构中显示的a,b,c,d这些字母是由于在生成apk时使用proguard混淆造成的，proguard混淆的类名是没办法反编译出对应的原类名的，这也是反编译代码中比较蛋疼的地方，下面给大家演示下结果：（还可以通过点击搜索按钮，搜索其中的代码）



有关jadx-gui工具的更多用法，只有靠大家自己去研究啦，到这里我们的源码就已经反编译出来了。 
反编译工具和微信的Classes文件，会在源码中给出大家，源码下载地址：Android WebView使用

原文地址：http://blog.csdn.net/lsyz0021/article/details/51473279
