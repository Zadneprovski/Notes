Для создания меню внизу экрана в Android вы можете использовать `BottomNavigationView`, который является частью `Material Design` компонентов. Этот компонент удобно использовать для создания навигационного меню внизу экрана, и он автоматически управляет состоянием выбранных элементов.

**Можно использовать в макете не более 5 item**

1. **Добавьте зависимость Material Components в ваш `build.gradle` файл**:

```gradle
implementation 'com.google.android.material:material:1.4.0
```


2. **Создайте меню для Bottom Navigation View**:

Создайте XML файл меню в папке `res/menu`, например, `bottom_nav_menu.xml`.


```xml
<!-- res/menu/bottom_nav_menu.xml -->
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/nav_home"
        android:icon="@drawable/ic_home"
        android:title="Home" />
    <item
        android:id="@+id/nav_search"
        android:icon="@drawable/ic_search"
        android:title="Search" />
    <item
        android:id="@+id/nav_notifications"
        android:icon="@drawable/ic_notifications"
        android:title="Notifications" />
    <item
        android:id="@+id/nav_profile"
        android:icon="@drawable/ic_profile"
        android:title="Profile" />
</menu>
```

3. **Добавьте `BottomNavigationView` в ваш макет**:

```xml
<!-- res/layout/activity_main.xml -->
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <!-- Другие элементы макета -->

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bottom_navigation"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        app:menu="@menu/bottom_nav_menu" />
</RelativeLayout>
```

4. **Настройте обработку нажатий в вашей Activity**:

В вашей Activity настройте обработку нажатий на элементы меню.

```java
import android.os.Bundle;
import androidx.annotation.NonNull;
import androidx.appcompat.app.AppCompatActivity;
import com.google.android.material.bottomnavigation.BottomNavigationView;
import com.google.android.material.navigation.NavigationBarView;

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        BottomNavigationView bottomNavigationView = findViewById(R.id.bottom_navigation);

        bottomNavigationView.setOnItemSelectedListener(new NavigationBarView.OnItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(@NonNull MenuItem item) {
                switch (item.getItemId()) {
                    case R.id.nav_home:
                        // Действие для Home
                        return true;
                    case R.id.nav_search:
                        // Действие для Search
                        return true;
                    case R.id.nav_notifications:
                        // Действие для Notifications
                        return true;
                    case R.id.nav_profile:
                        // Действие для Profile
                        return true;
                    case R.id.nav_settings:
                        // Действие для Settings
                        return true;
                }
                return false;
            }
        });

        // Устанавливаем начальный фрагмент
        if (savedInstanceState == null) {
            bottomNavigationView.setSelectedItemId(R.id.nav_home);
        }
    }
}

```
### Дополнительные улучшения

1. **Использование `Fragment` для каждого пункта меню**:

Вы можете использовать фрагменты для каждого пункта меню для управления различными экранами в приложении.

2. **Стиль и темы**:

Вы можете настроить стиль и тему `BottomNavigationView` в вашем `styles.xml` и `themes.xml`.

### Получение элемента меню
```java
MenuItem cutItem = bottomNavigationView.getMenu().findItem(R.id.move);
```