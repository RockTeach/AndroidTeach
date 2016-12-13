# SpannableString #
>* CharSequence的一个实现类，可以使我们的CharSequence变得更加丰富多彩，比如可以为其中的文字设置不同的颜色，再比如可以为其中的在TextView上实现图文混排，添加URL的超链接等等...

## 构造及使用 ##
>* SpannableString的构造直接通过new构造出来，参数是一个CharSequence对象
>* 当构造完成我们的SpannableString后，我们就可以去操作我们的SpannableString了，类中直接拥有的方法并不多setSpan(),removeSpan()
>* setSpan()方法中共有三个参数
    - ① Object对象，对应Span效果
    - ② int start 开始的位置
    - ③ int end 结束的位置
    - ④ int flag

### Span效果 ###
- ClickableSpan 为CharSequence添加局部点击 一定注意TextView需要设置setMovementMethod(LinkMovementMethod.getInstance())
- URLSpan 为TextView局部添加超链接，默认会跳外部浏览器，同样需要setMovementMethod(LinkMovementMethod.getInstance())，如果需要自己对连接处理需要自己实现 LinkMovementMethod
- ForegroundColorSpan 为CharSequence指定文字设置前景色（实际就是文字颜色）
- BackgroundColorSpan 为CharSequence指定文字设置背景色
- StrikeThroughSpan 过时线（中划线，删除线）常用来标记过时，打折信息
- UnderlineSpan 下划线,强调
- SubscriptSpan 下标，角标，通常用在数学公式上
- SuperscriptSpan 上标，通常用在引用和数学公式上
- AbsoluteSizeSpan 为CharSequence指定指定字符设置绝对大小
- RelativeSizeSpan 为CharSequence指定指定字符设置相对大小（比绝对大小的适配要好）
- StyleSpan 为文字设置粗体，斜体等样式
- TypefaceSpan 为文字设置字体
- TextAppearanceSpan 设置文字的偏好属性（其中包括大小，颜色，样式，字体）
- ScaleXSpan 基于x轴的缩放
- MaskFilterSpan 为文字设置过滤效果（有点类似滤镜的感觉，但是种类比较少),目前只有两种模糊和浮雕,关联BlurMaskFilter,EmbossMaskFilter
- RasterizerSpan 为文字设置光栅效果
- SuggestionSpan 用在EditText中，常用语输入法
- LocaleSpan 动态改变Local属性的Span
- ImageSpan 图片，加载drawable，实现TextView的图文混排，通常在聊天的表情中比较常用，还有就是社区微博类状态那比较常用
- 其中还有几个抽象类MetricAffectingSpan，使我们上面所描述大多类的父类
- DynamicDrawableSpan，ImageSpan的父类，想要定制一些特殊效果可以自己继承进行定制
- ReplacementSpan,DynamicDrawableSpan的父类

### Flag意义 ###
>* INCLUSIVE 包含的，EXCLUSIVE 排外的，不包含的
- SPAN_INCLUSIVE_EXCLUSIVE
- SPAN_INCLUSIVE_INCLUSIVE  
- SPAN_EXCLUSIVE_EXCLUSIVE
- SPAN_EXCLUSIVE_INCLUSIVE
>* 然后自己翻译过来就好啦，实际上在TextView上直接设置效果都一样,这些参数在EditText上才有效果

### TextPaint ###
>* 我们在定制一些具体效果的时候，经常会看到这个类的身影，其实这个类也没有多神秘，他是Paint的子类，只是携带了一些默认的属性
>* 在使用的时候，我们对画笔做的操作都可以用在上面
