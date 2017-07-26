- 设置主活动布局、菜单子项
- 数据包：

- ColorSuggession 建议数据

- ColorWarper 颜色数据

- DataHelper 在数据中找到推荐项

- 设置RecyclerView 的适配器

-

# 部分方法解析

- DataHelper.findSuggesstions()

```java
//DataHelper
public static void findSuggesstions(Context context, String query, final int limit, final OnFindSuggesstionsListener listener) {
        //测试
        xjbInitSuggesstionItemList();
        //
        new Filter() {

            @Override
            protected FilterResults performFiltering(CharSequence constraint) {
                DataHelper.resetSuggesstionHistory();
                //过滤后的SuggesstionItem
                List<SuggesstionItem> suggesstionItemList = new ArrayList<>();

                //把过滤后的suggestion存入list
                if (constraint != null && constraint.length() != 0) {
                    for (SuggesstionItem suggesstion : mSuggesstionItemList) {
                        if (suggesstion.getBody().toUpperCase().startsWith(constraint.toString().toUpperCase())) {
                            suggesstionItemList.add(suggesstion);
                            if (limit != -1 && suggesstionItemList.size() == limit) {
                                break;
                            }
                        }
                    }
                }

                //存放过滤后的list对象，list大小
                FilterResults results = new FilterResults();
                Collections.sort(suggesstionItemList, new Comparator<SuggesstionItem>() {
                    @Override
                    public int compare(SuggesstionItem o1, SuggesstionItem o2) {
                        return o1.isHistory() ? -1 : 0;
                    }
                });
                results.values = suggesstionItemList;
                results.count = suggesstionItemList.size();

                return results;
            }

            @Override
            protected void publishResults(CharSequence constraint, FilterResults results) {
                //保险措施
                if (listener != null) {
                    //强转
                    listener.onResults((List<SuggesstionItem>) results.values);
                }
            }
        }.filter(query);
    }
```

在调用这个方法的时候传入匿名listener接口重写,方法内建立了一个新的Fileter匿名类对象，调用此对象的filter方法，此方法依次调用Filter匿名类重写的两个方法，在publishResults方法里调用接口的方法。

* SuggesstionItem类的isHistory是用于onBindSuggesstion回调方法，用来判断是不是历史
