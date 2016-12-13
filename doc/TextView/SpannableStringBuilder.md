# SpannableStringBuilder #
>* CharSequence的一个实现类
>* 使用起来和StringBuilder有些类似
>*  append(CharSequence text, Object what, int flags)
  - 追加时的参数
  - ① 字符序列
  - ② 就是我们的Span对象了
  - ③ 还是我们说过的标记
>* 最后有一点就是我们在设置到TextView上前往不要toString，要记得咱们说过TextView的setText参数
