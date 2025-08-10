# Паттерн "Стратегия" (Strategy)

Стратегия (англ. Strategy) — поведенческий шаблон проектирования, он позволяет динамически выбирать нужный способ решения задачи (например, разные способы оплаты, сортировки или доставки), не переписывая основной код.

Паттерн "Стратегия" нужен, чтобы легко менять алгоритмы прямо во время работы программы. Он выносит разные способы выполнения одной задачи в отдельные классы, позволяя выбирать нужный вариант без переписывания основного кода. Например, как выбор способа оплаты (карта, PayPal, криптовалюта) в интернет-магазине — система просто подставляет нужный алгоритм, когда пользователь решает, как платить.

Пример реализации различных способ оплаты в корзине:

```javascript
// Контекст (корзина) использует стратегию оплаты
class ShoppingCart {
  constructor(paymentStrategy) {
    this.paymentStrategy = paymentStrategy;
    this.items = [];
  }

  setPaymentStrategy(strategy) {
    this.paymentStrategy = strategy;
  }

  checkout(amount) {
    this.paymentStrategy.pay(amount);
  }
}

// Стратегии оплаты
class CreditCardPayment {
  pay(amount) {
    console.log(`Оплата ${amount} руб. картой`);
  }
}

class PayPalPayment {
  pay(amount) {
    console.log(`Оплата ${amount} руб. через PayPal`);
  }
}

class CryptoPayment {
  pay(amount) {
    console.log(`Оплата ${amount} руб. криптовалютой`);
  }
}

// Использование
const cart = new ShoppingCart(new CreditCardPayment());
cart.checkout(1000); // Оплата 1000 руб. картой

cart.setPaymentStrategy(new PayPalPayment());
cart.checkout(2000); // Оплата 2000 руб. через PayPal
```
