<ul>
<li>DataGridView支持指定DataGridViewImageColumn、DataGridViewButtonColumn等特殊类型的列，加入到Columns中。</li>
</ul>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>             DataGridViewImageColumn imageButton = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DataGridViewImageColumn();
</span><span style="color: #008080;"> 2</span>             imageButton.Name =<span style="color: #000000;"> _column2Name;
</span><span style="color: #008080;"> 3</span>             <span style="color: #008000;">//</span><span style="color: #008000;">imageButton.DataPropertyName = _column2Name;</span>
<span style="color: #008080;"> 4</span>             imageButton.Width = <span style="color: #800080;">20</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 5</span>             imageButton.HeaderText =<span style="color: #000000;"> _column2Name;
</span><span style="color: #008080;"> 6</span>             imageButton.DefaultCellStyle.NullValue = <span style="color: #0000ff;">string</span><span style="color: #000000;">.Empty;
</span><span style="color: #008080;"> 7</span>             imageButton.ValuesAreIcons = <span style="color: #0000ff;">false</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 8</span>             imageButton.Image = ResourceHandler.LoadImage(<span style="color: #800000;">"</span><span style="color: #800000;">CloseInactive</span><span style="color: #800000;">"</span><span style="color: #000000;">);
</span><span style="color: #008080;"> 9</span>             imageButton.ImageLayout =<span style="color: #000000;"> DataGridViewImageCellLayout.Normal;
</span><span style="color: #008080;">10</span>             <span style="color: #0000ff;">this</span>.dgvDataSourceSearchHistory.Columns.Add(imageButton);</pre>
</div>
<p>&nbsp;</p>