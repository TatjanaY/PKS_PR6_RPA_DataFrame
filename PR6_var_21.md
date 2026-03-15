# Вариант 21 - SkillTracker: анализ выполненных учебных модулей

---

## **Часть 1**

Компания **SkillTracker** занимается отслеживанием прогресса студентов в онлайн-курсах.   
В системе регистрируются данные о прохождении учебных модулей, выполнении заданий и текущем прогрессе.  
Руководство хочет контролировать студентов с низким прогрессом, отслеживать статусы модулей и выявлять учащихся, требующих дополнительной поддержки.

Ваша задача как RPA-разработчика — автоматизировать работу с данными студентов через **DataFrame** в **Puzzle RPA**, отфильтровать записи по прогрессу, добавить аналитические столбцы, сохранить результаты и уведомить пользователя.

---

**Шаги:**

1. **Создать исходный файл `students.xlsx` с 10 записями:**

```python
# students.xlsx
| student_id | student_name | course_name   | module_name    | progress | start_date | status        |
|------------|--------------|---------------|----------------|----------|------------|---------------|
| S001       | "Иванов И."  | "Python Pro"  | "Основы"       | 85       | 2026-02-01 | "завершён"    |
| S002       | "Петров П."  | "Python Pro"  | "Функции"      | 35       | 2026-02-10 | "в процессе"  |
| S003       | "Сидоров С." | "Data Science"| "Статистика"   | 25       | 2026-02-15 | "в процессе"  |
| S004       | "Кузнецов К."| "Web Dev"     | "HTML/CSS"     | 90       | 2026-01-20 | "завершён"    |
| S005       | "Морозова М."| "Python Pro"  | "ООП"          | 40       | 2026-02-05 | "в процессе"  |
| S006       | "Новиков Н." | "Data Science"| "Pandas"       | 15       | 2026-02-20 | "в процессе"  |
| S007       | "Фролова Ф." | "Web Dev"     | "JavaScript"   | 55       | 2026-02-08 | "в процессе"  |
| S008       | "Смирнов С." | "Python Pro"  | "Асинхронность"| 30       | 2026-02-12 | "в процессе"  |
| S009       | "Егоров Е."  | "Data Science"| "Визуализация" | 20       | 2026-02-18 | "в процессе"  |
| S010       | "Лебедева Л."| "Web Dev"     | "React"        | 70       | 2026-01-25 | "в процессе"  |
```

* **Типы данных:**

```python
student_id: str
student_name: str
course_name: str
module_name: str
progress: int          # процент выполнения модуля
start_date: date
status: str            # "завершён", "в процессе", "не начат"
```

2. **Считать исходный файл `students.xlsx` в объект в DataFrame `students_df`**


3. **Добавить столбец `days_active` — дней с начала обучения:**

```python
students_df["days_active"] = (today - students_df["start_date"]).days
```

4. **Отфильтровать студентов с прогрессом < 40:**

```python
low_progress_df = FilterDataFrame(
    table=students_df,
    filter_condition='progress < 40'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="low_progress.xlsx")

# Уведомляем пользователя
NotifyUser(message="Студенты с низким прогрессом успешно сохранены в low_progress.xlsx!")
```


---

## **Часть 2**

После фильтрации студентов с низким прогрессом руководство хочет проанализировать статусы учебных модулей.   
Существует второй файл `modules.xlsx`, в котором указаны данные о модулях и их требованиях.   
Нужно объединить данные и выделить модули, которые не завершены, а результат сохранить и показать пользователю.

---

1. **Создать файл `modules.xlsx` с 8 записями:**

```python
# modules.xlsx
| module_name   | course_name   | module_status   | max_score | deadline   | instructor    |
|---------------|---------------|-----------------|-----------|------------|---------------|
| "Функции"     | "Python Pro"  | "не завершен"   | 100       | 2026-03-15 | "Петрова А."  |
| "Статистика"  | "Data Science"| "не завершен"   | 100       | 2026-03-20 | "Сидоров Д."  |
| "ООП"         | "Python Pro"  | "завершен"      | 100       | 2026-03-10 | "Петрова А."  |
| "Pandas"      | "Data Science"| "не завершен"   | 100       | 2026-03-25 | "Иванов М."   |
| "Асинхронность"| "Python Pro" | "не завершен"   | 100       | 2026-03-18 | "Кузнецова Е."|
| "Визуализация"| "Data Science"| "не завершен"  | 100       | 2026-03-22 | "Смирнов П."  |
| "JavaScript"  | "Web Dev"     | "завершен"      | 100       | 2026-03-12 | "Фролова К."  |
| "React"       | "Web Dev"     | "завершен"      | 100       | 2026-03-08 | "Егоров В."   |
```

* **Типы данных:**

```python
module_name: str
course_name: str
module_status: str   # "завершен", "не завершен"
max_score: int
deadline: date
instructor: str
```

2. **Считать исходный файл `modules.xlsx` в объект в DataFrame `modules_df`**

3. **Объединить `low_progress_df` и `modules_df` по `module_name`:**

```python
combined_df = JoinDataFrames(
    table1=low_progress_df,
    table2=modules_df,
    keys1=["module_name"],
    keys2=["module_name"],
    join_type="inner"
)
```

4. **Добавить расчет `progress_to_deadline`:**

```python
combined_df["progress_to_deadline"] = (combined_df["deadline"] - today).days
```

5. **Выбрать модули со статусом "не завершен":**

```python
incomplete_modules_df = FilterDataFrame(
    table=combined_df,
    filter_condition='module_status == "не завершен"'
)
```

6. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="incomplete_modules.xlsx")

# Уведомляем пользователя
NotifyUser(message="Аналитика по незавершённым модулям сохранена в incomplete_modules.xlsx!")
```

---