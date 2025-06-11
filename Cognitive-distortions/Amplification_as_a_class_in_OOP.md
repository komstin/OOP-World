# Амплификация как класс в ООП
*(Искажение: неоправданно высокие затраты ресурсов на простую цель)*

> Амплификация — это когнитивное искажение, при котором человек склонен преувеличивать значимость негативных событий или собственных симптомов, ощущая проблему гораздо серьёзнее, чем она есть на самом деле. Это может приводить к повышенной тревожности, панике и необоснованным страхам относительно своего здоровья или жизни.

## 1. Базовый класс AmplificationBias

```Python
class AmplificationBias:
    def __init__(self, target, optimal_effort):
        self.target = target            # Цель (например, "убить муху")
        self.optimal_effort = optimal_effort  # Адекватные затраты (например, 1 единица)
        self.applied_effort = 0         # Фактически потраченные ресурсы

    def execute(self):
        """Переопределяется в дочерних классах"""
        raise NotImplementedError

    def report(self):
        return f"Цель: {self.target} | Оптимум: {self.optimal_effort} | Потрачено: {self.applied_effort}"
```

## 2. Примеры конкретных амплификаций

### A. Избыточный код (Overengineering)

```Python
class Overengineering(AmplificationBias):
    def execute(self):
        # Вместо простого решения создаётся сложная система
        self.applied_effort = self.optimal_effort * 100  # Усилия умножены на 100
        return f"Написано 1000 строк кода вместо {self.optimal_effort}"

# Пример:
task = Overengineering("написать функцию сложения", optimal_effort=1)
print(task.execute())  # Вывод: "Написано 1000 строк кода вместо 1"
print(task.report())   # Вывод: "Цель: написать функцию сложения | Оптимум: 1 | Потрачено: 100"
```

### B. Гиперконтроль (Micromanagement)

```Python
class Micromanagement(AmplificationBias):
    def execute(self):
        # Руководитель тратит часы на контроль простой задачи
        self.applied_effort = self.optimal_effort * 50
        return f"Потрачено {self.applied_effort} часов на задачу, требующую {self.optimal_effort}"

# Пример:
project = Micromanagement("отправить письмо", optimal_effort=0.1)
print(project.execute())  # Вывод: "Потрачено 5.0 часов на задачу, требующую 0.1"
```

### C. Паранойя безопасности (Security Theater)

```Python
class SecurityTheater(AmplificationBias):
    def execute(self):
        # Непропорциональные меры защиты
        self.applied_effort = self.optimal_effort * 200
        return f"Установлено 10 замков на дверь вместо {self.optimal_effort}"

# Пример:
security = SecurityTheater("защитить дверь", optimal_effort=1)
print(security.execute())  # Вывод: "Установлено 10 замков на дверь вместо 1"
```

## 3. Причины амплификации как методы класса

```Python
class AmplificationBias:
    # ... (предыдущий код)
    
    def _causes(self):
        return {
            "страх": "Перестраховка из-за тревожности",
            "перфекционизм": "Попытка достичь идеала",
            "незнание": "Непонимание адекватных мер"
        }

    def diagnose(self):
        causes = self._causes()
        return f"Причины: {', '.join(causes.values())}"
```

### Пример:

```Python
task = Overengineering("написать функцию", optimal_effort=1)
print(task.diagnose())  
# Вывод: "Причины: Перестраховка из-за тревожности, Попытка достичь идеала, Непонимание адекватных мер"
```

## 4. Композиция с другими искажениями

Амплификация часто сочетается с:
- **Эффектом Даннинга-Крюгера** (неадекватная оценка сложности задачи),
- **Иллюзией контроля** (вера, что больше усилий = гарантированный результат).

### Пример связи:

```Python
class DunningKrugerAmplification(AmplificationBias):
    def execute(self):
        # Неверная оценка своих сил → гиперкомпенсация
        self.applied_effort = self.optimal_effort * 1000
        return "Потрачено ресурсов: 1000% от нормы"

task = DunningKrugerAmplification("написать отчёт", optimal_effort=2)
print(task.execute())  # Вывод: "Потрачено ресурсов: 1000% от нормы"
```

## 5. Дебайазинг (коррекция амплификации)

```Python
def debias_amplification(task: AmplificationBias) -> float:
    """Возвращает нормализованные усилия"""
    return min(task.applied_effort, task.optimal_effort * 2)  # Не больше 2x от оптимума

# Пример:
task = Overengineering("написать код", optimal_effort=1)
task.execute()  # Потрачено 100 единиц
print(debias_amplification(task))  # Вывод: 2 (максимум 2 единицы вместо 100)
```

## Итог

### Амплификация в ООП — это:

- **Класс-искажение**, переопределяющий execute() для неоправданных затрат.
- **Композиция** с другими когнитивными ошибками (перфекционизм, страх).
- **Методы диагностики** и коррекции (debias_amplification).

### Как выглядит в реальности:

```Python
# Создаём задачу "написать письмо" (оптимум: 5 минут)
email_task = Micromanagement("написать письмо", optimal_effort=5)
email_task.execute()  # Потрачено 250 минут
print(email_task.report())  
# Вывод: "Цель: написать письмо | Оптимум: 5 | Потрачено: 250"
```

Такой подход позволяет:

- Моделировать **иррациональное поведение**,
- Искать **триггеры** (страх, незнание),
- **Оптимизировать процессы** через "дебайазинг".

Для других искажений (например, "Прокрастинация как обратная амплификация") можно создать аналогичные классы.

Автор: [Константин Ляхов](https://orcid.org/0009-0006-7286-4803)
