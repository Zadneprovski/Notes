Начиная с версии библиотеки 3.2.0 [beta] [https://git.rg-gr.ru/rgrobotics/libraries/embedded/uc22base/-/tags/v3.2.0_beta](https://git.rg-gr.ru/rgrobotics/libraries/embedded/uc22base/-/tags/v3.2.0_beta) изменилась система сборки:

1. Теперь все программы и скрипты расположены в папке uc22base\build
2. Имеется возможность не копировать данные файлы в папку с проектом, а просто указать соответствующим образом параметры postbuild.

Для этих целей в Atollic требуется перейти в свойства проекта C/C++ Build → Settings → Tool Settings → C++ Linker → General и там указать следующий путь:

```plaintext
../../uc22base/build/STM32F205ZG_flash.lds
```


Затем перейти в Build Steps → Post-build steps и указать в поле Command: 

```plaintext
${ProjDirPath}\..\uc22base\build\postbuild_uc.bat ${PWD} arm-atollic-eabi-objcopy ${ProjName} ${ProjDirPath}\..\uc22base\build ${ProjDirPath}
```


После этих действий можно удалить все файлы из папки Debug. Сборка будет работать.