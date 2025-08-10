# Паттерн "Состояние" (State)

Состояние (англ. State) — поведенческий шаблон проектирования.
Используется в тех случаях, когда во время выполнения программы объект должен менять своё поведение в зависимости от своего состояния.

Реализация управления поведением объекта в зависимости от его состояния на примере плеера с разными режимами воспроизведения:

```javascript
// Контекст (Player) делегирует работу текущему состоянию
class Player {
  constructor() {
    this.state = new PausedState(this); // Начальное состояние
  }

  changeState(state) {
    this.state = state;
  }

  play() {
    this.state.play();
  }

  pause() {
    this.state.pause();
  }
}

// Абстрактное состояние
class PlayerBaseState {
  constructor(player) {
    this.player = player;
  }

  play() {
    throw new Error('Метод play() не реализован');
  }
  pause() {
    throw new Error('Метод pause() не реализован');
  }
}

// Конкретные состояния
class PlayingState extends PlayerBaseState {
  play() {
    console.log('Уже воспроизводится');
  }

  pause() {
    console.log('Пауза');
    this.player.changeState(new PausedState(this.player));
  }
}

class PausedState extends PlayerBaseState {
  play() {
    console.log('Запуск воспроизведения');
    this.player.changeState(new PlayingState(this.player));
  }

  pause() {
    console.log('Уже на паузе');
  }
}

// Использование
const player = new Player();
player.play(); // Запуск воспроизведения
player.pause(); // Пауза
player.pause(); // Уже на паузе
```

Плеер меняет поведение методов `play()` и ё в зависимости от текущего состояния (`PlayingState` или `PausedState`), избегая условных операторов.
