# gRPC

> **Remote Procedure Call** — высокопроизводительный RPC-фреймворк от Google.

## Что такое gRPC

gRPC позволяет вызывать **функции на удалённом сервере как локальные**. Основан на **HTTP/2** и **Protocol Buffers** (бинарный формат).

## Ключевые особенности

1. **HTTP/2** — мультиплексирование, сжатие заголовков, один TCP-коннект
2. **Protobuf** — бинарная сериализация данных (быстрее JSON, компактнее)
3. **Строгий контракт** — .proto файл определяет всё на 100%
4. **Четыре типа вызовов**: unary, server-streaming, client-streaming, bidirectional

## Схема работы

```
 .proto файл (контракт)
 ┌────────────────────────────┐
 │ service OrderService {      │
 │   rpc CreateOrder(OrderRequest)   │
 │        returns (OrderResponse);  │
 │ }                           │
 └────────────────────────────┘
        │ генерация кода
        ▼
 Клиент (stub) ──── HTTP/2 ──── Сервер (implementation)
```

## Типы вызовов gRPC

| Тип | Описание | Пример |
|-----|----------|--------|
| **Unary** | Запрос → Ответ (как REST) | Создать заказ |
| **Server streaming** | Запрос → Поток ответов | Подписка на обновления |
| **Client streaming** | Поток запросов → Ответ | Загрузка файла частями |
| **Bidirectional** | Оба потока одновременно | Чат, real-time |

## Пример .proto файла

```protobuf
syntax = "proto3";

package orders;

service OrderService {
  rpc CreateOrder (CreateOrderRequest) returns (Order);
  rpc ListOrders (ListOrdersRequest) returns (stream Order);  // server streaming
}

message CreateOrderRequest {
  string user_id = 1;
  string product_id = 2;
  int32 quantity = 3;
}

message Order {
  string id = 1;
  string user_id = 2;
  string product_id = 3;
  int32 quantity = 4;
  string status = 5;
}

message ListOrdersRequest {
  string user_id = 1;
}
```

## Пример клиента (Python)

```python
import grpc
import orders_pb2
import orders_pb2_grpc

with grpc.insecure_channel("localhost:50051") as channel:
    stub = orders_pb2_grpc.OrderServiceStub(channel)

    response = stub.CreateOrder(
        orders_pb2.CreateOrderRequest(
            user_id="u-42",
            product_id="p-99",
            quantity=2,
        )
    )
    print(f"Заказ создан: {response.id}, статус: {response.status}")
```

## Когда применять gRPC

**Применять:**
- Взаимодействие **микросервисов** внутри инфраструктуры
- Высоконагруженные системы (нужна производительность)
- Когда нужен **строгий контракт** (языковая агностичность + типы)
- Стриминг, real-time данные (котировки, логи, телеметрия)
- Мобильные/десктоп приложения к бэкенду (меньше трафика)

**НЕ применять:**
- **Публичные API** для внешних клиентов (сложно потребителям)
- Браузерные клиенты напрямую (нужен proxy/grpc-web)
- Простые CRUD-задачи без высоких требований (оверкилл)
- Интеграции с легаси

## gRPC vs REST

| Критерий | gRPC | REST |
|----------|------|------|
| Протокол | HTTP/2 | HTTP/1.1 (обычно) |
| Формат | Protobuf (бинарный) | JSON (текстовый) |
| Скорость | Выше | Ниже |
| Размер данных | Компактнее | Больше |
| Контракт | .proto (строгий, обязательный) | OpenAPI (опционально) |
| Стриминг | Встроенный | Нет (нужен WebSocket) |
| Читаемость | Низкая (бинарный) | Высокая (JSON) |
| Отладка | Сложнее | Проще | 
| Публичность | Неудобен клиентам | Стандарт |
| Инструменты | protoc, grpcurl | curl, Postman |

## Вопросы для самопроверки

1. Какие четыре типа вызовов поддерживает gRPC?
2. Почему gRPC быстрее REST? (2 причины)
3. Для чего нужен .proto файл?
4. Почему gRPC не подходит для публичных API?
5. Какая есть альтернатива браузеру для работы с gRPC?