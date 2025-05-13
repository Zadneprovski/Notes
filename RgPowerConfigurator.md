Путь до логов `C:\Users\MZadneprovskiy\AppData\Local\RG\RgPowerConfigurator\Logs`

# class App
Отвечает за настройку и управление жизненным циклом приложения.
### **1. Dependency Injection (DI) и Host Builder**

```csharp
private static readonly IHost host = Host
    .CreateDefaultBuilder()
    .ConfigureServices((_, services) =>
    {
        // Регистрация сервисов
        services.AddHostedService<ApplicationHostService>();
        services.AddSingleton<IMessenger, WeakReferenceMessenger>();
        // ... множество других сервисов
    })
    .Build();
```
Здесь используется **.NET Generic Host** для настройки DI-контейнера.

- `CreateDefaultBuilder()` создаёт хост с базовой конфигурацией (логирование, конфигурация и т. д.).
- В `ConfigureServices` регистрируются все сервисы приложения:
    
    - **Синглтоны** (`AddSingleton`) — один экземпляр на всё приложение.
    - **Scoped** (`AddScoped`) — новый экземпляр для каждой области (например, окна).
    - **HostedService** (`AddHostedService`) — фоновые задачи (например, `ApplicationHostService`).


Примеры сервисов:

- `INavigationService` — управление навигацией.
- `IThemeService` — управление темами.
- `ICanServiceProvider` — работа с CAN-шиной.
- `IPluginService` — управление плагинами.

### **2. Конструктор `App` и Squirrel (обновления)**
```csharp
public App()
{
    AppDomain.CurrentDomain.UnhandledException += OnUnhandledException;
    SquirrelAwareApp.HandleEvents(
        onInitialInstall: OnAppInstall,
        onAppUninstall: OnAppUninstall,
        onEveryRun: OnAppRun
    );
}
```
- **Обработка необработанных исключений** через `UnhandledException`.
    
- **Squirrel** — библиотека для автообновлений приложения.
    
    - `OnAppInstall` — создаёт ярлык при установке.
    - `OnAppUninstall` — удаляет ярлык.
    - `OnAppRun` — настраивает AppUserModelID (для панели задач Windows).

---

### **3. Методы для работы с DI**

```csharp
public static T? GetService<T>() where T : class
{
    return host.Services.GetService(typeof(T)) as T;
}
```
Позволяет получать зарегистрированные сервисы в любом месте приложения (аналог `ServiceProvider`).

### **4. Обработка запуска приложения (`OnStartup`)**
```csharp
private async void OnStartup(object sender, StartupEventArgs e)
{
    // Проверка, что приложение не запущено повторно
    var processes = Process.GetProcessesByName(currentProcess.ProcessName)
        .Where(p => p != currentProcess && p.MainWindowHandle != IntPtr.Zero);

    if (processes.Any())
    {
        // Попытка активировать уже запущенное окно
        User32Methods.ShowWindow(..., SW_RESTORE);
        User32Methods.SetForegroundWindow(...);
        Environment.Exit(-1); // Закрытие дубликата
    }

    // Приоритет процесса (RealTime в релизе, High в Debug)
    currentProcess.PriorityClass = ProcessPriorityClass.RealTime;

    await host.StartAsync(); // Запуск хоста
    GetService<IApplicationUpdateService>()!.Start(); // Запуск автообновлений
}
```
- **Защита от дубликатов** — если приложение уже запущено, активируется его окно, а новый экземпляр закрывается.
- **Установка высокого приоритета** — для лучшей производительности.
- **Запуск фоновых сервисов** через `host.StartAsync()`.

### **5. Обработка закрытия приложения (`OnExit`)**
```csharp
private async void OnExit(object sender, ExitEventArgs e)
{
    await host.StopAsync(); // Остановка сервисов
    host.Dispose(); // Очистка ресурсов
}
```
Корректно завершает работу всех сервисов перед закрытием.

---

### **6. Обработка исключений**

#### **DispatcherUnhandledException** (UI-поток)

```csharp
private void OnDispatcherUnhandledException(object sender, DispatcherUnhandledExceptionEventArgs e)
{
    // Проверка, возникло ли исключение в плагине
    var plugin = ...; // Анализ стека вызовов

    if (plugin != null)
    {
        // Показ уведомления через Snackbar
        snackbarService?.Show("Ошибка в плагине", ...);

        // Запись лога в папку плагина
        File.WriteAllText(Path.Combine(pluginDir, "Error_...log"), ...);
        e.Handled = true; // Исключение обработано
    }
}
```
- Ловит исключения в UI (например, в обработчиках кнопок).
- Если ошибка в плагине — показывает уведомление и логирует её.

#### **UnhandledException** (все потоки)

```csharp
private void OnUnhandledException(object sender, UnhandledExceptionEventArgs e)
{
    // Запись лога в %AppData%\RgPowerConfigurator\Logs\Crash_...log
    File.WriteAllText(logPath, $"Type: {exception.GetType()}\n...");
}
```
Ловит **все** необработанные исключения (даже в фоновых потоках) и сохраняет их в лог.

