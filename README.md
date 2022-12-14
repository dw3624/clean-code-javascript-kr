# clean-code-javascript
clean code Javascript (한글)

이 글은 [Clean-Code-JavaScript](ryanmcdermott/clean-code-javascript)를 번역한 글입니다.

## Table of Contents

1. [Introduction](#introduction)
2. [Variables](#variables)
3. [Functions](#functions)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## Introduction

![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](https://www.osnews.com/images/comics/wtfm.jpg)

Robert C. Martin의 저서 [_Clean Code_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)에서 소프트웨어 엔지니어링 원리를 Javascript에 적용시킨 그림입니다.
이 글은 스타일 가이드가 아닙니다. [가독성이 좋고, 재사용 가능하며, 리팩토링이 가능한](https://github.com/ryanmcdermott/3rs-of-software-architecture) JavaScript 소프트웨어를 만들기 위한 가이드입니다.

여기 기재된 모든 원리를 엄격하게 따라야 하는건 아닙니다. 보편적으로 합의된 규칙은 더 적습니다. 어디까지나 가이드에 불과하지만, _Clean Code_의 저자들이 오랜 시간동안 쌓아온 경험을 정리한 글입니다.

소프트웨어 엔지니어링 기술은 이제 막 50살이 넘었고, 우리는 아직 많은 것을 배워가고 있습니다. 만약 소프트웨어 아키텍처가 아키텍처 자체만큼이나 오래됐다면, 우리는 아마 더 엄격한 규칙을 따라야 했을 것입니다. 지금으로서는, 본 가이드라인이 여러분과 여러분의 팀이 작성한 Javascript 코드 품질을 평가하기 위한 하나의 기준이 될 것입니다.

한 가지 더: 여기에 있는 내용을 안다고 바로 더 나은 개발자가 되는 것은 아닙니다. 또한 오랫동안 본 규칙들을 따른다고 해서 실수를 하지 않는다는 것도 아닙니다. 모든 코드 조각은 젖은 점토를 원하는 모양으로 만들어나가는 것처럼 초안으로써 시작합니다. 이후 동료들과 리뷰를 하면서 불완전한 부분을 깎아냅니다. 개선이 필요한 초안 단계에서 너무 자신을 괴롭히지 마세요. 대신 코드를 괴롭힙시다!

## **Variables**

### Use meaningful and pronounceable variable names
### 의미가 있고 발음할 수 있는 변수명을 사용할 것

**Bad:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Good:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ back to top](#table-of-contents)**

### Use the same vocabulary for the same type of variable
### 같은 종류의 변수에는 같은 단어를 사용할 것

**Bad:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**

```javascript
getUser();
```

**[⬆ back to top](#table-of-contents)**

### Use searchable names
### 검색할 수 있는 이름을 사용할 것

코드를 작성하기보다 읽는 일이 훨씬 많습니다. 따라서 읽을 수 있고 검색할 수 있는 코드를 작성하는 것은 매우 중요합니다. 프로그램 이해에 도움이 안 되는 변수명은 읽는이를 괴롭히게 됩니다. 검색가능한 변수명을 사용하도록 합니다. [buddy.js](https://github.com/danielstjules/buddy.js)와
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)와 같은 도구들은 이름이 없는 변수 식별을 수월하게 해줍니다.

**Bad:**

```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

**Good:**

```javascript
// Declare them as capitalized named constants.
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

**[⬆ back to top](#table-of-contents)**

### Use explanatory variables
### 설명적인 변수를 사용할 것

**Bad:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Good:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ back to top](#table-of-contents)**

### Avoid Mental Mapping
### 멘탈맵핑을 피할 것

암묵적인 것보다 명시적인 것이 낫습니다.

> 멘탈맵핑(심상 지도): 개인의 ‘공간에 대한 인지 상태’를 자유롭게 표현한 지도. 실제 측량에 의해 제작된 지도와는 달리 각자의 경험과 판단, 지적 수준 등에 의해 결정된 ‘지역의 이미지’.

**Bad:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Good:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ back to top](#table-of-contents)**

### Don't add unneeded context
### 불필요한 컨텍스트를 추가하지 않을 것

만약 class/object명이 정보를 전달하고 있다면, 변수명으로 이를 반복하지 않습니다.

**Bad:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**Good:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car, color) {
  car.color = color;
}
```

**[⬆ back to top](#table-of-contents)**

### Use default parameters instead of short circuiting or conditionals
### 단축평가, 조건 대신 기본 파라미터값을 사용할 것

기본 파라미터값은 대체로 단축평가보다 간결합니다. 이를 사용할 때, 함수는 `undefined` 인수에 대해서만 기본값을 제공합니다. 기타 `''`, `""`, `false`, `null`, `0`, `NaN` 등의 "falsy" 값은 기본값으로 대체되지 않습니다.

**Bad:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Good:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **Functions**

### Function arguments (2 or fewer ideally)
### 함수의 인수 (2개 이하가 바람직)

함수 인수의 개수를 제한하는 것은 함수 테스트를 간편하게 만들어 주기 때문에 매우 중요합니다. 인수가 3개 이상이 되면, 서로 다른 인수를 사용한 수많은 경우를 고려해야 해, 조합수가 너무 많아집니다.

1개 혹은 2개가 적당하며, 3개 이상은 가능하다면 피하도록 합니다. 그 이상인 경우에는 통합할 필요가 있습니다. 만약 인수가 2개 이상이라면, 해당 함수가 너무 많은 역할을 하려 하는걸 수도 있습니다. 그렇지 않은 경우, 대부분 상위 오브젝트를 인수로 하면 충분합니다.

Javascript는 class 상용구 플레이트가 많이 없더라도 object를 재빨리 만들 수 있습니다. 따라서 인수가 여러개 필요한 경우 object를 사용할 수 있습니다.

함수가 어떤 속성을 기대하는지 명확히 하기 위해서 ES2015/ES6의 분할대입 구문을 사용할 수 있습니다. 여기엔 몇 가지 장점이 있습니다:

1. 다른 사람이 함수 정의를 봤을 때, 어떤 속성이 사용되고 있는지 명확합니다.
2. 이름이 있는 인수 시뮬레이션에 사용할 수 있습니다.
3. 분할대입은 함수에 넘겨진 인수의 지정된 원시값을 복제합니다. 이는 부작용을 막아줍니다. 주의: 인수 object에서 분할대입된 object와 array는 복제되지 않습니다.
4. Lint 툴이 사용되지 않은 속성에 대해 경고할 수 있습니다. 이런 경우 분할대입을 사용해야 합니다.

**Bad:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Good:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ back to top](#table-of-contents)**

### Functions should do one thing
### 하나의 함수는 한 가지 역할만

이는 소프트웨어 엔지니어링에 있어 가장 중요한 규칙입니다. 함수가 한 가지 이상의 역할을 한다면, 이를 만들거나 테스트하거나 추리하기 어려워집니다. 하나의 함수가 한 가지 역할만 하도록 하면, 리팩토링이 수월해지고 코드 가독성도 향상시킬 수 있습니다. 만약 여러분이 다른 가이드를 지키지 않더라도, 본 가이드만 지킨다면, 다른 개발자보다 한발 더 앞서있다고 할 수 있습니다.

**Bad:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ back to top](#table-of-contents)**

### Function names should say what they do
### 역할을 알 수 있는 함수 이름을 지을 것

**Bad:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
// 함수 이름만 보면 무엇을 추가하는지 알기 어렵습니다.
addToDate(date, 1);
```

**Good:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ back to top](#table-of-contents)**

### Functions should only be one level of abstraction
### 함수가 한 가지만 추상화하도록 할 것

함수가 두 가지 이상의 추상을 하고 있다면, 해당 함수가 너무 많은 일을 하고 있을 수 있습니다. 함수를 분리해 재사용성을 향상시키고 테스트를 더 간편하게 할 수 있도록 합니다.

**Bad:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Good:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ back to top](#table-of-contents)**

### Remove duplicate code
### 중복 코드를 없앨 것

최선을 다해 중복 코드를 없앱니다. 중복 코드가 있다는 건 로직을 바꿀 때 여러 군데를 고쳐야 한다는 걸 의미하기 때문에 바람직하지 않습니다.

여러분이 식당을 운영하고 있고 토마토나 양파, 마늘, 조미료 등의 재고를 모두 추적하고 있다고 상상해 봅시다. 만약 재고 목록이 여러개 있다면 토마토가 든 요리를 제공할 때마다 모든 목록을 갱신해야 합니다. 목록이 하나만 있다면 하나만 갱신하면 됩니다!

종종 공통점이 많음에도, 조금씩 다른 부분때문에 코드가 중복되는 일이 있습니다. 그러나 이 차이로 인해 비슷한 역할을 하는 함수가 여러개 생기게 됩니다. 중복 코드를 제거한다는 것은 한 가지 함수/모듈/클래스만을 사용해, 이 차이들을 처리할 수 있도록 추상화를 만드는 것을 말합니다.

좋은 추상화는 중요하기 때문에 _Classes_ 섹션에서 설명하는 SOLID 원리를 따라야 합니다. 안 좋은 추상화는 중복 코드보다 더 안 좋을 수 있기에 조심해야 합니다! 쉽지는 않겠지만 만약 좋은 추상화가 가능하다면 도전해 봅시다! 스스로 반복하지 않도록 합니다. 그렇지 않으면 한 가지를 바꿀 때 여러 군데를 바꿔야 합니다.

**Bad:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ back to top](#table-of-contents)**

### Set default objects with Object.assign
### Object.assign으로 기본 object를 설정할 것

**Bad:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ back to top](#table-of-contents)**

### Don't use flags as function parameters
### flag를 함수 인자처럼 사용하지 말 것

flag는 함수가 여러 역할을 한다고 유저에게 전합니다. 함수는 한 가지 역할만 해야 합니다. 만약 boolean에 따라 다른 코드 경로를 경유할 경우, 함수를 분리합니다.

**Bad:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid Side Effects (part 1)
### 부작용을 피할 것 (part 1)

함수는, 값을 받아 다른 값을 반환하는 것 외의 일을 할 때, 부작용을 일으킵니다. 여기서 부작용이란, 파일에 내용을 작성한다던지, 글로벌 변수를 덮어쓴다던지, 혹은 여러분의 계좌에서 모르는 사람에게 말못 송금한다던지 하는 것들이라 할 수 있습니다.

앞선 예시처럼 파일에 내용을 작성해야 할 때 등, 종종 부족용이 있는 프로그램이 필요하기도 합니다. 이 때 여러분이 하고 싶은 일은 어디서 이 동작을 할지 집중시키는 일입니다. 파일 내용을 작성하는 함수와 class를 여러개 만들지 않도록 합니다. 해당 동작을 하는 서비스는 오직 하나만, 단 하나만 만들도록 합니다.

아무 구조 없이 object 간 상태를 공유하거나, 뭐든 작성할 수 있는 가변 데이터 타입을 사용하거나, 혹은 부작용이 여러곳에서 일어나도록 한다거나 하는 등의 흔한 실수를 하지 않는 것이 핵심입니다. 만약 이를 잘 지킨다면, 여러분은 다른 많은 프로그래머들보다 행복할 수 있습니다.

**Bad:**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ back to top](#table-of-contents)**

### Avoid Side Effects (part 2)
### 부작용을 피할 것 (part 2)

Javascript에는 불변성을 지닌 값과 가변성을 지닌 값이 있습니다. object와 array는 가변성을 지닌 값들로, 이들을 함수 인수로 넘길 때는 주의깊게 다뤄야 합니다. Javascript 함수는 object의 특성을 바꾸거나 array의 내용을 바꿀 수 있으며, 이는 버그를 유발하기도 합니다.

쇼핑카트를 나타내는 array를 인수로 받는 함수를 가정해봅니다. 만약 어떤 함수가 이 쇼핑카트 array를 변경하면 - 예를 들어 구매할 물건을 추가한다던지 - 같은 `cart` array를 참조하는 다른 함수들도 그 영향을 받습니다. 이건 굉장해 보이지만 문제가 될 수도 있습니다. 안 좋은 경우을 상상해 봅시다:

한 유저가 "구매" 버튼을 클릭해, 네트워크 요청을 발생시키고 `cart` array를 서버로 보내는 `purchase` 함수를 실행합니다. 네트워크에 문제가 생겨, `purchase` 함수는 요청을 다시 보냅니다. 이때 만약 네트워크 요청이 시작하기 전에 유저가 실수로 "카트에 추가" 버튼을 눌러 원하지 않는 물건을 추가했다면 무슨 일이 일어날까요? 이 때 네트워크 요청이 시작되면, `cart` array가 변경됐기 때문에, 구매 함수는 잘못 추가한 물건도 같이 송신합니다.

좋은 해법으로는 `addItemToCart` 함수로 하여금 항상 `cart`를 복사하도록 하고, 이름 편집 및 반환하도록 하는 방법이 있습니다. 이를 통해 기존 cart를 참조하는 함수가 변경에 영향을 받지 않도록 할 수 있습니다.

본 접근법 관련 2가지 주의점:

1. 입력된 object값을 변경하고 싶은 경우가 있을 수 있습니다. 본 프로그래밍 기법을 사용하고 있는 경우 이런 경우는 매우 드물다는 것을 알 수 있습니다. 또한 리팩토링을 통해 부작용을 대부분 없앨 수 있습니다!
2. 방대한 object를 복사하는 일은 퍼포먼스적으로 부담이 되기도 합니다. 그러나 다행히도 본 기법에 있어서는 이는 큰 문제가 되지 않습니다. 왜냐하면, 이와 같은 프로그래밍적 접근을 더욱 신속하게 해주고, 일일이 object와 array를 복사하는 것보다 메모리 사용량을 줄일 수 있는 [훌륭한 라이브러리](https://facebook.github.io/immutable-js/)가 있기 때문입니다.

**Bad:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ back to top](#table-of-contents)**

### Don't write to global functions
### 글로벌 함수에 기입하지 말 것

글로벌 환경을 오염하는 것은 Javascript에서는 나쁜 습관입니다. 다른 라이브러리와 충돌을 일으킬 수 있고 여러분의 API 사용자는 배포 환경에서 예외상황을 만나기 전까지 이에 대해 아무것도 모르기 때문입니다. 예시를 하나 생각해봅시다: 만약 여러분이 Javascript의 기존  array 함수를 확장해, 두 array 간 차이를 보여주는 `diff` 함수를 만들고 싶다면 어떻게 해야 할까요? 새 함수를 `Array.prototype`에 추가할 수도 있지만, 같은 일을 하는 다른 라이브러리와 충돌할 수도 있습니다. 만약 다른 라이브러리가 `diff`를 array의 첫번째 요소와 마지막 인자의 차이를 찾는데 사용하고 있다면 어떻게 될까요? 이것이 글로벌 `Array`를 확장하기보다 ES2015/ES6의 class를 사용하는게 나은 이유입니다.

**Bad:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Favor functional programming over imperative programming
### 명령형 프로그래밍보다 함수형 프로그래밍을 우선할 것

Javascript는 Haskell과 같은 함수형 언어는 아니지만 부분적으로 그 기능을 가집니다. 함수형 언어는 더 명확하고 테스트하기 좋습니다. 가능하다면 이런 방식으로 프로그래밍하는게 바람직합니다.

**Bad:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ back to top](#table-of-contents)**

### Encapsulate conditionals
### 조건을 캡슐화할 것

**Bad:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Good:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid negative conditionals
### 부정적 조건을 피할 것

**Bad:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid conditionals
### 조건문을 피할 것

언뜻 봤을 때 본 항목은 불가능해 보입니다. 이걸 처음 들었을 때 대부분 이렇게 말합니다. "`if`문 없이 뭘 어떻게 해?" 여기에 대한 대답은, 대부분의 경우, 다형성(polymorphism)을 이용하면 같은 일을 할 수 있다는 것입니다. 두 번째 질문은 대체로 이렇습니다. "그건 굉장하지만 왜 그래야 하죠...?" 만약 여러분이 `if`문이 있는 class와 함수를 만들었다면, 여러분의 함수가 한 가지 이상의 일을 하고 있다는 것입니다. 하나의 함수는 한 가지 일만, 잊지마세요.

**Bad:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid type-checking (part 1)
### 타입 검사를 피할 것 (part 1)

Javascript는 타입이 없습니다. 즉 함수 인수로 모든 타입을 넘길 수 있습니다. 이러한 자유로움으로 인해 함수 안에서 타입 검사를 하고자 하는 유혹에 종종 빠지곤 합니다. 이를 피하는 방법은 많습니다. 첫 번째로 일관성 있는 API입니다.

**Bad:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Good:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid type-checking (part 2)
### 타입 검사를 피할 것 (part 2)

만약 당신이 문자열, 숫자 등 기본적인 원시값을 다루고 있고, 다형성을 사용할 수 없으나 타입 검사가 필요한 경우, Typescript 사용을 고려해볼 수 있습니다. Typescript는 정적 타이핑 기능을 표준 Javascript에 제공하기 때문에 Javascript의 훌륭한 대체제라 할 수 있습니다. 직접 Javascript 타입 검사를 하는 것의 문제는 너무 많은 구문이 추가로 필요하단 것입니다. 그리고 이를 통해 얻은 가짜 "타입 안전성"은 가독성을 떨어트립니다. 여러분의 Javascript를 깔끔하게 유지하고, 좋은 테스트 코드를 작성하고 좋은 코드 리뷰를 합시다. 그렇지 않다면 Typescript를 사용합니다(제가 말했던 것 처럼 좋은 대안이 될 겁니다!).

**Bad:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Good:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ back to top](#table-of-contents)**

### Don't over-optimize
### 과도한 최적화를 피할 것

모던 브라우저는 런타임 안에서 많은 최적화를 합니다. 만약 최적화를 많이 반복하고 있다면, 이는 시간 낭비입니다. 여기에 어디 최적화가 부족한지 알아볼 [좋은 리소스](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)가 있습니다. 이들이 수정되기 전까지는 이들만 최적화 대상으로 보도록 합니다.

**Bad:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
// 예전 브라우저에서는 캐시되지않은 'list.length'는 재계산으로 인해 cost가 요구됩니다.
// 모던 브라우저에서는 최적화돼 있습니다.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Remove dead code
### 안 쓰는 코드를 삭제할 것

안 쓰는 코드는 중복 코드만큼 나쁩니다. 굳이 이들을 여러분의 코드베이스에 남겨둘 이유가 없습니다. 만약 이들을 호출하지 않는다면, 삭제하십시오! 만약 아직 필요하다고 해도 버전 관리 이력에 안전하게 남아있을 겁니다.

**Bad:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Good:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

### Use getters and setters
### getters와 setters를 사용할 것

단순히 object의 property를 보기보다 getters와 setters를 사용해 object상의 data에 접근하는게 더 좋을 수 있습니다. "왜?" 궁금할 겁니다. 아래 목록에 그 이유를 정리했습니다:

- object propert를 얻는 것 뿐만 아닌 더 많은 일을 하려할 때 코드베이스 내 모든 accessor를 찾을 필요가 없습니다.
- `set`을 사용할 때 validation 추가가 간편합니다.
- 내부를 캡슐화합니다.
- getting, setting을 할 때 로깅과 에러 핸들링 추가가 간편합니다.
- sever에서 가져오는 object property의 lazy load가 가능합니다.

**Bad:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ back to top](#table-of-contents)**

### Make objects have private members
### object는 private member를 갖게 할 것

이를 위해 클로저를 사용할 수 있습니다 (ES5 이전의 경우).

**Bad:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ back to top](#table-of-contents)**

## **Classes**

### Prefer ES2015/ES6 classes over ES5 plain functions
### ES5 함수보다 ES2015/ES6 class를 우선할 것

기존의 ES5 class에서는 가독성이 좋은 class 계승, 구축, 메소드 정의를 하기가 매우 어렵습니다. 계승이 필요한 경우(그렇지 않다 하더라도), ES2015/ES6 class를 우선하도록 합니다. 단, 크고 복잡한 object의 필요성을 느끼기 전에는 class보다 작은 함수 단위를 우선하도록 합니다.

**Bad:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Use method chaining
### 메소드 체인을 사용할 것

본 패턴은 Javascript에서 매우 유용한 패턴으로 jQuery나 Lodash 등 많은 라이브러리 내에서 사용됩니다. 이는 여러분의 코드를 알기 쉽고 간결하게 해줍니다. 이에 저는 "메소드 체인을 통해 코드가 얼마나 깔끔해지는지 봐달라"고 말합니다. class 내 모든 함수에 단순히 `this` 반환을 추가해 class의 method를 chain시킬 수 있습니다.

**Bad:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Good:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ back to top](#table-of-contents)**

### Prefer composition over inheritance
### 계승보다 조합을 우선할 것

유명한 Gang of Four의 저서 [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns)에 따르면, 가능한 계승보다 조합을 우선해야 합니다. 계승이 좋은 이유도 많고 조합이 좋은 이유도 많이 있습니다. 본 항목의 요점은, 여러분이 본능적으로 계승을 사용하고자 할 때, 조합이 그 문제를 더 잘 모델화할 수 있는지 여부를 고려해 보는 것입니다. 가능한 경우가 종종 있습니다.

"언제 계승을 써야 하지?" 여러분은 궁금해 하실 겁니다. 이는 직면한 상황에 따라 다르겠지만, 다음은 계승이 조합보다 적합한 경우의 목록입니다.

1. 계승이 "has-a" 관계가 아닌 "is-a" 관계를 나타낼 때 (사용자->사용자 상세정보 vs. 사람->동물).
2. 기본 class의 코드를 다시 사용할 수 있을 때 (사람은 모든 동물처럼 움직일 수 있습니다).
3. 기본 class를 변경해, 파생 class를 전역적으로 변경하고자 할 때 (모든 동물의 이동 칼로리 소모를 변경).

**Bad:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### Single Responsibility Principle (SRP)
### 단일 책임의 원칙 (SRP)

Clean Code에 적혀 있듯이, "class를 변경하기 위한 이유는 1가지를 넘으면 안됩니다". 비행기 여행 때 하나의 짐만 갖고 탈 수 있을 때처럼, 하나의 class에 여러 기능을 넣는 것은 매우 유혹적입니다. 이 때 문제는 해당 class가 개념적으로 일관되지 않고 여러 이유로 인해 바뀔 수 있다는 것입니다. class 변경 횟수를 최소화하는 것은 중요합니다. 왜냐하면 하나의 class에 너무 많은 기능이 포함돼 있고 그 중 일부를 바꿀 경우, 코드베이스 내 다른 의존적 모듈에 어떤 영향을 줄 지 알기 어렵기 때문입니다.

**Bad:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)
### 개방/폐쇄 원칙 (OCP)

Bertrand Meyer에 의하면, "소프트웨어 개체 (class, 모듈, 함수 등)은 확장에 대해서 열려 있어야 하지만 변경에 대해서는 닫혀 있어야" 합니다. 이게 무슨 말일까요? 이 원칙은 기본적으로, 유저가 기존 코드를 바꾸지 않고도 함수를 새로 만들 수 있도록 해야 한다고 명시합니다.

**Bad:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)
## 리스코프 치환 원칙 (LSP)

이것은 어려운 이름을 가졌지만 매우 간단한 컨셉의 원칙입니다. 기존에 정의된 바에 의하면 "만약 S가 T의 하위형이라면, 프로그램이 갖춰야 할 속성들(정확성, 수행되는 작업 등)을 바꾸지 않더라도, 자료형 T의 객체는 자료형 S의 객체로 교체(치환)될 수 있다"는 내용입니다.

한 가지 예를 들어 설명하자면, 여러분이 부모 클래스와 자식 클래스를 갖고 있을 때 베이스 클래스와 자식 클래스는 잘못된 결과 없이 서로 교환해 사용할 수 있습니다. 여전히 잘 이해가 안된다면, 정사각형-직사각형 예시를 생각해 봅시다. 수학적으로, 정사각형은 직사각형이지만, 만약 여러분이 상속을 통한 "is-a" 관계를 사용해 모델링한다면, 문제가 발생합니다.

**Bad:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ back to top](#table-of-contents)**

### Interface Segregation Principle (ISP)
### 인터페이스 분리 원칙 (ISP)

JavaScript는 인터페이스가 없기 때문에, 이 원칙은 다른 원칙들처럼 엄격하게 적용시킬 순 없습니다. 그러나 JavaScript에 타입 시스템이 없더라도 이는 매우 중요하고 관련이 있습니다.

ISP는 "클라이언트는 사용하지 않는 인터페이스에 의존하도록 강요받지 않아야 한다"고 명시합니다. 덕 타이핑 때문에 인터페이스는 JavaScript에서는 그저 암시적인 계약입니다.

JavaScript에서 이를 잘 보여주는 좋은 예시는 많은 양의 설정 객체가 필요한 클래스입니다. 클라이언트가 방대한 양의 옵션을 설정하지 않아도 되도록 하는 것이 바랍직합니다. 왜냐하면 많은 경우 설정들이 모두 필요한 건 아니기 때문입니다. 설정을 선택적으로 할 수 있도록 해 "무거운 인터페이스 (fat interface)"를 만드는 것을 피할 수 있습니다.

**Bad:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Good:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)
### 의존성 역전 원칙 (DIP)

이 원칙은 두 가지 중요한 내용을 명시합니다.

1. 상위 모듈은 하위 모듈에 종속되지 않아야 합니다. 둘 다 추상화에 의존해야 합니다.
2. 추상화는 세부사항에 종속되지 않아야 합니다. 세부사항은 추상화에 따라 달라져야 합니다.

처음에는 이것을 이해하는게 어려울 수 있습니다. 그러나 만약 AngularJS를 써본 적이 있다면 의존성 주입(Dependency Injection) 형태로 이 원리를 구현하는 것을 본 적이 있을 겁니다. DIP는 동일한 개념은 아니지만 상위 모듈이 하위 모듈의 세부사항을 알지 못하도록 합니다. 이는 DI를 통해 구현할 수 있습니다. DI의 장점은 이를 통해 모듈 간 의존성을 감소시킬 수 있다는 데 있습니다. 모듈 간 의존성은 리팩토링을 어렵게 하기 때문에 매우 나쁜 개발 패턴입니다.

위에서 말했듯이, JavaScript는 인터페이스를 갖지 않습니다. 따라서 추상화에 의존하는 것은 암시적인 약속입니다. 이말인즉슨, 다른 객체나 클래스에 노출되는 메소드와 속성이 암시적인 약속(추상화)에 해당된다는 것을 말합니다. 아래 예시에서, 암시적 약속은 `InventoryTracker`에 대한 모든 요청 모듈이 `requestItems` 메소드를 가진다는 것을 말합니다.

**Bad:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Good:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-of-contents)**

## **Testing**
### 테스트

테스트는 배포보다 더 중요합니다. 충분한 테스트가 없다면, 배포를 할 때 여러분의 코드에 이상이 없는지  확신할 수 없습니다. 얼마나 많은 시간을 투자할지는 여러분이 함께 일하는 팀의 결정에 달렸지만,  Coverage가 100%라는 것은 개발자들에게 높은 자신감과 안도감을 줍니다. 이 말은 훌륭한 테스트 프레임워크 뿐만 아니라 [좋은 Coverage 도구](https://gotwarlost.github.io/istanbul/)를 사용할 필요가 있다는 말입니다.

테스트 코드를 작성하지 않는 것에 대한 변명은 없습니다. [좋은 JS 테스트 프레임워크]((https://jstherightway.org/#testing-tools)가 많기 때문에 이 중에서 팀의 기호에 맞는 것을 고르면 됩니다. 여러분의 팀에 맞는 것을 찾은 뒤에는, 새로 기능/모듈을 도입할 때마다 항상 테스트 코드를 작성하도록 합니다. 만약 테스트 주도 개발 방법론(Test Driven Development, TDD)을 선호한다면 훌륭하지만, 중요한 점은 기능을 배포하거나 기존 기능을 리팩토링하기 전에 Coverage 목표를 달성했는지 확실하게 확인하는 것입니다.

### Single concept per test
### 테스트 컨셉

**Bad:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Good:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-of-contents)**

## **Concurrency**
### 동시성

### Use Promises, not callbacks
### Callback 대신 Promise를 사용할 것

Callback은 깔끔하지 않습니다. 또한 엄청난 양의 중첩을 만듭니다. ES201/ES6에서는 Promise가 내장돼 있습니다. 이걸 사용합시다!

**Bad:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ back to top](#table-of-contents)**

### Async/Await are even cleaner than Promises
### Promise보다 더 깔끔한 Async/Await

Promise는 Callback에 비해 매우 깔끔하지만 ES2017/ES8엔더 깔끔한 async와 await가 있습니다. 함수 앞에 `async`를 붙이기만 하면 됩니다. 더이상 함수 연결을 위해 `then`을 쓸 필요가 없습니다. 만약 ES2017/ES8을 사용할 수 있다면 이걸 쓰세요!

**Bad:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Good:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ back to top](#table-of-contents)**

## **Error Handling**
### 에러 처리 (Error Handling)

에러를 뱉는다는건 좋은겁니다! 프로그램에서 뭔가 잘못됐을 때, 런타임이 이를 성공적으로 확인했다는 걸 의미합니다. 런타임은 현재 스택에서 함수 실행을 중단, (노드에서) 프로세스를 종료한 뒤, 스택 추적으로 콘솔에서  사용자에게 그 이유를 알려줍니다.

### Don't ignore caught errors
### 에러를 무시하지지 말 것

에러를 확인하고도 아무것도 안 한다면, 이를 처리거나 해결할 수 없습니다. `console.log`를 통해 콘솔에 에러를 로깅하는 것은 에러 로그를 잃어버리기 쉬워 좋은 방법이 아닙니다. 만약 `try/catch`로 어떤 코드를 감쌌다면 그건 당신이 그 코드에 대해 에러가 발생할지도 모르기 떄문에 감싼 것이므로, 이에 대해 계획을 마련하거나 코드를 추가해야합니다.

**Bad:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### Don't ignore rejected promises
### Promise 실패를 무시하지 말 것

같은 이유로 `try/catch`에서 발생한 에러러도 무시하면 안됩니다.

**Bad:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Good:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-of-contents)**

## **Formatting**
## 포매팅

포맷팅은 주관적입니다. 여기 있는 많은 규칙들처럼 꼭 따라야 하는 것은 아닙니다. 중요한 점은 포맷팅에 **과하게 집착하지 않는 것**입니다.
포매팅 체크를 자동으로 해주는 [많은 도구들]((https://standardjs.com/rules.html)이 있기 때문입니다.
이 중에서 하나를 골라 사용하세요! 엔지니어가 포맷팅에 관해 논쟁하는 것은 시간과 돈의 낭비입니다.

자동 서식 교정이 적용되지 않는 것들(들여쓰기, 탭 vs 스페이스, 큰 vs 작은 따옴표 등)에 관한 몇 가지 지침을 소개해드리겠습니다.

### Use consistent capitalization
### 일관된 대소문자를 사용할 것

JavaScript에는 정해진 타입이 없기 때문에, 대소문자를 구분하는 것으로 당신의 변수나 함수 등에 대해 많은 것을 알 수 있습니다. 이러한 규칙은 주관적인 것이며, 당신의 팀이 자유롭게 결정할 수 있습니다. 중요한 것은 어디까지나 규칙이 일관되도록 하는 것입니다.

**Bad:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-of-contents)**

### Function callers and callees should be close
### 함수호출자와 피호출자는 가까울 것

어떤 함수가 다른 함수를 호출한다면, 그 함수들이 소스 파일 안안에서 수직으로 근접해 있도록 합니다. 호출 함수를 피호출 함수 바로 위에 두는 것이 이상적입니다. 우리는 코드를 읽을 때 신문처럼 위에서 아래로 읽기 때문에, 코드를 작성할 때도 이를 고려해 작성해야 합니다.

**Bad:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**
## 주석

### Only comment things that have business logic complexity.
### 복잡한 비즈니스 로직에만 주석을 작성할 것

주석은 필수 사항이 아니며, 일종의 사과입니다. 좋은 코드는 대체로 그 자체로도 문서 역할을 합합니다.

**Bad:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Don't leave commented out code in your codebase
### 주석처리된 코드를 지울 것

버전 관리 도구가 히스토리에 코드를 남기기 때문에 주석처리된 코드를 남길 이유가 없습니다.

**Bad:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**

```javascript
doStuff();
```

**[⬆ back to top](#table-of-contents)**

### Don't have journal comments
### 주석으로 기록을 남기지 말 것

버전 관리 도구를 이용하세요! 죽은 코드, 주석처리된 코드, 그리고 특히 코드에 대한 기록을 남길 필요가 없습니다. 코드 기록을 보고 싶다면 `git log`를 사용하세요!

**Bad:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid positional markers
### 위치 조절용 주석을 사용하지 말 것

이들은 대부분 코드를 어지럽게 할 뿐입니다. 적절한 들여쓰기와 포매팅을 적용한 함수와 변수명으로 시각적인 구조를 만드세요.

**Bad:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ back to top](#table-of-contents)**

## Translation
## 번역

다른 언어로도 읽을 수 있습니다:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [eugene-augier/clean-code-javascript-fr](https://github.com/eugene-augier/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![rs](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Serbia.png) **Serbian**: [doskovicmilos/clean-code-javascript/](https://github.com/doskovicmilos/clean-code-javascript)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ back to top](#table-of-contents)**
