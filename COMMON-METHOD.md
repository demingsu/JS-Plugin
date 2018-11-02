### 获取事件源的节点名称
```
document.documentElement.onclick = function (evt) {
    evt = evt || window.event;
    var target = evt.target || evt.srcElement;
    console.log(target.nodeName);
}
```
### 阻止事件冒泡和取消默认事件
```
function fn(evt) {
    evt = evt || window.event;
    if(evt.stopPropagation) {
        evt.stopPropagation();
    } else {
        evt.cancelBubble = true;
    }
    if(evt.preventDefault) {
        evt.preventDefault();
    } else {
        evt.returnValue = false;
    }
    console.log(this.nodeName);
}
var nodes = document.getElementsByTagName('*');
for(var i = 0;i < nodes.length;i ++) {
    var el = nodes[i];
    if(el.addEventListener) {
        el.addEventListener('click', fn, false);
    } else {
        el.attachEvent('onclick', fn);
    }
}
```
### 获取元素的下一个和上一个节点的方法
```
function getNextEl(el) {
    if("getElementsByClassName" in window) {
        return el.nextElementSibling;
    }
    var next = el.nextSibling;
    while(next && next.nodeType !== 1) {
        next = next.nextSibling;
    }
    return next;
}
function getPreviousEl(el) {
    if('getElementsByClassName' in window) {
        return el.previousElementSibling;
    }
    var preNode = el.previousSibling;
    while(preNode && preNode.nodeType !== 1) {
        preNode = preNode.previousSibling;
    }
    return preNode;
}
```
### 页面返回顶部的方式
```
function moveTop(el) {
    var _ch = el.clientHeight,
            divEl = document.createElement('div');
    divEl.setAttribute('style', 'display: none; position: absolute; right: 10px; bottom: 20px; width: 30px; height: 30px;' +
            'cursor: pointer; border-radius: 4px; border: solid 1px #d7d7d7;');
    divEl.innerText = 'TOP';
    el.onscroll = function () {
        var _st = el.scrollTop;
        if(_st >= _ch) {
            divEl.style.display = 'block';
        } else {
            divEl.style.display = 'none';
        }
    }
    divEl.onclick = scrollTop;
    function scrollTop() {
        divEl.style.display = 'none';
        window.clearTimeout(this.timer);
        divEl.timer = null;
        el.scrollTop -= 100;
        if((el.scrollTop - 100) <= 0) {
            el.scrollTop = 0;
            return;
        }
        divEl.timer = window.setTimeout(scrollTop, 10);
    }
}
```
### 循环延迟图片加载
```
function DelayImage(el) {
    function loadImage() {
        var _elTop = el.clientHeight + el.scrollTop,
                imgNodes = el.getElementsByTagName('img');
        for(var i = 0;i < imgNodes.length;i ++) {
            var node = imgNodes[i],
                    nodeTop = offset(node).top,
                    imgPath = node.getAttribute('imgPath');
            if(nodeTop <= _elTop && !node.isLoad) {
                loadImg(node, imgPath);
            }
        }
    }
    function offset() {//获取偏移量；
        var curEle = arguments[0],
                par = curEle.offsetParent,
                left = curEle.offsetLeft,
                top = curEle.offsetTop;
        while (par) {
            left += par.offsetLeft;
            top += par.offsetTop;
            if (navigator.userAgent.indexOf("MSIE 8.0") <= -1) {
                left += par.clientLeft;
                top += par.clientTop;
            }
            par = par.offsetParent;
        }
        return {left: left, top: top};
    }
    function loadImg(ele, trueImg) {
        var oImg = new Image;
        oImg.src = trueImg;
        oImg.onload = function () {
            ele.src = trueImg;
            ele.isLoad = true;
            show(ele);
        }
    }
    function show(ele) {
        var speed = 0;
        _move();
        function _move() {
            window.clearTimeout(ele.timer);
            speed += 0.02;
            setCss(ele, "opacity", speed);
            if (speed >= 1) {
                setCss(ele, "opacity", 1);
                return;
            }
            ele.timer = window.setTimeout(_move, 10);
        }
    };
}
```
###  日期格式化
```
String.prototype.timeFormat = function (format) {
    var that = this;
    var date = new Date(that);
    var arr = [];
    arr.push(date.getFullYear());
    arr.push(date.getMonth());
    arr.push(date.getDate());
    arr.push(date.getHours());
    arr.push(date.getMinutes());
    arr.push(date.getSeconds());
    format = format || "{0}年{1}月{2}日 {3}:{4}:{5}";
    return format.replace(/{(\d+)}/g, function () {
        var val = arr[arguments[1]];
        console.log(val);
        return (val+'').length === 1 ? "0" + val : val;
    });
}
```
### 16进制颜色转rgba
```
let hexToRgba = (val: string, opacity = 1) => {
    if (/^#([0-9a-fA-F]{3}|[0-9a-fA-F]{6})$/.test(val)) {
        /* 自动补全3位16进制字符串 */
        if (val.length === 4) {
            let str = "#";
            for (let i = 1; i < 4; i ++) {
                let char = val.slice(i, i + 1);
                str += char.concat(char);
            }
            val = str;
        }
        let res = [];
        /* 遍历字符串转为十进制rgb */
        for (let i = 1; i < 7; i += 2) {
            /* 两个方法都可以转 */
            res.push(parseInt(val.slice(i, i + 2), 16));
        }
        res.push(opacity);
        val = `rgba(${res.join(',')})`;
    }
    return val;
};
```
### 去掉字段的两端空格
```
let trimObject = (obj: any): any => {
    let res: any = {} as any;
    for (let name in obj) {
        if (obj.hasOwnProperty(name)) {
            res[name] = obj[name].replace(/(^\s*)|(\s*$)/g, '');
        }
    }

    return res;
};
```
### 计算文件大小并返回格式化
```
let fileSize = (size: number = 0, count: number = 0): any => {
    if(!count) count = 0;
    if(isNaN(size)) return 0;
    var names = ['byte', 'KByte', 'MB', 'GB', 'TB'];
    if(size < 1024) {
        return size + names[count];
    } else {
        return fileSize(parseFloat((size / 1024).toFixed(2)), ++count);
    }
};
```
### 计算分页标签
```
let pagingCount = (total: number, page: number, size: number): string[] => {
    let res: any[] = ['1'];
    let tp = Math.ceil(total/size);
    if(tp > 7) {
        if(page < 4) {
            res = res.concat(['2', '3', '...', `${tp}`]);
        } else if(page > tp - 3) {
            res = res.concat(['...', `${tp - 2}`, `${tp - 1}`, `${tp}`]);
        } else {
            res = res.concat(['...', `${page - 1}`, `${page}`, `${page + 1}`, '...']);
        }
    } else {
        for(let i = 1;i < tp;i ++) {
            res.push(`${i + 1}`);
        }
    }
    return res;
};
```
