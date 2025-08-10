# Паттерн "Прототип" (Prototype)

Паттерн проектирования "Прототип" позволяет создавать новые объекты путем клонирования существующих экземпляров (прототипов).

## Пример реализации со свойством `prototype`

```javascript
class BoardSquare {
  constructor(color, row, file) {
    this.color = color;
    this.row = row;
    this.file = file;
    this.piece = startingPiece;
  }
}
```

Класс `BoardSquarePrototype` — его конструктор принимает свойство `prototype`, сохраняемое в экземпляре, предоставляет метод `clone()`, не принимающий никаких аргументов и возвращающий экземпляр `BoardSquare` с копией всех свойств `prototype`.

```javascript
class BoardSquarePrototype {
  constructor(prototype) {
    this.prototype = prototype;
  }

  clone() {
    const boardSquare = new BoardSquare();

    boardSquare.color = this.prototype.color;
    boardSquare.row = this.prototype.row;
    boardSquare.file = this.prototype.file;

    return boardSquare;
  }
}
```

Пример использования:

```javascript
const boardSquare = new BoardSquare('white');
const boardSquarePrototype = new BoardSquarePrototype(boardSquare);
const boardSquareTwo = boardSquarePrototype.clone();
// ...
const boardSquareLast = boardSquarePrototype.clone();
```

Клонированные экземпляры содержат одно и то же значение для свойства `color`, но представляют собой разные экземпляры объекта `BoardSquare`.

## Паттерн "Прототип" с помощью современного JavaScript

Реализация с копированием свойств `this.prototype` имеет один существенный недостаток,
добавление или удаление новых переменных в конструкторе `BoardSquare` влечет за собой изменение в методе `BoardSquarePrototype.clone`.

Что бы сделать класс-прототип устойчивым к изменениям переменных в конструкторе класса `BoardSquare`, можно использовать `Object.assign`:

```javascript
class BoardSquarePrototype {
  constructor(prototype) {
    this.prototype = prototype;
  }

  clone() {
    return Object.assign(new BoardSquare(), this.prototype);
  }
}
```

## Паттерн "Прототип" без классов в JavaScript

Один из способов клонировать объекты с их методами — использовать метод `Object.create`.

```javascript
const square = {
  color: 'white',

  occupySquare(piece) {
    this.piece = piece;
  },

  clearSquare() {
    this.piece = null;
  },
};

const otherSquare = Object.create(square);
```

`Object.create` ничего не копирует, он просто создает новый объект и устанавливает объект `square` в качестве прототипа.

## Подклассы в JavaScript как прототип объекта

```javascript
class Square {
  constructor() {}

  occupySquare(piece) {
    this.piece = piece;
  }

  clearSquare() {
    this.piece = null;
  }
}

class BlackSquare extends Square {
  constructor() {
    super();
    this.color = 'black';
  }
}
```

Выражение `class BlackSquare extends Square` задает свойство `prototype.__proto__` объекта `BlackSquare` прототипу `Square.prototype`.

```javascript
console.assert(
  BlackSquare.prototype.__proto__ === Square.prototype,
  'subclass prototype has prototype of superclass'
);
```
