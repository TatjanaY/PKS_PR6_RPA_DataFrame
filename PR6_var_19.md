# Вариант 19 - EduPlatform: анализ активности студентов в IT-курсах

---

## **Часть 1**

Компания **EduPlatform** занимается онлайн-обучением студентов по различным направлениям.   
В платформе представлены курсы по программированию, дизайну, маркетингу, аналитике и другим специальностям.  
Руководство хочет контролировать активность студентов в IT-курсах, отслеживать прогресс обучения и выявлять студентов, требующих дополнительной поддержки.

Ваша задача как RPA-разработчика — автоматизировать работу с данными студентов через **DataFrame** в **Puzzle RPA**, отфильтровать курсы по категории, добавить аналитические столбцы, сохранить результаты и уведомить пользователя.

---

**Шаги:**

1. **Создать исходный файл `students.xlsx` с 10 записями:**

```python
# students.xlsx
| student_id | student_name | course_name      | course_category | enrollment_date | progress | status      |
|------------|--------------|------------------|-----------------|-----------------|----------|-------------|
| S001       | "Иванов И."  | "Python Basic"   | "IT"            | 2026-02-01      | 75       | "активен"   |
| S002       | "Петров П."  | "Web Design"     | "дизайн"        | 2026-02-05      | 45       | "активен"   |
| S003       | "Сидоров С." | "Data Analysis"  | "IT"            | 2026-01-20      | 30       | "активен"   |
| S004       | "Кузнецов К."| "Marketing Pro"  | "маркетинг"     | 2026-02-10      | 60       | "активен"   |
| S005       | "Морозова М."| "SQL Advanced"   | "IT"            | 2026-01-15      | 85       | "активен"   |
| S006       | "Новиков Н." | "JS Frameworks"  | "IT"            | 2026-02-03      | 20       | "активен"   |
| S007       | "Фролова Ф." | "UX/UI Design"   | "дизайн"        | 2026-02-08      | 55       | "активен"   |
| S008       | "Смирнов С." | "Machine Learning"| "IT"           | 2026-01-25      | 40       | "активен"   |
| S009       | "Егоров Е."  | "Python Advanced"| "IT"            | 2026-02-12      | 15       | "активен"   |
| S010       | "Лебедева Л."| "SEO Optimization"| "маркетинг"    | 2026-02-06      | 70       | "активен"   |
```

* **Типы данных:**

```python
student_id: str
student_name: str
course_name: str
course_category: str   # "IT", "дизайн", "маркетинг"
enrollment_date: date
progress: int          # процент выполнения курса
status: str            # "активен", "завершён", "приостановлен"
```

2. **Считать исходный файл `students.xlsx` в объект в DataFrame `students_df`**


3. **Добавить столбец `days_enrolled` — дней с начала обучения:**

```python
students_df["days_enrolled"] = (today - students_df["enrollment_date"]).days
```

4. **Отфильтровать курсы категории "IT":**

```python
it_courses_df = FilterDataFrame(
    table=students_df,
    filter_condition='course_category == "IT"'
)
```

5. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="it_courses.xlsx")

# Уведомляем пользователя
NotifyUser(message="Студенты на IT-курсах успешно сохранены в it_courses.xlsx!")
```


---

## **Часть 2**

После фильтрации студентов на IT-курсах руководство хочет проанализировать детальный прогресс по курсам.   
Существует второй файл `courses.xlsx`, в котором указаны данные о курсах и требованиях к прогрессу.   
Нужно объединить данные и выделить студентов с прогрессом менее 50%, а результат сохранить и показать пользователю.

---

1. **Создать файл `courses.xlsx` с 8 записями:**

```python
# courses.xlsx
| course_name    | course_category | min_progress | deadline   | instructor    | difficulty |
|----------------|-----------------|--------------|------------|---------------|------------|
| "Python Basic" | "IT"            | 50           | 2026-04-01 | "Петрова А."  | "начальный"|
| "Data Analysis"| "IT"            | 40           | 2026-03-25 | "Сидоров Д."  | "средний"  |
| "SQL Advanced" | "IT"            | 60           | 2026-03-20 | "Иванов М."   | "продвинутый"|
| "JS Frameworks"| "IT"            | 35           | 2026-04-10 | "Кузнецова Е."| "средний"  |
| "Machine Learning"| "IT"         | 45           | 2026-04-15 | "Смирнов П."  | "продвинутый"|
| "Python Advanced"| "IT"          | 55           | 2026-04-20 | "Петрова А."  | "продвинутый"|
| "Web Design"   | "дизайн"        | 40           | 2026-03-30 | "Фролова К."  | "начальный"|
| "Marketing Pro"| "маркетинг"     | 50           | 2026-04-05 | "Егоров В."   | "средний"  |
```

* **Типы данных:**

```python
course_name: str
course_category: str
min_progress: int
deadline: date
instructor: str
difficulty: str   # "начальный", "средний", "продвинутый"
```

2. **Считать исходный файл `courses.xlsx` в объект в DataFrame `courses_df`**

3. **Объединить `it_courses_df` и `courses_df` по `course_name`:**

```python
combined_df = JoinDataFrames(
    table1=it_courses_df,
    table2=courses_df,
    keys1=["course_name"],
    keys2=["course_name"],
    join_type="inner"
)
```

4. **Добавить расчет `progress_gap`:**

```python
combined_df["progress_gap"] = combined_df["min_progress"] - combined_df["progress"]
```

5. **Выбрать студентов с `combined_df["progress_gap"]` < 50:**

```python
low_progress_df = FilterDataFrame(
    table=combined_df,
    filter_condition='progress_gap < 50'
)
```

6. **Сохранить результат в Excel и уведомить пользователя:**

```python
# Сохраняем Excel файл
SaveExcel(file_path="low_progress_students.xlsx")

# Уведомляем пользователя
NotifyUser(message="Аналитика по студентам с низким прогрессом сохранена в low_progress_students.xlsx!")
```

---
