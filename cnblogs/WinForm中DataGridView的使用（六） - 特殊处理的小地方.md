<ul>
<li>列标题不能居中的解决方法
<ul>
<li>一般列标题的居中我们都使用this.ColumnHeadersDefaultCellStyle.Alignment = DataGridViewContentAlignment.MiddleCenter; 这样就居中的了，但如果行单元格也居中时你仔细看会发现列标题并没有完全居中，而是略微往左边一点。</li>
<li>在DataGridview所在窗体的构造方法中添加语句，遍历DataGridview的列，<strong>设置列的排序模式为无排序</strong>
<ul>
<li><strong>注意要在绑定数据后，比如如果是动态设置的DataSource，那么就要设置完以后再修改</strong></li>
<li>
<p> foreach (DataGridViewColumn item in this.Columns)<br />                    {<br />                        item.SortMode = DataGridViewColumnSortMode.NotSortable;<br />                    }</p>


</li>


</ul>


</li>
<li>最后分析一下原因，其实是DataGridView列SortMode属性（排序模式）默认为Automatic 在此模式下，列标题右边有预留一个排序小箭头的位置，所以整个列标题就向左边多一点，而当把SortMode属性设置为NotSortable时，不使用排序，也就没有那个预留的位置，所有完全居中了</li>


</ul>


</li>


</ul>