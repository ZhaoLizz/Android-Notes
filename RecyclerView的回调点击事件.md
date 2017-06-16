# RecyclerView点击事件的回调机制
* 当需要用到点击事件发送intent时，必须用回调
* 可以利用  控件.setTag(),view.getTag()，利用view传递数据

1. 创建点击事件回调接口，创建对象，创建set接口的构造方法
2. 在recyclerView普通点击事件里调用接口的点击方法  (onCreateViewHolder内)
3. 在活动内调用set接口的方法来实现接口并设置具体的点击事件‘

```java
//Adapter内
public  interface OnRecyclerViewClickListener{
        void onItemClick(View v,String num);
    }

public void setOnRecyclerViewClickListener(OnRecyclerViewClickListener aonRecyclerViewClickListener){
        onRecyclerViewClickListener = aonRecyclerViewClickListener;
    }

private OnRecyclerViewClickListener onRecyclerViewClickListener = null;
```

```java
@Override
    public void onBindViewHolder(final ViewHolder holder, final int position) {
        Contacts contacts = mContactsList.get(position);
        holder.numberText.setText(contacts.getNumber());
        holder.nameText.setText(contacts.getName());
        //设置想向接口方法传递的数据
        //因为onClick只有View参数，只能通过view传递
        holder.itemView.setTag(holder.numberText.getText().toString());
    }
```

```java
@Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.contacts_layout, parent, false);
        final ViewHolder holder = new ViewHolder(view);
        view.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(onRecyclerViewClickListener != null){
                //调用接口的方法，通过View参数传入数据
                    onRecyclerViewClickListener.onItemClick(v,(String)v.getTag());
                }
            }
        });

        return holder;
    }
```
```java
//MainActivity
RecyclerView recyclerView = (RecyclerView)findViewById(R.id.recyclerView);
        ContactsAdapter adapter = new ContactsAdapter(contactsList);
        LinearLayoutManager manager = new LinearLayoutManager(this);
        
        adapter.setOnRecyclerViewClickListener(new ContactsAdapter.OnRecyclerViewClickListener() {
            @Override
            public void onItemClick(View v, String num) {
                if (ContextCompat.checkSelfPermission(MainActivity.this,Manifest.permission.CALL_PHONE) == PackageManager.PERMISSION_GRANTED){
                    Intent intent = new Intent(Intent.ACTION_CALL);
                    intent.setData(Uri.parse("tel:" + num));
                    startActivity(intent);
                }else{
                    ActivityCompat.requestPermissions(MainActivity.this,new String[]{Manifest.permission.CALL_PHONE},2);
                }
            }
        });

        recyclerView.setAdapter(adapter);
        recyclerView.setLayoutManager(manager);
```
