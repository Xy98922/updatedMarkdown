# 手写js

````js {.font}

//手写bind
Function.prototype.myBind=function(obj,...args){
    let fn=this;
    let res = function(...others){
       if(this === window){
           return fn.call(obj, ...args, ...others);
       }else{
           return fn.call(this, ...args, ...others);  //使用new,则this指向new出来的新对象，创建的新对象的构造函数为fn,但是对象的__proto__为res.prototype
       }
   };
    (()=>{
    function fnproto(){};
    fnproto.prototype=fn.prototype;
    res.prototype=new fnproto();
    })();    
    return res;          
}
//手写JSONP
const jsonp=(url)=>{
   let script=document.createElement('script');
   let scriptcallback=document.createElement('script');
   scriptcallback.innerText=`function fn(data){console.log(data);}`;
   script.src=url;
   document.head.appendChild(scriptcallback);
   document.head.appendChild(script);
   let p=new Promise((resolve,reject)=>{
       script.onload=resolve;
       script.onerror=reject;
   })
   p.then(()=>{
       document.head.removeChild(script);
       document.head.removeChild(scriptcallback);
   })
}
//****数组展平****
Array.prototype.my_flat = function (depth) {
    let  result = [];
    if(depth<0)
    return [this];
    for(let a of this){   
        if(a instanceof Array){
            result=result.concat(a.my_flat(depth-1));
        }   
        if(a===undefined){
            continue;
        } 
        else result.push(a);
    }
   return result;
}
//原生AJAX封装Promise
async function promiseAjax(url){
    let promise= new Promise((resolve)=>{
        let xhr=new XMLHttpRequest();
        xhr.open('GET',url,false);
        try{xhr.onreadystatechange=()=>{
            if(xhr.status===200&&xhr.readyState===4){
                resolve(xhr.responseText);
            }
        }
        xhr.send();}
        catch(e){
            console.log(e);
        }
    })
    let result=await promise;
    return result;
}
//数组去重
const unique=(array)=>{
    let hash={};
    for(let a of array){
        if(hash[a]===undefined){
            hash[a]=1;
        }
        else continue;
    }
    return Object.keys(hash);
}
//Promise.all  map set仍需要再讨论
function promiseAll(promiseList) {
    return new Promise((resolve, reject) => {
      if(promiseList[Symbol.iterator]===undefined){
        throw new TypeError(`argument must be a iterable object`)
      }
      let resolvedCounter = 0;
      const result = [];
      if(promiseList.length===0||promiseList.size===0)
         resolve([]);
      for(let i = 0; i < promiseList.length; i++) {
        Promise.resolve(promiseList[i]).then(value => {
          resolvedCounter ++;
          result[i] = value;
          if (resolvedCounter === promiseList.length) {
            resolve(result);
          }
        },reason => reject(reason))
      }
    })
  }
  
