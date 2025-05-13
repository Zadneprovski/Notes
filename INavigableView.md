## **Что такое `INavigableView` в C#?**

Это интерфейс, который стандартизирует взаимодействие между **View** (страницей/окном) и **навигационной системой** приложения. Он помогает:

- **Передавать параметры** между страницами.
- **Связывать View с ViewModel** (в паттерне MVVM).
- **Обрабатывать события навигации** (появление/исчезновение страницы).

```csharp
public interface INavigableView<TViewModel> where TViewModel : class
{
    TViewModel ViewModel { get; }  // ViewModel, связанная с View
}
```

## **Как использовать INavigableView?**

### **1. Реализация в странице (View)**

Допустим, у нас есть страница `ProductPage`, которая должна принимать параметр `productId`:
```csharp
public partial class ProductPage : ContentPage, INavigableView<ProductViewModel>
{
    public ProductViewModel ViewModel { get; }

    // Конструктор с DI (Dependency Injection)
    public ProductPage(ProductViewModel viewModel)
    {
        InitializeComponent();
        ViewModel = viewModel;
        BindingContext = ViewModel;  // Привязка ViewModel к XAML
    }

    // Обработка параметров навигации (если нужно)
    protected override void OnNavigatedTo(NavigatedToEventArgs args)
    {
        base.OnNavigatedTo(args);
        
        if (args.Parameter is int productId)
        {
            ViewModel.LoadProduct(productId);
        }
    }
}
```

### **2. ViewModel для страницы**
```csharp
public class ProductViewModel : INotifyPropertyChanged
{
    private Product _product;
    public Product Product
    {
        get => _product;
        set
        {
            _product = value;
            OnPropertyChanged();
        }
    }

    public async Task LoadProduct(int productId)
    {
        Product = await _productService.GetProductAsync(productId);
    }

    // Реализация INotifyPropertyChanged
    public event PropertyChangedEventHandler? PropertyChanged;
    protected virtual void OnPropertyChanged([CallerMemberName] string? propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

### **3. Навигация между страницами**

С использованием `INavigationService` (например, в .NET MAUI или с библиотекой `CommunityToolkit.Mvvm`):
```csharp
public class NavigationService
{
    public async Task NavigateTo<TView, TViewModel>(object? parameter = null)
        where TView : Page, INavigableView<TViewModel>, new()
        where TViewModel : class
    {
        var view = new TView();
        if (parameter != null && view.BindingContext is IQueryAttributable queryAttributable)
        {
            queryAttributable.ApplyQueryAttributes(new Dictionary<string, object> { { "param", parameter } });
        }
        
        await Application.Current.MainPage.Navigation.PushAsync(view);
    }
}
```

**Использование:**
```csharp
await _navigationService.NavigateTo<ProductPage, ProductViewModel>(productId: 123);
```