<ul>
<li>两种方式
<ul>
<li>TextRenderer.DrawText<br />
<ul>
<li><span style="color: #ff0000;"><strong>注意：默认在每次绘制的文本左右有padding，即使参数中设置了TextFormatFlags.NoPadding也是一样，因此在分段绘制文本时（比如绘制搜索结果文本中高亮一部分时），每次绘制前在定位传递Point参数时，需要进行修正，减去相应个数的padding的宽度（由于需要转成int，所以会有误差）</strong></span>
<ul>
<li><span style="color: #000000;">https://theartofdev.com/2013/08/12/the-wonders-of-text-rendering-and-gdi/<br /></span></li>
<li><span style="color: #000000;">Introduced in .NET 2.0&nbsp;<a href="http://msdn.microsoft.com/en-us/library/system.windows.forms.textrenderer.aspx">TextRenderer</a>&nbsp;is the recommended text rendering method, using GDI library under the hood it provided the best looking text, accurate measurement and proper international support.<br />Using TextRenderer.DrawText and TextRenderer.MeasureText is straightforward and well documented so I won't go into details. The only annoying issue is the padding added to the left (1/6 em) and right (1/4 em) of the drawn/measured text as described&nbsp;<a href="http://stackoverflow.com/questions/4428335/how-to-get-the-exact-text-margins-used-by-textrenderer">here</a>. To have exact draw/measure of text you need the subtract (1/6<em>font.GetHeight()) from left corner during draw and (2.5/6</em>font.GetHeight()) from the measured width (Figure 2).</span></li>

</ul>

</li>

</ul>

</li>
<li>e.Graphics.DrawString</li>

</ul>

</li>

