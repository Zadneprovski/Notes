Если при биндинге nullable значения (например  int?) в текстовом поле не используется конвертер, то ввести пустую строку будет невозможно, она не преобразуется в null. Для решения проблемы, когда нужно иметь возможность ввести null значение не используя конвертер, можно использовать параметр TargetNullValue :

```xml
 <TextBox Name="NewVariableMin">
     <TextBox.Text>
         <Binding 
	         Path="NewVariable.MinValue" 
	         UpdateSourceTrigger="PropertyChanged" 
	         TargetNullValue="Не задан"/>
     </TextBox.Text>
 </TextBox>
```

