# Паттерн "Декоратор" (Decorator)

Декоратор (англ. Decorator) — это структурный шаблон проектирования, предназначенный для динамического подключения дополнительного поведения к объекту.

Паттерн "Декоратор" как и "Заместитель" использует функцию обертку, но в отличии от последнего, декоратор добавляет функциональности к объекту.

## Декоратор для логирования работы API-методов

```javascript
// Декоратор для логирования выполнения асинхронных функций (API-методов)
const withLogging =
  (apiMethod, methodName) =>
  async (...args) => {
    // Фиксируем время начала выполнения для замера длительности
    const startTime = Date.now();

    // Извлекаем данные запроса (первый аргумент) или используем пустой объект
    const requestData = args[0] || {};

    // Логируем начало запроса
    console.log(`[API] ${methodName} request:`, {
      params: requestData, // Параметры запроса
      timestamp: new Date().toISOString(), // Временная метка
    });

    try {
      // Выполняем оригинальный метод с переданными аргументами
      const result = await apiMethod.apply(this, args);

      // Логируем успешное выполнение
      console.log(`[API] ${methodName} success:`, {
        duration: `${Date.now() - startTime}ms`, // Время выполнения
        response: result, // Результат выполнения
        timestamp: new Date().toISOString(), // Временная метка
      });

      return result; // Возвращаем результат оригинальной функции
    } catch (error) {
      // Логируем ошибку
      console.error(`[API] ${methodName} error:`, {
        duration: `${Date.now() - startTime}ms`, // Время до ошибки
        error: error.message, // Сообщение об ошибке
        stack: error.stack, // Стек вызовов
        timestamp: new Date().toISOString(), // Временная метка
      });

      throw error; // Пробрасываем ошибку дальше
    }
  };

// Пример API-метода для получения пользователя
const getUser = async (userId) => {
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/users/${userId}`
  );

  return response.json();
};

// Создаем декорированную версию функции с логированием
const getUserWithLogging = withLogging(getUser, 'getUser');

// Пример использования
(async function () {
  await getUserWithLogging(1); // Вызов с логированием
})();
```
