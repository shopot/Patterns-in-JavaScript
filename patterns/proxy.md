# Паттерн "Заместитель" (Proxy)

Паттерн "Заместитель" (Proxy) предоставляет объект-заменитель, который контролирует доступ к другому объекту (выполняет функцию контейнера).
В современном JavaScript (ES6+) этот паттерн можно легко реализовать с помощью встроенного объекта `Proxy` и вспомогательных методов `Reflect`.

## Логирующий прокси

Использование возможностей ES6 Proxy для перехвата вызовов методов и Reflect для работы с исходным объектом:

```javascript
const user = {
  name: 'Alex',
  age: 30,
};

const loggerHandler = {
  get(target, prop) {
    console.log(`Чтение свойства ${prop}`);

    return Reflect.get(target, prop);
  },

  set(target, prop, value) {
    console.log(`Запись свойства ${prop}: ${value}`);

    return Reflect.set(target, prop, value);
  },
};

const userProxy = new Proxy(user, loggerHandler);

userProxy.name; // Чтение свойства name
userProxy.age = 31; // Запись свойства age: 31
```

## Пример кэширующего прокси для API с временем жизни кэша

```javascript
const createCachedApiProxy = (api, ttl = 60000) => {
  const cache = new Map();

  // Создаем новый объект
  const cachedApi = Object.assign(api, {
    generateCacheKey(method, args) {
      return `${method}-${JSON.stringify(args)}`;
    },

    clearExpired() {
      const now = Date.now();

      for (const [key, entry] of this.cache.entries()) {
        if (now - entry.timestamp > ttl) {
          cache.delete(key);
        }
      }
    },

    clearAll() {
      cache.clear();
    },
  });

  return new Proxy(cachedApi, {
    get: (target, prop) => {
      // Пропускаем не-функции и служебные методы
      if (typeof target[prop] !== 'function' || prop === 'clearExpired') {
        return target[prop];
      }

      return async (...args) => {
        const cacheKey = target.generateCacheKey(prop, args);
        const cached = cache.get(cacheKey);

        // Проверяем есть ли свежий кэш
        if (cached && Date.now() - cached.timestamp < ttl) {
          console.log('Возвращаем данные из кэша');
          return cached.data;
        }

        // Делаем реальный запрос
        console.log('Выполняем реальный запрос к API');
        const data = await api[prop](...args);

        // Сохраняем в кэш с временной меткой
        cache.set(cacheKey, {
          data,
          timestamp: Date.now(),
        });

        return data;
      };
    },
  });
};

// Пример использования
const realApi = {
  async getUser(id) {
    console.log(`Выполнение реального запроса getUser(${id})`);

    const response = await fetch(
      `https://jsonplaceholder.typicode.com/users/${id}`
    );

    return response.json();
  },

  async getPosts(userId) {
    console.log(`Выполнение реального запроса getPosts(${userId})`);

    const response = await fetch(
      `https://jsonplaceholder.typicode.com/posts?userId=${userId}`
    );

    return response.json();
  },
};

// Создаем прокси с TTL = 5 секунд (для демонстрации)
const cachedApi = createCachedApiProxy(realApi, 5000);

// Тестируем
(async () => {
  console.log('=== Первый запрос (реальный) ===');
  console.log(await cachedApi.getUser(1));

  console.log('\n=== Повторный запрос (из кэша) ===');
  console.log(await cachedApi.getUser(1));

  console.log('\n=== Другой метод (реальный запрос) ===');
  console.log(await cachedApi.getPosts(1));

  console.log('\n=== Ожидаем 6 секунд... ===');
  await new Promise((resolve) => setTimeout(resolve, 6000));

  console.log('\n=== Запрос после истечения TTL (реальный) ===');
  console.log(await cachedApi.getUser(1));

  console.log('\n=== Очищаем кэш вручную ===');
  cachedApi.clearAll();
  console.log(await cachedApi.getUser(1));
})();
```
