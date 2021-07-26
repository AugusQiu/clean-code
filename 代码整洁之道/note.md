## 变量
### 对功能类似的变量名采用统一的命名风格
````js
// 反例
getUserInfo()
getClientData()
getCustomerRecord()

// 正例
getUser()
````
### 变量命名 易于检索和阅读
````js
const MINUTES_IN_A_YEAR = 525600
for(let i=0;i < MINUTES_IN_A_YEAR;i++){
    ...
}
````
### 显式优于隐式
````js
const locations = ['beijing','shanghai','hangzhou']

// 反例
locations.forEach((l)=>{
    ...
})

locations.forEach((location)=>{
   ...
})
````
### 避免重复的描述，当对象已经有意义时，其属性变量可以简略声明
````js
// 反例
var car = {
    carColor : 'black'
}

// 正例
var car = {
    color: 'black'
}
````
### 避免无意义的条件判断
````js
// 反例
function setMyName(name){
    var myName;
    if(name){
        myName = name
    }else{
        myName = 'qgq'
    }
}

// 正例
function setMyName(name){
    var myName = name || 'qgq'
}
````
## 函数
### 函数参数 理想下应该不超过2个
应避免3个以上参数的函数，通常情况下，参数超过两个意味着函数功能过于复杂，就需要考虑优化一下你的函数  
当确实需要多个参数的时，可以考虑将这些参数封装成一个对象  
````js
var menuConfig = {
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
}
function createMenu(menuConfig){
    ...
}
````
### 函数功能的单一性
````js
// 反例
function emailClients(clients){
    clients.forEach(client=>{
        let clientRecord = database.lookup(client)
        if(clientRecord.isActive()){
            email(client)
        }
    })
}

// 正例
function emailClients(clients){
    clients.forEach(client=>{
        if(isClientActive(client)){
            email(client)
        }
    })
}

function isClientActive(client){
    let clientRecord = database.lookup(client);
    return clientRecord.isActive();
}
````
### 函数名应明确表明其功能
````js
// 反例
function dateAdd(date,month){
    ...
}

let date = new Date();
dateAdd(date,1)// 很难理解dateAdd(date,1)是什么意思

// 正例
function dateAddMonth(date,month){
    ...
}
````
### 函数应该只做一层抽象
当函数需要的抽象多于一层时，通常意味着函数功能过于复杂，需要将其**进行分解**，以提高其可重用性、可测试性
````js
// 反例
function parseBetterJSAlternative(code){
    let REGEXES = [
        //...
    ];

    let statements = code.split(' ')
    let tokens;
    REGEXES.forEach((REGEX) => {
        statements.forEach((statement)=>{
            //...
        })
    });

    let ast;
    tokens.forEach((token)=>{
        // lex...
    })
    ast.forEach((node)=>{
        // parse...
    })
}

// 正例
function tokenize(code){
    let REGEXES = [
        //...
    ]

    let statements = code.split(' ')
    let tokens;
    REGEXES.forEach((REGEX) => {
        statements.forEach((statement)=>{
            //...
        })
    });
    return tokens;
}

function lexer(tokens){ // lexer词法分析
    let ast;
    tokens.forEach((token)=>{
        // lex...
    })
    return ast
}

