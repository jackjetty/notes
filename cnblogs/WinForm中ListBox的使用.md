<ul>
<li>获取选中数据：listbox.SelectedItem as XXX</li>
<li>重绘每一行item
<ul>
<li><strong>DrawMode</strong>设置为<strong>DrawMode.OwnerDrawVariable</strong></li>
<li>然后实现<strong>DrawItem</strong>(object sender, DrawItemEventArgs e)</li>
<li>重绘选中行（鼠标所在行），如修改选中行的背景色
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('3cc75f4f-c9b3-43b6-b210-8ec0b234b0ed')"><img id="code_img_closed_3cc75f4f-c9b3-43b6-b210-8ec0b234b0ed" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_3cc75f4f-c9b3-43b6-b210-8ec0b234b0ed" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('3cc75f4f-c9b3-43b6-b210-8ec0b234b0ed',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_3cc75f4f-c9b3-43b6-b210-8ec0b234b0ed" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span>             <span style="color: #0000ff;">if</span> ((e.State &amp; DrawItemState.Selected) ==<span style="color: #000000;"> DrawItemState.Selected)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 3</span>                 e = <span style="color: #0000ff;">new</span><span style="color: #000000;"> DrawItemEventArgs(e.Graphics,
</span><span style="color: #008080;"> 4</span> <span style="color: #000000;">                                  e.Font,
</span><span style="color: #008080;"> 5</span> <span style="color: #000000;">                                  e.Bounds,
</span><span style="color: #008080;"> 6</span> <span style="color: #000000;">                                  e.Index,
</span><span style="color: #008080;"> 7</span>                                   e.State ^<span style="color: #000000;"> DrawItemState.Selected,
</span><span style="color: #008080;"> 8</span> <span style="color: #000000;">                                  e.ForeColor,
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">                                  DataSourceUIParams.TableRowBackColorWhenHover);
</span><span style="color: #008080;">10</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">11</span> 
<span style="color: #008080;">12</span>             e.DrawBackground();</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
<li>重绘所有行</li>
</ul>
</li>
</ul>