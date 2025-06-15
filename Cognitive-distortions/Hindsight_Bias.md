    # Ретроспективное искажение

## Hindsight Bias

*«Эффект "Я так и знал"»*

## 1. Суть искажения

**Ретроспективное искажение** — это когнитивное искажение, при котором люди:
1. **После** наступления события считают его исход *очевидным и предсказуемым*,
2. **Переоценивают** свою способность предвидеть его до того, как оно произошло,
3. **Игнорируют** альтернативные варианты развития, которые казались вероятными ранее.

### Примеры:

- После биржевого краха 2008 года: «Все знали, что пузырь лопнет».
- После выборов: «Было очевидно, что победит этот кандидат».

## 2. Механизм возникновения

- **Упрощение нарратива:** Мозг выстраивает «логичную» историю, отбрасывая неудобные факты.
- **Иллюзия контроля:** Вера в предсказуемость мира снижает тревожность.
- **Селективная память:** Запоминаются только «подтверждающие» данные.

## 3. ООП-модель искажения

Вот классы для симуляции принятия решений до и после события:

```Python
from dataclasses import dataclass  
from typing import List  
import random  

@dataclass  
class Event:  
    name: str  
    possible_outcomes: List[str]  # Варианты исхода  
    actual_outcome: str = None    # Реализовавшийся исход  

class HumanAgent:  
    def __init__(self, name: str, expertise: float = 0.5):  
        self.name = name  
        self.expertise = expertise  # 0-1 (уровень "знаний")  
        self.pre_event_confidence = {}  # Уверенность в прогнозах до события  
        self.post_event_claims = {}     # Утверждения после события  

    def predict(self, event: Event) -> str:  
        """Прогнозирует исход до события (на самом деле гадает)"""  
        predicted = random.choice(event.possible_outcomes)  
        self.pre_event_confidence[event.name] = random.uniform(0.1, 0.7)  # Низкая уверенность  
        return predicted  

    def retrospect(self, event: Event) -> str:  
        """Даёт объяснение после события (с эффектом hindsight bias)"""  
        bias_strength = 1.0 - self.expertise  # Чем меньше экспертность, тем сильнее искажение  
        self.post_event_claims[event.name] = f"Я всегда знал, что '{event.actual_outcome}' произойдёт! (Уверенность: {bias_strength:.0%})"  
        return self.post_event_claims[event.name]  

class Experiment:  
    def __init__(self, agents: List[HumanAgent], events: List[Event]):  
        self.agents = agents  
        self.events = events  

    def run(self):  
        for event in self.events:  
            event.actual_outcome = random.choice(event.possible_outcomes)  # "Случайный" исход  
            for agent in self.agents:  
                agent.predict(event)  
                agent.retrospect(event)  
```

## 4. Пример использования

```Python
# Создаём событие с несколькими исходами  
election = Event(  
    name="Выборы президента",  
    possible_outcomes=["Победа А", "Победа Б", "Ничья"]  
)  

# Группа агентов с разной экспертизой  
agents = [  
    HumanAgent("Эксперт", expertise=0.9),  
    HumanAgent("Дилетант", expertise=0.2)  
]  

# Запускаем эксперимент  
exp = Experiment(agents, [election])  
exp.run()  

# Анализируем результаты  
for agent in agents:  
    print(f"{agent.name}:")  
    print(f"  До события: 'Победит {list(agent.pre_event_confidence.values())[0]:.0%} уверенности'")  
    print(f"  После: {agent.post_event_claims[election.name]}")  
```

### Вывод:

```
Эксперт:  
  До события: 'Победит 32% уверенности'  
  После: Я всегда знал, что 'Победа Б' произойдёт! (Уверенность: 10%)  

Дилетант:  
  До события: 'Победит 15% уверенности'  
  После: Я всегда знал, что 'Победа Б' произойдёт! (Уверенность: 80%)  
```

### Интерпретация:

- Дилетант демонстрирует сильное hindsight bias (80% уверенность после факта).
- Эксперт сохраняет скепсис (10%).

## 5. Как бороться с искажением?

### В коде:

```Python
class BiasCorrector:  
    @staticmethod  
    def debias(agent: HumanAgent, event: Event) -> float:  
        """Возвращает скорректированную уверенность"""  
        return agent.pre_event_confidence[event.name]  # Игнорируем пост-событийные заявления  
```

### В жизни:

1. **Ведение дневника прогнозов** (фиксация предсказаний до событий).
2. **Метод «Предсказание от противного»:** намеренный анализ альтернативных исходов.
3. **Статистическое мышление:** фокус на вероятностях, а не на «очевидности».

## 6. Отличие от других эффектов

Термин |	Суть |	Пример
--- | --- | ---
Hindsight bias |	«Я это знал» после события |	Кризис «был предсказуем»
Confirmation bias |	Поиск подтверждений своей правоты |	Чтение только «своих» СМИ
Dunning-Kruger |	Невежество + гиперуверенность |	Дилетант спорит с экспертом

## 7. Применение модели

- **Тестирование экспертов:** Сравнивать их пред- и пост-событийные утверждения.
- **Дизайн ИИ-систем:** Запрещать нейросетям «задним числом» менять уверенность в прогнозах.
- **Обучение аналитиков:** Тренировки на исторических данных без знания исхода.

