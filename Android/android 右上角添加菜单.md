source: http://blog.csdn.net/tingyuanss/article/details/45840713

一般如果不把标题栏隐藏(默认是显示的), UI的右上角会有一个默认菜单settings,并没起什么作用。

顺便说一下隐藏标题栏的三种做法：

1.在代码里实现

[java] view plaincopy
this.requestWindowFeature(Window.FEATURE_NO_TITLE);//去掉标题栏  
记住：这句代码要写在setContentView()前面。
2.在清单文件（manifest.xml）里面实现

[java] view plaincopy
<application android:icon="@drawable/icon"   
        android:label="@string/app_name"   
        android:theme="@android:style/Theme.NoTitleBar">  
这样用可以将整个应用设置成无标题栏，如果只需要在一个Activity设置成一个无标题栏的形式，只要把上面的第三行代码写到某一个Activity里面就可以了。
3.在style.xml文件里定义

[html] view plaincopy
<?xml version="1.0" encoding="UTF-8" ?>  
<resources>  
    <style name="notitle">  
        <item name="android:windowNoTitle">true</item>  
    </style>   
</resources>  
然后面manifest.xml中引用就可以了，这种方法稍麻烦了些。
[html] view plaincopy
<application android:icon="@drawable/icon"   
        android:label="@string/app_name"   
        android:theme="@style/notitle">  
跑题了，我只是想做个笔记，哈哈。

回归正题：

添加菜单也有两种，在xml文件里和在代码里添加。

1.xml形式，首先在res/menu目录下新建一个自定义的菜单info.xml,名字随便:

[html] view plain copy
<menu xmlns:android="http://schemas.android.com/apk/res/android" >  
  
    <item  
        android:id="@+id/quit1"  
        android:orderInCategory="100"  
        android:showAsAction="never"  
        android:title="Back"/>  
  
</menu>  
     
关于自定义菜单，这里只是最简单的形式，还有subitem, groupitem等。
在MainActivity.java文件里：

[java] view plain copy
@Override    
   public boolean onCreateOptionsMenu(Menu menu) {    
  
     getMenuInflater().inflate(R.menu.info, menu);   
  
     //menu.add(1, Menu.FIRST, 1, "Change Site ID");  
     return true;  
}  
  
@Override    
   public boolean onOptionsItemSelected(MenuItem item) {    
           
       switch(item.getItemId()){    
       case R.id.quit1:    
        super.finish();  
        System.exit(0);  
        return true;  
       default:    
           return false;    
       }       
   }    
2.在代码里添加菜单，一般实现动态添加菜单
在MainActivity.java文件里：

[java] view plain copy
@Override    
    public boolean onCreateOptionsMenu(Menu menu) {    
  
         getMenuInflater().inflate(R.menu.info, menu);  //初始化加载菜单项  
  
                  menu.add(1, Menu.FIRST, 1, "Change Site ID");   //四个参数，groupid, itemid, orderid, title  
         return true;  
    }  
  
    @Override    
    public boolean onOptionsItemSelected(MenuItem item) {    
            
        switch(item.getItemId()){    
        case 1:            //Menu.FIRST对应itemid为1  
            super.finish();  
            System.exit(0);  
            return true;  
        default:    
            return false;    
        }       
    }    
