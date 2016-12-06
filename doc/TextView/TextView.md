# TextView #
<p>
TextView在Android是一个使用非常频繁的控件，但是你真的把它用熟了嘛？有的同学说了，这有啥难的，天天用，都要用烂了。那么我想问你一个问题啊？TextView.setText()这个方法的参数是什么？有同学立马就答上来了，不就是String嘛，然后并不是的，实际上是CharSequence。
</p>

## CharSequence ##
>* 字符序列，Java中的一个接口，拥有大量的实现类,比如我们的String就是其中的一个子类
>* 其实在Android中还有一系列的CharSequence子类来使我们的文本更丰富

### SpannableString ###
>* CharSequence的一个实现类
