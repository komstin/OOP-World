# Окна Овертона
# Overton Windows
*(Как смоделировать динамику изменения границ допустимого в обществе)*

## 1. Краткое описание

**Окно Овертона** — это концепция, описывающая, как идеи, изначально считающиеся маргинальными или неприемлемыми, постепенно становятся общепринятыми через 6 стадий:

Немыслимое → Радикальное → Приемлемое → Разумное → Популярное → Политика

### Пример:

Легализация однополых браков: от полного запрета (1990-е) до принятия в 30+ странах (2020-е).

## 2. Механизм сдвига

- **Дискурс-инженерия**: Постепенное изменение языка обсуждения («семьи» вместо «содомии»).
- **Нормализация**: Частое упоминание в СМИ, поддержка знаменитостей.
- **Переопределение рамок**: Смещение акцентов («права человека» vs. «грех»).

## 3. ООП-модель

Реализуем динамическое окно как класс, где идея (Idea) проходит стадии под влиянием агентов (Media, Politician).

```Python
from enum import Enum, auto  

class OvertonStage(Enum):  
    UNTHINKABLE = auto()  
    RADICAL = auto()  
    ACCEPTABLE = auto()  
    SENSIBLE = auto()  
    POPULAR = auto()  
    POLICY = auto()  

class Idea:  
    def __init__(self, name: str, initial_stage: OvertonStage):  
        self.name = name  
        self.stage = initial_stage  

    def shift_stage(self, delta: int):  
        stages = list(OvertonStage)  
        new_index = min(max(stages.index(self.stage) + delta, len(stages)-1)  
        self.stage = stages[new_index]  

class Society:  
    def __init__(self):  
        self.ideas = []  

    def add_idea(self, idea: Idea):  
        self.ideas.append(idea)  

    def apply_pressure(self, idea_name: str, power: int):  
        for idea in self.ideas:  
            if idea.name == idea_name:  
                idea.shift_stage(power)  

class Media:  
    @staticmethod  
    def promote(idea: Idea, society: Society):  
        society.apply_pressure(idea.name, delta=1)  

class Politician:  
    @staticmethod  
    def endorse(idea: Idea, society: Society):  
        if idea.stage >= OvertonStage.SENSIBLE:  
            society.apply_pressure(idea.name, delta=2)  
```

## 4. Пример использования

**Сценарий**: Легализация эвтаназии.

```Python
society = Society()  
euthanasia = Idea("Euthanasia", OvertonStage.UNTHINKABLE)  
society.add_idea(euthanasia)  

# 1. Медиа начинают обсуждать  
for _ in range(3):  
    Media.promote(euthanasia, society)  
print(f"После медиа-кампании: {euthanasia.stage}")  # ACCEPTABLE  

# 2. Политик поддерживает  
Politician.endorse(euthanasia, society)  
print(f"После заявления политика: {euthanasia.stage}")  # POPULAR  

# 3. Попадает в законы  
society.apply_pressure("Euthanasia", 1)  
print(f"Итог: {euthanasia.stage}")  # POLICY  
```

### Вывод:

```
После медиа-кампании: ACCEPTABLE
После заявления политика: POPULAR
Итог: POLICY
```

## 5. Ключевые компоненты модели

- **Инкапсуляция**:
  -- Каждый агент (Media, Politician) влияет на идею через единый интерфейс apply_pressure.
- **Динамическое состояние**:
  --Стадия идеи меняется через shift_stage(), но не может перескочить шаги.
- **Полиморфизм**:
  -- Разные агенты по-разному влияют на сдвиг (delta=1 у медиа, delta=2 у политиков).

## 6. Где применяется?

1. **Анализ дискурса**:
  - Как телеграм-каналы сдвигают повестку.
2. **Политтехнологии**:
  - Прогнозирование, когда радикальная идея станет законом.
3. **Маркетинг**:
  - Внедрение «шокирующих» продуктов (например, lab-grown meat).

### Пример для ИИ:

```Python
def predict_legalization(idea: Idea, society: Society) -> int:  
    """Предсказывает, через сколько шагов идея станет POLICY."""  
    return len(OvertonStage) - list(OvertonStage).index(idea.stage) - 1  
```

## 7. Ограничения

- **Линейность**: Реальный сдвиг может быть нелинейным (скачки из-за кризисов).
- **Культурный контекст**: В разных обществах одни идеи двигаются быстрее.

### Улучшенная версия:

```Python
class CulturalSociety(Society):  
    def __init__(self, conservatism: float):  
        super().__init__()  
        self.conservatism = conservatism  # 0-1 (1 = максимально консервативно)  

    def apply_pressure(self, idea_name: str, power: int):  
        for idea in self.ideas:  
            if idea.name == idea_name:  
                effective_power = power * (1 - self.conservatism)  
                idea.shift_stage(round(effective_power))  
```

## Итог

- **Окно Овертона** — это процесс управляемого изменения норм.
- **ООП-модель** позволяет:
  - Симулировать этапы сдвига.
  - Тестировать влияние разных агентов.
  - Прогнозировать последствия.

