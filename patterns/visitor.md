# Паттерн "Посетитель" (Visitor)

Паттерн "Посетитель" - поведенческий шаблон проектирования, который позволяет добавлять новые операции к объектам, не изменяя их классы. Он выносит логику обработки в отдельный класс-посетитель, который "ходит" по элементам структуры и выполняет нужные действия. Это удобно, когда у вас есть сложная структура объектов (например, дерево элементов), и вы хотите применять к ним разные операции (экспорт, рендеринг, валидацию), не засоряя их исходный код.

## Пример реализации банковского счета и класса посетителя для начисления процентов

```javascript
// Класс банковского счета, представляющий базовый объект структуры
class BankAccount {
  constructor(accountType = 'CURRENT', currency = 'USD', balance = 0) {
    this.accountType = accountType; // Тип счета: 'CURRENT' (текущий) или 'SAVINGS' (сберегательный)
    this.currency = currency; // Валюта счета: 'USD', 'GBP' и т.д.
    this.balance = balance; // Текущий баланс (в минимальных единицах валюты, например центах/пенсах)
  }

  // Метод для установки нового значения баланса
  setBalance(balance) {
    this.balance = balance;
  }

  // Метод для принятия посетителя (реализация паттерна Visitor)
  accept(visitor) {
    visitor.visit(this); // Передаем текущий счет посетителю для обработки
  }
}

// Класс посетителя для начисления процентов
class InterestVisitor {
  constructor(interestRate, currency) {
    this.interestRate = interestRate; // Процентная ставка (например, 105 = 5% доход)
    this.currency = currency; // Валюта, к которой применяется ставка
  }

  // Метод visit, определяющий операцию над объектом BankAccount
  visit(bankAccount) {
    // Начисляем проценты только для сберегательных счетов в указанной валюте
    if (
      bankAccount.currency === this.currency &&
      bankAccount.accountType === 'SAVINGS'
    ) {
      // Расчет нового баланса с учетом процентов
      bankAccount.setBalance((bankAccount.balance * this.interestRate) / 100);
    }
  }
}
```

Пример использования:

```javascript
// Создаем массив тестовых банковских счетов
const accounts = [
  new BankAccount('SAVINGS', 'GBP', 500), // Сберегательный счет в фунтах
  new BankAccount('SAVINGS', 'USD', 500), // Сберегательный счет в долларах
  new BankAccount('CURRENT', 'USD', 10000), // Текущий счет в долларах (проценты не начисляются)
];

// Создаем посетителей для разных валют:
// - Для USD: interestRate = 105 (5% доход)
// - Для GBP: interestRate = 110 (10% доход)
const usdInterestVisitor = new InterestVisitor(105, 'USD');
const gbpInterestVisitor = new InterestVisitor(110, 'GBP');

accounts.forEach((account) => {
  account.accept(usdInterestVisitor);
  account.accept(gbpInterestVisitor);
});

console.log(accounts);
/*
[
  BankAccount { accountType: 'SAVINGS', currency: 'GBP', balance: 550 },
  BankAccount { accountType: 'SAVINGS', currency: 'USD', balance: 525 },
  BankAccount { accountType: 'CURRENT', currency: 'USD', balance: 10000 }
]
*/
```