---

### **Ключевые технологии и библиотеки**

1. **Microsoft.Extensions.Hosting** — DI и управление жизненным циклом.
2. **Wpf.Ui** — современный UI-фреймворк для WPF (темы, контролы).
3. **CommunityToolkit.Mvvm** — MVVM с Messenger для событий.
4. **Squirrel** — автообновления приложения.
5. **WinApi.User32** — вызов нативных API Windows (например, активация окна).

# class MainWindow

Представляет главное окно WPF-приложения, реализующее интерфейс `INavigationWindow`.

```csharp
public partial class MainWindow : INavigationWindow
{
    private readonly IApplicationSettingsService _settingsService;
    private readonly IThemeControllerService _themeService;
    public MainWindowViewModel ViewModel { get; }

    public MainWindow(
        MainWindowViewModel viewModel,
        IPageService pageService,
        INavigationService navigationService,
        ISnackbarService snackbar,
        IApplicationSettingsService settingsService,
        IThemeControllerService themeService)
    {
        // Инъекция зависимостей
        _settingsService = settingsService;
        _themeService = themeService;
        ViewModel = viewModel;
        DataContext = this; // Привязка ViewModel к окну

        InitializeComponent();
        SetPageService(pageService);

        navigationService.SetNavigationControl(RootNavigation);
        snackbar.SetSnackbarPresenter(MainSnackbarPresenter);
    }
}
```
- **Зависимости** (через конструктор):
    
    - `MainWindowViewModel` — логика окна (MVVM).
    - `IPageService` — управление страницами.
    - `INavigationService` — навигация между страницами.
    - `ISnackbarService` — уведомления (Snackbar).
    - `IApplicationSettingsService` — настройки приложения.
    - `IThemeControllerService` — управление темами.

- **Инициализация**:
    - `DataContext = this` — привязка ViewModel к XAML.
    - `SetPageService` — регистрация страниц для навигации.
    - Настройка Snackbar (показ уведомлений в `MainSnackbarPresenter`).
---

### **2. Навигация (`INavigationWindow`)**

Интерфейс [[INavigationWindow]] стандартизирует работу с навигацией:
```csharp
public bool Navigate(Type pageType) 
// Переход на страницу (например, DashboardPage)
  => RootNavigation.Navigate(pageType); 

public void ShowWindow() => Show(); // Открытие окна
public void CloseWindow() => Close(); // Закрытие окна
```
- `RootNavigation` — это `NavigationView` (UI-элемент из Wpf.Ui), отвечающий за отображение страниц.

В `App.xaml.cs` регистрируется реализация:
```csharp
services.AddScoped<INavigationWindow, MainWindow>();
```
Теперь любой сервис или ViewModel может запросить `INavigationWindow` через DI, не зная о существовании `MainWindow`.

---

### **3. События окна**

#### **Загрузка окна (`OnLoaded`)**

```csharp
private void OnLoaded(object sender, RoutedEventArgs e)
{
    _themeService.SetThemeMode(_themeService.CurrentMode); // Применение темы
    if (settings.Width != 0) LoadSettings(settings); // Восстановление размеров/позиции
    PutInRange(); // Проверка, что окно в пределах экрана
}
```
- **Тема**: применяется сохранённая тема (светлая/тёмная).
- **Позиция окна**:
    - Если настройки есть (`Width != 0`), восстанавливает размер/позицию.
    - `PutInRange()` гарантирует, что окно не "улетит" за границы экрана.
#### **Закрытие окна (`OnClosing`/`OnClosed`)**

```csharp
protected override void OnClosing(CancelEventArgs e)
{
    SaveSettings(_settingsService.Current.Appearance.UserInterface); // Сохранение настроек
    base.OnClosing(e);
}

protected override void OnClosed(EventArgs e)
{
    Application.Current.Shutdown(); // Завершение приложения
}
```
- При закрытии сохраняются:
    - Координаты (`Left`, `Top`).
    - Размеры (`Width`, `Height`).
    - Состояние (развёрнуто/свёрнуто).
    - Открыта ли панель навигации (`IsPaneOpen`).
---

### **4. Работа с экранами**

#### **Проверка позиции (`PutInRange`)**
```csharp
private void PutInRange()
{
    foreach (var screen in Screen.AllScreens)
    {
        if (Left < screen.Bounds.Right && Top < screen.Bounds.Bottom)
            return; // Окно в пределах экрана
    }
    PutInPrimaryCenter(); // Иначе — центрирование на основном экране
}
```
- Если окно выходит за границы **всех** экранов, оно перемещается в центр основного.
#### **Центрирование (`PutInPrimaryCenter`)**

```csharp
private void PutInPrimaryCenter()
{
    var primaryBounds = Screen.PrimaryScreen?.Bounds;
    if (primaryBounds != null)
    {
        var center = CalculateCenter(primaryBounds.Value);
        Left = center.X;
        Top = center.Y;
    }
}
```