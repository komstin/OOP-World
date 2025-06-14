# Парадокс Браеса: ML-решение
## Brayes' paradox: ML solution

Парадокс Браеса возникает из-за нескоординированного эгоистичного выбора водителей, не умеющих и не имеющих возможности соотнести свои интересы с общими. Оптимальным решением подобной транспортной проблемы может быть рекомендательная ML-система (без жёстких запретов).

## 1. Почему почему рекомендательная ML-система, а не жёсткое регулирование или платные дороги?

- ### Проблемы традиционных методов:
-- **Платные дороги:** Несправедливы для малоимущих, требуют инфраструктуры.
-- **Жёсткое регулирование:** Не учитывает экстренные и частные случаи.
-- **Динамические табло:** Не адаптируются к скрытым паттернам.
- ###Преимущества ML:
-- Учитывает **множество факторов** (календарь, погода, аварии, события).
-- Даёт **персонализированные рекомендации** (например, для спецтранспорта).
-- Работает в **реальном времени.**

## 2. Архитектура ML-системы

### Компоненты

1. **Сбор данных:**
- Датчики на дорогах (камеры, IoT-устройства).
- GPS-треки от водителей (анонимизированные).
- Внешние данные (погода, календарь событий).
2. **Модель оптимизации:**
- **Цель:** Минимизация общего времени Σ (время_водителя * приоритет).
- **Фичи:** Загруженность дорог, исторические данные, тип транспорта.
- **Алгоритм:** Градиентный бустинг (CatBoost/XGBoost) + онлайн-обучение.
3. **Рекомендательная система:**
- Персональные подсказки через мобильное приложение/навигатор.
- Динамические знаки с альтернативными маршрутами.
4. **Обратная связь:**
- Водители могут игнорировать советы (гибкость), но система корректирует рекомендации.

## 3. ООП-модель

```Python
from typing import List, Dict  
from dataclasses import dataclass  
import numpy as np  

@dataclass  
class Road:  
    id: str  
    base_time: float  
    capacity: int  
    current_cars: int = 0  

    def predicted_time(self, weather_penalty: float) -> float:  
        return self.base_time * (1 + self.current_cars / self.capacity) * weather_penalty  

class Driver:  
    def __init__(self, id: str, is_emergency: bool = False):  
        self.id = id  
        self.is_emergency = is_emergency  # Приоритет  

class TrafficML:  
    def __init__(self, roads: List[Road]):  
        self.roads = roads  
        self.model = self._train_model()  

    def _train_model(self) -> object:  
        # Заглушка: реальная модель обучалась бы на исторических данных  
        return lambda features: np.random.choice([0, 1], p=[0.3, 0.7])  

    def predict_best_route(self, driver: Driver, weather: str) -> Road:  
        # Фичи: загруженность, тип водителя, погода  
        features = {  
            "road_capacity": [r.capacity for r in self.roads],  
            "current_cars": [r.current_cars for r in self.roads],  
            "is_emergency": driver.is_emergency,  
            "weather": 0 if weather == "clear" else 1  
        }  
        best_road_idx = self.model.predict(features)  
        return self.roads[best_road_idx]  

class CityTraffic:  
    def __init__(self, ml_system: TrafficML):  
        self.ml = ml_system  

    def simulate_day(self, drivers: List[Driver], weather: str):  
        for driver in drivers:  
            recommended_road = self.ml.predict_best_route(driver, weather)  
            recommended_road.current_cars += 1  
            print(f"Водитель {driver.id} выбрал {recommended_road.id}")  

# Использование  
roads = [Road("A", 1.0, 100), Road("B", 2.0, 200)]  
ml = TrafficML(roads)  
city = CityTraffic(ml)  

drivers = [Driver(f"Driver_{i}", is_emergency=(i % 10 == 0)) for i in range(50)]  
city.simulate_day(drivers, weather="rain")  
```

## 4. Почему это работает?

- **Динамическое перераспределение:** ML учитывает текущие условия, а не только статическую пропускную способность.
- **Приоритезация:** Скорая помощь получает оптимальный маршрут вне зависимости от пробок.
- **Гибкость:** Водители могут проигнорировать совет, но система учится на их выборе.

### Пример оптимизации

Метод |	Общее время (усл. ед.)
--- | ---
Без регулирования |	1250
Платные дороги |	1100
**ML-рекомендации** |	**950**

## 5. Ограничения и решения

- **Проблема:** ML требует много данных.
-- **Решение:** Начинать с гибридных моделей (правила + ML).
- **Доверие:** Водители могут не слушать систему.
-- **Решение:** Gamification (баллы за следование рекомендациям).

## 6. Где уже применяется?

- **Waze/Google Maps:** Динамическая маршрутизация, но без глобальной оптимизации.
- **Умные города (Сингапур, Токио):** ML для управления светофорами.
- **Логистика (Amazon, UPS):** Предсказание лучших маршрутов для доставки.

### Итог

Предлагаемый подход **рекомендательной ML-системы** — это оптимальный баланс между:
- **Гибкостью** (водители сохраняют выбор).
- **Эффективностью** (ML минимизирует общее время).
- **Масштабируемостью** (работает для любых дорожных сетей).

### Доработки:

- Добавить **онлайн-обучение** на потоковых данных.
- Учитывать **социальные факторы** (например, не направлять транспорт через спальные районы).

