<ul>
<li>基本用法
<ul>
<li>默认从0开始分配各个枚举值对应的数字值
<ul>
<li>
<div class="cnblogs_code">
<pre>    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">enum</span><span style="color: #000000;"> VariableType
    {
        Type1,
        Type2
    }</span></pre>
</div>
</li>
</ul>
</li>
</ul>
</li>
<li>指定各个枚举值对应的数字值
<ul>
<li>
<div class="cnblogs_code">
<pre>    <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">enum</span><span style="color: #000000;"> VariableType
    {
        Type1 </span>= <span style="color: #800080;">1</span><span style="color: #000000;">,
        Type2 </span>= <span style="color: #800080;">2</span><span style="color: #000000;">
    }</span></pre>
</div>
</li>
</ul>
</li>
<li>转换</li>
<li>使用哲学
<ul>
<li>如果该枚举对应数据库中的字段，通常数据库中该字段存int值，代码中定义该枚举并赋上对应的值。</li>
</ul>
</li>
</ul>