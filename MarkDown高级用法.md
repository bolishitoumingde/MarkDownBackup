## 锚点链接

- Markdown 原始写法 `[名称](#id)`
- HTML 语法 `<a href="#id">名称</a>`

[方式一](#jump)

<a href="#jump">方式二</a>

















<span id="jump">跳转到的地方</span>

---

## 折叠

```html
<details>
<summary>展开</summary>
	这是展开后的内容
</details>
```

<details>
<summary>展开</summary>
	这是展开后的内容
</details>

---

## 待办事项

```
-空格[空格]空格待完成

-空格[x]空格已完成

-空格[空格]空格~~未完成~~
```

- [x] 完成
- [ ] 未完成

---

## 键盘输入

```
<kbd>ctrl</kbd> + <kbd>s</kbd>
```

<kbd>ctrl</kbd> +  <kbd>s</kbd>

---

## 高亮显示

```
<mark>高亮</mark> 
```

<mark>高亮</mark> 

---

## Emoji 表情的插入

- GitHub 支持的 Emoji 表情插入:
  [GitHub 链接](https://gist.github.com/rxaviers/7360908)

- Emoji Unicode Tables:

  [Emoji Unicode characters for use on the web (timwhitlock.info)](https://apps.timwhitlock.info/emoji/tables/unicode#block-4-enclosed-characters)

  示例: &#x1F602;

  使用方法: 

  ```
  &#x1F602;
  ```

- 最**简单粗暴**的方法:
  直接复制粘贴 Emoji 表情

---

## 公式

```
$内容$
$$空格
```

$$
f(x)=a+b
$$

---

## 流程图

```mermaid
graph LR
A[方形] -->B(圆角)
    B --> C{条件a}
    C -->|a=1| D[结果1]
    C -->|a=2| E[结果2]
    F[横向流程图]
```

```flow
st=>start: 开始框
op=>operation: 处理框
cond=>condition: 判断框(是或否?)
sub1=>subroutine: 子流程
io=>inputoutput: 输入输出框
e=>end: 结束框
st->op->cond
cond(yes)->io->e
cond(no)->sub1(right)->op
```

```sequence
对象A->对象B: 对象B你好吗?（请求）
Note right of 对象B: 对象B的描述
Note left of 对象A: 对象A的描述(提示)
对象B-->对象A: 我很好(响应)
对象A->对象B: 你真的好吗？
```

```sequence
Title: 标题：复杂使用
对象A->对象B: 对象B你好吗?（请求）
Note right of 对象B: 对象B的描述
Note left of 对象A: 对象A的描述(提示)
对象B-->对象A: 我很好(响应)
对象B->小三: 你好吗
小三-->>对象A: 对象B找我了
对象A->对象B: 你真的好吗？
Note over 小三,对象B: 我们是朋友
participant C
Note right of C: 没人陪我玩
```

```mermaid
%% 语法示例
        gantt
        dateFormat  YYYY-MM-DD
        title 软件开发甘特图
        section 设计
        需求                      :done,    des1, 2014-01-06,2014-01-08
        原型                      :active,  des2, 2014-01-09, 3d
        UI设计                     :         des3, after des2, 5d
    未来任务                     :         des4, after des3, 5d
        section 开发
        学习准备理解需求                      :crit, done, 2014-01-06,24h
        设计框架                             :crit, done, after des2, 2d
        开发                                 :crit, active, 3d
        未来任务                              :crit, 5d
        耍                                   :2d
        section 测试
        功能测试                              :active, a1, after des3, 3d
        压力测试                               :after a1  , 20h
        测试报告                               : 48h
```