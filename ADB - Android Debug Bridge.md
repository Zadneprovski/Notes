Утилита, позволяющая выполнять команды на девайсе через ПК.

**Получение информации об устройстве**:
```bash
adb shell getprop
```

**Получение информации о процессоре**:
```bash
adb shell cat /proc/cpuinfo
```

**Получение информации о памяти**:
```bash
adb shell free -h
```

**Получение информации о хранилище**:
```bash
adb shell df -h
```

**Узнать версию Android**
```bash
adb shell getprop ro.build.version.release
```
