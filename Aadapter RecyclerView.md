Идея для реализации адаптера

```java
List<String> items = Arrays.asList("Отчеты", "Облако", "Настройки", "Оборудование", "Персонал");  
List<Integer> icons = Arrays.asList(  
        R.drawable.file,  
        R.drawable.file,  
        R.drawable.file,  
        R.drawable.file,  
        R.drawable.file  
);  
  
MyAdapter adapter = new MyAdapter(getActivity(), items, icons, position -> {  
    // Обработчик нажатий на элементы списка  
    switch (position) {  
        case 0:  
            Intent intent = new Intent(getActivity(), ReportListActivity.class);  
            startActivity(intent);  
            break;  
        case 1:  
            // Код для "Облако"  
            break;  
        case 2:  
            // Код для "Настройки"  
            break;  
        case 3:  
            // Код для "Оборудование"  
            break;  
        case 4:  
            // Код для "Персонал"  
            break;  
    }  
});  
  
recyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));  
recyclerView.setAdapter(adapter);
```

```java
public class MyAdapter extends RecyclerView.Adapter<MyAdapter.ViewHolder> {  
  
    private final List<String> items;  
    private final List<Integer> icons;  
    private final Context context;  
    private final OnItemClickListener listener;  
  
    public interface OnItemClickListener {  
        void onItemClick(int position);  
    }  
  
    public MyAdapter(Context context, List<String> items, List<Integer> icons, OnItemClickListener listener) {  
        this.items = items;  
        this.icons = icons;  
        this.context = context;  
        this.listener = listener;  
    }  
  
    @NonNull  
    @Override    public ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {  
        View view = LayoutInflater.from(parent.getContext())  
                .inflate(R.layout.list_item, parent, false);  
        return new ViewHolder(view);  
    }  
  
    @Override  
    public void onBindViewHolder(@NonNull ViewHolder holder, int position) {  
        holder.itemText.setText(items.get(position));  
        holder.itemIcon.setImageResource(icons.get(position));  
        holder.itemView.setOnClickListener(v -> listener.onItemClick(position));  
    }  
  
    @Override  
    public int getItemCount() {  
        return items.size();  
    }  
  
    public static class ViewHolder extends RecyclerView.ViewHolder {  
        public ImageView itemIcon;  
        public TextView itemText;  
  
        public ViewHolder(View itemView) {  
            super(itemView);  
            itemIcon = itemView.findViewById(R.id.itemIcon);  
            itemText = itemView.findViewById(R.id.itemText);  
        }  
    }
```