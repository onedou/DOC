#PHP使用ob_flush不能每隔一秒输出原理分析

本文实例讲述了php使用ob_flush不能每隔一秒输出原理。分享给大家供大家参考。具体分析如下：

##实现功能：

浏览器每隔一秒输出一个数字。  
php.ini配置为：  
**版本5.3**

	implicit_flush = off
	output_buffering = off

另：查看output_buffering是否打开，可以：

	var_dump(ini_get('output_buffering'));

好我们再来看看这段代码：

	<?php
	  $i = 3;
	  ob_start();
	  while ($i--) {
	    echo $i, "<br />";
	    ob_flush();
	    flush();
	    sleep(1);
	  }
	  ob_end_clean();
	?>

可为什么：这段代码不能每隔一秒输出呢？？

**原因分析：**

apache运行原理：当你访问一个地址（发送请求）后，apache启动PHP,那么php执行是页面级的，即如果有可执行的代码：它全部执行完后再丢给apache，apache再丢给browser显示结果  

**如何实现？**

如果是cli 显示结果方式又不一样,那里不一样呢？

	linux cmd:
	php5 test.php

由php直接执行，不经过apache，web service，就可以实现：

	<?php
	  $i = 3;
	  while ($i--) {
	    echo $i, "\n";
	    sleep(1);
	  }
	  ob_end_clean();
	?>

希望本文所述对大家的php程序设计有所帮助。
