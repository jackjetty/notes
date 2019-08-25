<ul>
<li>TabControl和TabPage之间有一个默认颜色的边框，很难去除，需要重写TabControl控件重绘区域</li>
</ul>
<div class="cnblogs_code" onclick="cnblogs_code_show('7370c536-1b4c-4bdc-9953-3f85cf19d1d0')"><img id="code_img_closed_7370c536-1b4c-4bdc-9953-3f85cf19d1d0" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_7370c536-1b4c-4bdc-9953-3f85cf19d1d0" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('7370c536-1b4c-4bdc-9953-3f85cf19d1d0',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_7370c536-1b4c-4bdc-9953-3f85cf19d1d0" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span>     <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> FullTabControl : TabControl
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">    {
</span><span style="color: #008080;"> 3</span>         <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">override</span><span style="color: #000000;"> Rectangle DisplayRectangle
</span><span style="color: #008080;"> 4</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 5</span>             <span style="color: #0000ff;">get</span>
<span style="color: #008080;"> 6</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 7</span>                 Rectangle rect = <span style="color: #0000ff;">base</span><span style="color: #000000;">.DisplayRectangle;
</span><span style="color: #008080;"> 8</span>                 <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">new</span> Rectangle(rect.Left - <span style="color: #800080;">8</span>, rect.Top - <span style="color: #800080;">8</span>, rect.Width + <span style="color: #800080;">16</span>, rect.Height + <span style="color: #800080;">16</span><span style="color: #000000;">);
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">10</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">11</span>     }</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<ul>
<li style="list-style-type: none;">
<ul>
<li>&nbsp;如果需要与父控件之间有间隔，可以用tabpage的padding来模拟</li>
</ul>
</li>
</ul>