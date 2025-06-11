# Когнитивное искажение: Паралич анализа
*(Чрезмерное планирование при неопределённости, ведущее к бездействию)*

## 1. Класс AnalysisParalysis

```Python
class AnalysisParalysis:
    def __init__(self, uncertainty_level: float, planning_depth: int = 100):
        self.uncertainty = uncertainty_level  # Уровень неопределённости (0.0–1.0)
        self.planning_depth = planning_depth  # Глубина планирования (число шагов)
        self.status = "In Progress"          # Состояние: "Done"/"Overplanned"

    def plan(self, required_data: int, available_data: int):
        """Пытается построить идеальный план"""
        if available_data < required_data * 0.5:  # Данных недостаточно
            self._overcomplicate()                # Усложнение процесса
        else:
            self.status = "Done"

    def _overcomplicate(self):
        """Рекурсивно добавляет детализации"""
        self.planning_depth *= 2
        if self.planning_depth > 1000:  # Превышен лимит
            self.status = "Overplanned"
        else:
            self.plan(required_data, available_data)  # Рекурсия!

    def __str__(self):
        return f"Статус: {self.status} | Глубина: {self.planning_depth} шагов"
```

## 2. Пример использования

### Сценарий: Планирование стартапа в условиях неопределённости.

```Python
startup_plan = AnalysisParalysis(
    uncertainty_level=0.8,  # Высокая неопределённость
    planning_depth=10       # Начальная детализация
)

startup_plan.plan(
    required_data=100,      # Нужно 100 точек данных
    available_data=30       # Есть только 30
)

print(startup_plan)  
# Вывод: "Статус: Overplanned | Глубина: 1280 шагов"
```

### Что произошло:

1. Система обнаружила недостаток данных (30 < 50).
2. Запустила рекурсивное усложнение плана (planning_depth вырос до 1280).
3. Перешла в состояние Overplanned (паралич действий).

## 3. Связь с другими искажениями

### Композиция классов:

```Python
class AnalysisParalysisWithBias(AnalysisParalysis):
    def __init__(self, uncertainty_level: float, bias_type: str):
        super().__init__(uncertainty_level)
        self.bias = self._attach_bias(bias_type)

    def _attach_bias(self, bias_type):
        if bias_type == "Перфекционизм":
            return "Требование 100% точности"
        elif bias_type == "Иллюзия контроля":
            return "Вера, что больше деталей = меньше риска"
        else:
            return "Неизвестное искажение"

# Пример:
project = AnalysisParalysisWithBias(0.9, "Перфекционизм")
print(project.bias)  # Вывод: "Требование 100% точности"
```

## 4. Паттерны поведения

### A. Бесконечное уточнение

```Python
def add_detail(self):
    while self.status != "Overplanned":
        self.planning_depth += 1
        if random() < self.uncertainty:  # Случайный фактор
            self._overcomplicate()
```

### B. Дебайазинг (коррекция)

```Python
def debias(self, max_depth: int = 50):
    """Принудительное ограничение детализации"""
    if self.planning_depth > max_depth:
        self.planning_depth = max_depth
        self.status = "Done (Debiased)"
```

### Пример:

```Python
project.debias(max_depth=20)
print(project)  # Вывод: "Статус: Done (Debiased) | Глубина: 20 шагов"
```

## 5. Почему это искажение?

- **Ложное убеждение**: «Больше деталей = надёжность».
- **Игнорирование**:
-- Случайных факторов (random() < uncertainty),
-- Закона убывающей отдачи (после N шагов новые детали бесполезны).

## Итог в терминах ООП

1. **Класс** AnalysisParalysis инкапсулирует:
- Поля: uncertainty, planning_depth, status.
- Методы: plan(), _overcomplicate(), debias().
2. **Наследование**: Можно расширить для специфичных сценариев (например, финансовое планирование).
3. **Полиморфизм**: Разное поведение при разном уровне uncertainty.

### Как выглядит в коде:

```Python
# Создаём проект с высокой неопределённостью
ai_project = AnalysisParalysis(uncertainty_level=0.9)
ai_project.plan(required_data=200, available_data=40)
print(ai_project)  
# Вывод: "Статус: Overplanned | Глубина: 2560 шагов"
```

Этот класс — **модель искажения**, где система сама себя блокирует через избыточную сложность. Для выхода нужен debias() — осознанное ограничение детализации.
