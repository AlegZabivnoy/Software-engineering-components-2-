## 1 - будування use case діаграми
**Промт**
> Побудуй use case діаграму спираючись на мій spec.md (Use Case Diagram (PlantUML / Mermaid) з ≥2 типами акторів, include/extend, узагальненням.) зроби в мермейд

**Відповідь від AI**
```mermaid
flowchart LR
    Guest("Guest<br/>(Гість)")
    Customer("Customer<br/>(Клієнт)")
    Pay[["Payment API<br/>(зовнішня система)"]]
    NP[["Nova Poshta API<br/>(зовнішня система)"]]

    subgraph System["Онлайн-магазин техніки"]
        UC01(["UC-01 Переглянути каталог"])
        UC02(["UC-02 Відфільтрувати товари"])
        UC03(["UC-03 Оформити замовлення"])
        UC04(["UC-04 Перевірити наявність товару"])
        UC05(["UC-05 Обрати відділення Нової Пошти"])
        UC06(["UC-06 Оплатити замовлення"])
        UC07(["UC-07 Скасувати замовлення"])
        UC08(["UC-08 Переглянути історію та статус замовлень"])
        UC09(["UC-09 Зареєструвати ТТН"])
    end

    Customer ==>|"узагальнення (Customer успадковує Guest)"| Guest

    Guest --- UC01
    Guest --- UC02
    Customer --- UC03
    Customer --- UC06
    Customer --- UC07
    Customer --- UC08
    Pay --- UC06
    NP --- UC05
    NP --- UC09

    UC03 -. "«include»" .-> UC04
    UC05 -. "«extend» (доставка = Нова Пошта)" .-> UC03
    UC02 -. "«extend» (користувач задає фільтр)" .-> UC01
```