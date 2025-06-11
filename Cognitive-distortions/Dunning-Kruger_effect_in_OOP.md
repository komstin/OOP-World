# Эффект Даннинга-Крюгера в ООП
*(Гипертрофированная уверенность при низкой компетентности)*

> Эффект Даннинга-Крюгера — когнитивное искажение, при котором люди с низким уровнем квалификации склонны переоценивать свои способности, а высококвалифицированные специалисты, наоборот, часто недооценивают уровень своей компетентности.

## 1. Базовый класс эффекта

```Python
class DunningKrugerEffect:
    def __init__(self, skill_level: float):
        self.skill_level = skill_level  # Реальный уровень (0.0–1.0)
        self.self_estimate = None       # Самооценка (0.0–1.0)
        
    def assess(self):
        """Оценивает свою компетентность с искажением"""
        if self.skill_level < 0.2:
            # Пик сверхуверенности (новички)
            self.self_estimate = 0.9 - (self.skill_level * 0.5)  # Чем хуже навыки, тем выше оценка
        elif 0.2 <= self.skill_level < 0.7:
            # "Долина отчаяния" (осознание некомпетентности)
            self.self_estimate = self.skill_level * 0.6
        else:
            # Адекватная оценка (эксперты)
            self.self_estimate = self.skill_level * 0.9  # Эксперты слегка недооценивают себя
        return self.self_estimate
```

## 2. Примеры использования

### A. Новичок (Peak of "Mount Stupid")

```Python
noob = DunningKrugerEffect(skill_level=0.1)  # Реальный уровень: 10%
print(f"Самооценка: {noob.assess():.0%}")    # Вывод: "Самооценка: 85%"
```

#### Логика:

- Реальный уровень: 10% → самооценка 85% (гипертрофированная уверенность).

### B. Ученик (Valley of Despair)

```Python
learner = DunningKrugerEffect(skill_level=0.4)  
print(f"Самооценка: {learner.assess():.0%}")  # Вывод: "Самооценка: 24%"
```

#### Логика:

- Реальный уровень: 40% → самооценка 24% (переоценка сложности задачи).

### C. Эксперт (Slope of Enlightenment)

```Python
expert = DunningKrugerEffect(skill_level=0.9)  
print(f"Самооценка: {expert.assess():.0%}")  # Вывод: "Самооценка: 81%"
```

#### Логика:

- Реальный уровень: 90% → самооценка 81% (лёгкая недооценка).

## 3. Визуализация кривой Даннинга-Крюгера

```Python
import matplotlib.pyplot as plt

skills = [i/100 for i in range(0, 101)]  # Уровни навыков 0%-100%
estimates = [DunningKrugerEffect(skill).assess() for skill in skills]

plt.plot(skills, estimates, label="Самооценка")
plt.plot(skills, skills, "--", label="Реальный уровень")
plt.xlabel("Реальная компетентность")
plt.ylabel("Самооценка")
plt.legend()
plt.show()
```

### График:

- **Пик**: Новички (0–20% навыков) оценивают себя на ~80%.
- **Долина**: Средний уровень (20–70%) — самооценка падает до 30%.
- **Рост**: Эксперты (70%+) приближаются к адекватности.

## 4. Связь с амплификацией

Эффект объясняет, почему новички берутся за сложные задачи:

```Python
class OverconfidentTask:
    def __init__(self, skill_required: float):
        self.skill_required = skill_required
        
    def attempt(self, person: DunningKrugerEffect):
        if person.self_estimate >= self.skill_required:
            return "Берётся за задачу!"
        return "Отказывается"

task = OverconfidentTask(skill_required=0.8)
noob = DunningKrugerEffect(skill_level=0.1)
noob.assess()  # Самооценка: 85%
print(task.attempt(noob))  # Вывод: "Берётся за задачу!" (хотя навыков всего 10%)
```

## 5. Дебайазинг (коррекция самооценки)

```Python
def debias_dunning_kruger(person: DunningKrugerEffect, feedback: float) -> float:
    """Корректирует самооценку на основе внешней обратной связи"""
    person.self_estimate = (person.self_estimate + feedback) / 2
    return person.self_estimate

# Пример:
noob = DunningKrugerEffect(skill_level=0.1)
noob.assess()  # Самооценка: 85%
debias_dunning_kruger(noob, feedback=0.2)  # Внешняя оценка: 20%
print(f"Новая самооценка: {noob.self_estimate:.0%}")  # Вывод: "Новая самооценка: 52%"
```

### Почему это работает?

1. **Новички** не знают, чего не знают → переоценивают себя.
2. **Обучающиеся** сталкиваются с сложностью → самооценка падает.
3. **Эксперты** понимают глубину вопроса → оценка становится точнее.

### Как бороться:

- Для новичков: объективные **тесты навыков**.
- Для всех: **внешняя обратная связь** (как в debias_dunning_kruger).

## Итоговый пример

```Python
# Создаём дизайнера-новичка
designer = DunningKrugerEffect(skill_level=0.15)
designer.assess()
print(f"Дизайнeр считает, что его уровень: {designer.self_estimate:.0%}")  # 82%

# Он берётся за сложный проект
project = OverconfidentTask(skill_required=0.7)
print(project.attempt(designer))  # "Берётся за задачу!"

# После провала и обратной связи
debias_dunning_kruger(designer, feedback=0.2)
print(f"После коррекции: {designer.self_estimate:.0%}")  # 51%
```

Этот код моделирует **полный цикл**: от иллюзии компетентности → к осознанию ошибки → к коррекции.
