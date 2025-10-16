---
title: RecyclerView 滚动监听
date: 2018-05-28 17:17:17
tags:
  - android
categories: Android
---
偶然在在网上看到的，先抄下来~

```java
recyclerView = (RecyclerView) findViewById(R.id.recyclerView);
manager = new LinearLayoutManager(this);
adapter = new RecyclerAdapter();
//设置布局管理器
recyclerView.setLayoutManager(manager);
//给recyclerview设置适配器
recyclerView.setAdapter(adapter);
```

如果是想每个item横向滑动的话，只需要使用LinearLayoutManager的三个参数的构造方法就行了，new LInearLayoutManager(this, LinearLayoutManager.HORIZONTAL, true)就可以实现横向滑动的效果了。

给RecyclerView添加滚动监听。与listView和gridView有写不同，recyclerView判断滚动到哪一个位置的时候，需要使用到使用到布局管理器（前面设置的recyclerview.setLayoutManager()）。

```
recyclerView_follow.addOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
        super.onScrollStateChanged(recyclerView, newState);
        //滚动的状态改变时，调用此方法。
    }
 
    @Override
    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
        super.onScrolled(recyclerView, dx, dy);
        //屏幕中最下面一个item的所在数据源的位置(postion)。
        int lastVisiableItem = manager.findLastVisibleItemPosition();
        //一共有多少个
        int totalItemCount = manager.getItemCount();
        //当滑动到倒数第二个item时，即联网获取下一页的数据
        if (lastVisiableItem >= totalItemCount - 2 && dy > 0) {
            page++;//第二页
            reloadData(page);
        }
    }
});
```