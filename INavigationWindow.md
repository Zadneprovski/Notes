`INavigationWindow` — это интерфейс, который добавляет уровень абстракции над главным окном приложения.

### **1. Основная цель `INavigationWindow`**

Интерфейс служит для:
- **Стандартизации** работы с главным окном в MVVM-архитектуре.
- **Разделения ответственности** между окном и сервисами.
- **Упрощения тестирования** (можно подменить реальное окно моком).

Пример интерфейса:
```csharp
public interface INavigationWindow
{
    void ShowWindow();
    void CloseWindow();
    bool Navigate(Type pageType);
    INavigationView GetNavigation();
    void SetPageService(IPageService pageService);
}
```

### **2. Какие проблемы решает?**

#### **Проблема 1: Прямой доступ к окну из сервисов**

Без интерфейса сервисы напрямую зависят от конкретного класса `MainWindow`:
```csharp
// Плохо: сервис зависит от реализации окна
public class SomeService
{
    public void OpenWindow()
    {
        var window = new MainWindow(); // Жёсткая зависимость
        window.Show();
    }
}
```

С интерфейсом:
```csharp
// Хорошо: сервис зависит от абстракции
public class SomeService
{
    private readonly INavigationWindow _window;
    public SomeService(INavigationWindow window) // DI
    {
        _window = window;
    }
    
    public void OpenWindow()
    {
        _window.ShowWindow(); // Не знает, какое именно окно откроется
    }
}
```

#### **Проблема 2: Нарушение MVVM**

Без интерфейса логика управления окном может «проползти» во ViewModel:
```csharp
// Плохо: ViewModel управляет окном
public class MainViewModel
{
    public void Close()
    {
        Application.Current.MainWindow.Close(); // Прямой доступ
    }
}
```

С интерфейсом:
```csharp
// Хорошо: ViewModel использует абстракцию
public class MainViewModel
{
    private readonly INavigationWindow _window;
    public MainViewModel(INavigationWindow window)
    {
        _window = window;
    }

    public void Close()
    {
        _window.CloseWindow(); // ViewModel не знает, как именно закрывается окно
    }
}
```

### **3. Преимущества использования**

1. **Гибкость**:
    
    - Можно подменить реализацию окна (например, на модальное или полноэкранное) без изменения сервисов/ViewModel.
        
2. **Тестируемость**:
    
    - В unit-тестах вместо реального окна подставляется mock:
```csharp
var mockWindow = new Mock<INavigationWindow>();
var vm = new MainViewModel(mockWindow.Object);
vm.Close();
mockWindow.Verify(w => w.CloseWindow(), Times.Once);
```

```csharp

```

```csharp

```