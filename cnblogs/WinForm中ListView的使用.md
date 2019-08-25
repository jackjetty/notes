<ul>
<li><span>每一行是一个<strong>ListViewItem</strong>对象，每一项是一个<strong>ListViewSubItem</strong>对象</span></li>
<li><span style="color: #000000;">样式</span>
<ul>
<li><span style="color: #000000;">整行选择：this.lvDataSourceSearchHistory.<strong>FullRowSelect</strong> = true;</span></li>
<li><span style="color: #000000;">文本是否可编辑：this.lvDataSourceSearchHistory.<strong>LabelEdit</strong> = false;</span></li>
<li><span style="color: #000000;">隐藏列头：this.lvDataSourceSearchHistory.<strong>HeaderStyle</strong> = <strong>ColumnHeaderStyle.None</strong>;</span></li>
<li><span style="color: #000000;">边框样式：this.lvDataSourceSearchHistory.<strong>BorderStyle</strong> = <strong>BorderStyle.Fixed3D</strong>;</span></li>
</ul>
</li>
<li>绑定数据
<ul>
<li>this.lvDataSourceSearchHistory.<strong>Columns.Clear()</strong>;</li>
<li>this.lvDataSourceSearchHistory.<strong>Columns.Add</strong>("Search Text");</li>
<li>this.lvDataSourceSearchHistory.<strong>Columns.Add</strong>("Delete Button");</li>
<li>固定宽度：this.lvDataSourceSearchHistory.<strong>Columns[0].Width</strong> = 440;</li>
<li>固定宽度：this.lvDataSourceSearchHistory.<strong>Columns[1].Width</strong> = 15;</li>
</ul>
</li>
<li>自定义显示每一行每一项的显示
<ul>
<li>先要设置this.<strong>OwnerDraw</strong> = true;才能重绘每一项</li>
</ul>
</li>
</ul>
<p>&nbsp;</p>