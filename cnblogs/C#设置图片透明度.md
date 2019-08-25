<p>逐个像素进行Alpha值的设置，网上其他的代码不能处理有透明背景的图片，因此要对Alpha、R、G、B均为0的透明色进行特殊处理，不做转换。</p>
<p>&nbsp;</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #0000ff;">private</span> Bitmap SetImageOpacity(Image srcImage, <span style="color: #0000ff;">int</span><span style="color: #000000;"> opacity)
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 3</span>             Bitmap pic = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Bitmap(srcImage);
</span><span style="color: #008080;"> 4</span>             <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> w = <span style="color: #800080;">0</span>; w &lt; pic.Width; w++<span style="color: #000000;">)
</span><span style="color: #008080;"> 5</span> <span style="color: #000000;">            {
</span><span style="color: #008080;"> 6</span>                 <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> h = <span style="color: #800080;">0</span>; h &lt; pic.Height; h++<span style="color: #000000;">)
</span><span style="color: #008080;"> 7</span> <span style="color: #000000;">                {
</span><span style="color: #008080;"> 8</span>                     Color c =<span style="color: #000000;"> pic.GetPixel(w, h);
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">                    Color newC;
</span><span style="color: #008080;">10</span>                     <span style="color: #0000ff;">if</span> (!c.Equals(Color.FromArgb(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">)))
</span><span style="color: #008080;">11</span> <span style="color: #000000;">                    {
</span><span style="color: #008080;">12</span>                         newC =<span style="color: #000000;"> Color.FromArgb(opacity, c);
</span><span style="color: #008080;">13</span> <span style="color: #000000;">                    }
</span><span style="color: #008080;">14</span>                     <span style="color: #0000ff;">else</span>
<span style="color: #008080;">15</span> <span style="color: #000000;">                    {
</span><span style="color: #008080;">16</span>                         newC =<span style="color: #000000;"> c;
</span><span style="color: #008080;">17</span> <span style="color: #000000;">                    }
</span><span style="color: #008080;">18</span> <span style="color: #000000;">                    pic.SetPixel(w, h, newC);
</span><span style="color: #008080;">19</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">20</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">21</span>             <span style="color: #0000ff;">return</span><span style="color: #000000;"> pic;
</span><span style="color: #008080;">22</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">23</span> 
<span style="color: #008080;">24</span>         <span style="color: #0000ff;">private</span> Image SetImageOpacity2(Image srcImage, <span style="color: #0000ff;">int</span><span style="color: #000000;"> opacity)
</span><span style="color: #008080;">25</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">26</span>             Bitmap img = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Bitmap(srcImage);
</span><span style="color: #008080;">27</span>             <span style="color: #0000ff;">using</span> (Bitmap bmp = <span style="color: #0000ff;">new</span><span style="color: #000000;"> Bitmap(img.Width, img.Height, System.Drawing.Imaging.PixelFormat.Format32bppArgb))
</span><span style="color: #008080;">28</span> <span style="color: #000000;">            {
</span><span style="color: #008080;">29</span>                 <span style="color: #0000ff;">using</span> (Graphics g =<span style="color: #000000;"> Graphics.FromImage(bmp))
</span><span style="color: #008080;">30</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">31</span>                     g.DrawImage(img, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">);
</span><span style="color: #008080;">32</span>                     <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> h = <span style="color: #800080;">0</span>; h &lt;= img.Height - <span style="color: #800080;">1</span>; h++<span style="color: #000000;">)
</span><span style="color: #008080;">33</span> <span style="color: #000000;">                    {
</span><span style="color: #008080;">34</span>                         <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> w = <span style="color: #800080;">0</span>; w &lt;= img.Width - <span style="color: #800080;">1</span>; w++<span style="color: #000000;">)
</span><span style="color: #008080;">35</span> <span style="color: #000000;">                        {
</span><span style="color: #008080;">36</span>                             Color c =<span style="color: #000000;"> img.GetPixel(w, h);
</span><span style="color: #008080;">37</span>                             <span style="color: #0000ff;">if</span> (!c.Equals(Color.FromArgb(<span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span>, <span style="color: #800080;">0</span><span style="color: #000000;">)))
</span><span style="color: #008080;">38</span> <span style="color: #000000;">                            {
</span><span style="color: #008080;">39</span> <span style="color: #000000;">                                bmp.SetPixel(w, h, Color.FromArgb(opacity, c.R, c.G, c.B));
</span><span style="color: #008080;">40</span> <span style="color: #000000;">                            }
</span><span style="color: #008080;">41</span>                             <span style="color: #0000ff;">else</span>
<span style="color: #008080;">42</span> <span style="color: #000000;">                            {
</span><span style="color: #008080;">43</span> <span style="color: #000000;">                                bmp.SetPixel(w, h, Color.FromArgb(c.A, c.R, c.G, c.B));
</span><span style="color: #008080;">44</span> <span style="color: #000000;">                            }
</span><span style="color: #008080;">45</span> <span style="color: #000000;">                        }
</span><span style="color: #008080;">46</span> <span style="color: #000000;">                    }
</span><span style="color: #008080;">47</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">48</span>                 <span style="color: #0000ff;">return</span><span style="color: #000000;"> (Image)bmp.Clone();
</span><span style="color: #008080;">49</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">50</span>         }</pre>
</div>
<p>&nbsp;</p>