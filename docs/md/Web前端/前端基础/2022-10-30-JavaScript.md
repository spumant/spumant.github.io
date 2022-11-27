# JS的调用方式与执行顺序
使用方式
HTML页面中的任意位置加上<script type="module"></script>标签即可。

常见使用方式有以下几种：

 - 直接在<script type="module"></script>标签内写JS代码。
 - 直接引入文件：<script type="module" src="/static/js/index.js"></script>。
 - 将所需的代码通过import关键字引入到当前作用域。

例如：

/static/js/index.js文件中的内容为：
```
let name = "acwing";

function print() {
    console.log("Hello World!");
}

export {
    name,
    print
}
```
<script type="module"></script>中的内容为：
```
<script type="module">
    import { name, print } from "/static/js/index.js";

    console.log(name);
    print();
</script>
```
执行顺序

 1. 类似于HTML与CSS，按从上到下的顺序执行；
 2. 事件驱动执行；

HTML, CSS, JavaScript三者之间的关系

 - CSS控制HTML
 - JavaScript控制HTML与CSS
 - 为了方便开发与维护，尽量按照上述顺序写代码。例如：不要在HTML中调用JavaScript中的函数。
# 变量与运算符
let与const
用来声明变量，作用范围为当前作用域。
 - let用来定义变量；
 - const用来定义常量；

例如：
```
let s = "acwing", x = 5;

let d = {
    name: "yxc",
    age: 18,
}

const n = 100;
```
变量类型

 - number：数值变量，例如1, 2.5
 - string：字符串，例如"acwing", 'yxc'，单引号与双引号均可。字符串中的每个字符为只读类型。
 - boolean：布尔值，例如true, false
 - object：对象，类似于C++中的指针，例如[1, 2, 3]，{name: "yxc", age: 18}，null
 - undefined：未定义的变量

类似于Python，JavaScript中的变量类型可以动态变化。

运算符
与C++、Python、Java类似，不同点：

 - **表示乘方
 - 等于与不等于用===和!==
# 输入与输出
输入
 - 从HTML与用户的交互中输入信息，例如通过input、textarea等标签获取用户的键盘输入，通过click、hover等事件获取用户的鼠标输入。
 - 通过Ajax与WebSocket从服务器端获取输入
 - 标准输入，参考AcWing 1. A + B

输出

 - 调试用console.log，会将信息输出到浏览器控制台
 - 改变当前页面的HTML与CSS
 - 通过Ajax与WebSocket将结果返回到服务器

格式化字符串

 - 字符串中填入数值：
```
let name = 'yxc', age = 18;
let s = `My name is ${name}, I'm ${age} years old.`;
```
定义多行字符串：
```
let s = 
`<div>
    <h2>标题</h2>
    <p>段落</p>
/div>`
```
保留两位小数如何输出
```
let x = 1.234567;
let s = `${x.toFixed(2)}`;
```
# 判断语句
JavaScript中的if-else语句与C++、Python、Java中类似。

例如：
```
let score = 90;
if (score >= 85) {
    console.log("A");
} else if (score >= 70) {
    console.log("B");
} else if (score >= 60) {
    console.log("C");
} else {
    console.log("D");
}
```
JavaScript中的逻辑运算符也与C++、Java中类似：

 - &&表示与
 - ||表示或
 - !表示非
# 循环语句
JavaScript中的循环语句与C++中类似，也包含for、while、do while循环。

for循环
```
for (let i = 0; i < 10; i++) {
    console.log(i);
}
```
枚举对象或数组时可以使用：

for-in循环，可以枚举数组中的下标，以及对象中的key
for-of循环，可以枚举数组中的值，以及对象中的value
while循环
```
let i = 0;
while (i < 10) {
    console.log(i);
    i++;
}
```
do while循环
do while语句与while语句非常相似。唯一的区别是，do while语句限制性循环体后检查条件。不管条件的值如何，我们都要至少执行一次循环。
```
let i = 0;
do {
    console.log(i);
    i++;
} while (i < 10);
```
英文名称：Object。
类似于C++中的map，由key:value对构成。

value可以是变量、数组、对象、函数等。
函数定义中的this用来引用该函数的“拥有者”。
例如：
```
let person = {
    name: "yxc",
    age: 18,
    money: 0,
    add_money: function (x) {
        this.money += x;
    }
}
```
对象属性与函数的调用方式：

 - person.name、person.add_money()
 - person["name"]、person["add_money"]()

# 数组
数组是一种特殊的对象。
类似于C++中的数组，但是数组中的元素类型可以不同。

数组中的元素可以是变量、数组、对象、函数。
例如：
```
let a = [1, 2, "a", "yxc"];

