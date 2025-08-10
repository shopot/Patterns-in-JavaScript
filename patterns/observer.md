# Паттерн "Наблюдатель" (Observer)

## Реализация паттерна "Наблюдатель" для системы уведомлений

Центральный `NotificationService` уведомляет подписавшиеся компоненты (`ToastNotification`, `PushNotification`, `LogNotification`) о событиях, а те обрабатывают их по-своему (показ всплывашек, отправка пуша, логирование).

Схема работы:

- Создается централизованный сервис уведомлений (`NotificationService`), который хранит список подписчиков и умеет уведомлять их о событиях.
- Компоненты (например, всплывающие уведомления или система логов) подписываются на сервис и реализуют метод `update`, чтобы реагировать на изменения.
- При вызове `notify()` сервис автоматически рассылает данные всем подписчикам, которые обрабатывают их согласно своей логике (показ toast, отправка push-уведомления или запись в консоль).

```javascript
// Централизованный сервис уведомлений
class NotificationService {
  constructor() {
    this.observers = []; // Список подписчиков
  }

  // Метод подписки (добавление наблюдателя)
  subscribe(observer) {
    this.observers.push(observer);
    console.log('Новый подписчик добавлен');
  }

  // Метод отписки (удаление наблюдателя)
  unsubscribe(observer) {
    this.observers = this.observers.filter((obs) => obs !== observer);
    console.log('Подписчик удален');
  }

  // Уведомление всех подписчиков
  notify(data) {
    console.log(`Отправка уведомления ${data.type} всем подписчикам`);
    this.observers.forEach((observer) => observer.update(data));
  }
}

// Базовый класс Observer (интерфейс для подписчиков)
class NotificationObserver {
  update(notification) {
    throw new Error('Метод update должен быть реализован!');
  }
}

// Конкретные наблюдатели

// 1. Всплывающие уведомления в UI (Toast)
class ToastNotification extends NotificationObserver {
  update(notification) {
    console.log(`Toast: Показать "${notification.message}"`);
    // Реальная реализация:
    // toast.show(notification.message, { type: notification.type });
  }
}

// 2. Система веб-пуша (браузерные уведомления)
class PushNotification extends NotificationObserver {
  update(notification) {
    console.log(`Push: Отправить "${notification.message}"`);
    // Реальная реализация:
    // PushAPI.send(notification.userId, notification.message);
  }
}

// 3. Логирование в консоль разработчика
class LogNotification extends NotificationObserver {
  update(notification) {
    console.log(`Log: [${notification.type}] ${notification.message}`);
  }
}
```

Пример использования:

```javascript
// Создаем сервис уведомлений (Subject)
const notificationService = new NotificationService();

// Создаем подписчиков (Observers)
const toastNotifier = new ToastNotification();
const pushNotifier = new PushNotification();
const logNotifier = new LogNotification();

// Подписываем компоненты на уведомления
notificationService.subscribe(toastNotifier);
notificationService.subscribe(pushNotifier);
notificationService.subscribe(logNotifier);

// Имитируем событие, требующее уведомлений
notificationService.notify({
  type: 'success',
  message: 'Данные успешно сохранены!',
});

// Отписываем один из компонентов
notificationService.unsubscribe(pushNotifier);

// Новое событие - push-уведомление уже не придет
notificationService.notify({
  type: 'error',
  message: 'Ошибка соединения',
});
```
