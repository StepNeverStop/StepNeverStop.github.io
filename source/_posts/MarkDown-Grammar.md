---
title:  "MarkDown基本语法"
date:   2018-12-29 15:23:57
tags: markdown
---

# MarkDown基本语法

所有使用Markdown语法标记的符号后要加一个空格`Space`

## 一、 标题	{{ '{#' }}...}

使用`#`来设置标题级数,一个`#`则代表一级标题,字体大小最大

`#` 

# 一级标题

`##`

## 二级标题

`###`

### 三级标题

`####`

#### 四级标题

`#####`

##### 五级标题

`######`

###### 六级标题 

## 二、 列表

### 1. 无序列表
使用`-`、`+`、`*`三个符号都可以

- 使用`-`

+ 使用`+`

* 使用`*`

如果在列表中想取消下一行的列表性质,需要按下退格`Backspace`删除列表前的圆点后,然后按`Shift`+`Tab`组合键来退回首位.

- 一级列表
  - 二级列表
    - 三级列表
      - 四级列表

共有三级标题

### 2. 有序列表

数字加点加空格,如`1.[Space]`、`2.[Space]`

需要往前挪动请按`Tab`键,往后挪动请按`Shift`+`Tab`组合键

1. 第一级
2. 第二级
   1. 第二级第一小节
   2. 第二级第二小节
      1. 第二级第二小节第一小小节
      2. 第二级第二小节第二小小节
   3. 第二级第三小节
3. 第三级

# 三、 字体

- *斜体*

  用法:`*[内容]*`或`_[内容]_`,包含在两个`*`星号或两个`_`下划线中间的内容会倾斜

  `*Hello World*`:*Hello World*

  `_Hello World_`:_Hello World_

- **加粗**

  用法:`**[内容]**`,包含在四个`*`星号中间的内容会加粗

  `**Hello World**`:**Hello World**

- ***斜体加粗***

  用法:`***[内容]***`,包含在六个`*`星号中间的内容会加粗并斜体

  `***Hello World***`:***Hello World***

- ~~删除线~~

  用法:`~~[内容]~~`,包含在四个`~`波浪号中间的内容会添加删除线

  `~~Hello World~~`:~~Hello World~~

# 四、 引用

`>`表示引用,与`#`用法相同

`>`

> 一级引用

`>>`

> > 二级引用

`>>>`

> > > 三级引用

退格使用`Shift`+`Tab`

# 五、 分割线

大于等于三个的`-`或`+`或`*`

`---`

---

`+++`

+++

`***`

***

# 六、 图片

语法:`![图片文字](图片地址 "鼠标放置时显示的信息")`

例子:

`![大桥](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1546072097132&di=8669a22f7be9af8266cb1580d15c155d&imgtype=0&src=http%3A%2F%2Fd.hiphotos.baidu.com%2Fimage%2Fpic%2Fitem%2F63d9f2d3572c11dfea8f528e6e2762d0f603c2c5.jpg "美丽的桥梁")`

![大桥](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1546072097132&di=8669a22f7be9af8266cb1580d15c155d&imgtype=0&src=http%3A%2F%2Fd.hiphotos.baidu.com%2Fimage%2Fpic%2Fitem%2F63d9f2d3572c11dfea8f528e6e2762d0f603c2c5.jpg "美丽的桥梁")

# 七、 超链接

语法:`[超链接名](超链接地址 "鼠标放置时显示的信息")`

例子:`[百度一下,你就知道](www.baidu.com "我就是百度")`

[百度一下,你就知道](www.baidu.com "我就是百度")

# 八、 代码

## 1. 单行

使用<code>&#96;</code>反引号包裹

## 2.多行,代码块

使用三个反引号包裹

<code>&#96;&#96;&#96;</code>

使用<code>&#96;&#96;&#96;</code>+编程语言可以打开代码编辑器

如 <code>&#96;&#96;&#96;</code>+python

```python
import sys
这是一个python语法的编译器
```

# 九、 表格

每一行都使用`|`隔开

第二行使用`:`设置对齐,两边都加表示文字居中,加在左边表示居左

```
|标题1|标题2|标题3|
|-|:-:|:-|
|1|2|3|
```

| 标题1 | 标题2 | 标题3 |
| ----- | :---: | :---- |
| 1     |   2   | 3     |





# 十、 流程图

```
​```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
​```
```

```flow
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
```



