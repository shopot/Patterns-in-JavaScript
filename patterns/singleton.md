# Паттерн "Одиночка" (Singleton)

Паттерн "Одиночка" — это порождающий шаблон проектирования, который гарантирует, что у класса есть только один экземпляр, и предоставляет глобальную точку доступа к этому экземпляру.

## Классическая реализация (с классом)

```javascript
class Singleton {
  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;

      this.value = Math.random(); // Пример данных
    }

    return Singleton.instance;
  }

  static getInstance() {
    if (!this.instance) {
      this.instance = new Singleton();
    }

    return this.instance;
  }
}

// Использование конструктор класса
const instance1 = new Singleton();
const instance2 = new Singleton();

console.log(instance1.value, instance2.value);

// Или через статический метод
const instance3 = Singleton.getInstance();
const instance4 = Singleton.getInstance();

console.log(instance3.value, instance4.value);
```

## Реализация с замыканием (без класса)

Пример с использованием IIFE (от англ. Immediately Invoked Function Expression «немедленно вызываемое функциональное выражение»).

```javascript
const Singleton = (function () {
  let instance;

  const createInstance = () => ({
    value: Math.random(),

    showValue() {
      console.log(this.value);
    },
  });

  return {
    getInstance() {
      if (!instance) {
        instance = createInstance();
      }

      return instance;
    },
  };
})();

// Использование
const instance1 = Singleton.getInstance();
const instance2 = Singleton.getInstance();

console.log(instance1 === instance2); // true

instance1.showValue();
instance2.showValue(); // Выведет то же значение
```

## Модульный подход (современный ES6+)

Вот отформатированное предложение:

Система модулей JavaScript имеет следующее поведение кэширования: если модуль загружен, то все последующие импорты экспортов модуля будут кэшированными экземплярами экспортов.

```javascript
// CounterSingleton.js
let value = 0;

export const counter = {
  increment() {
    return ++value;
  },

  decrement() {
    return --value;
  },

  getValue() {
    return value;
  },
};
```

Использование (в другом файле):

```javascript
await Promise.all([
  import('./CounterSingleton.js'),
  import('./CounterSingleton.js'),
]).then(([import1, import2]) => {
  import1.counter.increment();
  import2.counter.increment();

  console.log(import1.counter.getValue()); // 2
  console.log(import2.counter.getValue()); // 2
});
```

Оба варианта выражения `import('./CounterSingleton.js')` приводят к одному и тому же объекту,
они возвращают один и тот же объект, потому что результат операции `import` для данного модуля является одиночкой.
