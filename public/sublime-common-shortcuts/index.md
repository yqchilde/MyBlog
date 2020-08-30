# Sublime及Emmet插件快捷键

<!--more-->

<font size=4 color=#ea6f5a>Sublime常用快捷键</font>

整理人：yqchilde

### 选择类

-   Ctrl+D 选中光标所占的文本，继续操作则会选中下一个相同的文本。
-   Alt+F3 选中文本按下快捷键，即可一次性选择全部的相同文本进行同时编辑。举个栗子：快速选中并更改所有相同的变量名、函数名等。
-   Ctrl+L 选中整行，继续操作则继续选择下一行，效果和 Shift+↓ 效果一样。
-   Ctrl+Shift+L 先选中多行，再按下快捷键，会在每行行尾插入光标，即可同时编辑这些行。
-   Ctrl+Shift+M 选择括号内的内容（继续选择父括号）。举个栗子：快速选中删除函数中的代码，重写函数体代码或重写括号内里的内容。
-   Ctrl+M 光标移动至括号内结束或开始的位置。
-   Ctrl+Enter 在下一行插入新行。举个栗子：即使光标不在行尾，也能快速向下插入一行。
-   Ctrl+Shift+Enter 在上一行插入新行。举个栗子：即使光标不在行首，也能快速向上插入一行。
-   Ctrl+Shift+[ 选中代码，按下快捷键，折叠代码。
-   Ctrl+Shift+] 选中代码，按下快捷键，展开代码。
-   Ctrl+K+0 展开所有折叠代码。
-   Ctrl+← 向左单位性地移动光标，快速移动光标。
-   Ctrl+→ 向右单位性地移动光标，快速移动光标。
-   shift+↑ 向上选中多行。
-   shift+↓ 向下选中多行。
-   Shift+← 向左选中文本。
-   Shift+→ 向右选中文本。
-   Ctrl+Shift+← 向左单位性地选中文本。
-   Ctrl+Shift+→ 向右单位性地选中文本。
-   Ctrl+Shift+↑ 将光标所在行和上一行代码互换（将光标所在行插入到上一行之前）。
-   Ctrl+Shift+↓ 将光标所在行和下一行代码互换（将光标所在行插入到下一行之后）。
-   Ctrl+Alt+↑ 向上添加多行光标，可同时编辑多行。
-   Ctrl+Alt+↓ 向下添加多行光标，可同时编辑多行。

### 编辑类

-   Ctrl+J 合并选中的多行代码为一行。举个栗子：将多行格式的CSS属性合并为一行。
-   Ctrl+Shift+D  复制光标所在整行，插入到下一行。
-   Tab 向右缩进。
-   Shift+Tab 向左缩进。
-   Ctrl+K+K 从光标处开始删除代码至行尾。
-   Ctrl+Shift+K 删除整行。
-   Ctrl+/ 注释单行。
-   Ctrl+Shift+/ 注释多行。
-   Ctrl+K+U 转换大写。
-   Ctrl+K+L 转换小写。
-   Ctrl+Z 撤销。
-   Ctrl+Y 恢复撤销。
-   Ctrl+U 软撤销，感觉和 Gtrl+Z 一样。
-   Ctrl+F2 设置书签
-   Ctrl+T 左右字母互换。
-   F6 单词检测拼写

### 搜索类

-   Ctrl+F 打开底部搜索框，查找关键字。
-   Ctrl+shift+F 在文件夹内查找，与普通编辑器不同的地方是sublime允许添加多个文件夹进行查找，略高端，未研究。
-   Ctrl+P 打开搜索框。举个栗子：1、输入当前项目中的文件名，快速搜索文件，2、输入@和关键字，查找文件中函数名，3、输入：和数字，跳转到文件中该行代码，4、输入#和关键字，查找变量名。
-   Ctrl+G 打开搜索框，自动带：，输入数字跳转到该行代码。举个栗子：在页面代码比较长的文件中快速定位。
-   Ctrl+R 打开搜索框，自动带@，输入关键字，查找文件中的函数名。举个栗子：在函数较多的页面快速查找某个函数。
-   Ctrl+： 打开搜索框，自动带#，输入关键字，查找文件中的变量名、属性名等。
-   Ctrl+Shift+P 打开命令框。场景栗子：打开命名框，输入关键字，调用sublime
    text或插件的功能，例如使用package安装插件。
-   Esc 退出光标多行选择，退出搜索框，命令框等。

### 显示类

-   Ctrl+Tab 按文件浏览过的顺序，切换当前窗口的标签页。
-   Ctrl+PageDown 向左切换当前窗口的标签页。
-   Ctrl+PageUp 向右切换当前窗口的标签页。
-   Alt+Shift+1 窗口分屏，恢复默认1屏（非小键盘的数字）
-   Alt+Shift+2 左右分屏-2列
-   Alt+Shift+3 左右分屏-3列
-   Alt+Shift+4 左右分屏-4列
-   Alt+Shift+5 等分4屏
-   Alt+Shift+8 垂直分屏-2屏
-   Alt+Shift+9 垂直分屏-3屏
-   Ctrl+K+B 开启/关闭侧边栏。
-   F11 全屏模式
-   Shift+F11 免打扰模式

