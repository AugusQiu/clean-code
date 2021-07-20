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
某些情况下，确实需要副作用这一行为，如先前例子中的写文件，那么就应该将这些功能集中在一起，不要用多个函数/类修改某个文件，