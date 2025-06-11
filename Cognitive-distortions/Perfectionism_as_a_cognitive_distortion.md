# Перфекционизм как когнитивное искажение
*(Навязчивое стремление к недостижимому идеалу, ведущее к прокрастинации или истощению ресурсов)*

> Перфекционизм — когнитивное искажение, характеризующееся стремлением к идеальному выполнению задач, сопровождающееся завышенными требованиями к себе и окружающим, страхом ошибок и самокритичностью.

## 1. Класс PerfectionismBias

```Python
class PerfectionismBias:
    def __init__(self, task_complexity: float, threshold: float = 0.99):
        self.task_complexity = task_complexity  # Сложность задачи (0.0–1.0)
        self.threshold = threshold              # Минимально допустимое качество (например, 99%)
        self.status = "Pending"                 # "Done", "Failed", "Overworked"
        self.resources_spent = 0                # Потраченные ресурсы (время/усилия)

    def execute(self):
        """Пытается выполнить задачу идеально"""
        while True:
            current_quality = self._attempt()
            self.resources_spent += 1
            
            if current_quality >= self.threshold:
                self.status = "Done"
                break
            elif self.resources_spent > 10 * self.task_complexity:  # Превышение лимита
                self.status = "Overworked"
                break

    def _attempt(self) -> float:
        """Возвращает качество выполнения (0.0–1.0)"""
        base_quality = min(0.8, self.task_complexity * 0.9)  # Реальное качество без искажения
        perfectionism_boost = random.uniform(0.0, 0.2)        # Случайная "доработка"
        return min(base_quality + perfectionism_boost, 1.0)   # Не может превысить 100%

    def __str__(self):
        return f"Статус: {self.status} | Ресурсы: {self.resources_spent} | Порог: {self.threshold:.0%}"
```

## 2. Примеры использования

### A. Завершённая задача (редкий случай)

```Python
task = PerfectionismBias(task_complexity=0.7, threshold=0.95)
task.execute()
print(task)  
# Возможный вывод: "Статус: Done | Ресурсы: 4 | Порог: 95%"
```

### B. Перегрузка (типичный сценарий)

```Python
task = PerfectionismBias(task_complexity=0.5, threshold=0.99)
task.execute()
print(task)  
# Возможный вывод: "Статус: Overworked | Ресурсы: 12 | Порог: 99%"
```

## 3. Связь с другими искажениями

### Композиция с прокрастинацией

```Python
class PerfectionismWithProcrastination(PerfectionismBias):
    def __init__(self, task_complexity: float, fear_of_failure: float):
        super().__init__(task_complexity)
        self.fear_of_failure = fear_of_failure  # Страх неудачи (0.0–1.0)

    def execute(self):
        if self.fear_of_failure > 0.7:
            self.status = "Delayed Indefinitely"  # Задача отложена
        else:
            super().execute()

# Пример:
project = PerfectionismWithProcrastination(task_complexity=0.6, fear_of_failure=0.8)
project.execute()
print(project)  # Вывод: "Статус: Delayed Indefinitely | Ресурсы: 0 | Порог: 99%"
```

### Наследование от AnalysisParalysis

```Python
class PerfectionismParalysis(PerfectionismBias, AnalysisParalysis):
    """Гибрид перфекционизма и паралича анализа"""
    def __init__(self, task_complexity: float):
        PerfectionismBias.__init__(self, task_complexity)
        AnalysisParalysis.__init__(self, uncertainty_level=0.9)

    def execute(self):
        if self.planning_depth > 100:  # Сначала перепланирует
            self.status = "Overplanned"
        else:
            super().execute()
```

## 4. Дебайазинг (коррекция)

### A. Метод «Минимально жизнеспособный результат» (MVP)

```Python
def debias_with_mvp(self, mvp_threshold: float = 0.7):
    """Принудительно снижает планку качества"""
    self.threshold = mvp_threshold
    self.execute()

# Пример:
task = PerfectionismBias(task_complexity=0.5, threshold=0.99)
task.debias_with_mvp(mvp_threshold=0.7)
print(task)  # Вывод: "Статус: Done | Ресурсы: 3 | Порог: 70%"
```

### B. Внешний аудит

```Python
def external_audit(self, realistic_quality: float):
    """Корректирует ожидания на основе внешней оценки"""
    if realistic_quality < self.threshold:
        self.threshold = realistic_quality * 1.1  # Чуть выше реального
```

## 5. Почему это искажение?

- **Игнорирование закона убывающей отдачи**:
```Python
quality = 0.8 + 0.01 * resources_spent  # После 20 итераций прирост всего 0.2
```
- **Подмена цели**: «Сделать хорошо» → «Сделать безупречно» (что невозможно).

## Итог в терминах ООП

1. **Инкапсуляция**:
- Скрытые параметры (_attempt(), threshold).
- Публичный интерфейс (execute(), debias_with_mvp()).
2. **Наследование**:
- Можно комбинировать с прокрастинацией, параличом анализа.
3. **Полиморфизм**:
- Разное поведение при высоком fear_of_failure vs. низком.

### Пример в коде:

```Python
# Писатель-перфекционист
writing = PerfectionismBias(task_complexity=0.3, threshold=0.99)
writing.execute()
print(writing)  
# Вывод: "Статус: Overworked | Ресурсы: 15 | Порог: 99%"
# (Потратил 15 единиц ресурса на текст, который можно было закончить за 3)
```

### Как бороться:

- Вызывать debias_with_mvp().
- Добавлять external_audit() от коллег.
- Наследовать от AntiPerfectionismStrategy (если такой класс есть).

Для других аспектов перфекционизма (например, боязнь делегирования) можно добавить новые методы.
