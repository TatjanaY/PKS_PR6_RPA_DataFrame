# Вариант 22 - LearningProgress: анализ прогресса студентов курса Data Science

---

## **Часть 1**

Компания **LearningProgress** занимается мониторингом обучения студентов на образовательной платформе.   
Ежедневно обрабатываются данные о студентах: часть проходит курсы по программированию, часть — по аналитике данных, а часть — по машинному обучению.  
Руководство хочет контролировать студентов, изучающих курс "Data Science", отслеживать их прогресс и выявлять учащихся с низкой успеваемостью.

Ваша задача как RPA-разработчика — автоматизировать работу с данными о студентах через **DataFrame** в **Puzzle RPA**, отфильтровать студентов по курсу, добавить аналитические столбцы, сохранить результаты и уведомить пользователя.

---

**Шаги:**

1. **Создать исходный файл `students.xlsx` с 10 записями:**

```python
# students.xlsx
| student_id | student_name  | course         | enrollment_date | progress | status     |
|------------|---------------|----------------|-----------------|----------|------------|
| S001       | "Иванов И."   | "Python"       | 2026-01-15      | 75       | "активен"  |
| S002       | "Петров П."   | "Data Science" | 2026-02-01      | 45       | "активен"  |
| S003       | "Сидоров С."  | "Data Science" | 2026-01-20      | 32       | "активен"  |
| S004       | "Кузнецов К." | "ML"           | 2026-02-10      | 60       | "активен"  |
| S005       | "Морозова М." | "Data Science" | 2026-01-25      | 88       | "активен"  |
| S006       | "Новиков Н."  | "Python"       | 2026-02-05      | 28       | "активен"  |
| S007       | "Фролова Ф."  | "Data Science" | 2026-02-12      | 41       | "активен"  |
| S008       | "Смирнов С."  | "ML"           | 2026-01-30      | 55       | "активен"  |
| S009       | "Егоров Е."   | "Data Science" | 2026-02-08      | 19       | "активен"  |
| S010       | "Лебедева Л." | "Python"       | 2026-01-18      | 92       | "активен"  |
```

* **Типы данных:**

```python
student_id: str
student_name: str
course: str  # "Python", "Data Science", "ML"
enrollment_date: date
progress: int  # прогресс в процентах (0-100)
status: str  # "активен", "завершен", "отчислен"
```

2. **Считать исходный файл `students.xlsx` в объект в DataFrame `students_df`**


3. **Добавить столбец `is_new` — флаг нового студента (записался за последние 30 дней):**

```python
students_df["is_new"] = students_df["enrollment_date"] > (today - 30)
```

4. **Отфильтровать студентов курса "Data Science":**

```python
ds_students_df = FilterDataFrame(
    table=students_df,
    filter_condition='course == "Data Science"'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="ds_students.xlsx")

# Уведомляем пользователя
NotifyUser(message="Студенты курса Data Science успешно сохранены в ds_students.xlsx!")
```


---

## **Часть 2**

После фильтрации студентов курса "Data Science" руководство хочет проанализировать результаты экзаменов.   
Существует второй файл `exams.xlsx`, в котором указаны оценки за итоговые экзамены.   
Нужно объединить данные и выделить студентов с оценкой за экзамен ниже 50 баллов, а результат сохранить и показать пользователю.

---

1. **Создать файл `exams.xlsx` с 8 записями:**

```python
# exams.xlsx
| student_id | exam_name      | exam_score | exam_date  |
|------------|----------------|------------|------------|
| S001       | "Python Basic" | 85         | 2026-03-05 |
| S002       | "DS Fundamentals" | 42      | 2026-03-06 |
| S003       | "DS Fundamentals" | 38      | 2026-03-04 |
| S004       | "ML Intro"     | 70         | 2026-03-07 |
| S005       | "DS Fundamentals" | 91      | 2026-03-05 |
| S007       | "DS Fundamentals" | 55      | 2026-03-08 |
| S009       | "DS Fundamentals" | 29      | 2026-03-09 |
| S010       | "Python Advanced" | 88      | 2026-03-06 |
```

* **Типы данных:**

```python
student_id: str
exam_name: str
exam_score: int  # оценка от 0 до 100
exam_date: date
```

2. **Считать исходный файл `exams.xlsx` в объект в DataFrame `exams_df`**

3. **Объединить `ds_students_df` и `exams_df` по `student_id`:**

```python
combined_df = JoinDataFrames(
    table1=ds_students_df,
    table2=exams_df,
    keys1=["student_id"],
    keys2=["student_id"],
    join_type="inner"
)
```

4. **Выбрать студентов с оценкой за экзамен < 50:**

```python
low_exam_df = FilterDataFrame(
    table=combined_df,
    filter_condition='exam_score < 50'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="low_exam_scores.xlsx")

# Уведомляем пользователя
NotifyUser(message="Аналитика по студентам с низкими экзаменационными оценками сохранена в low_exam_scores.xlsx!")
```

---