[[XAML]]
 [[INavigationWindow]]
 [[INavigableView]]
 [[Эволюция механизма уведомления об изменениях]]
 [[RelayCommand]]
### Фреймворки

- **.NET8** - актуальный кроссплатформенный
-  **.NET Framework** - ориентирован на Windows, с 2019 не развивается



Экземпляр класса `System.Reflection.Assembly` (Assembly - сборка), представляющий текущую сборку, в которой выполняется код. Класс `Assembly` предоставляет методы для работы с метаданными и ресурсами сборки. Используя этот класс, вы можете получить доступ к встроенным ресурсам (Embedded Resources) и другим элементам сборки.

![[Pasted image 20240521165349.png]]

- Создание CAN сообщения путем объединения байтов
```csharp
var message = new CanMessage(
    _targetCanMessageId, 
    new[] { (byte)CanCommandType.ReadRequest }.Concat(Enumerable.Repeat((byte)0, 7)).ToArray());
```

```csharp

```

```csharp

```

```csharp

```

```csharp

```