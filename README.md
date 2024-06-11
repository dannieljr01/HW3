# HW3
## chap.6
### 6-01(Person.prototype)
```
var Person = function(name) {
    this._name = name;
  };
  Person.prototype.getName = function() {
    return this._name;
  };
```
person의 인스턴스를 __proto__프로퍼티를 통해 getName을 호출 할 수 있음.
### 6-02(prototype과 __proto__)
```
var Constructor = function(name) {
    this.name = name;
  };
  Constructor.prototype.method1 = function() {};
  Constructor.prototype.property1 = 'Constructor Prototype Property';
  
  var instance = new Constructor('Instance');
  console.dir(Constructor);
  console.dir(instance);
```
자바스크립트는 함수에 자동으로 객체인 prototype 프로퍼티를 생성해 놓는데, 해당 함수를 생성자 함수로서 사용할 경우, 즉 new 연산자와 함께 함수를 호출할 경우, 그로부터 생성된 인스턴스에는 숨겨진 프로퍼티인 __proto__가 자동으로 생성되며, 이 프로퍼티는 생성자 함수의 prototype 프로퍼티를 참조한다. __proto__ 프로퍼티는 생략 가능하도록 구현돼 있기 때문에 생성자 함수의 prototype에 어떤 메서드나 프로퍼티가 있다면 인스턴스에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있게 됨.
### 6-03(constructor 프로퍼티)
```
var arr = [1, 2];
Array.prototype.constructor === Array; // true
arr.__proto__.constructor === Array; // true
arr.constructor === Array; // true

var arr2 = new arr.constructor(3, 4);
console.log(arr2); // [3, 4]
```
인스턴스의 __proto__가 생성자 함수의 prototype 프로퍼티를 참조하며 __proto__ 가 생략 가능하기 때문에 인스턴스에서 직접 constructor에 접근할 수 있는 수단이 생김. 한편 constructor는 읽기 전용 속성이 부여된 예외적인 경우(기본형 리터럴 변수 - number, string, boolean)를 제외하고는 값을 바꿀 수 있다.
### 6-04(constructor 변경)
```
var NewConstructor = function() {
    console.log('this is new constuctor!');
  };
  var dataTypes = [
    1, // Number & false
    'test', // String & false
    true, // Boolean & false
    {}, // NewConstructor & false
    [], // NewConstructor & false
    function() {}, // NewConstructor & false
    /test/, // NewConstructor & false
    new Number(), // NewConstructor & false
    new String(), // NewConstructor & false
    new Boolean(), // NewConstructor & false
    new Object(), // NewConstructor & false
    new Array(), // NewConstructor & false
    new Function(), // NewConstructor & false
    new RegExp(), // NewConstructor & false
    new Date(), // NewConstructor & false
    new Error(), // NewConstructor & false
  ];
  
  dataTypes.forEach(function(d) {
    d.constructor = NewConstructor;
    console.log(d.constructor.name, '&', d instanceof NewConstructor);
  });
```
모든 데이터가 d instanceof New Constructor 명령에 대해 false를 반환한다. 이로부터 constructor를 변경하더라도 참조하는 대상이 변경될 뿐 이미 만들어진 인스턴스의 원형이 바뀐다거나 데이터 타입이 변하는 것은 아님을 알 수 있다. 어떤 인스턴스의 생성자 정보를 알아내기 위해 constructor 프로퍼티에 의존하는게 항상 안전하지는 않다.
### 6-05(다양한 constructor 접근 방법)
```
var Person = function(name) {
    this.name = name;
  };
  var p1 = new Person('사람1'); // Person { name: "사람1" } true
  var p1Proto = Object.getPrototypeOf(p1);
  var p2 = new Person.prototype.constructor('사람2'); // Person { name: "사람2" } true
  var p3 = new p1Proto.constructor('사람3'); // Person { name: "사람3" } true
  var p4 = new p1.__proto__.constructor('사람4'); // Person { name: "사람4" } true
  var p5 = new p1.constructor('사람5'); // Person { name: "사람5" } true
  
  [p1, p2, p3, p4, p5].forEach(function(p) {
    console.log(p, p instanceof Person);
  });
```
p1부터 p5까지는 모두 Person의 인스턴스이다.
### 6-06(메서드 오버라이드)
```
var Person = function(name) {
    this.name = name;
  };
  Person.prototype.getName = function() {
    return this.name;
  };
  
  var iu = new Person('지금');
  iu.getName = function() {
    return '바로 ' + this.name;
  };
  console.log(iu.getName()); // 바로 지금
```
메서드 오버라이드는 메서드 위에 메서드를 덮어씌웠다는 표현이다. 원본을 제거하고 다른 대상으로 교체하는 것이 아니라 원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는 느낌이다.
### 6-07(배열에서 배열 메서드 및 객체 메서드 실행)
```
var arr = [1, 2];
arr.push(3);
arr.hasOwnProperty(2); // true
```
어떤 데이터의 __proto__ 프로퍼티 내부에 다시 __proto__ 프로퍼티가 연쇄적으로 이어진 것을 프로토타입 체인이라고 하고, 이 체인을 따라가며 검색하는 것을 프로토타입 체이닝이라고 한다. 프로토타입 체이닝은 6-2절에서 소개한 메서드 오버라이드와 동일한 맥락이다. 어떤 메서드를 호출하면 자바스크립트 엔진은 데이터 자신의 프로퍼티들을 검색해서 원하는 메서드가 있으면 그 메서드를 실행하고, 없으면 __proto__를 검색해서 있으면 그 메서드를 실행하고, 없으면 다시 __proto__ 를 검색해서 실행하는 식으로 진행된다.
### 6-08(메서드 오버라이드와 프로토타입 체이닝)
```
var arr = [1, 2];
Array.prototype.toString.call(arr); // 1,2
Object.prototype.toString.call(arr); // [object Array]
arr.toString(); // 1,2

arr.toString = function() {
  return this.join('_');
};
arr.toString(); // 1_2
```
메서드 오바리으다 프로토타입 체이닝 비교 코드.
### 6-09(Object.prototype에 추가한 메서드에의 접근)
```
Object.prototype.getEntries = function() {
    var res = [];
    for (var prop in this) {
      if (this.hasOwnProperty(prop)) {
        res.push([prop, this[prop]]);
      }
    }
    return res;
  };
  var data = [
    ['object', { a: 1, b: 2, c: 3 }], // [["a",1], ["b", 2], ["c",3]]
    ['number', 345], // []
    ['string', 'abc'], // [["0","a"], ["1","b"], ["2","c"]]
    ['boolean', false], // []
    ['func', function() {}], // []
    ['array', [1, 2, 3]], // [["0", 1], ["1", 2], ["2", 3]]
  ];
  data.forEach(function(datum) {
    console.log(datum[1].getEntries());
  });
```
객체만을 대상으로 동작하는 객체 전용 메서드들은 부득이 Object.prototype이 아닌 Object에 스태틱 메서드로 부여할 수 밖에 없다. 또한 생성자 함수인 Object와 인스턴스인 객체 리터럴 사이에는 this를 통한 연결이 불가능하기 때문에 여느 전용 메서드 처럼 '메서드명 앞의 대상이 곧 this'가 되는 방식 대신 this의 사용을 포기하고 대상 인스턴스를 인자로 직접 주입해야 하는 방식으로 구현되어있다.
### 6-10(Grade 생성자 함수와 인스턴스)
```
var Grade = function() {
    var args = Array.prototype.slice.call(arguments);
    for (var i = 0; i < args.length; i++) {
      this[i] = args[i];
    }
    this.length = args.length;
  };
  var g = new Grade(100, 80);
```
Grade의 인스턴스는 여러 개의 인자를 받아 각각 순서대로 인덱싱해서 저장하고 length 프로퍼티가 존재하는 등으로 배열의 형태를 지니지만, 배열의 메서드를 사용할 수는 없는 유사배열객체이다. 인스턴스에서 배열 메서드를 직접 쓸 수 있게 하고 싶다면 Grade.prototype이 배열의 인스턴스를 바라보게 하면된다.
## chap.7
### 7-01(스태틱 메서드, 프로토타입 메서드)
```
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  Rectangle.isRectangle = function(instance) {
    return (
      instance instanceof Rectangle && instance.width > 0 && instance.height > 0
    );
  };
  
  var rect1 = new Rectangle(3, 4);
  console.log(rect1.getArea()); // 12 (O)
  console.log(rect1.isRectangle(rect1)); // Error (X)
  console.log(Rectangle.isRectangle(rect1)); // true
```
전형적인 생성자 함수와 인스턴스이다. Rectangle 함수를 new 연산자와 함께 호출해서 생성된 인스턴스를 rect1에 할당했다. 인스턴스에서 직접 호출할 수 있는 메서드가 바로 프로토타입 메서드이다.
### 7-02(6-2-4절의 Grade 생성자 함수 및 인스턴스)
```
var Grade = function() {
    var args = Array.prototype.slice.call(arguments);
    for (var i = 0; i < args.length; i++) {
      this[i] = args[i];
    }
    this.length = args.length;
  };
  Grade.prototype = [];
  var g = new Grade(100, 80);
```
자바스크립트에서 클래스 상속을 구현했다는 것은 결국 프로토타입 체이닝을 잘 연결한 것으로 이해하면 된다. 위의 예제에는 length 프로퍼티가 configurable하다는 점과, Grade.prototype에 빈 배열을 참조시켰다는 문제가 있다.
### 7-03(length 프로퍼티를 삭제한 경우)
```
var Grade = function() {
    var args = Array.prototype.slice.call(arguments);
    for (var i = 0; i < args.length; i++) {
      this[i] = args[i];
    }
    this.length = args.length;
  };
  Grade.prototype = [];
  var g = new Grade(100, 80);
  
  g.push(90);
  console.log(g); // Grade { 0: 100, 1: 80, 2: 90, length: 3 }
  
  delete g.length;
g.push(70);
console.log(g); // Grade { 0: 70, 1: 80, 2: 90, length: 1 }
```
내장객체인 배열 인스턴스의 length 프로퍼티는 configurable 속성이 false라서 삭제가 불가능 하지만, Grade 클래스의 인스턴스는 배열 메서드를 상속하지만 기본적으로는 일반 객체의 성질을 그대로 지니므로 삭제가 가능해서 문제가 된다.
### 예제7-4(요소가 있는 배열을 prototype에 매칭한 경우)
```
var Grade = function() {
    var args = Array.prototype.slice.call(arguments);
    for (var i = 0; i < args.length; i++) {
      this[i] = args[i];
    }
    this.length = args.length;
  };
  Grade.prototype = ['a', 'b', 'c', 'd'];
  var g = new Grade(100, 80);
  
  g.push(90);
  console.log(g); // Grade { 0: 100, 1: 80, 2: 90, length: 3 }
  
  delete g.length;
  g.push(70);
  console.log(g); // Grade { 0: 100, 1: 80, 2: 90, ___ 4: 70, length: 5 }
```
클래스에 있는 값이 인스턴스의 동작에 영향을 줘서는 안됨. 인스턴스와의 관계에서는 구체적인 데이터를 지니지 않고 오직 인스턴스가 사용할 메서드만을 지니는 추상적인 '틀'로서만 작용하게끔 해야함.
### 7-05(Rectangle.Square 클래스)
```
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var rect = new Rectangle(3, 4);
  console.log(rect.getArea()); // 12
  
  var Square = function(width) {
    this.width = width;
  };
  Square.prototype.getArea = function() {
    return this.width * this.width;
  };
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
사용자가 정의한 두 클래스 사이에서의 상속관계를 구현함.
### 7-06(Square 클래스 변형)
```
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var rect = new Rectangle(3, 4);
  console.log(rect.getArea()); // 12
  
  var Square = function(width) {
    this.width = width;
    this.height = width;
  };
  Square.prototype.getArea = function() {
    return this.width * this.height;
  };
  
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
소스상으로도 Square를 Rectangle의 하위 클래스로 삼을 수 있다. getArea라는 메서드는 동일한 동작을 하므로 상위 클래스에서만 정의하고, 하위 클래스에서는 해당 메서드를 상속하면서 height 대신 width를 넣어주면 된다.
### 7-07(Rectangle을 상속하는 Square 클래스)
```
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var rect = new Rectangle(3, 4);
  console.log(rect.getArea()); // 12
  
  var Square = function(width) {
    Rectangle.call(this, width, width);
  };
  Square.prototype = new Rectangle();
  
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
Square의 생성자 함수 내부에서 Rectangle의 생성자 함수를 함수로써 호출했다. 이때 인자 height 자리에 width를 전달했다. 메서드를 상속하기 위해 Square의 프로토타입 객체에 Rectangle의 인스턴스를 부여했다.
### 7-08(클래스 상속 및 추상화 방법(1)-인스턴스 생성 후 프로퍼티 제거)
```
var extendClass1 = function(SuperClass, SubClass, subMethods) {
    SubClass.prototype = new SuperClass();
    for (var prop in SubClass.prototype) {
      if (SubClass.prototype.hasOwnProperty(prop)) {
        delete SubClass.prototype[prop];
      }
    }
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
  
  var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var Square = extendClass1(Rectangle, function(width) {
    Rectangle.call(this, width, width);
  });
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
extendClass1 함수는 SuperClass와 SubClass, SubClass에 추가할 메서드들이 정의된 객체를 받아서 SubClass의 prototype 내용을 정리하고 freeze하는 내용을 구성되어있다.
### 7-09(클래스 상속 및 추상화 방법(2)-빈 함수를 활용)
```
var extendClass2 = (function() {
    var Bridge = function() {};
    return function(SuperClass, SubClass, subMethods) {
      Bridge.prototype = SuperClass.prototype;
      SubClass.prototype = new Bridge();
      if (subMethods) {
        for (var method in subMethods) {
          SubClass.prototype[method] = subMethods[method];
        }
      }
      Object.freeze(SubClass.prototype);
      return SubClass;
    };
  })();
  
  var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var Square = extendClass2(Rectangle, function(width) {
    Rectangle.call(this, width, width);
  });
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
즉시실행함수 내부에서 Bridge를 선언해서 이를 클로저로 활용함으로써 메모리에 불필요한 함수 선언을 줄임. subMethods에는 SubClass의 prototype에 담길 메서드들을 객체로 전달하게끔 했음.
### 7-10(클래스 상속 및 추상화 방법(3)-Object.create활용)
```
var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var Square = function(width) {
    Rectangle.call(this, width, width);
  };
  Square.prototype = Object.create(Rectangle.prototype);
  Object.freeze(Square.prototype);
  
  var sq = new Square(5);
  console.log(sq.getArea()); // 25
```
ES5에서 도입된 Object.create를 이용한 방법. SubClass의 prototype의 __proto__가 SuperClass의 prototype을 바라보되, SuperClass의 인스턴스가 되지는 않으므로 앞서 소개한 방법들 보다 간단하면서 안전하다.
### 7-11(클래스 상속 및 추상화 방법-완성본(1)-인스턴스 생성 후 프로퍼티 제거)
```
var extendClass1 = function(SuperClass, SubClass, subMethods) {
    SubClass.prototype = new SuperClass();
    for (var prop in SubClass.prototype) {
      if (SubClass.prototype.hasOwnProperty(prop)) {
        delete SubClass.prototype[prop];
      }
    }
    SubClass.prototype.consturctor = SubClass;
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
```
SubClass.prototype.constructor가 원래의 SubClass를 바라보도록 함.
### 7-12(클래스 상속 및 추상화 방법-완성본(2)-빈 함수를 활용)
```
var extendClass2 = (function() {
    var Bridge = function() {};
    return function(SuperClass, SubClass, subMethods) {
      Bridge.prototype = SuperClass.prototype;
      SubClass.prototype = new Bridge();
      SubClass.prototype.consturctor = SubClass;
      Bridge.prototype.constructor = SuperClass;
      if (subMethods) {
        for (var method in subMethods) {
          SubClass.prototype[method] = subMethods[method];
        }
      }
      Object.freeze(SubClass.prototype);
      return SubClass;
    };
  })();
```
빈 함수를 활용한 클래스 상속 및 추상화 방법
### 7-13(클래스 상속 및 추상화 방법-완성본(3)-Object.create활용)
```
var extendClass3 = function(SuperClass, SubClass, subMethods) {
    SubClass.prototype = Object.create(SuperClass.prototype);
    SubClass.prototype.constructor = SubClass;
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
```
Object.create 활용한 클래스 상속 및 추상화 방법
### 7-14(상위 클래스 접근 수단인 super 메서드 추가)
```
var extendClass = function(SuperClass, SubClass, subMethods) {
    SubClass.prototype = Object.create(SuperClass.prototype);
    SubClass.prototype.constructor = SubClass;
    SubClass.prototype.super = function(propName) {
      // 추가된 부분 시작
      var self = this;
      if (!propName)
        return function() {
          SuperClass.apply(self, arguments);
        };
      var prop = SuperClass.prototype[propName];
      if (typeof prop !== 'function') return prop;
      return function() {
        return prop.apply(self, arguments);
      };
    }; // 추가된 부분 끝
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(SubClass.prototype);
    return SubClass;
  };
  
  var Rectangle = function(width, height) {
    this.width = width;
    this.height = height;
  };
  Rectangle.prototype.getArea = function() {
    return this.width * this.height;
  };
  var Square = extendClass(
    Rectangle,
    function(width) {
      this.super()(width, width); // super 사용 (1)
    },
    {
      getArea: function() {
        console.log('size is :', this.super('getArea')()); // super 사용 (2)
      },
    }
  );
  var sq = new Square(10);
  sq.getArea(); // size is : 100
  console.log(sq.super('getArea')()); // 100
```
SuperClass의 생성자 함수에 접근하고자 할 때는 this.super(), SuperClass의 프로토타입 메서드에 접근하고자 할 때는 this.super(propNmae)와 같이 사용하면 된다.
### 7-15(ES5와 ES6의 클래스 문법 비교)
```
var ES5 = function(name) {
    this.name = name;
  };
  ES5.staticMethod = function() {
    return this.name + ' staticMethod';
  };
  ES5.prototype.method = function() {
    return this.name + ' method';
  };
  var es5Instance = new ES5('es5');
  console.log(ES5.staticMethod()); // es5 staticMethod
  console.log(es5Instance.method()); // es5 method
  
  var ES6 = class {
    constructor(name) {
      this.name = name;
    }
    static staticMethod() {
      return this.name + ' staticMethod';
    }
    method() {
      return this.name + ' method';
    }
  };
  var es6Instance = new ES6('es6');
  console.log(ES6.staticMethod()); // es6 staticMethod
  console.log(es6Instance.method()); // es6 method
```
ES5와 ES6의 클래스 문법에 대한 비교이다.
### 7-16(ES6의 클래스 상속)
```
var Rectangle = class {
    constructor(width, height) {
      this.width = width;
      this.height = height;
    }
    getArea() {
      return this.width * this.height;
    }
  };
  var Square = class extends Rectangle {
    constructor(width) {
      super(width, width);
    }
    getArea() {
      console.log('size is :', super.getArea());
    }
  };
```
ES6의 클래스 상속에대한 코드이다.
