# Sth u dont know

## HTML篇

## 浏览器地址栏运行JavaScript代码

```
	
javascript:alert('hello from address bar :)');

```
将以上代码贴到浏览器地址栏回车后alert正常执行，一个弹窗神现。


> 需要注意的是如果是通过copy paste代码到浏览器地址栏的话，IE及Chrome会自动去掉代码开头的javascript:，所以需要手动添加起来才能正确执行，而Firefox中虽然不会自动去掉，但它根本就不支持在地址栏运行JS代码，sigh~

## 浏览器地址栏运行HTML代码

比如在地址栏输入以下代码然后回车运行，会出现指定的页面内容

```
data:text/html,<h1>Hello, world!</h1>
```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/1.jpg)

## 可以把浏览器当编辑器

还是浏览器地址栏上做文章，将以下代码贴到地址栏运行后浏览器变成了一个原始而简单的编辑器，与Windows自带的notepad一样

```

data:text/html, <html contenteditable>
```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/2.gif)


归根结底多亏了HTML5中新加的 contenteditable 属性，当元素指定了该属性后，元素的内容成为可编辑状态。

推而广之，将以下代码放到console执行后，整个页面将变得可编辑，随意践踏吧~

```
document.body.contentEditable='true';

```
![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/3.gif)

## 利用a标签自动解析URL

很多时候我们有从一个URL中提取域名，查询关键字，变量参数值等的需要，而万万没想到可以让浏览器方便地帮我们完成这一任务而不用我们写正则去抓取。方法就在JS代码里先创建一个 a 标签然后将需要解析的URL赋值给 a 的 href 属性，然后就得到了一切我们想要的了。

```

var a = document.createElement('a');
 a.href = 'http://www.cnblogs.com/wayou/p/';
 console.log(a.host);

```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/4.jpg)


利用这一原理，稍微扩展一下，就得到了一个更加健壮的解析URL各部分的通用方法了。

```

function parseURL(url) {
    var a =  document.createElement('a');
    a.href = url;
    return {
        source: url,
        protocol: a.protocol.replace(':',''),
        host: a.hostname,
        port: a.port,
        query: a.search,
        params: (function(){
            var ret = {},
                seg = a.search.replace(/^\?/,'').split('&'),
                len = seg.length, i = 0, s;
            for (;i<len;i++) {
                if (!seg[i]) { continue; }
                s = seg[i].split('=');
                ret[s[0]] = s[1];
            }
            return ret;
        })(),
        file: (a.pathname.match(/\/([^\/?#]+)$/i) || [,''])[1],
        hash: a.hash.replace('#',''),
        path: a.pathname.replace(/^([^\/])/,'/$1'),
        relative: (a.href.match(/tps?:\/\/[^\/]+(.+)/) || [,''])[1],
        segments: a.pathname.replace(/^\//,'').split('/')
    };
}

```

## 页面拥有ID的元素会创建全局变量

```
<div id="sample"></div>
<script type="text/javascript">
        console.log(sample);
</script>

```


![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/5.jpg)


## 加载CDN文件时，可以省掉HTTP标识

现在很流行的CDN即从专门的服务器加载一些通用的JS和CSS文件，出于安全考虑有的CDN服务器使用HTTPS方式连接，而有的是传统的HTTP，其实我们在使用时可以忽略掉这个，将它从URL中省去。

```
	
<script src="//domain.com/path/to/script.js"></script>
```


## 利用script标签保存任意信息

将script标签设置为 type='text' 然后可以在里面保存任意信息，之后可以在JavaScript代码中很方便地获取。

```
<script type="text" id="template">
	<h1>This won't display</h1>
</script>
```

## CSS篇

## 关于CSS的恶作剧

```
*{
    cursor: none!important;
}

```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/6.jpg)


## 实时编辑CSS

通过设置style标签的 display:block 样式可以让页面的style标签显示出来，并且加上 contentEditable 属性后可以让样式成为可编辑状态，更改后的样式效果也是实时更新呈现的。此技巧在IE下无效。

```
<!DOCTYPE html>
<html>
    <body>
        <style style="display:block" contentEditable>
        	body { color: blue }
        </style>
    </body>
</html>

```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/7.gif)

## 创建长宽比固定的元素

