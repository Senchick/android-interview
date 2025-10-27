### Принципы и шаблоны проектирования

#### *SOLID*
**SOLID** — это аббревиатура, обозначающая пять базовых принципов объектно-ориентированного программирования и дизайна, которые помогают разработчикам создавать более устойчивые, удобные в обслуживании и расширяемые системы.

- #### S — Принцип единственной ответственности (Single Responsibility Principle)
  Этот принцип гласит, что класс должен иметь только одну причину для изменения. Это означает, что класс должен выполнять только одну задачу или иметь одну область ответственности.
    ```kotlin
  // Нарушение принципа
  class UserSettings {
      fun changeUserEmail(user: User, newEmail: String) {
          // Изменение email
      }

      fun printUserReport(user: User) {
          // Печать отчета пользователя
      }
  }

  // Соблюдение принципа
  class EmailManager {
      fun changeUserEmail(user: User, newEmail: String) {
          // Изменение email
      }
  }

  class ReportPrinter {
      fun printUserReport(user: User) {
          // Печать отчета пользователя
      }
  }
  ```
- #### O — Принцип открытости/закрытости (Open/Closed Principle)
  Классы должны быть открыты для расширения, но закрыты для модификации. Это означает, что можно добавлять новые функциональности, не изменяя существующий код.
  ```kotlin
  // Нарушение принципа
  class GraphicEditor {
      fun drawShape(shape: Shape) {
          when (shape) {
              is Circle -> drawCircle(shape)
              is Square -> drawSquare(shape)
              // При добавлении новых фигур необходимо изменять этот класс
          }
      }
  
      fun drawCircle(circle: Circle) { /*...*/ }
      fun drawSquare(square: Square) { /*...*/ }
  }
  
  // Соблюдение принципа
  abstract class Shape {
      abstract fun draw()
  }
  
  class Circle : Shape() {
      override fun draw() { /* Рисование круга */ }
  }
  
  class Square : Shape() {
      override fun draw() { /* Рисование квадрата */ }
  }
  
  class GraphicEditor {
      fun drawShape(shape: Shape) {
          shape.draw()
      }
  }
  ```
- #### L — Принцип подстановки Барбары Лисков (Liskov Substitution Principle)
  Объекты в программе можно заменить их наследниками без изменения свойств программы. Наследуемый класс должен дополнять, а не заменять поведение базового класса.
  ```kotlin
    // Нарушение принципа
    open class Bird {
        open fun fly() {
            // реализация полета
        }
    }

    class Penguin : Bird() {
        override fun fly() {
            throw UnsupportedOperationException("Пингвины не умеют летать.")
        }
    }
  
    // Соблюдение принципа
    open class Bird

    open class FlyingBird : Bird() {
        open fun fly() {
            // реализация полета
        }
    }

    class Penguin : Bird()

  ```
- #### I — Принцип разделения интерфейса (Interface Segregation Principle)
  Клиенты не должны быть вынуждены реализовывать интерфейсы, которые они не используют. Интерфейсы должны быть специфическими, а не универсальными.
  ```kotlin
  // Нарушение принципа
  interface Worker {
    fun work()
    fun eat()
  }
  
  class Human : Worker {
    override fun work() { /* Работаем */ }
    override fun eat() { /* Едим */ }
  }
  
  class Robot : Worker {
    override fun work() { /* Работаем */ }
    override fun eat() { /* Роботы не едят, нарушение принципа */ }
  }
  
  // Соблюдение принципа
  interface Workable {
    fun work()
  }
  
  interface Eatable {
    fun eat()
  }
  
  class Human : Workable, Eatable {
    override fun work() { /* Работаем */ }
    override fun eat() { /* Едим */ }
  }
  
  class Robot : Workable {
    override fun work() { /* Работаем */ }
  }
  ```
- #### D — Принцип инверсии зависимостей (Dependency Inversion Principle)
  Модули высокого уровня не должны зависеть от модулей низкого уровня. Оба типа модулей должны зависеть от абстракций. Абстракции не должны зависеть от деталей. Детали должны зависеть от абстракций.
  ```kotlin
  // Нарушение принципа
  class LightBulb {
    fun turnOn() { /* Включение */ }
    fun turnOff() { /* Выключение */ }
  }
  
  class ElectricPowerSwitch {
    var bulb: LightBulb = LightBulb()
  
    fun press() {
        if (bulb.turnOn()) {
            bulb.turnOff()
        } else {
            bulb.turnOn()
        }
    }
  }
  
  // Соблюдение принципа
  interface Switchable {
    fun turnOn()
    fun turnOff()
  }
  
  class LightBulb : Switchable {
    override fun turnOn() { /* Включение */ }
    override fun turnOff() { /* Выключение */ }
  }
  
  class ElectricPowerSwitch(var device: Switchable) {
    fun press() {
        if (device.turnOn()) {
            device.turnOff()
        } else {
            device.turnOn()
        }
    }
  }

  ```
#### *Design Patterns, GoF (Gang of Four) банда четырех*
- #### Порождающие паттерны (Creational)
  Эти паттерны отвечают за удобное и безопасное создание новых объектов или даже целых семейств объектов.
  <br/>
    - Фабричный метод (Factory Method)
    - Абстрактная фабрика (Abstract Factory)
    - Строитель (Builder)
    - Прототип (Prototype)
    - Одиночка (Singleton)

- #### Структурные паттерны (Structural)
  Эти паттерны отвечают за построение удобных в поддержке иерархий классов.
  <br/>
    - Адаптер (Adapter)
    - Мост (Bridge)
    - Компоновщик (Composite)
    - Декоратор (Decorator)
    - Фасад (Facade)
    - Легковес (Flyweight)
    - Заместитель (Proxy)
- #### Поведенческие паттерны (Behavioral)
  Эти паттерны решают задачи эффективного и безопасного взаимодействия между объектами программы.
  <br/>
    - Цепочка обязанностей (Chain Of Responsibility)
    - Команда (Command)
    - Итератор (Iterator)
    - Посредник (Mediator)
    - Снимок (Memento)
    - Наблюдатель (Observer)
    - Состояние (State)
    - Стратегия (Strategy)
    - Шаблонный метод (Template Method)
    - Посетитель (Visitor)
