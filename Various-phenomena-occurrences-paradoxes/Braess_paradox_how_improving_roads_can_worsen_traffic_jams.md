# Парадокс Браеса: как улучшение дорог может ухудшить пробки
## Braess' paradox: how improving roads can worsen traffic jams

## 1. Суть парадокса

> **Парадокс Браеса** (1968) — ситуация, когда добавление новой дороги или улучшение существующей инфраструктуры увеличивает общее время в пути для всех водителей.

### Классический пример:

- **Было**: 2 пути из A в B — узкий (1 час) и широкий (2 часа). Все едут по узкому, итого 1 час.
- **Стало**: Добавили супер-скоростную дорогу (0.5 часа). Теперь все переключаются на неё, создавая пробку, и общее время растёт до 1.5 часов.

**Почему?** Равновесие Нэша (когда каждый выбирает оптимальный для себя маршрут) не совпадает с общественно оптимальным решением.

## 2. ООП-модель

Реализуем транспортную сеть с водителями (**Driver**), дорогами (**Road**) и системой (**TransportNetwork**), где каждый агент действует эгоистично.

### Классы:

```Python
from dataclasses import dataclass  
from typing import List  

@dataclass  
class Road:  
    name: str  
    base_time: float  # Время без пробок  
    capacity: int     # Макс. машин  
    cars: int = 0     # Текущее кол-во машин  

    @property  
    def actual_time(self) -> float:  
        """Время с учётом загруженности (чем больше машин, тем хуже)."""  
        return self.base_time * (1 + self.cars / self.capacity)  

class Driver:  
    def __init__(self, name: str):  
        self.name = name  

    def choose_road(self, roads: List[Road]) -> Road:  
        """Выбирает дорогу с минимальным текущим временем."""  
        return min(roads, key=lambda r: r.actual_time)  

class TransportNetwork:  
    def __init__(self, roads: List[Road], drivers: List[Driver]):  
        self.roads = roads  
        self.drivers = drivers  

    def simulate(self):  
        # Все водители выбирают "лучшую" дорогу  
        for driver in self.drivers:  
            chosen_road = driver.choose_road(self.roads)  
            chosen_road.cars += 1  

        # Считаем общее время  
        total_time = sum(road.actual_time * road.cars for road in self.roads)  
        return total_time  
```

## 3. Пример использования

### Сценарий 1: Исходная сеть

```Python
# Дороги: узкая (ёмкость 1000 машин) и широкая (ёмкость 2000)  
road_narrow = Road("Узкая", base_time=1.0, capacity=1000)  
road_wide = Road("Широкая", base_time=2.0, capacity=2000)  

# 1000 водителей  
drivers = [Driver(f"Driver_{i}") for i in range(1000)]  

# Симуляция  
network = TransportNetwork([road_narrow, road_wide], drivers)  
print(f"Общее время: {network.simulate()}")  # Все выбрали узкую → 1000 * 1.0 = 1000  
```

### Сценарий 2: Добавили скоростную дорогу

```Python
road_fast = Road("Скоростная", base_time=0.5, capacity=500)  
network.roads.append(road_fast)  

# Перезапускаем симуляцию  
print(f"Общее время после добавления дороги: {network.simulate()}")  
# Теперь 500 машин на скоростной (0.5 * 2 = 1.0), 500 на узкой (1.0 * 1.5 = 1.5)  
# Итог: 500*1.0 + 500*1.5 = 1250 (хуже, чем было 1000!)  
```

### Вывод:

Добавление «удобной» дороги увеличило общее время на 25%!

## 4. Как это работает?

- **Принцип равновесия**: Каждый водитель выбирает оптимальный для себя маршрут, игнорируя влияние на других.
- **Эффект перераспределения**: Новый маршрут переманивает часть потока, но из-за ограниченной пропускной способности создаёт пробки.

### Диаграмма

```
A ────────────────> B  
   (Узкая: 1 час)  
A ────────────────> B  
   (Широкая: 2 часа)  

После добавления:  
A ────────> (Скоростная: 0.5 часа) ───> B  
```

## 5. Решения парадокса

1. ### Платные дороги:
- Взимание платы за скоростную дорогу снижает её привлекательность.
```Python
class TollRoad(Road):  
    def __init__(self, name: str, base_time: float, capacity: int, toll: float):  
        super().__init__(name, base_time, capacity)  
        self.toll = toll  

    @property  
    def actual_time(self) -> float:  
        return super().actual_time + self.toll  # Добавляем "цену" к времени  
```
2. ### Централизованное управление:
- Алгоритм, который принудительно распределяет водителей.
```Python
def centralized_choose_road(drivers: List[Driver], roads: List[Road]) -> None:  
    for i, driver in enumerate(drivers):  
        driver.chosen_road = roads[i % len(roads)]  # Распределение по модулю  
```
3. ### Динамические табло:
- Показывают не текущее, а прогнозируемое время с учётом будущих выборов других.

## 6. Где встречается?

- **Города:** Добавление новых магистралей в Москве/Лос-Анджелесе иногда ухудшает пробки.
- **IT:** Добавление сервера может перенаправить трафик и вызвать лавинообразную нагрузку.
- **Биология:** Мутации, выгодные отдельной особи, могут вредить популяции.

### Пример для маршрутизаторов:

```Python
class NetworkRouter:  
    def __init__(self, paths: List[Path]):  
        self.paths = paths  

    def choose_path(self, packet):  
        return min(self.paths, key=lambda p: p.latency)  # Аналог водителя  
```

## 7. Ограничения модели

- **Упрощение:** Не учитывает неравномерный спрос, время суток.
- **Динамика:** В реальности водители учатся и меняют поведение.

### Улучшенная версия:

```Python
class AdaptiveDriver(Driver):  
    def choose_road(self, roads: List[Road], memory: dict) -> Road:  
        # Учитывает прошлый опыт пробок  
        least_congested = min(roads, key=lambda r: memory.get(r.name, r.actual_time))  
        memory[least_congested.name] = least_congested.actual_time  
        return least_congested  
```

## Итог

- **Парадокс Браеса** — это конфликт между индивидуальной и общей оптимальностью.
- **ООП-модель** показывает, как эгоистичный выбор приводит к системному коллапсу.
- **Решение:** Алгоритмы, учитывающие коллективный эффект (платные дороги, ML-оптимизация).