通过设置父级窗口的 padding-bottom 可以达到让容器保持一定的长度比的目的，这在响应式页面设计中比较有用，能够保持元素不变形。

```
<div style="width: 100%; position: relative; padding-bottom: 20%;">
    <div style="position: absolute; left: 0; top: 0; right: 0; bottom: 0;background-color:yellow;">
        this content will have a constant aspect ratio that varies based on the width.
    </div>
</div>
```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/8.gif)


## JavaScript篇

## 生成随机字符串

利用 Math.random 和 toString 生成随机字符串，来自前一阵子看到的一篇博文。这里的技巧是利用了 toString 方法可以接收一个基数作为参数的原理，这个基数从2到36封顶。如果不指定，默认基数是10进制

```


function generateRandomAlphaNum(len) {
    var rdmString = "";
    for (; rdmString.length < len; rdmString += Math.random().toString(36).substr(2));
    return rdmString.substr(0, len);
}

```

![](https://github.com/DesperadoH/Articles-other/raw/master/imgs/sth u dont know/9.jpg)



## 整数的操作

JavaScript中是没有整型概念的，但利用好位操作符可以轻松处理，同时获得效率上的提升。

|0 和 ~~ 是很好的一个例子，使用这两者可以将浮点转成整型且效率方面要比同类的 parseInt , Math.round  要快。在处理像素及动画位移等效果的时候会很有用。性能比较见此。

```
var foo = (12.4 / 4.13) | 0;//结果为3
var bar = ~~(12.4 / 4.13);//结果为3

```

顺便说句， !! 将一个值方便快速转化为布尔值 !!window===true  。

## 重写原生浏览器方法以实现新功能

下载的代码通过重写浏览器的 alert 让它可以记录弹窗的次数。

```
(function() {
    var oldAlert = window.alert,
        count = 0;
    window.alert = function(a) {
        count++;
        oldAlert(a + "\n You've called alert " + count + " times now. Stop, it's evil!");
    };
})();
alert("Hello World");


```

## 万物皆对象

在JavaScript的世界，万物皆对象。除了 null 和 undefined ，其他基本类型数字，字符串和布尔值都有对应有包装对象。对象的一个特征是你可以在它身上直接调用方法。对于数字基本类型，当试图在其身上调用 toString 方法会失败，但用括号括起来后再调用就不会失败了，内部实现是用相应的包装对象将基本类型转为对象。所以 (1).toString() 相当于 new Number(1).toString() 。因此，你的确可以把基本类型数字，字符串，布尔等当对象使用的，只是注意语法要得体。

同时我们注意到，JavaScript中数字是不分浮点和整形的，所有数字其实均是浮点类型，只是把小数点省略了而以，比如你看到的1可以写成1.，这也就是为什么当你试图1.toString()时会报错，所以正确的写法应该是这样：1..toString()，或者如上面所述加上括号，这里括号的作用是纠正JS解析器，不要把1后面的点当成小数点。内部实现如上面所述，是将1.用包装对象转成对象再调用方法。

## If语句的变形

当你需要写一个if语句的时候，不妨尝试另一种更简便的方法，用JavaScript中的逻辑操作符来代替

```
var day=(new Date).getDay()===0;
//传统if语句
if (day) {
	alert('Today is Sunday!');
};
//运用逻辑与代替if
day&&alert('Today is Sunday!');

```
比如上面的代码，首先得到今天的日期，如果是星期天，则弹窗，否则什么也不做。我们知道逻辑操作存在短路的情况，对于逻辑与表达式，只有两者都真才结果才为真，如果前面的day变量被判断为假了，那么对于整个与表达式来说结果就是假，所以就不会继续去执行后面的 alert 了，如果前面day为真，则还要继续执行后面的代码来确定整个表达式的真假。利用这点达到了if的效果。

对于传统的if语句，如果执行体代码超过了1 条语句，则需要加花括号，而利用逗号表达式，可以执行任意条代码而不用加花括号。



```
	
if(conditoin) alert(1),alert(2),console.log(3);
```

上面if语句中，如果条件成立则执行三个操作，但我们不需要用花括号将这三句代码括起来。当然，这是不推荐的，这里是冷知识课堂:)


## 禁止别人以iframe加载你的页面

下面的代码已经不言自明了，没什么好多说的。

```

if (window.location != window.parent.location) window.parent.location = window.location;


```























