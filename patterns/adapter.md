# Паттерн "Адаптер" (Adapter)

Паттерн "Адаптер" позволяет объектам с несовместимыми интерфейсами работать вместе, выступая в роли промежуточного преобразователя.
Он оборачивает один интерфейс в другой, делая их взаимодействие возможным без изменения исходного кода. Это как переходник для розетки, который позволяет включить вилку одного стандарта в розетку другого.

Адаптер полезен при интеграции старых компонентов в новые системы или при работе со сторонними библиотеками, интерфейсы которых отличаются от ожидаемых в вашем коде.

## Пример реализации

Исходный класс `Database` предоставляет два метода `createEntry` который возвращает `id` созданного элемента и метод `get` который возвращает найденный элемент по `id`.

```javascript
// Исходный класс Database
class Database {
  constructor(idGenerator) {
    this.idGenerator = idGenerator;
    this.entries = {};
  }

  createEntry(entryData) {
    const id = this.idGenerator(entryData);

    this.entries[id] = entryData;

    return id;
  }

  get(id) {
    return this.entries[id];
  }
}
```

Допустим, что новая система ожидает другой интерфейс для работы с базой данных:

```javascript
// Новый интерфейс, который ожидает наша система
class ModernDBAdapter {
  constructor(database) {
    this.database = database;
  }

  // Новый метод вместо createEntry
  insert(document) {
    const id = this.database.createEntry(document);
    return { id, document };
  }

  // Новый метод вместо get
  findById(id) {
    const document = this.database.get(id);
    if (!document) {
      throw new Error('Document not found');
    }
    return { id, document };
  }

  // Дополнительный метод, которого не было в оригинале
  exists(id) {
    return !!this.database.get(id);
  }
}
```

Пример использования:

```javascript
const uuidGenerator = (data) => Math.random().toString(36).substring(2, 9);
const oldDatabase = new Database(uuidGenerator);

// Адаптируем старую базу данных к новому интерфейсу
const adaptedDatabase = new ModernDBAdapter(oldDatabase);

// Работаем через новый интерфейс
const result = adaptedDatabase.insert({ name: 'John', age: 30 });
console.log('Inserted:', result);

const found = adaptedDatabase.findById(result.id);
console.log('Found:', found);

console.log('Exists:', adaptedDatabase.exists(result.id));
```
