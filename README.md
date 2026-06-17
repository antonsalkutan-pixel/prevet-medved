# АИС «Аптечный склад»
> Spring Boot 3.2 · Spring JDBC · MySQL 8

## Структура проекта

```
src/main/java/com/pharmacy/warehouse/
├── model/
│   ├── Medication.java          # Медикамент (название, доза, форма)
│   ├── MedicationForm.java      # Enum: форма выпуска
│   ├── WarehouseStock.java      # Складской остаток
│   └── StockOperation.java      # Операция прихода/расхода
├── repository/
│   ├── MedicationRepository.java
│   ├── WarehouseStockRepository.java
│   └── StockOperationRepository.java
├── service/
│   ├── MedicationService.java
│   └── WarehouseService.java
├── controller/
│   ├── MedicationController.java
│   └── WarehouseController.java
└── exception/
    ├── ResourceNotFoundException.java
    ├── InsufficientStockException.java
    └── GlobalExceptionHandler.java
```

## Запуск

### 1. Создать БД MySQL
```sql
CREATE DATABASE pharmacy_warehouse CHARACTER SET utf8mb4;
```

### 2. Настроить подключение
Отредактировать `src/main/resources/application.properties`:
```properties
spring.datasource.url=jdbc:mysql://localhost:3306/pharmacy_warehouse
spring.datasource.username=root
spring.datasource.password=YOUR_PASSWORD
```

### 3. Запустить приложение
```bash
mvn spring-boot:run
```
Схема и тестовые данные создадутся автоматически.

---

## API Reference

### Медикаменты `/api/medications`

| Метод  | URL                        | Описание                  |
|--------|----------------------------|---------------------------|
| GET    | /api/medications           | Список всех медикаментов  |
| GET    | /api/medications/{id}      | Получить по ID            |
| GET    | /api/medications/search?name= | Поиск по названию     |
| GET    | /api/medications/form?form=   | Фильтр по форме выпуска|
| POST   | /api/medications           | Создать медикамент        |
| PUT    | /api/medications/{id}      | Обновить медикамент       |
| DELETE | /api/medications/{id}      | Удалить медикамент        |

### Склад `/api/warehouse`

| Метод  | URL                              | Описание                     |
|--------|----------------------------------|------------------------------|
| GET    | /api/warehouse/stock             | Все складские остатки        |
| GET    | /api/warehouse/stock/{id}        | Позиция по ID                |
| GET    | /api/warehouse/stock/expiring?days=30 | Истекающие скоро      |
| GET    | /api/warehouse/stock/low?threshold=10 | С низким остатком     |
| POST   | /api/warehouse/stock             | Добавить складскую позицию   |
| PUT    | /api/warehouse/stock/{id}        | Обновить позицию             |
| POST   | /api/warehouse/operations/receive  | Оприходовать товар         |
| POST   | /api/warehouse/operations/dispense | Отпустить/списать товар    |
| GET    | /api/warehouse/operations        | Журнал всех операций         |
| GET    | /api/warehouse/operations/type?type=IN | Операции по типу      |

---

## Примеры запросов

### Создать медикамент
```bash
curl -X POST http://localhost:8080/api/medications \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Аспирин",
    "dosage": "500 мг",
    "form": "TABLET",
    "manufacturer": "Байер"
  }'
```

### Оприходовать товар
```bash
curl -X POST http://localhost:8080/api/warehouse/operations/receive \
  -H "Content-Type: application/json" \
  -d '{
    "medicationId": 1,
    "quantity": 100,
    "unitPrice": 15.50,
    "documentNumber": "ПН-2025-010",
    "reason": "Плановое пополнение"
  }'
```

### Отпустить товар
```bash
curl -X POST http://localhost:8080/api/warehouse/operations/dispense \
  -H "Content-Type: application/json" \
  -d '{
    "medicationId": 1,
    "quantity": 20,
    "unitPrice": 15.50,
    "documentNumber": "РН-2025-005",
    "reason": "Отпуск в аптеку №3"
  }'
```

### Проверить истекающие товары
```bash
curl http://localhost:8080/api/warehouse/stock/expiring?days=60
```

## Формы выпуска (MedicationForm)
`TABLET` · `CAPSULE` · `SOLUTION` · `SUSPENSION` · `OINTMENT` · `SUPPOSITORY` · `INJECTION` · `DROPS` · `SPRAY` · `POWDER`