//isArray
Array.isArray=function(obj){
    return Object.prototype.toString.call(obj)==='[object Array]'
}
//Call
Function.prototype.my_call=function(context){
    context=context||Window;   //空对象
    let [,...args]=arguments;
    context.fn=this;           //创建属性
    res=context.fn(...args);   //获取结果
    delete context.fn;         //删除属性
    return res;
}
//深克隆 Function Array Object + 基本数据类型
function deepCopy(obj) {
    if (!obj || typeof obj !== 'object') {    //typedof==='object'可能的情况 Array Null Object 所以要用!obj来排除Null
      throw new TypeError('the parameter are not object');
    }
    const newObj = Array.isArray(obj) ? [] : {};
    for(let key in obj) {
      if (obj.hasOwnProperty(key)) {
        newObj[key] = typeof obj[key] === 'object' ? deepCopy(obj[key]) : obj[key];
      }
    }
    return newObj;
} 
//函数柯里化sum(3)(4)(5)=>3*4*5
function sum(a){
   return function(b){
        return function(c){
            return a*b*c;
        }
   }
}
//手写apply
Function.prototype.my_apply=function(context){
    context=context||Window;
    let [,args]=arguments;
    context.fn=this;
    let result=context.fn(args);
    delete context['fn'];      //点取值与[]取值，见JS高级程序设计
    return result;
}
//寄生式组合继承(注意constructor)
function inherit1(){
    function inheritPrototype(subType,superType){
        subType.prototype=Object.create(superType.prototype);
        subType.prototype.constructor = subType; 
      }
    function subType(){
        superType.call(this);//函数执行前面无点，this为Window，所以用call来转移this，入口参数的this，为new出来的新对象
    }
}
//组合继承  superType被构造两次(没有用到Object.create(original))
function inherit2(){
    subType.prototype = new superType();
    subType.prototype.constructor = subType;
    function subType(){
       superType.call(this);
    }
}
//寄生式继承(没有用到superType.call(this)，就是原型式继承可以获得一份目标对象的浅拷贝，然后再添加一些方法，存在内存共享的问题)
function inherit3(){
function creatAnother(original) {
    let clone = Object.create(original);
    clone.getFriends = function() {
      return this.friends
    };
    return clone;
  }
  let subType = clone(subType);
}
//原型式继承(适合不需要单独创建构造函数，但仍需要在对象间数据共享的场景，存在内存共享的问题)
function inherit4(){
  let superType = {
    name: "parent4",
    friends: ["p1", "p2", "p3"],
    getName: function() {
      return this.name;
    }
  };
  let subType = Object.create(superType);
}
//原型链继承(存在内存空间共享的问题)
function inherit5(){ 
  function superType() {
    this.name = 'parent1';
    this.play = [1, 2, 3]
  }
  function subType(){};
  subType.prototype = new superType();
}
//盗用函数继承(无法继承父类原型对象中定义的方法)
function inherit6(){
  function superType(){
    this.name = 'parent1';
  }
  superType.prototype.getName = function () {
    return this.name;
  }
  function subType(){
    superType.call(this);
}
}
//封装Axios
const api=(method,url,params)=>{
    return new Promise((resolve)=>{
        let promise;
        if(method==='GET')
             promise=axios.get(url,{params});
        else if(method==='POST')
             promise=axios.post(url,params);
        else resolve('没有创建此方法');
        promise.then(data=>resolve(data)).catch(e=>console.log(e));
    })
}
//防抖函数(非立即执行:触发事件后函数不会立即执行，而是在停止触发行为timedelay后执行)
function debounce(fn,timedelay){
    let timer;
    return function(){  //返回的函数 this 指向不变;以及依旧能接受到event参数
       clearTimeout(timer)
       timer=setTimeout(()=>{
       return fn.call(this,...arguments);
       },timedelay)
        }  
}
//防抖函数(立即执行:触发事件后函数立即执行，然后timedelay时间内不触发事件才能继续执行函数)
function debounce1(fn,timedelay){
    let timer;
    return function(){
        clearTimeout(timer);
        if(!timer) fn.call(this,...arguments);
        timer=setTimeout(()=>{
         timer=null;
        },timedelay)
    }
}
//节流函数
function throttle(fn,timedelay) {
    let done = 1;		            //记录是否可执行
    return function () {
        if(done) {
            fn.call(this,...arguments)
            done = 0		        //执行后置为不可执行
            setTimeout(()=>{		//计时结束后再置为可执行
                done =1
            }, timedelay)
        }
    }
}
//模拟new(new过程 做了三件事，注意返回值)
function mockNew() {
    const constructor = [].shift.call(arguments);
    // 判断第一个参数是否为构造函数
    if (typeof constructor !== 'function') {
      throw new TypeError('第一个参数不是构造函数');
    }
    // 新建一个空对象，对象的原型为构造函数的 prototype 对象
    const newObject = Object.create(constructor.prototype); //__proto__已被抛弃
    // 将 this 指向新建对象，并执行函数
    const result = constructor.call(newObject, arguments);
    // 判断返回结果
    return result && ['object', 'function'].includes(typeof result) ? result : newObject;
//=>return result instanceof Object?result:newObject  等价于
  }
  
  
//找到最多的tagName
function findMostTagname(){ 
    let hash=new Map(),max=-Infinity;
    function dfs(node){
        for(let i=0;i<node.childNodes.length;i++) { 
            if(node.childNodes[i].tagName){
                if(hash.has(node.childNodes[i].tagName)){
                    let count=hash.get(node.childNodes[i].tagName);
                    hash.set(node.childNodes[i].tagName, count+1);
                    max=Math.max(max, count+1);
                }
                else{
                    hash.set(node.childNodes[i].tagName,1);
                } 
                if(node.childNodes[i].length!==0){
                    dfs(node.childNodes[i]);       
                } 
            } 
        }
    }
    let html=document.querySelector('html');
    dfs(html);
    for(let a of hash.keys()){
        if(hash.get(a)===max){
            return(a);
      }
    };
};

````