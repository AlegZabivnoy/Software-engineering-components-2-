## Матриця трасування

| Вимога/UC | Трасується на | Коментар                                                               |
|---|---|------------------------------------------------------------------------|
| Req-01 | UC-03; US-01 Сценарій 1 | покрита                                                                |
| Req-02 | UC-06 | UC є, Gherkin-сценарію немає                                           |
| Req-03 | UC-07 | UC є, Gherkin-сценарію немає                                           |
| Req-04 | UC-03; UC-04; US-01 Сценарій 1 (частково); US-01 Сценарій 2 | покрита кількома сценаріями                                            |
| Req-05 | UC-03 (частково); UC-05; US-02 Сценарій 1 | покрита |                                |
| Req-07 | UC-11 | UC є, Gherkin-сценарію немає                                           |
| Req-08 | UC-11 | UC є, Gherkin-сценарію немає                                           |
| Req-09 | UC-12 | UC є, Gherkin-сценарію немає                                           |
| Req-10 | UC-12; US-04 Сценарій 1 | покрита                                                                |
| Req-11 | UC-03; UC-06; UC-07; UC-08; UC-13 | **наскрізна вимога**: без авторизації не працює жоден UC клієнта       |
| Req-12 | UC-10; US-03 Сценарій 1 | покрита                                                                |
| Req-13 | UC-10; US-03 Сценарій 2 | покрита                                                                |
| Req-14 | UC-13 | UC є, Gherkin сценарію немає                                           |
| Req-15 | UC-14; UC-09 | UC є, Gherkin-сценарію немає |
| Req-16 | UC-03 (частково); UC-05; US-02 Сценарій 2 | покрита |
| NFR-01, NFR-02 | — | **не трасуються на UC**: нефункціональні вимоги описують якість системи (надійність, безпеку), а не дії акторів |
| UC-01 | — | немає формальної вимоги (відповідає меті «Зручна навігація каталогом») |
| UC-02 | — | немає формальної вимоги                                                |

**Чому багато-до-багатьох:** UC-03 реалізує відразу три вимоги (Req-01, Req-04, Req-05), а Req-04 своєю чергою перевіряється і в UC-03, і в UC-04, і в обох сценаріях US-01 — тобто зв'язок не 1:1 в обидва боки. Req-11 є наскрізною — від неї залежать UC-03, UC-06, UC-07, UC-08 і UC-13: якщо авторизація зламана, клієнт не може зробити жодної дії з кошиком і замовленнями

## Трасування компонентів (Завдання 5)

### Компонент → попередні моделі
| Компонент          | Дані (сутності) | Вимоги / UC                                        | Взаємодія (sequence) | Процес (activity) |
|--------------------|---|----------------------------------------------------|---|---|
| WebUI              | — | UC-01…UC-03, UC-05…UC-08, UC-10…UC-14              | `WebUI` | доріжка «Система (магазин)» |
| Backend API        | — | Req-11; UC-06                                      | `Backend API` | доріжка «Система (магазин)» |
| CatalogService     | PRODUCT, CATEGORY, BRAND | UC-01, UC-02, UC-10; Req-12, Req-13                | — | — |
| AuthService        | CUSTOMER | UC-11, UC-12; Req-07…Req-11; NFR-02                | — | — |
| CartService        | CART, CART_ITEM | UC-13; Req-14                                      | — | — |
| OrderService       | ORDER, ORDER_ITEM | UC-03, UC-04, UC-07, UC-08; Req-01, Req-03, Req-04 | `OrderService` | доріжка «Система (магазин)» |
| PaymentService     | PAYMENT | UC-06; Req-02                                      | — | — |
| DeliveryService    | DELIVERY | UC-05, UC-09, UC-14; Req-05, Req-15, Req-16        | `DeliveryService` | доріжка «Система (магазин)» |
| CustomerRepository | CUSTOMER | UC-11, UC-12                                       | — | — |
| ProductRepository  | PRODUCT | UC-04; Req-01, Req-04                              | `ProductRepository` | доріжка «Система (магазин)» |
| CartRepository     | CART, CART_ITEM | UC-03, UC-13                                       | `CartRepository` | доріжка «Система (магазин)» |
| OrderRepository    | ORDER, ORDER_ITEM | UC-03, UC-07, UC-08                                | `OrderRepository` | доріжка «Система (магазин)» |
| PaymentRepository  | PAYMENT | UC-06                                              | — | — |
| DeliveryRepository | DELIVERY | UC-05, UC-09                                       | — | — |
| База даних         | усі сутності ER моделі | NFR-01                                             | — | — |
| Payment API        | PAYMENT (`provider`, `transaction_id`) | UC-06; Req-02                                      | — | — |
| Nova Poshta API    | DELIVERY (`branch_id`, `ttn`) | UC-05, UC-09; Req-05, Req-15, Req-16               | `Nova Poshta API` | доріжка «Nova Poshta API» |
| Хмарне сховище     | — | NFR-01                                             | — | — |

### Залежність → джерело
| Звідки → Куди (інтерфейс)             | Джерело |
|---------------------------------------|---|
| WebUI → IApi                          | sequence: `WebUI` викликає систему; усі UC клієнта |
| Backend API → ICatalogService         | UC-01, UC-02, UC-10 |
| Backend API → IAuthService               | Req-07, Req-09, Req-11 |
| Backend API → ICartService               | UC-13; Req-14 |
| Backend API → IOrderService              | sequence: `createOrder`; UC-07, UC-08 |
| Backend API → IPaymentService            | UC-06; Req-02 |
| Backend API → IDeliveryService           | sequence: `getBranches`; Req-05 |
| CatalogService → IProductRepository   | Req-12, Req-13 |
| AuthService → ICustomerRepository     | Req-07…Req-10 |
| CartService → ICartRepository         | Req-14 |
| OrderService → ICartRepository        | sequence: `getCartItems`, `clearCart` |
| OrderService → IProductRepository     | sequence: `getProductForUpdate`, `decreaseStock`; Req-03 |
| OrderService → IOrderRepository       | sequence: `saveOrder`; Req-03 |
| OrderService → IDeliveryService       | sequence: `saveDelivery` |
| PaymentService → IPaymentRepository   | PAYMENT; UC-06 |
| PaymentService → IOrderRepository     | Req-02 (статус замовлення) |
| PaymentService → IPaymentGateway      | UC-06; актор Payment API |
| DeliveryService → IDeliveryRepository | DELIVERY; Req-15 (`Delivery.ttn`) |
| DeliveryService → IOrderRepository    | Req-15 (статус «відправлене») |
| DeliveryService → INovaPoshta         | sequence: `getBranches`; Req-05, Req-15, Req-16 |
| Репозиторії (6) → SQL                 | ER модель |
| База даних → ICloudStorage            | NFR-01 |
| Payment API → IPaymentCallback        | UC-06; Req-02 |