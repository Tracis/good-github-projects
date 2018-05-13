# code-review-tips(代码评审的技巧)[(英文版请戳这里)](https://github.com/ryanmcdermott/code-review-tips)

- [code-review-tips(代码评审的技巧)](#code-review-tips)
  - [1. 引言](#1)
  - [2. 为什么要进行代码评审？](#2)
  - [3. 基本内容](#3)
    - [3.1 代码评审应该尽量自动化](#31)
    - [3.2 代码评审应该避免对API进行讨论](#32-api)
    - [3.3 代码评审应该尽量态度友善](#33)
  - [4. 可读性](#4)
    - [4.1 拼写错误应该被纠正](#41)
    - [4.2 变量和函数名应该尽量清楚](#42)
    - [4.3 函数应该尽量短一些](#43)
    - [4.4 文件名应该尽量短](#44)
    - [4.5 对外的函数应该写好文档注释](#45)
    - [4.6 为复杂的代码添加注释](#46)
  - [5. 副作用](#5)
    - [5.1 函数应该尽可能纯净](#51)
    - [5.2 I/O函数应该包含错误处理](#52-i-o)
  - [6. 限制](#6)
    - [6.1 Null值处理](#61-null)
    - [6.2 应该考虑到大量数据的情况](#62)
    - [6.3 应该考虑到一些特例](#63)
    - [6.4 应该限制用户的输入](#64)
    - [6.4 函数应该可以处理用户意想不到的输入](#64)
  - [7. 安全性](#7)
    - [7.1 跨站脚本攻击应该被禁止](#71)
    - [7.2 个人身份信息（PII）不应该被泄露](#72-pii)
  - [8. 性能](#8)
    - [8.1 函数应该使用高效的算法和数据结构](#81)
    - [8.2 重要的操作要记录日志](#82)
  - [9. 测试](#9)
    - [9.1 应该测试新的代码](#91)
    - [9.2 测试应该覆盖到函数的全部功能](#92)
    - [9.3 测试应该特别注意函数的边界值和极限值](#93)
  - [10. 其他](#10)
    - [10.1 应该追踪TODO注释](#101-todo)
    - [10.2 commit message应清晰准确地描述新代码](#102-commit-message)
    - [10.3 代码应该做它该做的事情](#103)

## 1. 引言

代码评审可以激发评审人和被评审人的恐惧。让别人评审你的代码会有一种被侵犯的感觉，就像你外出时被美国运输安全管理局评审了一样。更糟糕的是，评审别人的代码是一个痛苦并且含糊的过程，因为你是在找问题但是又不知道从何下手。
本项目旨在提供一些关于如何评审你和你的团队的代码的一些实用的技巧。所有的例子都是用JavaScript写的，但是这些评审建议应该适用于任何语言的项目。这绝不是一个详尽的清单，但希望这会帮助你发现许多在发布给用户之前的bugs。

## 2. 为什么要进行代码评审？

代码评审在软件工程过程中是必须的部分，因为你无法察觉到你自己代码中的所有的问题。没关系！即使是世界上最好的篮球运动员也会出现投篮不中的情况。

让其他人来对我们的代码进行评审，可以确保我们向用户交付最好的产品，并且减少错误数量。确保你的团队在将代码提交代码库之前对代码进行了评审，找一套适合自己团队的代码评审流程。不存在“放之四海而皆准”的流程，重要的一点事尽可能地定期对代码进行评审。

## 3. 基本内容

### 3.1 代码评审应该尽量自动化

避免讨论静态分析工具可以处理的细节。代码评审不要争论细节问题，例如代码格式，是使用let还是var，使用格式化的工具和linter这些计算机能做到的可以为团队节省大量的时间。

### 3.2 代码评审应该避免对API进行讨论

这些讨论应该在写代码前进行，“一旦已经在地基上浇筑了混凝土，就不要再争论楼层的平面图了”。

### 3.3 代码评审应该尽量态度友善

对代码进行评审很可怕，即使是最有经验的开发人员也会存在不安全感。用更多积极的语言让你的同事可以在工作中保持自在和安全。

## 4. 可读性

### 4.1 拼写错误应该被纠正

避免挑剔一些细枝末节，这些让你的linter、编译器或格式化工具来做。如果这些工具做不了，比如一些拼写错误的情况，请留下一条提示修复的注释。这些小事情有时候会有很大的意义。

### 4.2 变量和函数名应该尽量清楚

命名是计算机科学中最难的问题之一。如果出现了让人困惑的变量名、函数名或者是文件名，造成你阅读起来不清晰，请帮助你的同事提出一个更加清晰的命名。

```javascript
// 这个函数以驼峰法(namesToUpperCase)命名更好
function u(names) {
  // ...
}
```

### 4.3 函数应该尽量短一些

一个函数只做一件事情。很长的函数通常意味着它们做得事情太多。告诉你的同事把不同的功能拆分成不同的函数吧。

```javascript
// This is both emailing clients and deciding which are active. Should be
// 2 different functions.
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

### 4.4 文件名应该尽量短

就像是函数一样，一个文件尽量只做一件事情。一个文件代表一个模块，一个模块应该只为你的项目做意见事情。

例如，如果你的模块叫做fake-name-generator，它就应该只负责创建假名字，比如"Keyser Söze"。如果fake-name-generator还包括一堆用于查询名字数据库的函数，name这些函数应该放在不同的模块中。

对于文件名应该多长没有具体的规定，但是如果它跟下面的名字一样长，并且包含了一些不相关的的函数，那么它应该被拆分到不同的文件中。

```javascript
1: import _ from 'lodash';
2: function generateFakeNames() {
3:   // ..
4: }
...
1128: function queryRemoteDatabase() {
1129:   // ...
1130: }
```

### 4.5 对外的函数应该写好文档注释

如果你的函数打算被其他库引用，这样就有助于添加文档，以便于用户知道函数的功能。

```javascript
// This needs documentation. What is this function for? How is it used?
export function networkMonitor(graph, duration, failureCallback) {
  // ...
}
```

### 4.6 为复杂的代码添加注释

如果你的命名足够好，但是逻辑仍然会让人感到困惑，那么是时候加上注释了。

```javascript
function leftPad (str, len, ch) {
  str = str + '';
  len = len - str.length;

  while (true) {
    // This needs a comment, why a bitwise and here?
    if (len & 1) pad += ch;
    // This needs a comment, why a bit shift here?
    len >>= 1;
    if (len) ch += ch;
    else break;
  }

  return pad + str;
}
```

## 5. 副作用

### 5.1 函数应该尽可能纯净

```javascript
// 下面的函数引用了一个全局变量name，如果有另外一个函数也用到了这个name，这个函数可能会把它当做数组使用，那么就有可能会改变它的值。相反，更好的做法是传入一个叫name的参数。
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();
```

### 5.2 I/O函数应该包含错误处理

任何做I/O操作的函数都应该有错误处理

```javascript
function getIngredientsFromFile() {
  const onFulfilled = (buffer) => {
    let lines = buffer.split('\n');
    return lines.forEach(line => <Ingredient ingredient={line}/>)
  };

  // What about when this rejected because of an error? What do we return?
  return readFile('./ingredients.txt').then(onFulfilled);
}
```

## 6. 限制

### 6.1 Null值处理

如果你写了一个列表组件，如果显示出所有的数据列表会显示出一个漂亮的表格，那么一切都很好。用户喜欢他，你获得了鼓励。但是当没有数据返回时会发生什么？在返回null的时候你怎么显示？你的代码应该可以应对每一种可能发生的情况，如果代码中留有坑，那么它最终肯定会出现的。

```javascript
class InventoryList {
  constructor(data) {
    this.data = data;
  }

  render() {
    return (
      <table>
        <tbody>
          <tr>
            <th>
              ID
            </th>
            <th>
              Product
            </th>
          </tr>
          // We should show something for the null case here if there's
          // nothing in the data inventory
          {Object.keys(this.data.inventory).map(itemId => (
              <tr key={i}>
                <td>
                  {itemId}
                </td>

                <td>
                  {this.state.inventory[itemId].product}
                </td>
              </tr>
          ))}
        </tbody>
      </table>
    );
  }
}
```

### 6.2 应该考虑到大量数据的情况

在上面的例子中如果有10000条数据返回呢？在这种情况下，你需要使用某种形式的分页或者无限滚动。一定要估计下数据的边界值，特别是在做UI编程时。

### 6.3 应该考虑到一些特例

```javascript

class MoneyDislay {
  constructor(amount) {
    this.amount = amount;
  }

  render() {
    // What happens if the user has 1 dollar? You can't say plural "dollars"
    return (
      <div className="fancy-class">
        You have {this.amount} dollars in your account
      </div>
    );
  }
}
```

### 6.4 应该限制用户的输入

用户可能会输入无限的数据然后返回到后端。如果函数可以接受任何类型的数据输入，那么设置一个上限是很重要的。

```javascript
router.route('/message').post((req, res) => {
  const message = req.body.content;

  // What happens if the message is many megabytes of data? Do we want to store
  // that in the database? We should set limits on the size.
  db.save(message);
});
```

### 6.4 函数应该可以处理用户意想不到的输入

用户总是给你一些惊喜。不要期望你总是从用户的请求中得到正确的数据类型，用户可能输入任何数据。[不要只依赖客户端的验证](https://twitter.com/ryconoclast/status/885523459748487169)。

```javascript
router.route('/transfer-money').post((req, res) => {
  const amount = req.body.amount;
  const from = user.id;
  const to = req.body.to;

  // What happens if we got a string instead of a number as our amount? This
  // function would fail
  transferMoney(from, to, amount);
});
```

## 7. 安全性

数据安全性是程序最重要的部分。如果用户不能信任他们的数据，那么你就不会有生意。根据特定的语言和运行环境，有很多不同类型的安全漏洞可能会影响到你的程序。下面是一个小的和不完整的常见安全问题的列表。不要仅仅依赖这个！在每次的提交上尽可能多的进行安全审查，并且实行常规的安全审计。

### 7.1 跨站脚本攻击应该被禁止

跨站脚本攻击（XSS）是Web应用程序面临的最多的安全攻击。它会在你获取到用户数据并且将其包含在页面中，但是没有对其进行安全审查的情况下。这会导致站点从远程执行源代码。

```javascript
function () {
  let badge = document.getElementsByClassName('badge');
  let nameQueryParam = getQueryParams('name');

  /**
    * What if nameQueryParam was `<script>sendCookie(document.cookie)</script>`?
    * If that was the query param, a malicious user could lure a user to click a
    * link with that as the `name` query param, and have the user unknowingly
    * send their data to a bad actor.
    */
  badge.children[0].innerHTML = nameQueryParam;
}

```

### 7.2 个人身份信息（PII）不应该被泄露

每次你获取用户数据时，你都承担着巨大的责任。如果你在URL中泄漏了数据，比如在向第三方的提供的分析跟踪服务，或者向不应该访问的员工公开了数据，则会极大的伤害到用户和你的业务。对待别人的数据时要小心了!

```javascript
router.route('/bank-user-info').get((req, res) => {
  const name = user.name;
  const id = user.id
  const socialSecurityNumber = user.ssn;

  // There's no reason to send a socialSecurityNumber back in a query parameter
  // This would be exposed in the URL and potentially to any middleman on the
  // network watching internet traffic
  res.addToQueryParams({
    name,
    id,
    socialSecurityNumber
  })
});
```

## 8. 性能

### 8.1 函数应该使用高效的算法和数据结构

对于不同的情况，应对方案不一样，但是你要用你最好的判断力来确定是否有任何的方法来提高一段代码的效率。用户会对你的代码执行速度表示感谢。

```javascript
// If mentions was a hash data structure, you wouldn't need to iterate through
// all mentions to find a user. You could simply return the presence of the
// user key in the mentions hash
function isUserMentionedInComments(mentions, user) {
  let mentioned = false;
  mentions.forEach(mention => {
    if (mention.user === user) {
      mentioned = true;
    }
  })

  return mentioned;
}
```

### 8.2 重要的操作要记录日志

日志记录有助于提供有关性能和用户行为的分析。不是每一个操作都需要记录日志，但是要取决于你的团队需要做哪些有意义的数据分析，并且要确保用户的个人身份信息不要被泄露。

```javascript
router.route('/request-ride').post((req, res) => {
  const currentLocation = req.body.currentLocation;
  const destination = req.body.destination;

  requestRide(user, currentLocation, destination).then(result => {
    // We should log before and after this block to get a metric for how long
    // this task took, and potentially even what locations were involved in ride
    // ...
  });
});
```

## 9. 测试

### 9.1 应该测试新的代码

所有的代码都应该包括一个测试用例，它是否修复了一个bug，或者添加了一个新的功能。如果它修复了一个bug，那么它应该有一个测试bug被修复的代码。如果它是一个新的功能，那么每个组件都应该进行单元测试，并且应该有一个集成测试，确保该功能与系统的其余部分可以正常运行。

### 9.2 测试应该覆盖到函数的全部功能

```javascript
function payEmployeeSalary(employeeId, amount, callback) {
  db.get('EMPLOYEES', employeeId).then(user => {
    return sendMoney(user, amount);
  }).then(res => {  
    if (callback) {
      callback(res);
    }

    return res;
  })
}

const callback = (res) => console.log('called', res);
const employee = createFakeEmployee('john jacob jingleheimer schmidt');
const result = payEmployeeSalary(employee.id, 1000, callback);
assert(result.status === enums.SUCCESS);
// What about the callback? That should be tested

```

### 9.3 测试应该特别注意函数的边界值和极限值

```javascript
function dateAddDays(dateTime, day) {
  // ...
}


let dateTime = '1/1/2017'
let date1 = dateAddDays(dateTime, 5);

assert(date1 === '1/6/2017');

// What happens if we add negative days?
// What happens if we add fractional days: 1.2, 8.7, etc.
// What happens if we add 1 billion days?
```

## 10. 其他

> 一切都可以被归类到其他

> ——萧伯纳

### 10.1 应该追踪TODO注释

TODO注释对于让你和你的同事知道一些以后需要修复的问题是很好的。有时候你必须部署代码然后等待之后修复问题。但是你必须最终把他们清理干净。这就是为什么要跟踪TODO，并且从你的问题跟踪系统中给出对应的ID，便于以后可以排期解决和跟踪你们项目的问题所在。

### 10.2 commit message应清晰准确地描述新代码

我们每次都写了commit message，比如“改了一些垃圾代码”，“该死的！”， “再次修复这个煞笔bug！”。这些是有趣和令人满意的。但是当你周五提交了代码，周六早上起床之后这些信息就不是很有帮助性了。周五晚上你在抱怨这些烂代码时，你没有搞清楚这些烂代码在做什么就提交了。写commit message时要清晰地描述代码，如果有问题的跟踪ID时要包含进来，这样以后可以更加方便的搜索提交日志。

### 10.3 代码应该做它该做的事情

这似乎是显而易见的，但是大多数代码评审的人没有时间去手动测试每一个用户界面的变化，所以代码评审时要确保每一个改动的业务逻辑都是按照策划来做的，因为当你在代码中找问题时很容易忘记这个问题。