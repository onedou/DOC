#JS 替换所有的空格

在JS中替换掉输入框内的空格，是在处理表单需求的时候极为常用的一项操作，以防止用户的操作习惯引起数据异常，保证传参的安全性。

>NO.1

	name.replace(" ","");

上述方法是很简单的替换，但是有两个弱点：

1.只能替换单个英文空格或者中文空格（全角）；  
2.只能替换当前字符串的第一个匹配项。  

>NO.2

	name.replace(new RegExp(/( )/g),"");

上述方法是通过正则匹配，能够进行全部替换，但是还是有一个弱点：

1.只能替换英文空格或者中文空格（全角）中的一种。 

>NO.3

	name.split(" ").join("");

上述方法是通过字符分隔再合并，能够进行全部替换，但是还是有一个弱点：

1.只能替换英文空格或者中文空格（全角）中的一种。  

>NO.4

	name.replace(/(^\s*)|(\s*$)/g,"");

上述方法是通过正则匹配，能够替换英文或者中文空格，但是有一个弱点：

1.只能替换首尾的空格，对字符串中间的空格不起作用。

>终极杀招

	name.replace(/\s+/g,"");

上述方法是通过正则匹配，能够替换英文或者中文空格，并进行全部替换。

#####【注意】JS中并没有所谓的replaceAll方法，经笔者测试结果“undefined”，页面上无法识别的。当然也有一种可迂回的方案，那就是根据replace的功能进行replaceAll方法原型重写：

	String.prototype.replaceAll = function(reallyDo, replaceWith, ignoreCase) {  
	    if (!RegExp.prototype.isPrototypeOf(reallyDo)) {  
	        return this.replace(new RegExp(reallyDo, (ignoreCase ? "gi": "g")), replaceWith);  
	    } else {  
	        return this.replace(reallyDo, replaceWith);  
	    }  
	}  