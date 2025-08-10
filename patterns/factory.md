# Паттерн "Фабричный метод" (Factory Method)

Фабричный метод — это порождающий паттерн, который определяет интерфейс для создания объекта, но позволяет подклассам изменять тип создаваемых экземпляров.

📌 Основная идея:

- Класс делегирует создание объектов своим подклассам, но при этом работает с ними через общий интерфейс.
- Позволяет системе оставаться гибкой и расширяемой, не привязываясь к конкретным классам.

## Структура паттерна (Design Patterns «Банда четырёх»)

1. `Product` – абстрактный класс/интерфейс объекта, который будет создаваться.
2. `ConcreteProduct` – конкретная реализация `Product`.
3. `Creator` – абстрактный класс, объявляющий фабричный метод (`factoryMethod()`).
4. `ConcreteCreator` – переопределяет `factoryMethod()`, возвращая конкретный `ConcreteProduct`.

## Пример на JavaScript c использованием классов

```javascript
// Product - интерфейс для создаваемых объектов
class Transport {
  deliver() {
    throw new Error('Метод deliver должен быть реализован');
  }
}

// Concrete Products - конкретные реализации
class Truck extends Transport {
  deliver() {
    return 'Доставка по суше в кузове';
  }
}

class Ship extends Transport {
  deliver() {
    return 'Доставка по морю в контейнере';
  }
}

class Airplane extends Transport {
  deliver() {
    return 'Доставка по воздуху в грузовом отсеке';
  }
}

// Creator - базовый класс с фабричным методом
class Logistics {
  // Фабричный метод (может быть абстрактным)
  createTransport() {
    throw new Error('Метод createTransport должен быть реализован');
  }

  // Бизнес-логика, не зависящая от конкретного типа транспорта
  planDelivery() {
    const transport = this.createTransport();
    return `Планирование доставки: ${transport.deliver()}`;
  }
}

// Concrete Creators - переопределяют фабричный метод
class RoadLogistics extends Logistics {
  createTransport() {
    return new Truck();
  }
}

class SeaLogistics extends Logistics {
  createTransport() {
    return new Ship();
  }
}

class AirLogistics extends Logistics {
  createTransport() {
    return new Airplane();
  }
}

// Клиентский код
const clientCode = (logistics) => console.log(logistics.planDelivery());

// Использование
console.log('Доставка по суше:');
clientCode(new RoadLogistics());

console.log('\nДоставка по морю:');
clientCode(new SeaLogistics());

console.log('\nДоставка по воздуху:');
clientCode(new AirLogistics());
```

## Современный JavaScript

Ключевая особенность JavaScript — это поддержка функций как объектов первого класса (которые можно передавать, присваивать и возвращать) и возможность создавать объекты через литералы без обязательного использования классов.

```javascript
// Конкретные реализации
const createTruck = () => ({
  deliver() {
    return 'Доставка по суше в кузове';
  },
});

const createShip = () => ({
  deliver() {
    return 'Доставка по морю в контейнере';
  },
});

const createAirplane = () => ({
  deliver() {
    return 'Доставка по воздуху в грузовом отсеке';
  },
});

// Автономная функция - фабричный метод
const createTransport = (makeLogistic) => {
  return {
    transport: makeLogistic(),

    // Бизнес-логика, не зависящая от конкретного типа транспорта
    planDelivery() {
      return `Планирование доставки: ${this.transport.deliver()}`;
    },
  };
};

// Клиентский код
const clientCode = (logistics) => console.log(logistics.planDelivery());

// Использование
console.log('Доставка по суше:');
clientCode(createTransport(createTruck));

console.log('\nДоставка по морю:');
clientCode(createTransport(createShip));

console.log('\nДоставка по воздуху:');
clientCode(createTransport(createAirplane));
```

В JavaScript прямое использование функций удобнее, чем наследование классов, так как нет необходимости явно проверять переопределение методов, а поддержка функций первого класса делает этот паттерн менее нужным, чем в языках с абстрактными методами, особенно учитывая, что ранние версии JavaScript вообще не имели синтаксиса классов.
