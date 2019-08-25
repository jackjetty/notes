<ul>
<li><strong>CellMouseDown/CellMouseUp</strong>
<ul>
<li>可获得行、列号</li>
<li>可用<strong>if (rowIndex &gt;= 0 &amp;&amp; e.Y &gt; 4 &amp;&amp; e.Y &lt; (this.CurrentCell.Size.Height - 4))</strong>来排除对行、列分割线的操作（如调整行列大小），其实就是判断鼠标的操作是否是在边框/分隔线附近</li>
</ul>
</li>
<li><strong>CellContentClick</strong>等事件，必须是点击在文本内容上时才能触发，空白处不行</li>
</ul>