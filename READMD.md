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