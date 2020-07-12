# DataBindingAdapter
  RecyclerView快速实现数据绑定以及多布局实现
  
  [![](https://jitpack.io/v/shengjiangbo/DataBindingAdapter.svg)](https://jitpack.io/#shengjiangbo/DataBindingAdapter)
  
  将其添加到存储库末尾的root build.gradle中：
```
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```
  添加依赖项

```
	dependencies {
		implementation 'com.github.shengjiangbo:DataBindingAdapter:1.0.1'
	}
```

# 两种实现方式
  1. 直接继承 BaseDataBindingAdapter
     
     如果要实现item复杂逻辑 请实现:
```
    @Override
    protected void convert(BaseBindHolder holder, ViewDataBinding binding, BaseBindBean item) {
        holder.addOnLongClickListener(R.id.img, R.id.msg);
        holder.addOnClickListener(R.id.img, R.id.msg);
        if (binding instanceof ItemBinding) {
            ItemBinding itemBinding = (ItemBinding) binding;
            DemoBean bean = (DemoBean) item;
        }
        if (binding instanceof Item1Binding) {
            Item1Binding item1Binding = (Item1Binding) binding;
            Demo1Bean bean = (Demo1Bean) item;
        }
    }
```
    
    单或多布局实现 直接在继承BaseDataBindingAdapter类的构造方法添加 addItemType(0, R.layout.item, BR.data);
```
    public DemoAdapter() {
        //参数1:多布局区分type(数据Bean继承BaseDataBindingBean 实现getItemType,对应以下布局)
        //参数2:布局
        //参数3:DataBinding BR 绑定数据Variable name
        addItemType(0, R.layout.item, BR.data);
        addItemType(1, R.layout.item1, BR.data);
    }
```
    添加数据方式
```
    //直接使用BaseBindBean 添加数据的时候可以直接添加
    private List<BaseBindBean> list = new ArrayList<>();
    Demo1Bean bean1 = new Demo1Bean();
    bean1.setType(1);//type区分
    DemoBean bean = new DemoBean();
    bean.setType(0);
    list.add(bean1);
    list.add(bean);
    mAdapter.setNewData(list);
    mAdapter.addData(list);
    mAdapter.addData(index,list);
    mAdapter.addData(index,bean);
```
    设置上拉加载更多(注意一定一定要自己继承LoadMoreView自定义布局)
```
     mAdapter.setLoadMoreView(new MainLoadMoreView());//MainLoadMoreView 具体实现请查看demo
     mAdapter.setOnLoadMoreListener(this, binding.recyclerView);
     @Override
     public void onLoadMoreRequested() {
     //...获取数据
     }
```
  2. 直接链式调用方式
```
      mQuickAdapter = QuickBindingAdapter.Create()
                .bindingItem(0, R.layout.item, BR.data)//type 根据布局继承BaseDataBindingBean 添加布局 BR.data 要跟布局variable name保持一 致 添加数据的时候 type 数据也要一致
                .bindingItem(1, R.layout.item1, BR.data)//添加第二个布局布局
                .setLoadMoreView(new MainLoadMoreView())//设置上拉加载更多布局  继承 LoadMoreView
                .setOnLoadMoreListener(this, binding.recyclerView)//上拉加载更多监听
                .addOnClickListener(R.id.msg, R.id.img)//设置控件的点击监听
                .addOnLongClickListener(R.id.msg, R.id.img)//设置控件长按监听
                .setOnQuickConvertListener()//用于自定义更多功能
                .setOnItemChildClickListener()//实现item子控件点击监听
                .setOnItemClickListener()//实现item控件点击监听
                .setOnItemLongClickListener()//实现item控件长按监听
                .setOnItemChildLongClickListener();//实现item子控件长按监听
```
    添加数据同上