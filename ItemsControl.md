`ItemsControl` — это базовый элемент управления в WPF, предназначенный для отображения коллекций данных. Он является фундаментом для других элементов управления, таких как `ListBox`, `ComboBox`, `DataGrid` и т.д. `ItemsControl` предоставляет гибкий способ отображения элементов данных, позволяя кастомизировать их внешний вид с помощью шаблонов и стилей.

---

### Основные свойства `ItemsControl`

#### **`ItemsSource`**

   - Используется для привязки коллекции данных, которые будут отображены в `ItemsControl`.
   - Коллекция может быть любого типа, поддерживающего интерфейс `IEnumerable` (например, массивы, списки, ObservableCollection).

```xml
<ItemsControl ItemsSource="{Binding MyCollection}" />
```

####  **`Items`**

- Содержит коллекцию элементов, добавленных вручную (не через привязку).
```xml
<ItemsControl>
    <TextBlock Text="Item 1" />
    <TextBlock Text="Item 2" />
</ItemsControl>
```

####  **`ItemTemplate`**

- Определяет, как будет отображаться каждый элемент коллекции.
- Используется для кастомизации внешнего вида элементов.
```xml
<ItemsControl ItemsSource="{Binding MyCollection}">
    <ItemsControl.ItemTemplate>
        <DataTemplate>
            <TextBlock Text="{Binding Name}" />
        </DataTemplate>
    </ItemsControl.ItemTemplate>
</ItemsControl>
```

####  **`ItemContainerStyle`**

   - Определяет стиль контейнера (например, `ContentPresenter` или другой элемент), который оборачивает каждый элемент.

####  **`ItemsPanel`**

   - Указывает, какой панелью (`Panel`) будут размещаться элементы. По умолчанию используется `StackPanel`.
```xml
<ItemsControl ItemsSource="{Binding MyCollection}">
    <ItemsControl.ItemsPanel>
        <ItemsPanelTemplate>
            <WrapPanel />
        </ItemsPanelTemplate>
    </ItemsControl.ItemsPanel>
</ItemsControl>
```

### Расширенные возможности

#### **1. Привязка к динамическим коллекциям**

Если источник данных реализует интерфейс `INotifyCollectionChanged` (например, `ObservableCollection`), элементы в `ItemsControl` будут автоматически обновляться при изменении коллекции.

```csharp
ObservableCollection<string> MyCollection = new ObservableCollection<string> { "Item 1", "Item 2" };
```

#### **2. Группировка данных**

С помощью `CollectionViewSource` можно группировать данные в `ItemsControl`.

```xml
<ItemsControl 
			ItemsSource="{Binding Source={StaticResource MyCollectionViewSource}}">
    <ItemsControl.GroupStyle>
        <GroupStyle>
            <GroupStyle.HeaderTemplate>
                <DataTemplate>
                    <TextBlock Text="{Binding Name}" FontWeight="Bold" />
                </DataTemplate>
            </GroupStyle.HeaderTemplate>
        </GroupStyle>
    </ItemsControl.GroupStyle>
</ItemsControl>
```

Код для настройки `CollectionViewSource`:
```csharp
CollectionViewSource collectionView = new CollectionViewSource();
collectionView.Source = MyCollection;
collectionView.GroupDescriptions.Add(new PropertyGroupDescription("Category"));
```

```xml

```


```xml

```