function parseBetterJSAlternative(code){
    let tokens = tokenize(code)
    let ast = lexer(tokens)
    ast.forEach((node)=>{
        // parse
    })
}
````
### 移除重复的代码
永远不要在任何循环下写有重复的代码，重复的代码意味着逻辑变化时需要对不止一处进行修改，js弱类型的特点使得函数拥有更强的普适性  
````js
// 反例
function showDeveloperList(developers) {
  developers.forEach(developer => {
    var expectedSalary = developer.calculateExpectedSalary();
    var experience = developer.getExperience();
    var githubLink = developer.getGithubLink();
    var data = {
      expectedSalary: expectedSalary,
      experience: experience,
      githubLink: githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    var expectedSalary = manager.calculateExpectedSalary();
    var experience = manager.getExperience();
    var portfolio = manager.getMBAProjects();
    var data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}


// 正例
function showList(employees) {
  employees.forEach(employee => {
    var expectedSalary = employee.calculateExpectedSalary();
    var experience = employee.getExperience();
    var portfolio;

    if (employee.type === 'manager') {
      portfolio = employee.getMBAProjects();
    } else {
      portfolio = employee.getGithubLink();
    }

    var data = {
      expectedSalary: expectedSalary,
      experience: experience,
      portfolio: portfolio
    };

    render(data);
  });
}
````
### 采用默认参数精简代码
````js
// 反例
function foo(subject,body){
    subject = subject || 'No Subject';
    body = body || 'No text';
}

// 正例
function foo(subject = 'No subject', body="No text"){..}
````
### 使用Object.assign 设置默认对象
````js
var menuConfig = {
    title: 'order',
    buttonText: 'send',
    cacellable: true
}

function createMenu(config){
    config = Object.assign({
        title: 'Foo',
        body: 'Bar',
        buttonText: 'Baz',
    },config)
}
createMenu(menuConfig)
````
````js
let o1 = { a:1, b:2 }
let o2 = { a:2, b:2, c:3 }
let o3 = Object.assign(o1,o2)
console.log(o3) // {a: 2, b: 2, c: 3}
console.log(o1) // {a: 2, b: 2, c: 3}
console.log(o2) // {a: 2, b: 2, c: 3}

let o1 = { a:1, b:2 }
let o2 = { a:2, b:2, c:3 }
let o3 = { ...o1, ...o2 }
console.log(o3) // { a:2, b:2, c:3}
console.log(o1) // { a:1, b:2 }
console.log(o2) // { a:2, b:2, c:3}
````
**其实推荐还是使用解构符号，这样不会修改目标对象**
### 不要使用标记(Flag)作为函数参数
函数单一性被破坏
````js
// 反例
function createFile(name,temp){
    if(temp){
        fs.create('./temp/' + name)
    }else{
        fs.create(name)
    }
}

// 正例
function createTempFile(name){
    fs.create('./temp' + name)
}

function createFile(name){
    fs.create(name)
}
````
### 避免函数副作用
当函数有除了 “接受一个参数，返回一个结果” 之外的行为时，该函数就产生了副左右。比如写文件、修改全局变量等  
某些情况下，确实需要副作用这一行为，如先前例子中的写文件，那么就应该将这些功能集中在一起，不要用多个函数/类修改某个文件

### 不要污染全局函数
比如你想扩JS中的Array，为其添加一个 diff 函数显示两个数组间的差异，此时应如何去做？你可以将 diff 写入 Array.prototype，但这么做会和其他有类似需求的库造成冲突。如果另一个库对 diff 的需求为比较一个数组中首尾元素间的差异呢？  
````js
// 反例
Array.prototype.diff = function(comparisonArr){
    ......
}

// 正例 用ES6的 class 对全局的Array做简单的扩展
class SuperArray extends Array{
    constructor(...args) {
       super(...args);
    }
}
````
### 采用函数式编程，风格干净
````js
// 采用函数式编程
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

var totalOutput = 0;

for (var i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}

// 正例
var totalOutput = programmerOutput
                .map((programmer) => programmer.linesOfCode)
                .reduce((acc, linesOfCode) => acc + linesOfCode, 0);
````
### 避免“否定情况”的判断
````js
// 反例
if(!isDOMNodeNotPresent(node)){
    ......
}

// 正例
if(isDOMNodePresent(node)){
    ......
}
````
### 尝试用多态（polymorphism) 来代替过多的条件判断
其实多态体现的就是开放封闭原则，扩展性
````js
// 反例
class Airplane {
  //...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return getMaxAltitude() - getPassengerCount();
      case 'Air Force One':
        return getMaxAltitude();
      case 'Cessna':
        return getMaxAltitude() - getFuelExpenditure();
    }
  }
}

// 正例
class Airplane {
  //...
}

class Boeing777 extends Airplane {
  //...
  getCruisingAltitude() {
    return getMaxAltitude() - getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  //...
  getCruisingAltitude() {
    return getMaxAltitude();
  }
}

class Cessna extends Airplane {
  //...
  getCruisingAltitude() {
    return getMaxAltitude() - getFuelExpenditure();
  }
}
````
### 避免过度优化
现代的浏览器在运行时，会对代码自动进行优化，其实有些老旧的优化方案是没必要的
````js
// 反例
// 这里使用变量len是因为在老式浏览器中，
// 直接使用正例中的方式会导致每次循环均重复计算list.length的值，
// 而在现代浏览器中会自动完成优化，这一行为是没有必要的
for (var i = 0, len = list.length; i < len; i++) {
  // ...
}

// 正例
for (var i = 0; i < list.length; i++) {
  // ...
}
````
### 不再被调用的代码应该被删除
## 对象和数据结构
### 使用getters和setters
````js
// 反例
class BankAccount{
  constructor() {
	   this.balance = 1000;
  }
}
let backAccount = new BankAccount();

// Buy shoes
backAccount.balance = bankAccount.balance - 100
````
````js
// 正例
class BankAccount {
  constructor() {
	   this.balance = 1000;
  }

  // It doesn't have to be prefixed with `get` or `set` to be a getter/setter
  withdraw(amount) {
  	if (verifyAmountCanBeDeducted(amount)) {
  	  this.balance -= amount;
  	}
  }
}

let bankAccount = new BankAccount();

// Buy shoes...
bankAccount.withdraw(100);
````
### 让对象拥有私有成员
可以通过闭包完成
````js
// 反例
var Employee = function(name){
    this.name = name;
}

Employee.prototype.getName = function(){
  return this.name
};

var employee = new Employee('qgq')
console.log(employee.getName()) // qgq
delete employee.name // 不具有私有化的特性，实例的name属性可以直接被删掉
console.log(employee.getName()) // undefined
````
````js
var Employee = (function() {
  function Employee(name) {
    this.getName = function() {
      return name;
    };
  }
  return Employee;
}());

var employee = new Employee('John Doe');
console.log('Employee name: ' + employee.getName()); // Employee name: John Doe
delete employee.name;
console.log('Employee name: ' + employee.getName()); // Employee name: John Doe
````
````js
// 闭包实现私有化的另一个方式，构造函数内 定义局部变量 和特权函数，其实例只能通过特权函数来访问此变量，变量被约束在了constructor域内
function Person() {
    var name = 'Eric' //私有属性，通过实例对象不能访问
    
    // 这里的this指向window
    this.getName = function() {
        return name; // 外界访问不到函数内局部变量，这里就是利用闭包暴露出去
    }
    this.setName = function(value) {
        name = value
    }
}
Person.prototype = {
    getNameTest() {
        return this.name;
    },
}

let p = new Person()
console.log(p.getName()) // Eric
p.setName('Amy')
console.log(p.getName()) //Amy

//直接通过实例对象访问
console.log(p.name) // undefined

//通过原型上的方法访问时
console.log(p.getNameTest())// undefined

// 还有一点，上面的getName，setName方法既可以访问私有属性，也可以访问实例对象的共有属性，成为特权方法 
````
## 类
### 单一职责原则(SRP)
修改一个类的理由不应该超过一个     
不要将太多功能硬塞进一个类里面。如果一个类有太多太杂的功能，当你对其中一小部分进行修改时，就很难想象一个修改会对其它依赖模块带来怎样的影响  
````js
// 反例
class UserSettings{
  constructor(user){
    this.user = user;
  }

  changeSettings(settings){
    if(this.verifyCredentials(user)){
      // ......
    }
  }

  verifyCredentials(user){
     // ......
  }
}
````
````js
// 正例
class UserAuth{
  constructor(user){
    this.user = user
  }

  verifyCredentials(user){
    // ......
  }
}

class UserSettings{
  constructor(user){
    this.user = user
    this.auth = new UserAuth(user)
  }

  changeSetting(settings){
    if(this.auth.verifyCredentials()){
      //......
    }
  }
}
````
### 开放封闭原则（OCP)
代码实体（类、模块、函数等）应当是易于扩展、难于修改的  
这一原则指的是我们应允许用户方便扩展模块的功能，但是不需要再打开js文件源码手动修改
````js
class AjaxRequester{
   constructor(){
     this.HTTP_METHODS = ['POST','PUT','GET']
   }

   get(url){
     // ...
   }
   
   // 自由添加http请求类型
   addHTTPMethod(method){
     this.HTTP_METHODS.push(method)
   }
}
````
### 利斯科夫替代原则（LSP）
子类对象应该能够替换其超类对象被使用，子类尽量不要重写父类的方法
### 接口隔离原则（ISP)
客户端不应该依赖它不需要的接口，一个类对另一个类的依赖应该建立在最小的接口上  
当js中，当一个类需要许多参数设置才能生成一个对象时，或许大多时候不需要设置这么多的参数，此时应该减少对配置参数数量的需求  
````js
// 反例
class DOMTraverser{
  constructor(settings){
    this.settings = settings
    this.setup()
  }

  setup(){
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup()
  }
}

let $ = new DOMTraverser({
    rootNode: document.getElementsByTagName('body'),
    animationModule: function(){} // 这个选项貌似不需要
})
````
````js
class DOMTraverser{
   constructor(settings){
     this.settings = settings;
     this.options = settings.options;
     this.setup()
   }

   setup(){
     this.rootNode = this.settings.rootNode;
     this.setupOptions();
   }

   setupOptions(){
     if(this.options.animationModule){
       //...
     }
   }
}

let $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule: function() {}
  }
})
````
### 依赖反转原则（DIP)
该原则有两个核心点：
* 1. 高层模块不应该依赖于地层模块，它们都应该依赖于抽象接口
* 2. 抽象接口应该脱离具体实现，具体实现应该依赖于抽象接口  
````js
// 正例
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
let inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
````
我个人觉得这个示例不太清晰，可以看这个例子[设计原则之依赖倒置js](https://www.cnblogs.com/netUserAdd/p/10436694.html)  
比如说高层是一个人，抽象出来的接口是定义“吃”这个行为，吃什么是下层模块具体实现的，吃米饭、吃面条、吃包子等

### 使用ES6的classes而不是ES5的Function
### 使用方法链
有的人觉得方法链不够干净且违反了德米特法则（一个类或对象对于其他类、对象知道的越少越好），但这种方法在js及许多库(jq)中显得非常实用  
````js
// 在class的函数这灵活返回this
class Person{
  constructor(){
    this.name  = 'qgq'
    this.age   = 23
    this.color = 'yellow'
  }

  setName(name){
    this.name = name
    return this
  }

  setAge(age){
    this.age = age
    return this
  }

  setColor(color){
    this.color = color
    return this
  }

  save(){
    console.log(this.name,this.age,this.color)
  }
}

let p = new Person()
       .setColor('pink')
       .setName('abc')
       .setAge(12)
       .save()
````
### 优先使用组合模式而非继承
在使用继承之前，可以多想想是否能通过组合模式来满足需求呢  
什么时候该用继承，而非组合？
* 继承关系表现为“是一个“而非”有一个“（如动物 -> 人)
* 子类可以复用基类的代码
* 希望当基类改变时，所有派生类都受到影响
````js
// 反例
class Employee{
  constructor(name,email){
    this.name  = name
    this.emial = email
  }
}

