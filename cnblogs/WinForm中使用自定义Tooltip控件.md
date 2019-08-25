<ul>
<li>private <strong>ToolTip</strong> tooltipCtr;</li>
<li>构造函数中：
<ul>
<li>隐藏默认的Tooltip：this.<strong>ShowCellToolTips</strong> = false;</li>
<li>this.tooltipCtr = new ToolTip();</li>
<li>设置停留时间（还有许多其他时间设置）：this.tooltipCtr.<strong>AutoPopDelay</strong> = 1000 * 60;</li>
<li><strong>在CellMouseEnterHandler等事件中设置数据，在鼠标处</strong>设置文本：tooltipCtr.<strong>Show</strong>(HttpUtility.HtmlDecode(((BasicData)this.Rows[rowIndex].DataBoundItem).VarDescLong), this, <strong>PointToClient</strong>(MousePosition));</li>
<li>限制宽度/每行字符数（自动换行）
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('c8f6be46-c366-45eb-9f17-86fc49321b51')"><img id="code_img_closed_c8f6be46-c366-45eb-9f17-86fc49321b51" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_c8f6be46-c366-45eb-9f17-86fc49321b51" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('c8f6be46-c366-45eb-9f17-86fc49321b51',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_c8f6be46-c366-45eb-9f17-86fc49321b51" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">const</span> <span style="color: #0000ff;">int</span> maximumSingleLineTooltipLength = <span style="color: #800080;">120</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 2</span> 
<span style="color: #008080;"> 3</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">string</span> AddNewLinesForTooltip(<span style="color: #0000ff;">string</span><span style="color: #000000;"> text)
</span><span style="color: #008080;"> 4</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 5</span>             <span style="color: #0000ff;">if</span> (text.Length &lt;<span style="color: #000000;"> maximumSingleLineTooltipLength)
</span><span style="color: #008080;"> 6</span>                 <span style="color: #0000ff;">return</span><span style="color: #000000;"> text;
</span><span style="color: #008080;"> 7</span> 
<span style="color: #008080;"> 8</span>             StringBuilder sb = <span style="color: #0000ff;">new</span><span style="color: #000000;"> StringBuilder();
</span><span style="color: #008080;"> 9</span> 
<span style="color: #008080;">10</span>             <span style="color: #0000ff;">int</span> currentLinePosition = <span style="color: #800080;">0</span><span style="color: #000000;">;
</span><span style="color: #008080;">11</span>             <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> textIndex = <span style="color: #800080;">0</span>; textIndex &lt; text.Length; textIndex++<span style="color: #000000;">)
</span><span style="color: #008080;">12</span> <span style="color: #000000;">            {
</span><span style="color: #008080;">13</span> <span style="color: #000000;">                sb.Append(text[textIndex]);
</span><span style="color: #008080;">14</span> 
<span style="color: #008080;">15</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> if reach the original new line in the text
</span><span style="color: #008080;">16</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> just recount</span>
<span style="color: #008080;">17</span>                 <span style="color: #0000ff;">if</span><span style="color: #000000;"> (text[textIndex].Equals(Environment.NewLine)
</span><span style="color: #008080;">18</span>                     || (text[textIndex].Equals(<span style="color: #800000;">'</span><span style="color: #800000;">\n</span><span style="color: #800000;">'</span>) &amp;&amp; textIndex &gt;= <span style="color: #800080;">1</span> &amp;&amp; text[textIndex - <span style="color: #800080;">1</span>].Equals(<span style="color: #800000;">'</span><span style="color: #800000;">\r</span><span style="color: #800000;">'</span><span style="color: #000000;">)))
</span><span style="color: #008080;">19</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">20</span>                     currentLinePosition = <span style="color: #800080;">0</span><span style="color: #000000;">;
</span><span style="color: #008080;">21</span>                     <span style="color: #0000ff;">continue</span><span style="color: #000000;">;
</span><span style="color: #008080;">22</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">23</span> 
<span style="color: #008080;">24</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> If we have reached the target line length 
</span><span style="color: #008080;">25</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> and the nextcharacter is whitespace 
</span><span style="color: #008080;">26</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> and don't break \r\n
</span><span style="color: #008080;">27</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> then begin a new line.</span>
<span style="color: #008080;">28</span>                 <span style="color: #0000ff;">if</span> (currentLinePosition &gt;=<span style="color: #000000;"> maximumSingleLineTooltipLength
</span><span style="color: #008080;">29</span>                     &amp;&amp; <span style="color: #0000ff;">char</span><span style="color: #000000;">.IsWhiteSpace(text[textIndex])
</span><span style="color: #008080;">30</span>                     &amp;&amp; !(text[textIndex].Equals(<span style="color: #800000;">'</span><span style="color: #800000;">\r</span><span style="color: #800000;">'</span>) &amp;&amp; textIndex &lt; text.Length &amp;&amp; text[textIndex + <span style="color: #800080;">1</span>].Equals(<span style="color: #800000;">'</span><span style="color: #800000;">\n</span><span style="color: #800000;">'</span><span style="color: #000000;">)))
</span><span style="color: #008080;">31</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">32</span> <span style="color: #000000;">                    sb.Append(Environment.NewLine);
</span><span style="color: #008080;">33</span>                     currentLinePosition = <span style="color: #800080;">0</span><span style="color: #000000;">;
</span><span style="color: #008080;">34</span>                     <span style="color: #0000ff;">continue</span><span style="color: #000000;">;
</span><span style="color: #008080;">35</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">36</span> 
<span style="color: #008080;">37</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> Append the next character.</span>
<span style="color: #008080;">38</span>                 <span style="color: #0000ff;">if</span> (textIndex &lt;<span style="color: #000000;"> text.Length)
</span><span style="color: #008080;">39</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">40</span>                     currentLinePosition++<span style="color: #000000;">;
</span><span style="color: #008080;">41</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">42</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">43</span> 
<span style="color: #008080;">44</span>             <span style="color: #0000ff;">return</span><span style="color: #000000;"> sb.ToString();
</span><span style="color: #008080;">45</span>         }</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>&nbsp;</p>
</li>
</ul>
</li>
</ul>
</li>
</ul>