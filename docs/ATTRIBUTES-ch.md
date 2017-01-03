为了提供丰富的布局能力，我们为LayoutHelper设计了一系列布局属性，用来控制布局逻辑和样式。这里介绍这些属性的概念和用法。

# Margin, Padding

Margin, Padding就是外边距、内边距，概念与Android系统的Margin, Padding一样，但也有不同的地方：

+ 它不是整个RecyclerView页面的Margin和Padding，它是每一块LayoutHelper所负责的区域的Margin和Padding。
+ 一个页面里可以有多个LayoutHelper，意味着不同LayoutHelper可以设置不同的Margin和Padding。
+ LayoutHelper的Margin和Padding与页面RecyclerView的MarginPadding可以共存。

![TODO]()

### 接口

对于LayoutHelper，调用

```public void setPadding(int leftPadding, int topPadding, int rightPadding, int bottomPadding)```
```public void setMargin(int leftMargin, int topMargin, int rightMargin, int bottomMargin)```

# bgColor, bgImg

背景颜色或者背景图，这其实不是布局属性，但是由于在vlayout对View进行了直接布局，不同区域的View的父节点都是RecyclerView，如果想要针对某一块区域单独绘制背景，就很难做到了。vlayout框架对此做了特殊处理，对于非fix、非float类型的LayoutHelper，支持配置背景色或背景图。

### 接口

使用背景色

```public void setBgColor(int bgColor)```

使用背景图

首先为LayoutManager提供一个ImageView简单工厂

```
this.mLayoutManager.setLayoutViewFactory(new LayoutViewFactory() {
            @Override
            public View generateLayoutView(@NonNull Context context) {
                return new XXImageView(context);
            }
        });
```

再为LayoutHelper提设置图片加载的Listener

```
baseHelper.setLayoutViewBindListener(new BindListener(imgUrl));
baseHelper.setLayoutViewUnBindListener(new UnbindListener(imgUrl));


private static class BindListener implements BaseLayoutHelper.LayoutViewBindListener {
        private String imgUrl;

        public BindListener(String imgUrl) {
            this.imgUrl = imgUrl;
        }

        @Override
        public void onBind(View layoutView, BaseLayoutHelper baseLayoutHelper) {
            //loading image
        }
    }

    private static class UnbindListener implements BaseLayoutHelper.LayoutViewUnBindListener {
        private String imgUrl;

        public UnbindListener(String imgUrl) {
            this. imgUrl = imgUrl;
        }

        @Override
        public void onUnbind(View layoutView, BaseLayoutHelper baseLayoutHelper) {
        		//cancel loading image
        }
    }
```

# AspectRatio

为了保证布局过程中View的高度一致，我们设计了AspectRatio属性，它是宽与高的比例，LayoutHelper里有AspectRatio属性，通过VirtualLayout添加的View的LayoutParams也有AspectRatio属性，后者的优先级比前者高，但含义不一样。

+ LayoutHelper定义的AspectRatio，指的是一行View整体的宽度与高度之比，当然整体的宽度是减去了RecyclerView和对应的LayoutHelper的Margin, Padding。
+ View的LayoutParams定义的AspectRatio，指的是在LayoutHelper计算出View的宽度之后，用来确定View的高度时使用的，它会覆盖通过LayoutHelper的AspectRatio计算出来的View的高度，因此具备更高优先级。

![TODO]()

### 接口

对于LayoutHelper，调用

```public void setAspectRatio(float aspectRatio)```

对于View，调用

```((VirutalLayoutManager.LayoutParams) layoutParams).mAspectRatio```

# dividerHeight

LinearLayoutHelper的属性，LinearLayoutHelper是像ListView一样的线性布局，dividerHeight就是每个item之间的间距。

![TODO]()

### 接口

对于LinearLayoutHelper，调用

```public void setDividerHeight(int dividerHeight)```

# weights

ColumnLayoutHelper, GridLayoutHelper的属性，它们都是提供网格状的布局能力，**建议使用GridLayoutHelper**，它的能力更加强大，参考下文介绍。默认情况下，每个网格中每一列的宽度是一样的，通过weights属性，可以指定让每一列的宽度成比例分配，就行LinearLayout的weight属性一样。
weights属性是一个float数组，每一项代表某一列占父容器宽度的百分比，总和建议是100，否则布局会超出容器宽度；如果布局中有4列，那么weights的长度也应该是4；长度大于4，多出的部分不参与宽度计算；如果小于4，不足的部分默认平分剩余的空间。

![TODO]()

### 接口

对于ColumnLayoutHelper, GridLayoutHelper，调用

```public void setWeights(float[] weights)```

# vGap, hGap

GridLayoutHelper与StaggeredGridLayoutHelper都有这两个属性，分别控制View之间的垂直间距和水平间距。

![TODO]()

### 接口

对于GridLayoutHelper, StaggeredGridLayoutHelper ，调用

```public void setHGap(int hGap)```
```public void setVGap(int vGap)```

# spanCount, spanSizeLookup

参考于系统的GridLayoutManager，spanCount表示网格的列数，默认情况下每一个View都占用一个网格区域，但通过提供自定义的spanSizeLookUp，可以指定某个位置的View占用多个网格区域。

![TODO]()

### 接口

使用spanCount调用

```public void setSpanCount(int spanCount)```

使用spanSizeLookup

```public void setSpanSizeLookup(SpanSizeLookup spanSizeLookup)```


# lane

StaggeredGridLayoutHelper中有这个属性，与GridLayoutHelper里的spanCount类似，控制瀑布流的列数。

### 接口

调用

```public void setLane(int lane)```