# RecyclerView

- ViewHolder只做一件事：持有(容纳)View视图
- Adapter的职责：创建必要的ViewHolder、绑定ViewHolder至模型曾数据
- Recycler先调用getItemCount询问数组列表中包含多少个对象、然后调用onCreateViewHolder创建ViewHolder及其要显示的视图、最后调用onBindViewHolder绑定数据

- 先在app/build.gradle中添加依赖库

- 创建子项信息的类，子项的布局

- 为RecyclerView准备适配器

- 设置recyclerView实例(设置布局方式、适配器)

- 构建适配器

```java
//新建FruitAdapter类
public class FruitAdapter extends RecyclerView.Adapter<FruitAdapter.ViewHolder> {
    private List<Fruit> mFruitList;

    //视图持有器，持有视图的信息
    static class ViewHolder extends RecyclerView.ViewHolder {
        ImageView fruitImage;
        TextView fruitName;
        View fruitView;     //为了持有传入的形参view，设置点击事件

        public ViewHolder(View view) {
            super(view);
            fruitImage = (ImageView) view.findViewById(R.id.fruir_image);
            fruitName = (TextView) view.findViewById(R.id.fruit_name);
            fruitView = view;
        }
    }

    public FruitAdapter(List<Fruit> fruitList) {
        mFruitList = fruitList;
    }


    @Override
    //获取视图持有器ViewHolder实例.设置视图的点击事件
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        //为了ViewHolder的构造函数，先动态加载布局view
        //由父布局找到子布局
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.fruit_item, parent, false);
        final ViewHolder holder = new ViewHolder(view);

        //分别为view、Image注册点击事件
        holder.fruitView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //获取当前fruit实例
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(),"you clicked view"+fruit.getName(),Toast.LENGTH_SHORT).show();
            }
        });

        holder.fruitView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int position = holder.getAdapterPosition();
                Fruit fruit = mFruitList.get(position);
                Toast.makeText(v.getContext(),"you clicked view"+fruit.getName(),Toast.LENGTH_SHORT).show();
            }
        });

        return holder;
    }

    @Override
    //在子项滚动入屏幕时自动执行，为子项的数据动态赋值
    public void onBindViewHolder(ViewHolder holder, int position) {
        Fruit fruit = mFruitList.get(position); //获取fruit实例，为视图添加数据
        holder.fruitName.setText(fruit.getName());
        holder.fruitImage.setImageResource(fruit.getImageId());
    }

    @Override
    public int getItemCount() {
        return mFruitList.size();
    }
}
```

- 设置recyclerView实例

```java
RecyclerView recyclerView = (RecyclerView)findViewById(R.id.recycler_view);

        //指定recyclerView的布局方式
        LinearLayoutManager layoutManager = new LinearLayoutManager(this);
        //设置为横向滚动  layoutManager.setOrientation(LinearLayoutManager.HORIZONTAL);
        //瀑布流：
        //StaggeredGridLayoutManager layoutManager1 = new StaggeredGridLayoutManager(3,StaggeredGridLayoutManager.VERTICAL);

        //获取适配器实例
        FruitAdapter adapter = new FruitAdapter(fruitList);
        recyclerView.setLayoutManager(layoutManager);
        recyclerView.setAdapter(adapter);
```

## 更新数据

- 模型对象的数据改变后要调用`mAdapeter.notifyDataSetChanged()`通知recyclerView刷新数据
- 一般来说，要保证fragmen视图得到刷新，在`onResume()`方法内更新代码是最安全的选择

```java
public void updataUI() {
        //单例类创建实例
        CrimeLab crimeLab = CrimeLab.get(getActivity());
        List<Crime> crimes = crimeLab.getCrimes();

        if (crimes.size() != 0) {
            mNullView.setVisibility(View.GONE);
        } else {
            mNullView.setVisibility(View.VISIBLE);
        }

        if (mAdapter == null) {
            mAdapter = new CrimeAdapter(crimes);
            mCrimeRecycler.setAdapter(mAdapter);
        } else {
            mAdapter.setCrimes(crimes);
            mAdapter.notifyItemChanged(position);
        }

        updateSubtitle();

    }
```
