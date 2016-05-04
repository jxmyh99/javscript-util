# javscript-util
js常用的函数
### "$"获取dom
    function $(targ) {
        //判断传入的是不是字符
        if(typeof targ === "string") {
            //分开传入的arguments
            var first_letter = targ.substr(0, 1),
                other_letter = targ.substr(1);
            switch (first_letter) {
                case "#":
                    return document.getElementById(other_letter);
                    break;
                case ".":
                    if (document.querySelectorAll) {
                        return document.querySelectorAll(targ);
                    } else {
                        var targArr = [];
                        function getNode(elem){
                            if(elem.className){
                                var classArr = elem.className.split(" ");
                                for(var i = 0; i < classArr.length; i++){
                                    if(classArr[i] == other_letter) {
                                        targArr.push(elem);
                                        break;
                                    }
                                }
                            }
                            if(elem.childNodes.length){
                                for(var i = 0; i < elem.childNodes.length; i++){
                                    if(elem.childNodes[i].nodeType == 1){
                                        getNode(elem.childNodes[i]);
                                    }
                                }
                            }
                        }
                        getNode(document.body);
                        return targArr;
                    }
                    break;
                default: 
                    return document.getElementsByTagName(targ);
            }
        }
    }

### 另类获取dom的方法

    function $$(selector, context) {
        context = context || document;
        var elements = context.querySelectorAll(selector);
        return Array.prototype.slice.call(elements);
    }

### 添加class
    $.addClass = function(elem, newClass){
        if(!elem) 
            return false;
        else if(!elem.className) {
            elem.className = newClass;
            return false; 
        }
        else {
            var ownClass = elem.className.split(" "), had = false;
            for(var i = 0; i < ownClass.length; i++){
                if(ownClass[i] === newClass){
                    had = true;
                    break;
                }
            }
            if(!had){
                elem.className += " " + newClass;
            }
            return had;
        }
    };
### 删除class
    $.removeClass = function(elem, oneClass){
        if(!elem || !elem.className) return false;
        var ownClass = elem.className.split(" "),
            had = false;
        for(var i = 0; i < ownClass.length; i++){
            if(ownClass[i] === oneClass){
                ownClass.splice(i, 1);
                had = true;
                break;
            }
        }
        if(had){
            elem.className = "";
            if(ownClass.length < 1){
                return had;
            }else if(ownClass.length == 1){
                elem.className = ownClass[0];
            }else if(ownClass.length >1){
                for(var i = 0; i < ownClass.length; i++){
                    if(i == ownClass.length - 1){
                        elem.className += ownClass[i];
                    }else{
                        elem.className += ownClass[i] + " ";
                    }
                }
            }
        }   
        return had; 
    };
### 添加事件
    $.addEvent = function(elem, eventName, handler){
        if(elem){
            if(elem.addEventListener){
                return elem.addEventListener(eventName, handler, false);
            }else if(elem.attachEvent){
                return elem.attachEvent("on" + eventName, handler);
            }else {
                elem["on" + eventName] = handler;
            }
        }
    };
### 删除事件
    $.removeEvent = function(elem, eventName, handler){
        if(elem){
            if(elem.removeEventListener){
                return elem.removeEventListener(eventName, handler, false);
            }else if(elem.detachEvent){
                return elem.detachEvent("on" + eventName, handler);
            }else {
                elem["on" + eventName] = null;
            }
        }   
    };
### 兼容event
    $.getEvent = function(event){
        return event ? event : window.event;
    };
###　获取event.target
    $.getTarget = function(event){
        return event.target || event.srcElement;
    };
### 获取enent.target里的element
    $.getRelatedTarget = function(event){
        return event.relatedTarget || event.toElement || event.fromElement || null;
    };
### 暂时空
    $.contains = function(parent, cur){
        while(cur.parentNode){
            if(cur.parentNode === parent){
                return true;
            }
            cur = cur.parentNode;
        }
        return false;
    };
### 阻止事件冒泡
    $.preventDefault = function(event){
        if(event.preventDefault){
            event.preventDefault();
        }else{
            event.returnValue = false;
        }
    };