# Emmet插件快捷键

缩写:`!`

缩写:`html:5`

```html
<!doctype html>

<html lang="en">

<head>

<meta charset="UTF-8">

<title>Document</title>

</head>

<body>

</body>

</html>
```

### #(id) .(class) [href=#]

缩写：`p#info`

缩写：`p.info`

缩写：`a[href=’#’]`

```html
<p id="info"></p>

<p class="info"></p>

<a href="#"></a>
```

### 后代 >

缩写：`div#warp>ul>li*5`

```html
<div id="warp">

<ul>

<li></li>

<li></li>

<li></li>

<li></li>

<li></li>

</ul>

</div>
```

### 兄弟：+

缩写：`div#warp>p.one+span.two`

```html
<div id="warp">

<p class="one"></p>

<span class="two"></span>

</div>
```

### ^ 可以使该符号前的标签提升一行（换行符）

缩写 `p.class>span^div.info`

缩写 `ul>li*5^ol>li*6`

```html
<p class="class"><span></span></p>

<div class="info"></div>

<ul>

<li></li>

<li></li>

<li></li>

<li></li>

<li></li>

</ul>

<ol>

<li></li>

<li></li>

<li></li>

<li></li>

<li></li>

<li></li>

</ol>
```

### 分组 通过嵌套和括号快速生成一些代码块

缩写：`(div.foo>h1>p)+(div.bar>[h3]>p)`

```html
<div class="foo">

<h1>

<p></p>

</h1>

</div>

<div class="bar">

<h3>

<p></p>

</h3>

</div>
```

### 隐藏标签的快捷键

.itrm 表示 div.item

但是在不同的标签下面代表的情况不一样

li : 在ul或者ol中

tr : table

td : 用于tr中

option : 用于select

### 定义多个元素

缩写：`ul>li*3`

```html
<ul>

<li></li>

<li></li>

<li></li>

</ul>
```

缩写：`ul>li.item$*3`

```html
<ul>

<li class="item1"></li>

<li class="item2"></li>

<li class="item3"></li>

</ul>
```

### css 缩写

1.  `w100 => width:100px;`

2.  `m100 =>margin: 100px;`

3.  `h100 => height :100px;`

默认是 px 其他的单位需要表示

p 表示%

e 表示 em

x 表示 ex

缩写： @f   
缩写： @f

```css
@font-face {

font-family:;

src:url();

}
```

```css
@font-face {

font-family: 'FontName';

src: url('FileName.eot');

src: url('FileName.eot?#iefix') format('embedded-opentype'),

url('FileName.woff') format('woff'),

url('FileName.ttf') format('truetype'),

url('FileName.svg#FontName') format('svg');

font-style: normal;

font-weight: normal;

}
```

### 模糊匹配

缩写： `fz => font-size:; `
缩写： `fs => font-style: italic;`

### 供应商模式

w 表示 -webkit-

m 表示 -moz-

s 表示 -ms-

o 表示 -o-

### 渐变

缩写：`[lg](left,#fff 50%, #000)`

```css
background-image: -webkit-gradient(linear, 0 0, 100% 0, color-stop(0.5, #fff),
to(#000));

background-image: -webkit-linear-gradient(left, #fff 50%, #000);

background-image: -moz-linear-gradient(left, #fff 50%, #000);

background-image: -o-linear-gradient(left, #fff 50%, #000);

background-image: linear-gradient(left, #fff 50%, #000);
```

### 填充内容

//缩写：`h$[title=item$]{Header $}*3`

```html
<h1 title="item1">Header 1</h1>

<h2 title="item2">Header 2</h2>

<h3 title="item3">Header 3</h3>
```

//缩写：`ul>li.item$$${item $}*5`

```html
<ul>

<li class="item001">item 1</li>

<li class="item002">item 2</li>

<li class="item003">item 3</li>

<li class="item004">item 4</li>

<li class="item005">item 5</li>

</ul>
```

缩写：`ul>li.item$@-*5 //$@- 倒数`

```html
<ul>

<li class="item5"></li>

<li class="item4"></li>

<li class="item3"></li>

<li class="item2"></li>

<li class="item1"></li>

</ul>
```

缩写：`ul>li.item$@3*5 //$@3 从三开始`

```html
<ul>

<li class="item3"></li>

<li class="item4"></li>

<li class="item5"></li>

<li class="item6"></li>

<li class="item7"></li>

</ul>
```

缩写：`ul>li.item$@-3*5 //$@-3 倒数到三`

```html
<ul>

<li class="item7"></li>

<li class="item6"></li>

<li class="item5"></li>

<li class="item4"></li>

<li class="item3"></li>

</ul>
```

