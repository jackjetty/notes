<ul>
<li><strong>数据绑定</strong>
<ul>
<li>直接指定源数据（List&lt;T&gt;）：this.<strong>DataSource</strong> = data;</li>
<li>通常也可以直接指定<strong>DataTable</strong>类型的数据
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('bda27647-c085-4951-addd-aaf5aebf6d8f')"><img id="code_img_closed_bda27647-c085-4951-addd-aaf5aebf6d8f" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_bda27647-c085-4951-addd-aaf5aebf6d8f" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('bda27647-c085-4951-addd-aaf5aebf6d8f',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_bda27647-c085-4951-addd-aaf5aebf6d8f" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span> DataTable dt = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DataTable();
</span><span style="color: #008080;"> 2</span>             DataColumn dc1 = <span style="color: #0000ff;">new</span> DataColumn(_column1Name, Type.GetType(<span style="color: #800000;">"</span><span style="color: #800000;">System.String</span><span style="color: #800000;">"</span><span style="color: #000000;">));
</span><span style="color: #008080;"> 3</span> <span style="color: #000000;">            dt.Columns.Add(dc1);
</span><span style="color: #008080;"> 4</span>             <span style="color: #0000ff;">foreach</span> (<span style="color: #0000ff;">string</span> searchText <span style="color: #0000ff;">in</span> VisualXmlApp.Instance.searchHistoryGridView.Take(<span style="color: #800080;">5</span><span style="color: #000000;">))
</span><span style="color: #008080;"> 5</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 6</span>                 DataRow dr =<span style="color: #000000;"> dt.NewRow();
</span><span style="color: #008080;"> 7</span>                 dr[_column1Name] =<span style="color: #000000;"> searchText;
</span><span style="color: #008080;"> 8</span> <span style="color: #000000;">                dt.Rows.Add(dr);
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">10</span>             <span style="color: #0000ff;">this</span>.dgvDataSourceSearchHistory.DataSource = dt;</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
<li>自定义列
<ul>
<li>取消自动生成列：this.<strong>AutoGenerateColumns</strong> = false;</li>
<li>自定义列数、列名、列宽权重、列填充
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('02389841-e197-452a-be85-2a6fd37749d2')"><img id="code_img_closed_02389841-e197-452a-be85-2a6fd37749d2" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_02389841-e197-452a-be85-2a6fd37749d2" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('02389841-e197-452a-be85-2a6fd37749d2',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_02389841-e197-452a-be85-2a6fd37749d2" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span>             <span style="color: #0000ff;">this</span><span style="color: #000000;">.Columns.Clear();
</span><span style="color: #008080;"> 2</span>             <span style="color: #0000ff;">this</span>.ColumnCount = <span style="color: #800080;">3</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 3</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">0</span>].Name = <span style="color: #800000;">"</span><span style="color: #800000;">Column 1</span><span style="color: #800000;">"</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 4</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">0</span>].DataPropertyName = <span style="color: #800000;">"</span><span style="color: #800000;">VarName</span><span style="color: #800000;">"</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 5</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">0</span>].FillWeight = <span style="color: #800080;">200</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 6</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">1</span>].Name = <span style="color: #800000;">"</span><span style="color: #800000;">Column 2</span><span style="color: #800000;">"</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 7</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">1</span>].DataPropertyName = <span style="color: #800000;">"</span><span style="color: #800000;">VarPath</span><span style="color: #800000;">"</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 8</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">1</span>].FillWeight = <span style="color: #800080;">300</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 9</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">2</span>].Name = <span style="color: #800000;">"</span><span style="color: #800000;">Column 2</span><span style="color: #800000;">"</span><span style="color: #000000;">;
</span><span style="color: #008080;">10</span>             <span style="color: #0000ff;">this</span>.Columns[<span style="color: #800080;">2</span>].FillWeight = <span style="color: #800080;">100</span>;</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><strong>样式</strong></li>
<li>
<ul>
<li><strong>整体背景色（当窗口变大时，空白处的颜色）</strong>
<ul>
<li>this.BackgroundColor = DataSourceUIParams.WindowBackColor;</li>
</ul>
</li>
<li><strong>边框</strong>
<ul>
<li><strong><strong>风格</strong></strong>
<ul>
<li><strong><strong>DataGridView.BorderStyle</strong></strong></li>
<ul>
<li>BorderStyle&nbsp;枚举：&nbsp;FixedSingle（单线，默认）、Fixed3D、None</li>
</ul>
</ul>
</li>
<li><strong><strong>颜色（所有边框，含普通数据行和头部）</strong></strong>
<ul>
<li>this.<strong>GridColor</strong>&nbsp;=&nbsp;Color.FromArgb(173, 190, 203);</li>
<li>如果要头部生效，别忘了取消系统风格影响：this.<strong>EnableHeadersVisualStyles</strong>=<span class="hljs-literal">false;</span></li>
<li>默认是&nbsp;ControlDarkDark&nbsp;。但是只有在&nbsp;CellBorderStyle&nbsp;被设定为&nbsp;Single、SingleHorizontal、SingleVertical&nbsp;&nbsp;的条件下才能改变其边框线的颜色。</li>
</ul>
</li>
<li>如果是自定义了一个控件继承自DataGridView，那么即使在这个控件中设置了不显示边框，<span style="color: #ff0000;"><strong>在父控件中仍然需要再设置一次其BorderStyle = BorderStyle.None;</strong></span></li>
<li>其他可参考（如颜色、四个方位边框的单独设置）：https://blog.csdn.net/yunhaic/article/details/7176015</li>
</ul>
</li>
<li><strong>头部<br /></strong>
<ul>
<li>取消使用系统风格：this.<strong>EnableHeadersVisualStyles</strong>&nbsp;= false;</li>
<li><strong>行头</strong>
<ul>
<li>取消显示行头
<ul>
<li>this.<strong>RowHeadersVisible</strong>&nbsp;= false;</li>



</ul>



</li>
<li>边框
<ul>
<li>风格
<ul>
<li>直接设置<strong>RowHeadersBorderStyle</strong>，默认是DataGridViewHeaderBorderStyle.<strong>Raised</strong>，可改为Single、None等。属性设定值是<strong>DataGridViewHeaderBorderStyle</strong>枚举的值</li>




</ul>




</li>




</ul>




</li>




</ul>




</li>
<li><strong>列头</strong>
<ul>
<li>取消显示列头
<ul>
<li>this.<strong>ColumnHeadersVisible</strong>&nbsp;= false;</li>



</ul>



</li>
<li>高度
<ul>
<li>先把<strong>ColumnHeadersHeightSizeMode</strong>从默认的<strong>AutoSize</strong>设置为<strong>EnableResizing</strong></li>
<li>再用<strong>ColumnHeadersHeight</strong>直接设置高度</li>
<li>注：代码构造函数中直接设置这两个属性也可以</li>
<li>注：将DataGridView设置固定高度，并自动显示滚动条时，如果Header高度是AutoSize的，可能会使最后一行显示不全</li>




</ul>




</li>
<li>字体</li>
<li>边框
<ul>
<li>风格
<ul>
<li>直接设置<strong>ColumnHeadersBorderStyle</strong>，默认是DataGridViewHeaderBorderStyle.<strong>Raised</strong>，可改为Single、None等。属性设定值是<strong>DataGridViewHeaderBorderStyle</strong>枚举的值</li>
<li>同样，<strong>ColumnHeadersBorderStyle</strong>只能设定单元格全部边框线的式样。要单独改变单元格某一边边框式样的话，需要用到设定行头单元格的属性是：&nbsp;<strong>RowHeadersBorderStyle</strong>，&nbsp;设定列头单元格属性是：<strong>ColumnHeadersBorderStyle</strong></li>




</ul>




</li>




</ul>




</li>




</ul>




</li>




</ul>




</li>
<li><strong>数据行</strong>
<ul>
<li><strong>高度</strong>
<ul>
<li>this.<strong>RowTemplate.Height</strong> = 30;</li>




</ul>




</li>
<li><strong>奇偶行</strong>
<ul>
<li>背景色
<ul>
<li>this.<strong>AlternatingRowsDefaultCellStyle</strong>.BackColor = Color.FromArgb(223, 230, 237);</li>
<li>this.<strong>RowsDefaultCellStyle</strong>.BackColor = Color.White;</li>




</ul>




</li>




</ul>




</li>
<li><strong>边框</strong>
<ul>
<li>风格
<ul>
<li>直接设置<strong>CellBorderStyle</strong>，默认为<strong>BorderStyle.FixedSingle</strong>，但这个时候底边框有可能消失，可以考虑改为<strong>BorderStyle.Fixed3D</strong></li>
<li>CellBorderStyle只能设定单元格全部边框线的式样。要单独改变单元格某一边边框式样的话，需要用到<strong>DataGridView.AdvancedCellBorderStyle</strong>属性。</li>



</ul>



</li>



</ul>



</li>



</ul>



</li>



</ul>



</li>
<li><strong>是否可编辑、新增、删除</strong></li>
<ul>
<li>this.<strong>ReadOnly</strong> = true;</li>
<li>this.<strong>AllowUserToAddRows</strong>&nbsp;= false;</li>
<li>this.<strong>AllowUserToDeleteRows&nbsp;</strong>= false;</li>









</ul>
<li><strong>是否可调整列宽、行宽</strong>
<ul>
<li>this.<strong>AllowUserToResizeColumns</strong> = false;<br />this.<strong>AllowUserToResizeRows</strong> = false;</li>









</ul>









</li>
<li><strong>是否可选中及选中模式</strong>
<ul>
<li><strong>是否可多选：</strong>this.<strong>MultiSelect</strong> = false;</li>
<li>设置选中模式
<ul>
<li>直接设置<strong>SelectionMode</strong>属性，包括DataGridViewSelectionMode.<strong>FullRowSelect</strong>等选项</li>












</ul>












</li>
<li>取消首行或首个单元格的默认选中
<ul>
<li>在DataGridView的RowsPrePaint事件处理函数中
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('800a6555-5241-42f0-9778-43edf6f517a7')"><img id="code_img_closed_800a6555-5241-42f0-9778-43edf6f517a7" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_800a6555-5241-42f0-9778-43edf6f517a7" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('800a6555-5241-42f0-9778-43edf6f517a7',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_800a6555-5241-42f0-9778-43edf6f517a7" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span> RowsPrePaintHandler(<span style="color: #0000ff;">object</span><span style="color: #000000;"> sender, DataGridViewRowPrePaintEventArgs e)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 3</span>             <span style="color: #0000ff;">int</span> index =<span style="color: #000000;"> e.RowIndex;
</span><span style="color: #008080;"> 4</span> 
<span style="color: #008080;"> 5</span>             <span style="color: #008000;">//</span><span style="color: #008000;"> cancel default selected first row</span>
<span style="color: #008080;"> 6</span>             <span style="color: #0000ff;">if</span> (index == <span style="color: #800080;">0</span><span style="color: #000000;">)
</span><span style="color: #008080;"> 7</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 8</span>                 <span style="color: #0000ff;">this</span>.Rows[index].Selected = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">10</span>         }</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>