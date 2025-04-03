```java
public class MyViewModel extends ViewModel {
    private MutableLiveData<String> liveDataString = new MutableLiveData<>();

    public LiveData<String> getLiveDataString() {
        return liveDataString;
    }

    public void setLiveDataString(String value) {
        liveDataString.setValue(value);
    }
}

```
 **Наблюдение за LiveData в Activity или Fragment**:
```java
public class MainActivity extends AppCompatActivity {
    private MyViewModel myViewModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        myViewModel = new ViewModelProvider(this).get(MyViewModel.class);

        final Observer<String> stringObserver = new Observer<String>() {
            @Override
            public void onChanged(@Nullable final String newValue) {
                // Ваш код, который будет выполняться при изменении значения
                Toast.makeText(
			                MainActivity.this, 
				            "Value changed to: " + newValue,
				            Toast.LENGTH_SHORT).show();
            }
        };

        myViewModel.getLiveDataString().observe(this, stringObserver);

        // Пример изменения значения
        myViewModel.setLiveDataString("New Value");
    }
}
```
