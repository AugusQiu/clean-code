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