let b = [
    1,  // 变量
    "yxc",  // 变量
    ['a', 'b', 3],  // 数组
    function () {  // 函数
        console.log("Hello World");
    },
    { name: "yxc", age: 18 }  // 对象
];
```
访问数组中的元素
通过下标。

例如：
```
a[0] = 1; // 访问数组a[]的第0个元素
console.log(a[0]);
```
数组的常用属性和函数

 - 属性length：返回数组长度。注意length是属性，不是函数，因此调用的时候不要加()
 - 函数push()：向数组末尾添加元素
 - 函数pop()：删除数组末尾的元素
 - 函数splice(a, b)：删除从a开始的b个元素
 - 函数sort()：将整个数组从小到大排序
     - 自定义比较函数：array.sort(cmp)，函数cmp输入两个需要比较的元素，返回一个实数，负数表示第一个参数小于第二个参数，0表示相等，正数表示大于。
# 函数
函数是用对象来实现的。
函数也C++中的函数类似。

定义方式
```
function add(a, b) {
    return a + b;
}

let add = function (a, b) {
    return a + b;
}

let add = (a, b) => {
    return a + b;
}
```
返回值
如果未定义返回值，则返回undefined。

# 类
与C++中的Class类似。但是不存在私有成员。

 - this指向类的实例。

定义
```
class Point {
    constructor(x, y) {  // 构造函数
        this.x = x;  // 成员变量
        this.y = y;

        this.init();
    }

    init() {
        this.sum = this.x + this.y;  // 成员变量可以在任意的成员函数中定义
    }

    toString() {  // 成员函数
        return '(' + this.x + ', ' + this.y + ')';
    }
}

let p = new Point(3, 4);
console.log(p.toString());
```
继承
```
class ColorPoint extends Point {
    constructor(x, y, color) {
        super(x, y); // 这里的super表示父类的构造函数
        this.color = color;
    }

    toString() {
        return this.color + ' ' + super.toString(); // 调用父类的toString()
    }
}
```
注意：

 - super这个关键字，既可以当作函数使用，也可以当作对象使用。
   - 作为函数调用时，代表父类的构造函数，且只能用在子类的构造函数之中。
   - super作为对象时，指向父类的原型对象。
 - 在子类的构造函数中，只有调用super之后，才可以使用this关键字。
 - 成员重名时，子类的成员会覆盖父类的成员。类似于C++中的多态。

静态方法
在成员函数前添加static关键字即可。静态方法不会被类的实例继承，只能通过类来调用。例如：
```
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }

    static print_class_name() {
        console.log("Point");
    }
}

let p = new Point(1, 2);
Point.print_class_name();
p.print_class_name();  // 会报错
```
静态变量
在ES6中，只能通过class.propname定义和访问。例如：
```
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;

        Point.cnt++;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

Point.cnt = 0;

let p = new Point(1, 2);
let q = new Point(3, 4);

