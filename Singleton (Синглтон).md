При реализации синглтона, необходимо запрещать копирование

Макрос для создания синглтона класса ClassName

```cpp
#define SINGLETON(ClassName)                \
public:                                     \
    static ClassName& getInstance() {       \
        static ClassName instance;          \
        return instance;                    \
    }                                       \
                                            \
private:                                    \
    ClassName() = default;                  \
    ~ClassName() = default;                 \
    ClassName(const ClassName&) = delete;   \
    ClassName& operator=(const ClassName&) = delete;
```

