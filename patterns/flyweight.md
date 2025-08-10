# Паттерн "Приспособленец" (Flyweight)

Паттерн "Приспособленец" (Flyweight) позволяет эффективно работать с большим количеством объектов, разделяя их общее состояние между множеством экземпляров вместо хранения одинаковых данных в каждом объекте.
Он выносит неизменяемые ("внутренние") свойства в отдельные объекты-приспособленцы, а уникальные ("внешние") свойства хранит непосредственно в объектах.

Этот подход значительно экономит память, когда система создаёт множество похожих объектов, отличающихся лишь некоторыми характеристиками.

Пример реализация шаблона "Приспособленец" для системы учета монет в бумажнике, где оптимизируется память, вынося общие свойства монет (номинал и валюта) в отдельные объекты-приспособленцы.

```javascript
// Объект-значение содержащий параметры суммы и валюты
class CoinFlyweight {
  constructor(amount, currency) {
    this.amount = amount;
    this.currency = currency;
  }
}

// Фабрика приспособленцев (хранит общие данные монет)
class CoinFlyweightFactory {
  static #flyweights = new Map();

  static get(amount, currency) {
    const flyWeightKey = `${amount}-${currency}`;

    if (this.#flyweights.get(flyWeightKey)) {
      return this.#flyweights.get(flyWeightKey);
    }

    const instance = new CoinFlyweight(amount, currency);

    this.#flyweights.set(flyWeightKey, instance);

    return instance;
  }

  static getCount() {
    return this.#flyweights.size;
  }
}

// Класс монеты (использует приспособленец)
class Coin {
  #flyweight;
  constructor(amount, currency, yearOfIssue, materials) {
    this.#flyweight = CoinFlyweightFactory.get(amount, currency);
    this.yearOfIssue = yearOfIssue;
    this.materials = materials;
  }

  get amount() {
    return this.#flyweight.amount;
  }

  get currency() {
    return this.#flyweight.currency;
  }
}

// Бумажник для хранения монет
class Wallet {
  constructor() {
    this.coins = [];
  }

  add(amount, currency, yearOfIssue, materials) {
    const coin = new Coin(amount, currency, yearOfIssue, materials);
    this.coins.push(coin);
  }

  getCount() {
    return this.coins.length;
  }

  getTotalValueForCurrency(currency) {
    return this.coins
      .filter((coin) => coin.currency === currency)
      .reduce((acc, curr) => acc + curr.amount, 0);
  }
}

// Пример использования
const wallet = new Wallet();

// Добавляем монеты (некоторые с одинаковыми характеристиками)
wallet.add(1, 'EUR', '2023', ['nickel-brass', 'nickel-plated alloy']);
wallet.add(5, 'EUR', '2022', ['nickel-brass', 'nickel-plated alloy']);
wallet.add(5, 'RUB', '2021', ['nickel-brass', 'nickel-plated alloy']);
wallet.add(5, 'RUB', '2021', ['nickel-brass', 'nickel-plated alloy']);
wallet.add(10, 'RUB', '2021', ['nickel-brass', 'cupro-nickel']);
wallet.add(5, 'USD', '1990', ['copper', 'nickel']);
wallet.add(5, 'USD', '1990', ['copper', 'nickel']);
wallet.add(1, 'USD', '2010', ['copper', 'zinc']);

console.log('\nОбщее количество монет:', wallet.getCount());
console.log(
  'Количество уникальных типов монет:',
  CoinFlyweightFactory.getCount()
);
console.log('Общая сумма в USD:', wallet.getTotalValueForCurrency('USD'));
console.log('Общая сумма в RUB:', wallet.getTotalValueForCurrency('RUB'));
console.log('Общая сумма в EUR:', wallet.getTotalValueForCurrency('EUR'));
```
