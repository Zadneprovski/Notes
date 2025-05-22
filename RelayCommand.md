##  Что делает `[RelayCommand]`?

Атрибут `[RelayCommand]`:

- автоматически генерирует `ICommand`-свойство;
- создаёт соответствующий `CanExecute` (если указать `bool`-метод или свойство);
- поддерживает команды с параметрами;
- поддерживает `async`-методы;
- работает с `partial`-методами `OnXChanged()` (например, для состояния кнопки).

---

## БАЗОВЫЙ ПРИМЕР (без CanExecute)

```csharp
[RelayCommand]
private void Save() => MessageBox.Show("Сохраняю...");
```
Создаёт команду `SaveCommand`.

---

## `CanExecute` — включение/отключение кнопки

Чтобы команда могла быть включена или выключена, нужно создать **`bool`-свойство или метод**, имя которого совпадает с названием команды + `CanExecute`.

### Пример 1: Свойство
```csharp
[ObservableProperty]
private bool canSave = true;

[RelayCommand(CanExecute = nameof(CanSave))]
private void Save()
{
    MessageBox.Show("Сохраняю...");
}
```
Если значение `CanSave` изменится, команда автоматически вызовет `CanExecuteChanged` и обновит состояние кнопки.

---

### Пример 2: Метод вместо свойства
```csharp
[RelayCommand(CanExecute = nameof(CanExecuteSave))]
private void Save() => MessageBox.Show("Сохраняю...");

private bool CanExecuteSave() => DateTime.Now.Second % 2 == 0;
```
---

## `async` команды
```csharp
[RelayCommand]
private async Task LoadDataAsync()
{
    await Task.Delay(1000);
    MessageBox.Show("Данные загружены");
}
```
Создаёт `IAsyncRelayCommand` → `LoadDataAsyncCommand`.

Можно использовать `CanExecute` так же, как для обычной команды.

---

## Команда с параметром
```csharp
[RelayCommand]
private void ShowMessage(string? text)
{
    MessageBox.Show(text ?? "Пусто");
}
```
В XAML:
```xml
<Button Content="Показать" 
		Command="{Binding ShowMessageCommand}" 
		CommandParameter="Привет"/>
```
---

## Вызов `CanExecuteChanged` вручную

Иногда нужно вручную обновить состояние кнопки. Используй:
```csharp
SaveCommand.NotifyCanExecuteChanged();
```
Для этого тебе нужно сохранить ссылку на команду:
```csharp
public IRelayCommand SaveCommand => _saveCommand;
[RelayCommand(CanExecute = nameof(CanSave))]
private readonly IRelayCommand _saveCommand;

[ObservableProperty]
private bool canSave = true;
```
Но чаще проще просто обновить `CanSave`, и генератор сам вызовет `CanExecuteChanged`.
```csharp

```

```csharp

```
