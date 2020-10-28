---
title: Blob 对象
description: Blob，Binary Large Object的缩写，代表二进制类型的对象.
tags: 
- Web
---

**概念：**一个 Blob 对象表示一个不可变的，原始数据的类似文件对象。Blob 表示的数据不一定是一个 JavaScript 原生格式。File 接口也是基于 Blob，继承 blob 功能并将其扩展为支持用户系统上的文件.

***

## 构造 Blob 对象

1. 使用 Blob 构造函数.<br>
2. 对已有的 Blob 对象使用 slice() 方法切除一段.<br>

#### 1. Blob 构造函数

```
var blob = new Blob(data[, option])
```

**解释：**new Blob() 会返回一个新创建的 Blob 对象，即使只有一个字符串必须用数组组装起来.
**参数：**
1. 参数 data 是一组数据，所有必须是数组，即使只有一个字符串也必须用数组组装起来.<br>
2. 参数 options 是对这一 Blob 对象的配置属性，目前也只有一个 type 也就是相关的 MIME 需要设置 type 的值.<br>
**例子：**
```
var blob = new Blob(['我是 Blob'], {type: 'text/html'})
```

#### 2. slice 创建

```
Blob.slice([start[, end[, contentType]]])
```

**解释：**slice() 返回一个新的 Blob 对象，包含了源 Blob 对象重指定范围内的数据，用法类似于 Array.prototype.slice().<br>
**参数：**
1. start 开始索引，可以为负数，默认值为 0.<br>
2. end 结束索引，可以为负数，默认最后一个索引.<br>
3. contentType 新的 Blob 对象的 MIME 类型，这个值将会成为新的 Blob 对象的 type 属性的值，默认为一个空字符串.<br>

***

## Blob 属性

1. blob.size           Blob 大小（单位字节）<br>
2. blob.type           Blob 的 MIME 类型，如果是未知，则是 " "（空字符串）<br>

***

## URL.createObjectURL()

**概念：**URL.createObjectURL() 静态方法会创建一个 DOMString，其中包含一个表示参数中给出的对象的 URL。这个 URL 的生命周期和创建它的窗口中的 document 绑定。这个新的 URL 对象表示指定的 File 对象或 Blob 对象。<br>

```
objectURL = URL.createObjectURL(blob)
```

1. 使用 URL.createObjectURL() 函数可以创建一个 Blob URL，参数 blob 是用来创建 URL 的 File 对象或者 Blob 对象，返回值的格式是：blob://URL.<br>
2. 1. 在每次使用 URL.createObjectURL() 方法时，都会创建一个新的 URL 对象，即使你已经用相同的对象作为参数创建过。当不在需要这些 URL 对象时，每个对象都必须通过调用 URL.revokeObjectURL() 方法传入创建的 URL 为参数，用来释放它。浏览器中会自动释放它们，但为了获得最佳性能和内存使用状况，应该在安全的时机主动释放掉它们。

## URL.revokeObjectURL()

**概念：**URL.revokeObjectURL() 静态方法用来释放一个之前通过调用 URL.createObjectURL() 创建的已经存在的 URL 对象。当你结束使用某个 URL 对象时，应该通过调用这个方法来让浏览器知道不再需要保持这个文件的引用了。<br>

```
window.URL.revokeObjectURL(objectURL)
```

**参数：**objectURL 是一个通过 URL.createObjectURL() 方法创建的对象 URL.

***

## Blob 应用

```
const opts = {
  url,
  method,
  responseType: 'blob',                                // 一定要设为 blob
  headers
};

axios(opts).then(res => {
  const blob = new Blob([res.data], { type: 'application/x-gzip' });
  // 非 IE 下载
  if ('download' in document.createElement('a')) {
    const elink = document.createElement('a');
    elink.download = `${newName}.tar`;                    // 可以在此对下载下的文件重命名
    elink.style.display = 'none';
    elink.href = URL.createObjectURL(blob);
    document.body.appendChild(elink);
    elink.click();
    URL.revokeObjectURL(elink.href);
    document.body.removeChild(elink);
  } else {
    // IE 10+ 下载
    navigatar.msSaveBlob(blob, `${newName}.tar`);
  }
});
```
