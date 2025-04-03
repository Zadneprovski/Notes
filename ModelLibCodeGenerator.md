Путь до логов `C:\Users\MZadneprovskiy\AppData\Local\ModelLibCodeGenerator\app-x.x.x`

Отправить параметр по RG протоколу через библиотеку на C#
```
CommandParameter RememberRequest = new CommandParameter(new ParameterId(0x17F0, ParameterGroup.User));//DEBUG
_protocol.Parameters.Add(RememberRequest);
_protocol.SendParameter(RememberRequest);
```