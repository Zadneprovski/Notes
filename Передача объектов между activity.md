
```java
Intent intent = new Intent(this, AnotherActivity.class);
intent.putExtra("targetActivityClass", targetActivityClass);
startActivity(intent);
```

```java
Class<? extends AppCompatActivity> targetActivityClass = 
        (Class<? extends AppCompatActivity>) getIntent().getSerializableExtra("targetActivityClass");

```