console.log(Point.cnt);
```
# 事件
JavaScript的代码一般通过事件触发。

可以通过addEventListener函数为元素绑定事件的触发函数。

常见的触发函数有：

鼠标

 - click：鼠标左键点击
 - dblclick：鼠标左键双击
 - contextmenu：鼠标右键点击
 - mousedown：鼠标按下，包括左键、滚轮、右键
 - event.button：0表示左键，1表示中键，2表示右键
 - mouseup：鼠标弹起，包括左键、滚轮、右键
 - event.button：0表示左键，1表示中键，2表示右键

键盘

 - keydown：某个键是否被按住，事件会连续触发
   - event.code：返回按的是哪个键
   - event.altKey、event.ctrlKey、event.shiftKey分别表示是否同时按下了alt、ctrl、shift键。
 - keyup：某个按键是否被释放
   - event常用属性同上
 - keypress：紧跟在keydown事件后触发，只有按下字符键时触发。适用于判定用户输入的字符。
   - event常用属性同上

keydown、keyup、keypress的关系类似于鼠标的mousedown、mouseup、click

表单

 - focus：聚焦某个元素
 - blur：取消聚焦某个元素
 - change：某个元素的内容发生了改变

窗口
需要作用到window元素上。

 - resize：当窗口大小放生变化
 - scroll：滚动指定的元素
 - load：当元素被加载完成

# 常用库
## jQuery
使用方式
在<head>元素中添加：
```
<script src="https://cdn.acwing.com/static/jquery/js/jquery-3.3.1.min.js"></script>
```
按jQuery官网提示下载
选择器
$(selector)，例如：
```
$('div');
$('.big-div');
$('div > p')
```
selector类似于CSS选择器。

事件
$(selector).on(event, func)绑定事件，例如：
```
$('div').on('click', function (e) {
    console.log("click div");
})
```
$(selector).off(event, func)删除事件，例如：
```
$('div').on('click', function (e) {
    console.log("click div");

    $('div').off('click');
});
```
当存在多个相同类型的事件触发函数时，可以通过click.name来区分，例如：
```
$('div').on('click.first', function (e) {
    console.log("click div");

    $('div').off('click.first');
});
```
在事件触发的函数中的return false等价于同时执行：

 - e.stopPropagation()：阻止事件向上传递
 - e.preventDefault()：阻止事件的默认行为

元素的隐藏、展现

 - $A.hide()：隐藏，可以添加参数，表示消失时间
 - $A.show()：展现，可以添加参数，表示出现时间
 - $A.fadeOut()：慢慢消失，可以添加参数，表示消失时间
 - $A.fadeIn()：慢慢出现，可以添加参数，表示出现时间

元素的添加、删除

 - $('<div class="mydiv"><span>Hello World</span></div>')：构造一个jQuery对象
 - $A.append($B)：将$B添加到$A的末尾
 - $A.prepend($B)：将$B添加到$A的开头
 - $A.remove()：删除元素$A
 - $A.empty()：清空元素$A的所有儿子

对类的操作

 - $A.addClass(class_name)：添加某个类
 - $A.removeClass(class_name)：删除某个类
 - $A.hasClass(class_name)：判断某个类是否存在

对CSS的操作

 - $("div").css("background-color")：获取某个CSS的属性
 - $("div").css("background-color","yellow")：设置某个CSS的属性
 - 同时设置多个CSS的属性：
```
$('div').css({
    width: "200px",
    height: "200px",
    "background-color": "orange",
});
```
对标签属性的操作
 - $('div').attr('id')：获取属性
 - $('div').attr('id', 'ID')：设置属性

对HTML内容、文本的操作
不需要背每个标签该用哪种，用到的时候Google或者百度即可。

 - $A.html()：获取、修改HTML内容
 - $A.text()：获取、修改文本信息
 - $A.val()：获取、修改文本的值

查找

 - $(selector).parent(filter)：查找父元素
 - $(selector).parents(filter)：查找所有祖先元素
 - $(selector).children(filter)：在所有子元素中查找
 - $(selector).find(filter)：在所有后代元素中查找

**ajax**
GET方法：
```
$.ajax({
    url: url,
    type: "GET",
    data: {
    },
    dataType: "json",
    success: function (resp) {

    },
});
```
POST方法：
```
$.ajax({
    url: url,
    type: "POST",
    data: {
    },
    dataType: "json",
    success: function (resp) {

    },
});
```
 ## setTimeout与setInterval
setTimeout(func, delay)
delay毫秒后，执行函数func()。

clearTimeout()
关闭定时器，例如：
```
let timeout_id = setTimeout(() => {
    console.log("Hello World!")
}, 2000);  // 2秒后在控制台输出"Hello World"

