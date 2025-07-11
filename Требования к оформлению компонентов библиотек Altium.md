## Schematic Library

- **Сетка**: 2.5 мм
- **Длина пина**: 5 мм
- **Шрифт**: GOST type B, размер 10
- **Отображать на схеме**:
  - Designator
  - PartNumber (для микросхем)
  - Value (для пассивных элементов)
- **Толщина линии**: Small
- **Только Ч/Б цвета**

### У пинов указывать **Electrical Type**:

- **Power**: пины VCC и GND
- **Passive**: все остальные

**Заполнять Description пина** из даташита (если много текста, то несколько предложений).

Из параметров к заполнению **обязательны** (если это элемент не из DbLib):

- Manufacturer (производитель)
- Package (тип корпуса)
- PartNumber
- Montage (SMD или DIP)
- Replacement (указывает на возможный заменяющий элемент или аналог для компонента.)

 Из параметров к заполнению **желательны** (если это элемент не из DbLib) все ключевые параметры, характеризующие элемент (напряжения, токи, температуры, частоты и т.д.).

**Компонент должен размещаться по центру**.

Для микросхем — компонент должен называться по названию микросхемы.

---

## PCB Library

- Слои принадлежат не компоненту, а библиотеке, поэтому рекомендуется **не создавать библиотеку с нуля**, а брать готовый пустой шаблон, в котором все слои уже определены должным образом. При копировании элемента из библиотеки с правильно определёнными слоями в библиотеку с неправильными слоями, слои останутся неправильными.

- **Геометрический центр** должен быть строго по центру элемента.
- Наличие **3D модели** обязательно, если это не Pad.
- Обязательно указывать **высоту элемента**.
- **Пин №1** обязательно должен располагаться сверху и слева относительно остальных.

---

### Правила оформления слоев

#### Assembly (Сборка)

Этот слой используется для отображения элементов, которые видны на сборочной стадии платы.

- Рисуется схематичное изображение элемента (желательно внутри нарисовать УГО).
- **Толщина линии**: 0.1 мм

#### Component Center (Центр компонента)

Используется для указания центра компонента, то есть точку, через которую будет проходить ось компонента при его размещении.

- Рисуется крест 0.5 мм x 0.5 мм.
- **Толщина линии**: 0.1 мм

#### Value (Значение)

Используется для размещения информации о компоненте.

В Properties поля **Text** указывается:

- **.PartNumber** — для микросхем
- **.Value** — для пассивных элементов

- **Тип шрифта**: Stroke
- **Толщина линии**: 0.1 мм

#### Designator (Обозначение)

Располагаются уникальные идентификаторы компонентов (например, R1, C1, U1 и т.д.).

- **Тип шрифта**: Stroke
- **Толщина линии**: 0.1 мм

#### Component Outline (Контур компонента)

Содержит контуры компонентов, то есть их физические размеры и границы (включая Solder Mask), которые необходимо учитывать при проектировании платы.

- **Толщина линии**: 0.05 мм

#### Countyard (Площадка компонента)

Определяет площадку компонента — пространство вокруг компонента, которое гарантирует, что компоненты не будут мешать друг другу, а также обеспечит соответствующие зазоры для монтажа.

- Обычно граница проходит на 0.25 мм (0.2 мм для малых элементов) шире границы **Component Outline**.
- Обязательно должен включать в себя **Overlay**, то есть границы **Overlay** не должны выходить за границы **Countyard**.
- **Толщина линии**: 0.025 мм

#### Overlay (Шелкография)

Ограничивает корпус и указывает каким-либо способом, где находится первый пин (точка или выемка в корпусе). Метка, указывающая правильное расположение элемента, должна быть видна после монтажа этого элемента.

- **Толщина линии**: 0.2 мм

#### Solder и Paste

Размеры определяются даташитом компонента.

- По возможности, необходимо делать скругление площадок компонента на 25%.
- Если под компонентом есть контактная площадка и корпус микросхемы типа BGA, LGA, QFN, то необходимо обратить внимание на даташит, там может быть указано, что пасту на данную площадку необходимо наносить не полностью, а делить на зоны. Это делается для того, чтобы паста не мешала прилеганию выводов компонента к плате при пайке.
