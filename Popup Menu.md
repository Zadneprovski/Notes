
```xml
<menu xmlns:android="http://schemas.android.com/apk/res/android">  
    <item  
        android:id="@+id/delete"  
        android:icon="@drawable/delete"  
        android:title="Удалить" />  
    <item  
        android:id="@+id/move"  
        android:icon="@drawable/move"  
        android:title="Переместить" />  
    <item  
        android:id="@+id/add"  
        android:icon="@android:drawable/ic_input_add"  
        android:title="Создать" />  
  
    <item  
        android:id="@+id/copy"  
        android:icon="@drawable/copy"  
        android:title="Копировать" />  
  
    <item  
        android:id="@+id/rename"  
        android:icon="@drawable/rename"  
        android:title="Переименовать" />  
</menu>
```

```java
PopupMenu popupMenu = new PopupMenu(context, view);  
popupMenu.inflate(R.menu.tree_node_menu);  
  
popupMenu.setOnMenuItemClickListener(new PopupMenu.OnMenuItemClickListener() {  
    @Override  
    public boolean onMenuItemClick(MenuItem item) {  
        switch (item.getItemId()) {  
            case R.id.move:  
                return true;  
            case R.id.copy:  
                return true;  
            case R.id.paste:  
                return true;  
            case R.id.rename:  
                return true;  
            case R.id.add:  
                return true;  
            default:  
                return false;  
        }  
    }  
});  
  
popupMenu.show();
```

