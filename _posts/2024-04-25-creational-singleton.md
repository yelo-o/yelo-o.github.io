---
layout: single
classes: wide
title:  "싱글톤 패턴(생성형 디자인 패턴)"
categories: coding
tag: [coding, javascript, singleton, design-pattern]
toc: true
toc_sticky: true
author_profile: false
---
# 싱글톤 패턴 (Singleton Pattern) 가이드

## 목차
1. [싱글톤 패턴이란?](#싱글톤-패턴이란)
2. [싱글톤 패턴의 사용 이유](#싱글톤-패턴의-사용-이유)
3. [자바스크립트에서의 싱글톤 패턴 구현](#자바스크립트에서의-싱글톤-패턴-구현)
   - [클래식한 구현 방식](#클래식한-구현-방식)
   - [ES6 클래스를 이용한 구현](#es6-클래스를-이용한-구현)
   - [모듈 패턴을 이용한 구현](#모듈-패턴을-이용한-구현)
   - [ES 모듈을 이용한 구현](#es-모듈을-이용한-구현)
4. [싱글톤 패턴의 장단점](#싱글톤-패턴의-장단점)
5. [싱글톤 패턴 사용 시 고려사항](#싱글톤-패턴-사용-시-고려사항)
6. [실제 사용 사례](#실제-사용-사례)
7. [결론](#결론)

## 싱글톤 패턴이란?

싱글톤 패턴(Singleton Pattern)은 생성형 디자인 패턴(Creational Design Pattern) 중 하나로, **클래스의 인스턴스가 오직 하나만 생성되도록 보장**하고 이에 대한 전역적인 접근점을 제공하는 패턴입니다. 

이름에서 알 수 있듯이 "Single(단일)" + "ton(개체)"의 의미로, 단 하나의 객체만 존재하도록 제한하는 패턴입니다. 이 패턴은 애플리케이션이 시작될 때 해당 클래스의 인스턴스가 단 한 번만 메모리에 할당되어 사용됩니다.

## 싱글톤 패턴의 사용 이유

싱글톤 패턴을 사용하는 주요 이유는 다음과 같습니다:

1. **리소스 공유**: 데이터베이스 연결, 파일 시스템, 로깅 등과 같은 공유 리소스에 대한 접근을 제어할 때 유용합니다.
2. **메모리 효율성**: 한 번만 인스턴스화되므로 메모리 사용량을 줄일 수 있습니다.
3. **전역 접근성**: 애플리케이션의 어디서든 동일한 인스턴스에 접근할 수 있습니다.
4. **상태 공유**: 애플리케이션 전체에서 상태를 공유해야 할 때 유용합니다.
5. **설정 관리**: 애플리케이션 설정을 관리하는 데 사용됩니다.

## 자바스크립트에서의 싱글톤 패턴 구현

자바스크립트에서 싱글톤 패턴을 구현하는 방법은 여러 가지가 있습니다. 각각의 구현 방식에 대해 살펴보겠습니다.

### 클래식한 구현 방식

가장 기본적인 싱글톤 패턴 구현은 다음과 같습니다:

```javascript
const Singleton = (function() {
  let instance;
  
  function createInstance() {
    // 싱글톤 객체
    const object = new Object({
      name: '싱글톤 인스턴스',
      getTime: function() {
        return new Date().getTime();
      }
    });
    return object;
  }
  
  return {
    getInstance: function() {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// 사용 예시
const instance1 = Singleton.getInstance();
const instance2 = Singleton.getInstance();

console.log(instance1 === instance2); // true - 같은 인스턴스임을 확인
console.log(instance1.getTime()); // 현재 시간 출력
```

이 구현에서는 즉시 실행 함수(IIFE)를 사용하여 클로저를 만들고, 내부 변수인 `instance`를 외부에서 직접 접근할 수 없도록 보호합니다.

### ES6 클래스를 이용한 구현

ES6 클래스를 사용하여 싱글톤 패턴을 구현할 수도 있습니다:

```javascript
class Singleton {
  constructor() {
    if (Singleton.instance) {
      return Singleton.instance;
    }
    
    // 인스턴스 속성 정의
    this.data = [];
    this.name = '싱글톤 인스턴스';
    
    // 인스턴스 저장
    Singleton.instance = this;
  }
  
  // 메서드 정의
  addData(item) {
    this.data.push(item);
  }
  
  getData() {
    return this.data;
  }
}

// 사용 예시
const instance1 = new Singleton();
const instance2 = new Singleton();

console.log(instance1 === instance2); // true - 같은 인스턴스

instance1.addData('Item 1');
console.log(instance2.getData()); // ['Item 1'] - 데이터 공유 확인
```

이 방식에서는 생성자 내부에서 이미 인스턴스가 존재하는지 확인하고, 존재한다면 새로운 인스턴스를 생성하지 않고 기존 인스턴스를 반환합니다.

### 모듈 패턴을 이용한 구현

모듈 패턴과 함께 싱글톤을 구현하는 방법입니다:

```javascript
const DatabaseConnection = (function() {
  // 비공개 변수 및 함수
  let instance;
  let connectionString = 'mongodb://localhost:27017/mydb';
  
  // 비공개 메서드
  function connect() {
    console.log(`${connectionString}에 연결됨`);
    // 실제 연결 로직은 여기에...
  }
  
  function createInstance() {
    const object = {
      // 공개 메서드
      query: function(sql) {
        console.log(`쿼리 실행: ${sql}`);
        // 쿼리 실행 로직...
      },
      connect: function() {
        connect();
      },
      setConnectionString: function(newConnectionString) {
        connectionString = newConnectionString;
      },
      getConnectionString: function() {
        return connectionString;
      }
    };
    return object;
  }
  
  return {
    // 공개 메서드
    getInstance: function() {
      if (!instance) {
        instance = createInstance();
      }
      return instance;
    }
  };
})();

// 사용 예시
const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();

console.log(db1 === db2); // true

db1.connect(); // mongodb://localhost:27017/mydb에 연결됨
db1.setConnectionString('mongodb://localhost:27017/newdb');
console.log(db2.getConnectionString()); // mongodb://localhost:27017/newdb - 변경된 값 공유
```

이 구현은 데이터베이스 연결과 같은 실제 사용 사례에 더 가깝습니다. 비공개 변수와 메서드를 가지며, 공개 인터페이스만 노출합니다.

### ES 모듈을 이용한 구현

ES 모듈을 사용하면 자연스럽게 싱글톤을 구현할 수 있습니다:

```javascript
// singleton.js
class Configuration {
  constructor() {
    this.config = {
      apiUrl: 'https://api.example.com',
      timeout: 3000,
      debug: false
    };
  }
  
  get(key) {
    return this.config[key];
  }
  
  set(key, value) {
    this.config[key] = value;
  }
}

// 모듈에서 인스턴스 생성 및 내보내기
export default new Configuration();
```

```javascript
// app.js
import config from './singleton.js';

// 구성 값 사용
console.log(config.get('apiUrl')); // https://api.example.com

// 구성 값 변경
config.set('debug', true);
```

```javascript
// another-module.js
import config from './singleton.js';

// 다른 모듈에서도 같은 인스턴스에 접근
console.log(config.get('debug')); // true (app.js에서 변경된 값)
```

ES 모듈은 기본적으로 한 번만 평가되므로, 모듈에서 인스턴스를 생성하고 내보내면 자연스럽게 싱글톤 패턴을 구현할 수 있습니다. 이는 현대 자바스크립트 개발에서 가장 널리 사용되는 방식 중 하나입니다.

## 싱글톤 패턴의 장단점

### 장점

1. **전역 접근성**: 애플리케이션의 어디서든 동일한 인스턴스에 접근할 수 있습니다.
2. **한 번만 생성**: 리소스를 효율적으로 사용할 수 있습니다.
3. **지연 초기화(Lazy Initialization)**: 필요할 때만 인스턴스를 생성할 수 있습니다.
4. **상태 공유**: 여러 구성 요소 간에 상태를 쉽게 공유할 수 있습니다.

### 단점

1. **전역 상태**: 싱글톤은 본질적으로 전역 변수와 유사하므로, 코드의 결합도가 높아집니다.
2. **테스트 어려움**: 싱글톤은 하드 코딩된 의존성으로 작용하여 단위 테스트를 어렵게 만들 수 있습니다.
3. **동시성 문제**: 멀티스레드 환경에서는 싱글톤 접근을 동기화해야 합니다(자바스크립트는 기본적으로 싱글 스레드이지만, Web Worker 등을 사용할 경우 고려해야 함).
4. **책임이 너무 많아질 수 있음**: 싱글톤이 너무 많은 책임을 가지게 되면 단일 책임 원칙을 위반하게 됩니다.

## 싱글톤 패턴 사용 시 고려사항

1. **필요성 평가**: 정말 단일 인스턴스가 필요한지 확인해야 합니다. 무분별한 싱글톤 사용은 코드 품질을 저하시킬 수 있습니다.
2. **의존성 주입 고려**: 테스트 용이성을 위해 싱글톤 대신 의존성 주입을 고려해 볼 수 있습니다.
3. **불변성 유지**: 가능하면 싱글톤 객체를 불변(immutable)하게 유지하는 것이 좋습니다.
4. **적절한 접근 제한**: 인스턴스 생성 로직이 노출되지 않도록 주의해야 합니다.

## 실제 사용 사례

자바스크립트에서 싱글톤 패턴이 사용되는 실제 사례를 살펴보겠습니다:

### 1. 로거(Logger)

```javascript
// logger.js
class Logger {
  constructor() {
    this.logs = [];
  }
  
  log(message) {
    const timestamp = new Date().toISOString();
    this.logs.push({ message, timestamp });
    console.log(`${timestamp} - ${message}`);
  }
  
  getLogs() {
    return this.logs;
  }
}

const logger = new Logger();
Object.freeze(logger); // 인스턴스 수정 방지

export default logger;
```

```javascript
// usage.js
import logger from './logger.js';

logger.log('사용자 로그인');
logger.log('데이터 요청');

// 다른 파일에서도 동일한 로그 히스토리에 접근 가능
```

### 2. 스토어(Store) - Redux와 유사한 간단한 상태 관리

```javascript
// store.js
class Store {
  constructor() {
    this.state = {};
    this.listeners = [];
  }
  
  getState() {
    return this.state;
  }
  
  setState(newState) {
    this.state = { ...this.state, ...newState };
    this.notify();
  }
  
  subscribe(listener) {
    this.listeners.push(listener);
    // 구독 취소 함수 반환
    return () => {
      this.listeners = this.listeners.filter(l => l !== listener);
    };
  }
  
  notify() {
    this.listeners.forEach(listener => listener(this.state));
  }
}

export default new Store();
```

```javascript
// component.js
import store from './store.js';

// 상태 변경 구독
const unsubscribe = store.subscribe(state => {
  console.log('상태 업데이트:', state);
});

// 상태 업데이트
store.setState({ user: { id: 1, name: '홍길동' } });

// 구독 취소
unsubscribe();
```

## 결론

싱글톤 패턴은 자바스크립트 애플리케이션에서 리소스 공유, 상태 관리, 설정 등을 효율적으로 처리하기 위한 강력한 도구입니다. 그러나 모든 문제에 대한 만능 해결책은 아니며, 적절한 상황에서 신중하게 사용해야 합니다.

현대 자바스크립트 개발에서는 ES 모듈을 활용한 싱글톤 구현이 가장 간단하고 자연스러운 방법으로 간주됩니다. 또한 의존성 주입과 함께 사용하면 싱글톤의 테스트 용이성 문제를 완화할 수 있습니다.

싱글톤 패턴을 사용할 때는 전역 상태 관리로 인한 부작용을 최소화하고, 객체의 책임을 명확히 하는 것이 중요합니다. 올바르게 사용된다면, 싱글톤 패턴은 코드의 가독성과 유지보수성을 높이는 데 큰 도움이 될 것입니다.