### 前言

曾经，我接手了一份大佬的代码，里面充满了各种“骚操作”，还不加注释那种，短短几行的函数花了很久才弄懂。

![](https://pic3.zhimg.com/v2-9b11384132f3b33599f7a2e205739889_b.jpeg)

这世上，“只有魔法才能对抗魔法”，于是后来，翻阅各种“黑魔法”的秘籍，总结了一些比较实用的“骚操作”，让我们装X的同时，提升代码运行的效率（请配合健身房一起使用）。

![](https://pic3.zhimg.com/v2-eb0d8ca2b1697278d67635cacd0de19f_b.jpeg)

---

### 位运算

JavaScript 中最臭名昭著的 Bug 就是 `0.1 + 0.2 !== 0.3`，因为精度的问题，导致所有的浮点运算都是不安全的，具体原因可详见《0.1 + 0.2不等于0.3？为什么JavaScript有这种“骚”操作？》。

因此，之前有大牛提出，不要在 JS 中使用位运算：

> Javascript 完全套用了 Java 的位运算符，包括按位与`&`、按位或`|`、按位异或`^`、按位非`~`、左移`<<`、带符号的右移`>>`和用`0`补足的右移`>>>`。这套运算符针对的是整数，所以对 JavaScript 完全无用，因为 JavaScript 内部，所有数字都保存为双精度浮点数。如果使用它们的话，JavaScript 不得不将运算数先转为整数，然后再进行运算，这样就降低了速度。而且"按位与运算符"`&`同"逻辑与运算符"`&&`，很容易混淆。

但是在我看来，如果对 JS 的运用达到炉火纯青的地步，能避开各种“Feature”的话，偶尔用一下位运算符也无所谓，还能提升运算性能，毕竟直接操作的是计算机最熟悉的二进制。

位运算的原理可以参考这篇文章 《位运算符在JS中的妙用》

### 1\. 使用左移运算符 `<<` 迅速得出2的次方

```js
    1 << 2  // 4, 即 2的2次方
    1 << 10// 1024, 即 2的10次方

    // 但是要注意使用场景
    a = 2e9;   // 2000000000
    a << 1;    // -294967296
```

### 2\. 使用 `^` 切换变量 0 或 1

```js
    // --- before ---
    // if 判断
    if (toggle) {
        toggle = 0;
    } else {
        toggle = 1;
    }
    // 三目运算符
    togle = toggle ? 0 : 1;

    // --- after ---
    toggle ^= 1;
```

### 3\. 使用 `&` 判断奇偶性

偶数 \& 1 = 0

奇数 \& 1 = 1

```js
    console.log(7 & 1);    // 1
    console.log(8 & 1) ;   // 0
```

### 4\. 使用 `!!` 将数字转为布尔值

所有`非0`的值都是`true`，包括负数、浮点数：

```js
    console.log(!!7);       // true
    console.log(!!0);       // false
    console.log(!!-1);      // true
    console.log(!!0.71);    // true
```

### 5\. 使用`~`、`>>`、`<<`、`>>>`、`|`来取整

相当于使用了 Math.floor\(\)

```js
    console.log(~~11.71)     // 11
    console.log(11.71 >> 0)  // 11
    console.log(11.71 << 0)  // 11
    console.log(11.71 | 0)   // 11
    console.log(11.71 >>> 0) // 11
```

> 注意 >>> 不可对负数取整

  

![](https://picb.zhimg.com/v2-3a845097c1f48138db8f55ab20752606_b.jpeg)

  

### 6\. 使用`^`来完成值交换

这个符号的用法前面提到过，下面介绍一些高级的用法，在 ES6 的解构赋值出来之前，用这种方式会更快\(但必须是整数\)：

```js
    // --- before ---
    let temp = a; a = b; b = temp; // 传统，但需要借助临时变量
    b = [a, a = b][0] // 借助数组

    // --- after ---
    let a = 7
    let b = 1
    a ^= b
    b ^= a
    a ^= b
    console.log(a)   // 1
    console.log(b)   // 7

    [a, b] = [b, a]; // ES6，解构赋值
```

### 7\. 使用`^`判断符号是否相同

```js
    (a ^ b) >= 0; //  true 相同; false 不相同
```

![](https://pic3.zhimg.com/v2-930029040feefdd19c1f61fda6a7c639_b.jpeg)

### 8\. 使用`^`来检查数字是否不相等

```js
    // --- before ---
    if (a !== 1171) {...};

    // --- after ---
    if (a ^ 1171) {...};
```

![](https://pic3.zhimg.com/v2-2c35db8fc5dfe0b139fb178f258f11c3_b.jpeg)

### 9\. `n & (n - 1)`，如果为 0，说明 n 是 2 的整数幂

![](https://pic1.zhimg.com/v2-1f7f2e22c7e2771abbb6b4cb11939837_b.jpeg)

### 10\. 使用 `A + 0.5 | 0` 来替代 Math.round\(\)

  

![](https://pic4.zhimg.com/v2-5dcbf01f157ce815dfde78c15eb77bf3_b.jpeg)

  

如果是负数，只需要`-0.5`

  

![](https://pic1.zhimg.com/v2-69816654eb23667699f08473782d668c_b.jpeg)

  

### String

### 1\. 使用`toString(16)`取随机字符串

```js
    Math.random().toString(16).substring(2, 15);
```

> .substring\(\) 的第二个参数控制取多少位 \(最多可取13位\)

![](https://pic3.zhimg.com/v2-de6d862038af9a7c5cb3a0247051552e_b.jpeg)

### 2\. 使用 split\(0\)

使用数字来做为 split 的分隔条件可以节省2字节

```js
    // --- before ---
    "alpha,bravo,charlie".split(",");

    // --- after ---
    "alpha0bravo0charlie".split(0);
```

### 3\. 使用`.link()` 创建链接

一个鲜为人知的方法，可以快速创建 a 标签

```js
    // --- before ---
    let b = `<a herf="www.google.com">google</a>`;

    // --- after ---
    let b =  google .link( www.google.com );
```

![](https://pic4.zhimg.com/v2-4d10f1322b2c4a2631e0d35741caa909_b.jpeg)

### 3\. 使用 `Array` 来重复字符

```js
    // --- before ---
    for (let a = "", i = 7; i--;) a+= 0;

    // --- after ---
    let b = Array(7).join(0); // "0000000"let c = Array(7).join( La ) // "LaLaLaLaLaLa"

    // ES6
    let d = "0".repeat(7); // "0000000"
```

### 其他一些花里胡哨的操作

### 1\. 使用当前时间创建一个随机数

```js
    // --- before ---
    let b = 0 | Math.random() * 100

    // --- after ---
    let a;
    a = newDate % 100; // 两位随机数
    a = newDate % 1000; // 三位随机数
    a = newDate % 10000; // 四位随机数...依次类推
    // 不要在快速循环中使用，因为毫秒可能没有变化；
```

### 2\. 一些可以替代 `undefined` 的操作

1.  `""._`, `1.._` 和 `0[0]`

![](https://pic3.zhimg.com/v2-8ec4a15711772cba6407de92d06b8e9f_b.jpeg)

1.  `void 0` 会比写 `undefined` 要快一些

```js
    let d = void 0;
    console.log(d); // undefined
```

![](https://picb.zhimg.com/v2-96dad3f78ab9258e6781e30fdbb98d95_b.jpeg)

### 3\. 使用 `1/0` 来替代 `Infinity`

```js
    // --- before ---
    [Infinity, -Infinity]

    // --- after ---
    [1/0, -1/0]
```

### 4\. 使用 `Array.length = 0` 来清空数组

![](https://pic4.zhimg.com/v2-436e3c7091a70a991af15cbcc3865606_b.jpeg)

### 5\. 使用 `Array.slice(0)` 实现数组浅拷贝

![](https://pic1.zhimg.com/v2-be4fe26250d709f7f796d759829c2ee3_b.jpeg)

### 6\. 使用 `!+"1"` 快速判断 IE8 以下的浏览器

谷歌浏览器：

  

![](https://pic1.zhimg.com/v2-8db46d830aa56133727a5a3d7a440efc_b.jpeg)

  

IE 9（10，11）：

  

![](https://pic4.zhimg.com/v2-ad3b39229e868db2962bb49856edf334_b.jpeg)

  

IE 8（7，6，5）：

  

![](https://picb.zhimg.com/v2-936e616191f4731dc61b55e3c3603fc9_b.jpeg)

  

### 7\. for 循环条件的简写

```js
    // --- before ---
    for(let i = 0; i < arr.length; i++) {...}

    // --- after ---
    for(let i = arr.length; i--;) {...} // 注意 i-- 后面的分号别漏了
```

---

### 结尾

虽然上述操作能在一定程度上使代码更简洁，但会降低可读性。在目前的大环境下，机器的性能损失远比不上人力的损失，因为升级机器配置的成本远低于维护晦涩代码的成本，所以请谨慎使用这些“黑魔法”。就算要使用，也请加上注释，毕竟，这世上还有很多“麻瓜”需要生存。

![](https://pic4.zhimg.com/v2-e589cddf270aafe6ae45132cd7b06381_b.jpeg)

  

还有一些其他骚操作，可以参考这位大神总结的 《Byte-saving Techniques》，有些很常见，有些使用环境苛刻，这里就不一一列出了。

最后，来一个彩蛋，在控制台输入：

```text
    (!(~+[])+{})[--[~+""][+[]]*[~+[]]+~~!+[]]+({}+[])[[~!+[]]*~+[]]
```

如果以后有人喷你的代码，你就可以将此代码发给他。

> 原文：[https://juejin.im/post/5e044eb5f265da33b50748c8](https://link.zhihu.com/?target=https%3A//juejin.im/post/5e044eb5f265da33b50748c8)  
> 公众号：前端日刊[这么骚的 js 代码，不怕被揍么](https://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/1hGZgQoHDhUPqIYZjDcQCQ)

![](https://pic3.zhimg.com/v2-f1a90dd7b2a7e9c2d3e5e313d59b5290_b.png)

![](https://picb.zhimg.com/v2-d8b31f4ee2d4657f67651bee71b46860_b.png)

