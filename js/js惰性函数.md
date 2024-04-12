
什么是惰性函数
> 惰性函数 表示函数执行的分支只会在函数 第一次调用 的时候执行，在第一次调用过程中，该函数会被覆盖为另一个按照合适方式执行的函数，这样任何对原函数的调用就不用再经过执行的分支了。

---
#### 笔记
使用案例
1. 复制操作 - 避免频繁的判断浏览器是否支持navigator.clipboard
在第一次调用的时候，该函数会被覆盖为另一个按照合适方式执行的函数，这样任何对原函数的调用就不用再经过执行的分支了。
```javascript
function copyText(text) {
    if(navigator.clipboard) {
        copyText = (text)=>{
            navigator.clipboard.writeText(text);
        } 
    }else {
        copyText = (text)=>{
            var input = document.createElement('input');
            input.setAttribute('value', text);
            document.body.appendChild(input);
            input.select();
            document.execCommand('copy');
            document.body.removeChild(input);
        }
    }
    copyText(text);
}
```
惰性函数的本质就是覆盖函数
再看另一个例子：
```javascript
function addEvent(type,element,func) {
    if(element.addEventListener) {
        addEvent = (type,element,func)=>{
            element.addEventListener(type,func);
        }
    }else if(element.attachevent) {
        addEvent = (type,element,func)=>{
            element.attachevent('on'+type,func);
        }
    }else{
        addEvent = (type,element,func)=>{
            elelmnet['on'+type] = func;
        }
    }
    //最后记得执行函数
    return addEvent(type,element,func);
}
```
---
使用扩展



---

#### 参考
1. 4.15 S@Y.ZM 01/11 Slc:/ 惰性函数 # JavaScript # 前端开发工程师 # 编程 # 程序员 # web前端  https://v.douyin.com/iY5WpC8H/ 复制此链接，打开Dou音搜索，直接观看视频！
2. https://tsejx.github.io/javascript-guidebook/core-modules/ecmascript-function-objects/function-types/lazy-function/

---

#### 其他GET

1. MARKDOWN 中 使用 （---）或者（***）或者（___）来创建分割线
2. MarkDown 中使用 > 来创建引用
3. 小鹤双拼输入法 o 表示 uo o