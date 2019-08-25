<p>&nbsp;</p>
<p>注：brush通过起止坐标来控制重绘范围及方向。比如从上到下渐变时，brush第二个Point参数是左下角坐标。</p>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> PaintGradientBackground(Button btn)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 3</span>             Bitmap newGradientBackImg = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Bitmap(btn.Width, btn.Height);
</span><span style="color: #008080;"> 4</span>             LinearGradientBrush brush = <span style="color: #0000ff;">new</span> LinearGradientBrush(<span style="color: #0000ff;">new</span> PointF(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>), <span style="color: #0000ff;">new</span> PointF(<span style="color: #800080;">0</span><span style="color: #000000;">, btn.Height), GlobalParams.UnSelected_TreeTab_Top_Color, GlobalParams.UnSelected_TreeTab_Bottom_Color);
</span><span style="color: #008080;"> 5</span>             Graphics gr =<span style="color: #000000;"> Graphics.FromImage(newGradientBackImg);
</span><span style="color: #008080;"> 6</span>             gr.FillRectangle(brush, <span style="color: #0000ff;">new</span> RectangleF(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">, btn.Width, btn.Height));
</span><span style="color: #008080;"> 7</span>             <span style="color: #008000;">//</span><span style="color: #008000;">btn.BackColor = Color.Transparent;</span>
<span style="color: #008080;"> 8</span>             btn.BackgroundImage =<span style="color: #000000;"> newGradientBackImg;
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">10</span> 
<span style="color: #008080;">11</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> ClearGradientBackground(Button btn)
</span><span style="color: #008080;">12</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">13</span>             btn.BackgroundImage = <span style="color: #0000ff;">null</span><span style="color: #000000;">;
</span><span style="color: #008080;">14</span>         }</pre>
</div>
<p>&nbsp;</p>