### 阻止事件默认行为
    $.stopPropagation = function(event){
        if(event.stopPropagation){
            event.stopPropagation();
        }else{
            event.cancleBubble = true;
        }
    };
### 获取elem的位置
    $.get_pos = function(elem){
        if(!elem) return false;
        var left = elem.offsetLeft,
            top = elem.offsetTop,
            current = elem.offsetParent;
        while(current !== null){
            left += current.offsetLeft;
            top += current.offsetTop;
            current = current.offsetParent;
        }
        return {"left": left, "top": top};
    };
### 这里的方法暂时还没有分析
    $.get_dir = function(elem, mouse_pos){
        if(!elem) return false;
        var pos = $.get_pos(elem),
            size = {"width": elem.offsetWidth, "height": elem.offsetHeight},
            dx = mouse_pos.x - pos.left - size.width/2,
            dy = (mouse_pos.y - pos.top - size.height/2)*-1,
            eve_tan = dy/dx,
            tan = size.height/size.width;
        if(dx != 0){
            if(eve_tan > tan*-1 && eve_tan < tan && dx < 0){
                return "left";
            }else if(eve_tan > tan*-1 && eve_tan < tan && dx > 0){
                return "right";
            }else if((eve_tan > tan || eve_tan < tan*-1) && dy > 0){
                return "top";
            }else if((eve_tan > tan || eve_tan < tan*-1) && dy <= 0){
                return "bottom";
            }
        }else if(dy > 0){
            return "top";
        }else {
            return "bottom";
        }
    };
### 这里的方法暂时还没有分析
    function params(o){ //将要传给后台的参数转化为字符串，以加入到url中
        var arr = [];
        for(var i in o){
            arr.push(i + "=" + encodeURIComponent(o[i]));
        }
        return arr.join("&");
    }
### 这里的方法暂时还没有分析
    $.ajaxp = function(args){ //创建script节点，向后台请求js，src节点携带我的参数信息
        var script = document.createElement("script");
        script.type="text/javascript";
        script.src = args.url + "?" + params(args.params);
        document.body.appendChild(script);
        var time = setTimeout(args.params.callback + "()", 6000); //设置超时时间，觉得不妥可以更改
        script.onload = function(){
            clearTimeout(time);
        };
    };
### 这里的方法暂时还没有分析
    $.show_tips = function(words, timeout) {
        var tips = $('.tips')[0] || (function() {
            var tips = document.createElement('div');
            tips.className = 'tips animated';
            return document.body.insertBefore(tips, document.body.firstChild);
        })();
        tips.innerHTML = words;
        $.removeClass(tips, 'hide');
        $.addClass(tips, 'show');
        setTimeout($.hide_tips, timeout + 1000);
    };
### 这里的方法暂时还没有分析
    $.hide_tips = function() {
        var tips = $('.tips')[0];
        $.removeClass(tips, 'show');
        $.addClass(tips, 'hide');
        setTimeout('$.removeClass($(".tips")[0], "hide")', 1000);
    };
### 判断是否支持某css的写法
    var root = document.documentElement;//获取document的文档根节点
    if('clip' in root.style){
        alert("支持clip");
    }else{
        alert("不支持clip");
    }
### 在html上加入class
    var root = document.documentElement;
    root.classList.add("您要添加的class");//可以在html即文档的根节点上加入class
    root.classList.remove("您要删除的class");//可以把html即文档根节点上删除一个class
### 封装判断是否支持某个css的写法的函数
    function testProperty(property) {
        var root = document.documentElement;
        if (property in root.style) {
            root.classList.add(property.toLowerCase());
            return true;
        }
        root.classList.add('no-' + property.toLowerCase());
        return false;
    }
### 封装判断是否支持某个css写法的一某个属性
    function testValue(id, value, property) {
        //id 给html根节点增加的class名
        //value 要检查的css的属性
        //property 要检查的css属性的css名
        var dummy = document.createElement('p');
        dummy.style[property] = value;
        if (dummy.style[property]) {
            root.classList.add(id);
            return true;
        }
        root.classList.add('no-' + id);
        return false;
    }