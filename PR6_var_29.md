# Вариант 29 - SubscriptionFinance: анализ просроченных платежей по подпискам

---

## **Часть 1**

Компания **SubscriptionFinance** занимается учётом подписок и регулярных платежей клиентов в сервисной платформе.   
В системе регистрируются данные о статусах подписок, датах продления и история платежей.  
Руководство хочет контролировать активные подписки, отслеживать задержки оплат и выявлять клиентов с просроченными платежами.

Ваша задача как RPA-разработчика — автоматизировать работу с данными подписок через **DataFrame** в **Puzzle RPA**, отфильтровать подписки по статусу, добавить аналитические столбцы, сохранить результаты и уведомить пользователя.

---

**Шаги:**

1. **Создать исходный файл `subscriptions.xlsx` с 10 записями:**

```python
# subscriptions.xlsx
| sub_id | client_name   | subscription_type | subscription_status | start_date | monthly_price | renewal_date |
|--------|---------------|-------------------|-------------------|------------|---------------|--------------|
| SUB001 | "Иванов И."   | "премиум"         | "активна"         | 2026-01-15 | 990           | 2026-03-15   |
| SUB002 | "Петров П."   | "базовая"         | "активна"         | 2026-02-01 | 490           | 2026-03-01   |
| SUB003 | "Сидоров С."  | "премиум"         | "приостановлена"  | 2026-01-20 | 990           | 2026-03-20   |
| SUB004 | "Кузнецов К." | "базовая"         | "активна"         | 2026-02-10 | 490           | 2026-03-10   |
| SUB005 | "Морозова М." | "про"             | "активна"         | 2026-01-05 | 1490          | 2026-03-05   |
| SUB006 | "Новиков Н."  | "премиум"         | "отменена"        | 2026-02-15 | 990           | 2026-03-15   |
| SUB007 | "Фролова Ф."  | "базовая"         | "активна"         | 2026-02-05 | 490           | 2026-03-05   |
| SUB008 | "Смирнов С."  | "про"             | "активна"         | 2026-01-25 | 1490          | 2026-03-25   |
| SUB009 | "Егоров Е."   | "премиум"         | "активна"         | 2026-02-20 | 990           | 2026-03-20   |
| SUB010 | "Лебедева Л." | "базовая"         | "активна"         | 2026-01-30 | 490           | 2026-03-30   |
```

* **Типы данных:**

```python
sub_id: str
client_name: str
subscription_type: str   # "базовая", "премиум", "про"
subscription_status: str # "активна", "приостановлена", "отменена"
start_date: date
monthly_price: int
renewal_date: date
```

2. **Считать исходный файл `subscriptions.xlsx` в объект в DataFrame `subscriptions_df`**


3. **Добавить столбец `days_until_renewal` — дней до продления:**

```python
subscriptions_df["days_until_renewal"] = (subscriptions_df["renewal_date"] - today).days
```

4. **Отфильтровать подписки со статусом "активна":**

```python
active_subscriptions_df = FilterDataFrame(
    table=subscriptions_df,
    filter_condition='subscription_status == "активна"'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="active_subscriptions.xlsx")

# Уведомляем пользователя
NotifyUser(message="Активные подписки успешно сохранены в active_subscriptions.xlsx!")
```


---

## **Часть 2**

После фильтрации активных подписок руководство хочет проанализировать историю платежей клиентов.   
Существует второй файл `payments.xlsx`, в котором указаны данные о фактических оплатах и задержках.   
Нужно объединить данные и выделить подписки с задержкой платежа более 5 дней, а результат сохранить и показать пользователю.

---

1. **Создать файл `payments.xlsx` с 8 записями:**

```python
# payments.xlsx
| sub_id | payment_date | payment_amount | payment_status | payment_delay | invoice_id |
|--------|--------------|----------------|----------------|---------------|------------|
| SUB001 | 2026-03-10   | 990            | "оплачено"     | 0             | "INV-001"  |
| SUB002 | 2026-03-08   | 490            | "оплачено"     | 7             | "INV-002"  |
| SUB004 | 2026-03-12   | 490            | "ожидается"    | 2             | "INV-004"  |
| SUB005 | 2026-03-01   | 1490           | "оплачено"     | 0             | "INV-005"  |
| SUB007 | 2026-03-14   | 490            | "ожидается"    | 9             | "INV-007"  |
| SUB008 | 2026-03-20   | 1490           | "оплачено"     | 0             | "INV-008"  |
| SUB009 | 2026-03-18   | 990            | "ожидается"    | 6             | "INV-009"  |
| SUB010 | 2026-03-25   | 490            | "оплачено"     | 0             | "INV-010"  |
```

* **Типы данных:**

```python
sub_id: str
payment_date: date
payment_amount: int
payment_status: str   # "оплачено", "ожидается", "отклонено"
payment_delay: int    # дней задержки платежа
invoice_id: str
```

2. **Считать исходный файл `payments.xlsx` в объект в DataFrame `payments_df`**

3. **Объединить `active_subscriptions_df` и `payments_df` по `sub_id`:**

```python
combined_df = JoinDataFrames(
    table1=active_subscriptions_df,
    table2=payments_df,
    keys1=["sub_id"],
    keys2=["sub_id"],
    join_type="inner"
)
```

4. **Добавить расчет `total_delay_cost`:**

```python
combined_df["total_delay_cost"] = combined_df["payment_delay"] * combined_df["monthly_price"] * 0.01
```

5. **Выбрать подписки с задержкой платежа > 5 дней:**

```python
delayed_payments_df = FilterDataFrame(
    table=combined_df,
    filter_condition='payment_delay > 5'
)
```

6. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="delayed_subscription_payments.xlsx")

# Уведомляем пользователя
NotifyUser(message="Аналитика по подпискам с просроченными платежами сохранена в delayed_subscription_payments.xlsx!")
```

---