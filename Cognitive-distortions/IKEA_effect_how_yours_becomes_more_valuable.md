# Эффект IKEA: как «своё» становится ценнее
# The IKEA effect: how “yours” becomes more valuable

## 1. Описание эффекта

> **Эффект IKEA** — когнитивное искажение, при котором люди склонны переоценивать ценность объектов, которые они **собрали или создали самостоятельно**, даже если результат объективно хуже аналогов.

### Примеры:

- Мебель из IKEA кажется ценнее купленной готовой (отсюда название).
- DIY-проекты, собранные Lego, кастомизированные товары.
- В цифровом мире: переоценка своих настроек интерфейса или кода.

## 2. Психологический механизм

- **Теория усилий**: Чем больше труда вложено, тем выше субъективная ценность (Festinger, 1957).
- **Самоидентификация**: Созданный объект становится частью «Я»-концепции.
- **Когнитивный диссонанс**: Оправдание вложенных усилий («Раз потратил время — значит, это важно»).

## 3. Модель ООП

Реализуем систему, где агенты (User) оценивают продукты (Product) в зависимости от своего участия в создании.

```Python
from dataclasses import dataclass  

@dataclass  
class Product:  
    name: str  
    quality: float  # Объективное качество (0-10)  
    base_value: float  # Базовая стоимость  

class User:  
    def __init__(self, effort_bias: float = 0.3):  
        self.effort_bias = effort_bias  # Сила эффекта IKEA (0-1)  

    def evaluate(self, product: Product, personal_effort: float) -> float:  
        """Оценка стоимости с учётом личных усилий."""  
        # Чем больше усилий, тем выше субъективная ценность  
        subjective_value = product.base_value * (1 + self.effort_bias * personal_effort)  
        return subjective_value  

# Пример использования  
ikea_chair = Product(name="Стул IKEA", quality=6.0, base_value=50.0)  
premium_chair = Product(name="Дизайнерский стул", quality=9.0, base_value=200.0)  

user = User(effort_bias=0.4)  # Пользователь с сильным эффектом IKEA  

# Оценка после сборки (effort=1.0 — максимальные усилия)  
print(user.evaluate(ikea_chair, personal_effort=1.0))  # 50 * (1 + 0.4*1) = 70  
print(user.evaluate(premium_chair, personal_effort=0.1))  # 200 * (1 + 0.4*0.1) = 208  
```

### Вывод:

- Собранный стул IKEA субъективно ценится как 70$ (вместо 50$).
- Готовый дизайнерский стул почти не получает «надбавки» (208$ vs 200$).

## 4. Расширение модели

### Добавим искажение качества

Эффект IKEA заставляет пользователей **переоценивать даже объективное качество**:

```Python
def evaluate_quality(self, product: Product, personal_effort: float) -> float:  
    perceived_quality = product.quality * (1 + self.effort_bias * personal_effort)  
    return min(perceived_quality, 10.0)  # Ограничение сверху  

print(user.evaluate_quality(ikea_chair, personal_effort=1.0))  # 6 * 1.4 = 8.4  
```

### Интеграция с рынком

Класс **Market** для сравнения субъективных оценок разных пользователей:

```Python
class Market:  
    def __init__(self, products: list[Product]):  
        self.products = products  

    def simulate_sales(self, users: list[User]):  
        for product in self.products:  
            total_value = sum(  
                user.evaluate(product, personal_effort=1.0 if "IKEA" in product.name else 0.1)  
                for user in users  
            )  
            print(f"{product.name}: средняя оценка = {total_value / len(users):.1f}")  

market = Market(products=[ikea_chair, premium_chair])  
market.simulate_sales(users=[User(effort_bias=0.3) for _ in range(1000)])  
```

### Результат:

```Python
Стул IKEA: средняя оценка = 65.0  
Дизайнерский стул: средняя оценка = 206.0  
```

## 5. Где применяется в реальности

1. **Маркетинг**:
    - IKEA, Lego, DIY-наборы — намеренно вовлекают пользователя в сборку.
    - Кастомизация товаров (Nike ID, Starbucks).
2. **UX-дизайн**:
    - Настройка интерфейсов (как в Notion или Trello) повышает привязанность.
3. **Образование**:
    - Студенты выше ценят знания, полученные через практику (vs лекции).

## 6. Как бороться с эффектом?

- **Для бизнеса**: Вовлекать клиентов в создание продукта (например, голосования за новые функции).
- **Для себя**: Метод «стороннего наблюдателя»: спросить, купили бы вы это, если бы сделал кто-то другой.

### Код для «анти-ИКЕА» проверки:

```Python
def is_this_really_worth_it(self, product: Product) -> bool:  
    return product.base_value > self.evaluate(product, personal_effort=0.0)  

user = User(effort_bias=0.5)  
print(user.is_this_really_worth_it(ikea_chair))  # False (50 vs 70)  
```

## Итог

- **Эффект IKEA** — это bias, который можно смоделировать через **персональные коэффициенты усилий**.
- В ООП он реализуется как **метод оценки**, зависящий от personal_effort.
- Используется в маркетинге, но требует контроля для избежания нерациональных решений.