// 不应该用继承，因为员工 是有 税收，税收并不是员工的一种类型
class EmployeeTaxData extends Employee{
  constructor(ssn,salary){
    super()
    this.ssn = ssn
    this.salary = salary
  }
}
````
````js
class Employee{
   constructor(name,email){
     this.name  = name;
     this.email = email;
   }

   setTaxData(ssn,salary){
     this.taxData = new EmployeeTaxData(ssn,salary)
   }
   // ...
}

class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }
  // ...
}
````
## 并发
### 用Promises替代callback回调，防止出现“回调地狱”
````js
require('request-promise').get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then(function(response) {
    return require('fs-promise').writeFile('article.html', response);
  })
  .then(function() {
    console.log('File written');
  })
  .catch(function(err) {
    console.error(err);
  })
````
### async/await是较Promises更好的选择
````js
async function getCleanCodeArticle() {
  try {
    var request = await require('request-promise')
    var response = await request.get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    var fileHandle = await require('fs-promise');

    await fileHandle.writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.log(err);
  }
}
````
## 错误处理
### try/catch 捕获错误
````js
try {
  functionThatMightThrow();
} catch (error) {
  console.error(error);
  notifyUserOfError(error);
  reportErrorToService(error);
}


getdata()
.then(data => {
  functionThatMightThrow(data);
})
.catch(error => {
  console.error(error);
  notifyUserOfError(error);
  reportErrorToService(error);
});
````
## 格式化
### 定义变量、函数名风格一致性
### 调用函数的函数和被调函数应放在较近的位置

## 注释
### 只对业务逻辑复杂的代码进行注释，不用过多无谓注释