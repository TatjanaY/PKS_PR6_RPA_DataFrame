# Вариант 20 - CourseAnalytics: анализ прохождения курсов студентами

---

## **Часть 1**

Компания **CourseAnalytics** занимается мониторингом обучения студентов на образовательной платформе.   
Ежедневно обрабатываются данные о студентах: часть проходит курсы по программированию, часть — по дизайну, а часть — по маркетингу.  
Руководство хочет контролировать студентов, изучающих курс "Python", отслеживать их прогресс и выявлять учащихся с низкой успеваемостью.

Ваша задача как RPA-разработчика — автоматизировать работу с данными о студентах через **DataFrame** в **Puzzle RPA**, отфильтровать студентов по курсу, добавить аналитические столбцы, сохранить результаты и уведомить пользователя.

---

**Шаги:**

1. **Создать исходный файл `students.xlsx` с 10 записями:**

```python
# students.xlsx
| student_id | student_name  | course        | enrollment_date | progress | status     |
|------------|---------------|---------------|-----------------|----------|------------|
| S001       | "Иванов И."   | "Python"      | 2026-01-15      | 75       | "активен"  |
| S002       | "Петров П."   | "JavaScript"  | 2026-02-01      | 45       | "активен"  |
| S003       | "Сидоров С."  | "Python"      | 2026-01-20      | 32       | "активен"  |
| S004       | "Кузнецов К." | "Data Science"| 2026-02-10      | 60       | "активен"  |
| S005       | "Морозова М." | "Python"      | 2026-01-25      | 88       | "активен"  |
| S006       | "Новиков Н."  | "JavaScript"  | 2026-02-05      | 28       | "активен"  |
| S007       | "Фролова Ф."  | "Python"      | 2026-02-12      | 41       | "активен"  |
| S008       | "Смирнов С."  | "Data Science"| 2026-01-30      | 55       | "активен"  |
| S009       | "Егоров Е."   | "Python"      | 2026-02-08      | 19       | "активен"  |
| S010       | "Лебедева Л." | "Маркетинг"   | 2026-01-18      | 92       | "активен"  |
```

* **Типы данных:**

```python
student_id: str
student_name: str
course: str  # "Python", "JavaScript", "Data Science", "Маркетинг"
enrollment_date: date
progress: int  # прогресс в процентах (0-100)
status: str  # "активен", "завершен", "отчислен"
```

2. **Считать исходный файл `students.xlsx` в объект в DataFrame `students_df`**


3. **Добавить столбец `is_new` — флаг нового студента (записался за последние 30 дней):**

```python
students_df["is_new"] = students_df["enrollment_date"] > (today - 30)
```

4. **Отфильтровать студентов курса "Python":**

```python
python_students_df = FilterDataFrame(
    table=students_df,
    filter_condition='course == "Python"'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="python_students.xlsx")

# Уведомляем пользователя
NotifyUser(message="Студенты курса Python успешно сохранены в python_students.xlsx!")
```


---

## **Часть 2**

После фильтрации студентов курса "Python" руководство хочет проанализировать результаты выполненных заданий.   
Существует второй файл `assignments.xlsx`, в котором указаны оценки за домашние задания.   
Нужно объединить данные и выделить студентов с оценкой за задание ниже 60 баллов, а результат сохранить и показать пользователю.

---

1. **Создать файл `assignments.xlsx` с 8 записями:**

```python
# assignments.xlsx
| student_id | assignment_name | assignment_score | submission_date |
|------------|-----------------|------------------|-----------------|
| S001       | "Функции"       | 85               | 2026-03-05      |
| S002       | "Циклы"         | 72               | 2026-03-06      |
| S003       | "Функции"       | 45               | 2026-03-04      |
| S004       | "Классы"        | 90               | 2026-03-07      |
| S005       | "Функции"       | 95               | 2026-03-05      |
| S007       | "Функции"       | 58               | 2026-03-08      |
| S009       | "Функции"       | 35               | 2026-03-09      |
| S010       | "Аналитика"     | 88               | 2026-03-06      |
```

* **Типы данных:**

```python
student_id: str
assignment_name: str
assignment_score: int  # оценка от 0 до 100
submission_date: date
```

2. **Считать исходный файл `assignments.xlsx` в объект в DataFrame `assignments_df`**

3. **Объединить `python_students_df` и `assignments_df` по `student_id`:**

```python
combined_df = JoinDataFrames(
    table1=python_students_df,
    table2=assignments_df,
    keys1=["student_id"],
    keys2=["student_id"],
    join_type="inner"
)
```

4. **Выбрать студентов с оценкой за задание < 60:**

```python
low_score_df = FilterDataFrame(
    table=combined_df,
    filter_condition='assignment_score < 60'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="low_score_assignments.xlsx")

# Уведомляем пользователя
NotifyUser(message="Аналитика по студентам с низкими оценками сохранена в low_score_assignments.xlsx!")
```

---