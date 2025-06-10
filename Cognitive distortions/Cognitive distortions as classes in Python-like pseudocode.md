# Когнитивные искажения как классы на Python-подобном псевдокоде

## 1. Базовый класс искажения

'''Python
class CognitiveBias:
    def __init__(self, name, trigger, effect):
        self.name = name          # Название искажения
        self.trigger = trigger    # Условие активации
        self.effect = effect      # Как искажает реальность

    def distort(self, input_data):
        """Искажает входные данные (мысли/восприятие)"""
        if self.trigger(input_data):
            return self.effect(input_data)
        return input_data
'''

## 2. Конкретные искажения как дочерние классы

### A. Подтверждающее искажение (Confirmation Bias)

'''Python
class ConfirmationBias(CognitiveBias):
    def __init__(self):
        super().__init__(
            name="Confirmation Bias",
            trigger=lambda data: data.get("belief"),  # Есть ли устоявшееся убеждение?
            effect=lambda data: {**data, "new_info": data["info"].filter(
                lambda x: x.matches(data["belief"])  # Отфильтровать несоответствующее
            }
        )

# Пример использования:
user_belief = {"belief": "Все политики лгут", "info": ["A лжёт", "B честен", "C лжёт"]}
bias = ConfirmationBias()
print(bias.distort(user_belief))  
# Вывод: {"belief": "Все политики лгут", "new_info": ["A лжёт", "C лжёт"]}
'''

### B. Эффект Даннинга-Крюгера (Dunning-Kruger Effect)

'''Python
class DunningKrugerEffect(CognitiveBias):
    def __init__(self):
        super().__init__(
            name="Dunning-Kruger Effect",
            trigger=lambda data: data["skill_level"] < 0.2,  # Низкий уровень навыка
            effect=lambda data: {**data, "self_estimate": 0.9}  # Завышенная самооценка
        )

# Пример:
beginner = {"skill_level": 0.1, "self_estimate": None}
bias = DunningKrugerEffect()
print(bias.distort(beginner))  
# Вывод: {"skill_level": 0.1, "self_estimate": 0.9}
'''

### C. Каскад доступности (Availability Heuristic)

'''Python
class AvailabilityHeuristic(CognitiveBias):
    def __init__(self):
        super().__init__(
            name="Availability Heuristic",
            trigger=lambda data: data["is_vivid"],  # Яркое/недавнее событие
            effect=lambda data: {**data, "probability_estimate": data["base_rate"] * 10}
        )

# Пример:
event = {"is_vivid": True, "base_rate": 0.01}  # Базовый риск 1%, но событие запомнилось
bias = AvailabilityHeuristic()
print(bias.distort(event))  
# Вывод: {"is_vivid": True, "probability_estimate": 0.1}  # Оценка риска как 10%
'''

## 3. Композиция искажений
*(Как искажения взаимодействуют друг с другом)*

### Система с несколькими искажениями

'''Python
class Mind:
    def __init__(self):
        self.biases = [
            ConfirmationBias(),
            DunningKrugerEffect(),
            AvailabilityHeuristic()
        ]

    def process(self, input_data):
        for bias in self.biases:
            input_data = bias.distort(input_data)
        return input_data

# Пример:
mind = Mind()
data = {
    "belief": "Летать опасно",
    "info": ["Крушение A", "Рейс B безопасен", "Крушение C"],
    "skill_level": 0.15,
    "is_vivid": True,
    "base_rate": 0.0001
}
print(mind.process(data))
'''

### Вывод:

'''Python
{
    "belief": "Летать опасно",
    "new_info": ["Крушение A", "Крушение C"],  # ConfirmationBias
    "skill_level": 0.15,
    "self_estimate": 0.9,                      # Dunning-Kruger
    "is_vivid": True,
    "probability_estimate": 0.001              # AvailabilityHeuristic (0.0001 * 10)
}
'''

## 4. Паттерны для работы с искажениями

### Декоратор для "очистки" данных

'''Python
def debias(func):
    def wrapper(data):
        # Удаляем поля, созданные искажениями
        cleaned = {k: v for k, v in data.items() 
                 if k not in ["new_info", "self_estimate", "probability_estimate"]}
        return func(cleaned)
    return wrapper

@debias
def make_decision(data):
    return f"Решение на основе {data}"

print(make_decision(mind.process(data)))
# Вывод: "Решение на основе {'belief': 'Летать опасно', 'skill_level': 0.15, 'is_vivid': True, 'base_rate': 0.0001}"
'''

### Фабрика искажений

'''Python
class BiasFactory:
    @staticmethod
    def create(bias_name):
        if bias_name == "confirmation":
            return ConfirmationBias()
        elif bias_name == "dunning_kruger":
            return DunningKrugerEffect()
        # ... другие искажения
        else:
            raise ValueError(f"Unknown bias: {bias_name}")

factory = BiasFactory()
bias = factory.create("confirmation")
'''

## 5. Тестирование системы

### UnitTest для ConfirmationBias

'''Python
def test_confirmation_bias():
    data = {"belief": "Кошки умнее собак", "info": ["Исследование A: кошки умны", "Исследование B: собаки умны"]}
    expected = {"belief": "Кошки умнее собак", "new_info": ["Исследование A: кошки умны"]}
    assert ConfirmationBias().distort(data) == expected
'''

## Итог

Такой подход позволяет:
1. Моделировать искажения как независимые модули.
2. Анализировать их взаимодействие (например, как Availability Heuristic усиливает Confirmation Bias).
3. Тестировать "когнитивную архитектуру" — например, как система ведёт себя при дебайазинге.

---

### Пример из реального кода:

'''Python
# Проверка, как Dunning-Kruger искажает самооценку
user_skill = 0.3  # Реальный уровень
print(DunningKrugerEffect().distort({"skill_level": user_skill}))  
# Выведет: {"skill_level": 0.3, "self_estimate": 0.9} — гипертрофированная уверенность
'''

Для конкретного искажения можно добавить:
- **Вес** (насколько сильно искажает),
- **Условия переопределения** (например, если есть противоречивые данные).
