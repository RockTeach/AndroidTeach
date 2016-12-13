# 焦点抢占 #
>* 当我们使用RecyclerView加载布局并在item中使用按压的selector的时候,我们会发现selector没有效果
>* 实际上是由于RecyclerView抢占了焦点，焦点并没有传递给item

# 解决方案 #
>* 在RecyclerView标签上使用 android:descendantFocusability="afterDescendants"
>* 在item布局上添加android:focusable="true"，android:clickable="true"

# 这里顺便说一下共享焦点的属性 #
>* android:duplicateParentState="true" 