</ul>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span> smartTipListBox_DrawItem(<span style="color: #0000ff;">object</span><span style="color: #000000;"> sender, DrawItemEventArgs e)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 3</span>             ListBox eObj = sender <span style="color: #0000ff;">as</span><span style="color: #000000;"> ListBox;
</span><span style="color: #008080;"> 4</span> 
<span style="color: #008080;"> 5</span> <span style="color: #000000;">            e.DrawBackground();
</span><span style="color: #008080;"> 6</span> 
<span style="color: #008080;"> 7</span>             <span style="color: #0000ff;">string</span> entireText =<span style="color: #000000;"> ((EntireInfo)eObj.Items[e.Index]).VarName;
</span><span style="color: #008080;"> 8</span> 
<span style="color: #008080;"> 9</span>             <span style="color: #0000ff;">int</span> index =<span style="color: #000000;"> entireText.IndexOf(searchingStr, StringComparison.InvariantCultureIgnoreCase);
</span><span style="color: #008080;">10</span>             <span style="color: #0000ff;">if</span> (index &gt;= <span style="color: #800080;">0</span><span style="color: #000000;">)
</span><span style="color: #008080;">11</span> <span style="color: #000000;">            {
</span><span style="color: #008080;">12</span>                 TextFormatFlags formatFlags = TextFormatFlags.NoPadding |<span style="color: #000000;"> TextFormatFlags.SingleLine;
</span><span style="color: #008080;">13</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">TextFormatFlags formatFlags = TextFormatFlags.TextBoxControl | TextFormatFlags.WordBreak;</span>
<span style="color: #008080;">14</span>                 <span style="color: #0000ff;">int</span> leftAndTopPadding = <span style="color: #800080;">5</span><span style="color: #000000;">;
</span><span style="color: #008080;">15</span>                 <span style="color: #0000ff;">string</span> headText = entireText.Substring(<span style="color: #800080;">0</span><span style="color: #000000;">, index);
</span><span style="color: #008080;">16</span>                 <span style="color: #0000ff;">string</span> matchedText =<span style="color: #000000;"> entireText.Substring(index, searchingStr.Length);
</span><span style="color: #008080;">17</span>                 <span style="color: #0000ff;">string</span> tailText = entireText.Substring(index + matchedText.Length, entireText.Length - index -<span style="color: #000000;"> matchedText.Length);
</span><span style="color: #008080;">18</span>                 <span style="color: #0000ff;">int</span> headTextPartWidth = TextRenderer.MeasureText(headText, e.Font, <span style="color: #0000ff;">new</span> Size(<span style="color: #0000ff;">int</span>.MaxValue, <span style="color: #0000ff;">int</span><span style="color: #000000;">.MaxValue), formatFlags).Width;
</span><span style="color: #008080;">19</span>                 <span style="color: #0000ff;">int</span> highlightedTextWidth = TextRenderer.MeasureText(matchedText, e.Font, <span style="color: #0000ff;">new</span> Size(<span style="color: #0000ff;">int</span>.MaxValue, <span style="color: #0000ff;">int</span><span style="color: #000000;">.MaxValue), formatFlags).Width;
</span><span style="color: #008080;">20</span>                 <span style="color: #0000ff;">int</span> yPosition = e.Index * smartTipListBox.ItemHeight +<span style="color: #000000;"> leftAndTopPadding;
</span><span style="color: #008080;">21</span>                 <span style="color: #0000ff;">int</span> leftTextMargin = (<span style="color: #0000ff;">int</span>)(<span style="color: #800080;">1</span> * e.Font.GetHeight() / <span style="color: #800080;">6</span><span style="color: #000000;">);
</span><span style="color: #008080;">22</span>                 <span style="color: #0000ff;">int</span> rightTextMargin = (<span style="color: #0000ff;">int</span>)(<span style="color: #800080;">1</span> * e.Font.GetHeight() / <span style="color: #800080;">4</span><span style="color: #000000;">);
</span><span style="color: #008080;">23</span>                 <span style="color: #0000ff;">if</span> (index &gt; <span style="color: #800080;">0</span><span style="color: #000000;">)
</span><span style="color: #008080;">24</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">25</span>                     TextRenderer.DrawText(e.Graphics, headText, e.Font, <span style="color: #0000ff;">new</span><span style="color: #000000;"> Point(leftAndTopPadding, yPosition), Color.Black, formatFlags);
</span><span style="color: #008080;">26</span>                     TextRenderer.DrawText(e.Graphics, matchedText, e.Font, <span style="color: #0000ff;">new</span> Point(leftAndTopPadding + headTextPartWidth - <span style="color: #800080;">2</span> * leftTextMargin -<span style="color: #000000;"> rightTextMargin, yPosition), Color.Red, formatFlags);
</span><span style="color: #008080;">27</span>                     TextRenderer.DrawText(e.Graphics, tailText, e.Font, <span style="color: #0000ff;">new</span> Point(leftAndTopPadding + headTextPartWidth + highlightedTextWidth - <span style="color: #800080;">3</span> * leftTextMargin - <span style="color: #800080;">2</span> *<span style="color: #000000;"> rightTextMargin, yPosition), Color.Black, formatFlags);
</span><span style="color: #008080;">28</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">29</span>                 <span style="color: #0000ff;">else</span>
<span style="color: #008080;">30</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">31</span>                     TextRenderer.DrawText(e.Graphics, matchedText, e.Font, <span style="color: #0000ff;">new</span> Point(leftAndTopPadding +<span style="color: #000000;"> headTextPartWidth, yPosition), Color.Red, formatFlags);
</span><span style="color: #008080;">32</span>                     TextRenderer.DrawText(e.Graphics, tailText, e.Font, <span style="color: #0000ff;">new</span> Point(leftAndTopPadding + headTextPartWidth + highlightedTextWidth - <span style="color: #800080;">2</span> * leftTextMargin -<span style="color: #000000;"> rightTextMargin, yPosition), Color.Black, formatFlags);
</span><span style="color: #008080;">33</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">34</span> 
<span style="color: #008080;">35</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">Brush blackBrush = Brushes.Black;
</span><span style="color: #008080;">36</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">Brush redBrush = Brushes.Red;
</span><span style="color: #008080;">37</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">Rectangle initRectangle = e.Bounds;
</span><span style="color: #008080;">38</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">e.Graphics.DrawString(entireText.Substring(0, index), e.Font, blackBrush, initRectangle, null);
</span><span style="color: #008080;">39</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">initRectangle.Offset(Convert.ToInt32(e.Graphics.MeasureString(entireText.Substring(0, index), e.Font).Width), 0);
</span><span style="color: #008080;">40</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">e.Graphics.DrawString(entireText.Substring(index, searchingStr.Length), e.Font, redBrush, initRectangle, null);
</span><span style="color: #008080;">41</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">initRectangle.Offset(Convert.ToInt32(e.Graphics.MeasureString(entireText.Substring(index, searchingStr.Length), e.Font).Width), 0);
</span><span style="color: #008080;">42</span>                 <span style="color: #008000;">//</span><span style="color: #008000;">e.Graphics.DrawString(entireText.Substring(index + searchingStr.Length, entireText.Length - index - searchingStr.Length), e.Font, blackBrush, initRectangle, null);</span>
<span style="color: #008080;">43</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">44</span>         }</pre>
</div>
<p>&nbsp;</p>