clearTimeout(timeout_id);  // 清除定时器
```
setInterval(func, delay)
每隔delay毫秒，执行一次函数func()。
第一次在第delay毫秒后执行。

clearInterval()
关闭周期执行的函数，例如：
```
let interval_id = setInterval(() => {
    console.log("Hello World!")
}, 2000);  // 每隔2秒，输出一次"Hello World"

clearInterval(interval_id);  // 清除周期执行的函数
```
## requestAnimationFrame
requestAnimationFrame(func)
该函数会在下次浏览器刷新页面之前执行一次，通常会用递归写法使其每秒执行60次func函数。调用时会传入一个参数，表示函数执行的时间戳，单位为毫秒。

例如：
```
let step = (timestamp) => {  // 每帧将div的宽度增加1像素
    let div = document.querySelector('div');
    div.style.width = div.clientWidth + 1 + 'px';
    requestAnimationFrame(step);
};
```
requestAnimationFrame(step);
与setTimeout和setInterval的区别：

 - requestAnimationFrame渲染动画的效果更好，性能更加。
   该函数可以保证每两次调用之间的时间间隔相同，但setTimeout与setInterval不能保证这点。setTmeout两次调用之间的间隔包含回调函数的执行时间；setInterval只能保证按固定时间间隔将回调函数压入栈中，但具体的执行时间间隔仍然受回调函数的执行时间影响。
 - 当页面在后台时，因为页面不再渲染，因此requestAnimationFrame不再执行。但setTimeout与setInterval函数会继续执行。
## Map与Set
Map
Map 对象保存键值对。
 - 用for...of或者forEach可以按插入顺序遍历。
 - 键值可以为任意值，包括函数、对象或任意基本类型。

常用API：

 - set(key, value)：插入键值对，如果key已存在，则会覆盖原有的value
 - get(key)：查找关键字，如果不存在，返回undefined
 - size：返回键值对数量
 - has(key)：返回是否包含关键字key
 - delete(key)：删除关键字key
 - clear()：删除所有元素

Set
Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

 - 用for...of或者forEach可以按插入顺序遍历。

常用API：

 - add()：添加元素
 - has()：返回是否包含某个元素
 - size：返回元素数量
 - delete()：删除某个元素
 - clear()：删除所有元素
## localStorage
可以在用户的浏览器上存储键值对。

常用API：

 - setItem(key, value)：插入
 - getItem(key)：查找
 - removeItem(key)：删除
 - clear()：清空
## JSON
JSON对象用于序列化对象、数组、数值、字符串、布尔值和null。

常用API：

 - JSON.parse()：将字符串解析成对象
 - JSON.stringify()：将对象转化为字符串
## 日期
返回值为整数的API，数值为1970-1-1 00:00:00 UTC（世界标准时间）到某个时刻所经过的毫秒数：
 - Date.now()：返回现在时刻。
 - Date.parse("2022-04-15T15:30:00.000+08:00")：返回北京时间2022年4月15日
   15:30:00的时刻。

与Date对象的实例相关的API：

 - new Date()：返回现在时刻。
 - new Date("2022-04-15T15:30:00.000+08:00")：返回北京时间2022年4月15日
   15:30:00的时刻。
 - 两个Date对象实例的差值为毫秒数
 - getDay()：返回星期，0表示星期日，1-6表示星期一至星期六
 - getDate()：返回日，数值为1-31
 - getMonth()：返回月，数值为0-11
 - getFullYear()：返回年份
 - getHours()：返回小时
 - getMinutes()：返回分钟
 - getSeconds()：返回秒
 - getMilliseconds()：返回毫秒
## WebSocket
与服务器建立全双工连接。

常用API：

 - new WebSocket('ws://localhost:8080');：建立ws连接。
 - send()：向服务器端发送一个字符串。一般用JSON将传入的对象序列化为字符串。
 - onopen：类似于onclick，当连接建立时触发。
 - onmessage：当从服务器端接收到消息时触发。
 - close()：关闭连接。
## window
 - window.open("https://www.acwing.com")在新标签栏中打开页面。
 - location.reload()刷新页面。
 - location.href = "https://www.acwing.com"：在当前标签栏中打开页面。
 - onclose：当连接关闭后触发。
## canvas
[canvas教程][1]


  [1]: https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial