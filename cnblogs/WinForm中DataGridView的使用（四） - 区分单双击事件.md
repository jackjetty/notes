<p>虽然DataGridView单双击事件都有，但双击事件其实也会触发单击事件的处理，所以如果双击事件和单击事件的行为不同，或者双击时不想触发单击事件，或者单击事件会阻塞双击事件的处理时（比如单击后会有弹窗），就需要自行去实现了。</p>
<p>&nbsp;</p>
<ul>
<li>整体思路：
<ul>
<li>对于DataGridView当前页的每一行，维护一份点击信息，用于后续判断和处理。</li>
<li><span style="color: #000000;">两个线程或者说处理逻辑之间互相依赖。主线程的事件处理依赖timer线程去清理数据，比如在判断是否发生了双击时，其实隐含依赖于timer线程处理中未超时清理第一次点击标识；timer线程的处理逻辑依赖主线程的是否发生过一次点击和是否发生了双击的标识。</span>
<ul>
<li><span style="color: #0000ff;">当点击某一行时（UI主线程）</span>
<ul>
<li><span style="color: #0000ff;">如果是第一次点击，则标识这一行已经点击过一次了，并启动一个timer开始计时和处理（另一个线程）。</span></li>
<li><span style="color: #0000ff;">如果是第二次点击，则标识这一行发生了双击，这个标识用于timer中判断是否触发双击事件处理函数。</span></li>
</ul>
</li>
<li><span style="color: #800080;">timer的处理逻辑（timer线程）</span>
<ul>
<li><span style="color: #800080;">这个timer运行到系统认为的双击间隔时间后（一定要跑到这个时间点，否则不知道是否会再有第二次点击）</span>
<ul>
<li><span style="color: #800080;">如果没有再次发生第二次单击，则认为这一行是发生了双击事件，然后调用双击事件处理函数后，最后清理数据（以便下次判断单双击和计时）。</span></li>
<li><span style="color: #800080;">如果已经发生了第二次单击，则认为这一行是发生了单击事件，然后调用单击事件处理函数后，最后清理数据（以便下次判断单双击和计时）。<br /></span></li>


</ul>


</li>


</ul>


</li>


</ul>


</li>
<li><span style="color: #000000;">缺点：即使只是单击，也要等到双击间隔接触才能确认不是单击，才能触发单击事件处理函数，感觉上有一点延迟，但这个逻辑貌似也是合理的。</span></li>
<li><span style="color: #000000;">即使发生连续点击多次的情况，也可以近似完美处理，因为主线程的事件处理肯定触发多次，第二次点击时已经设为了双击，timer事实上是只有达到双击间隔时才触发处理逻辑，并没有清理数据，所以大不了设置多次的双击标识。但timer的间隔可以考虑设置短一点，因为达到双击间隔后，最差情况下会经过一个间隔时间才会触发处理逻辑。</span></li>



</ul>



</li>



</ul>
<p>&nbsp;</p>
<p>如果是自定义的一个DataGridView控件，需要定义一个单独的类，作为内部的列表或数组变量去储存DataGridView每一行对应的点击信息，用来区分单击还是双击。</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;">2</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> saved row click info to identify whether this mouse click is double click
</span><span style="color: #008080;">3</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;">4</span>         <span style="color: #0000ff;">private</span> RowClickInfo[] rowClickInfoOfCurrentPage;</pre>
</div>
<p>详细类定义如下：</p>
<div class="cnblogs_code">
<pre> 1    /// &lt;summary&gt;
 2     /// click info for each data row to distinguish click and double click, and has a timer to execute single/double click action
 3     /// &lt;/summary&gt;
 4     class<span> RowClickInfo
 5 <span>    {
 6         public int CurRowIndex { get; set<span>; }
 7 
 8         /// &lt;summary&gt;
 9         /// mark if the first click happened
10         /// &lt;/summary&gt;
11         public bool HasFirstClickHappened { get; set<span>; }
12 
13         public bool IsDoubleClick { get; set<span>; }
14 
15         private int _milliseconds { get; set<span>; }
16 
17         /// &lt;summary&gt;
18         /// this timer is a must, or we don't know when to do single or double click action only using CellMouseDown event
19         /// &lt;/summary&gt;
20         public System.Windows.Forms.Timer DoubleClickTimer { get; set<span>; }
21 
22         private const int _timerInterval = 100<span>;
23 
24         public<span> RowClickInfo()
25 <span>        {
26             HasFirstClickHappened = false<span>;
27             IsDoubleClick = false<span>;
28             _milliseconds = 0<span>;
29 
30             DoubleClickTimer = new<span> System.Windows.Forms.Timer();
31             DoubleClickTimer.Interval =<span> _timerInterval;
32             DoubleClickTimer.Tick += new<span> EventHandler(_doubleClickTimer_Tick);
33 <span>        }
34 
35         private void _doubleClickTimer_Tick(object<span> sender, EventArgs e)
36 <span>        {
37             _milliseconds +=<span> _timerInterval;
38 
39             // if exceed double click time span, stop timer, do single/double click action and clear data
40             if (_milliseconds &gt;=<span> SystemInformation.DoubleClickTime)
41 <span>            {
42 <span>                DoubleClickTimer.Stop();
43 
44                 if<span> (IsDoubleClick)
45 <span>                {
46 <span>                    RowDoubleClick(CurRowIndex);
47 <span>                }
48                 else
49 <span>                {
50 <span>                    RowSingleClick(CurRowIndex);
51 <span>                }
52 
53                 // clear data
54                 IsDoubleClick = false<span>;
55                 HasFirstClickHappened = false<span>;
56                 _milliseconds = 0<span>;
57 <span>            }
58 <span>        }
59 
60         public delegate void RowSingleClickHandler(int<span> curRowIndex);
61         public event<span> RowSingleClickHandler RowSingleClick;
62 
63         public delegate void RowDoubleClickHandler(int<span> curRowIndex);
64         public event<span> RowDoubleClickHandler RowDoubleClick;
65     }</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
</div>
<p>然后在绑定数据后要初始化当前页所有行的点击信息类：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span>                 <span style="color: #008000;">//</span><span style="color: #008000;"> init row click info</span>
<span style="color: #008080;">2</span>                 rowClickInfoOfCurrentPage = <span style="color: #0000ff;">new</span> RowClickInfo[<span style="color: #0000ff;">this</span><span style="color: #000000;">.pageSize];
</span><span style="color: #008080;">3</span>                 <span style="color: #0000ff;">for</span> (<span style="color: #0000ff;">int</span> i = <span style="color: #800080;">0</span>; i &lt; <span style="color: #0000ff;">this</span>.pageSize; i++<span style="color: #000000;">)
</span><span style="color: #008080;">4</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">5</span>                     rowClickInfoOfCurrentPage[i] = <span style="color: #0000ff;">new</span><span style="color: #000000;"> RowClickInfo();
</span><span style="color: #008080;">6</span>                     rowClickInfoOfCurrentPage[i].CurRowIndex =<span style="color: #000000;"> i;
</span><span style="color: #008080;">7</span>                     rowClickInfoOfCurrentPage[i].RowSingleClick += <span style="color: #0000ff;">this</span><span style="color: #000000;">.DataSourceRowClick;
</span><span style="color: #008080;">8</span>                     rowClickInfoOfCurrentPage[i].RowDoubleClick += <span style="color: #0000ff;">this</span><span style="color: #000000;">.DataSourceRowDoubleClick;
</span><span style="color: #008080;">9</span>                 }</pre>
</div>
<p>接着在构造函数中绑定自定义的点击事件处理函数：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span> <span style="color: #0000ff;">this</span>.CellMouseDown += <span style="color: #0000ff;">new</span> DataGridViewCellMouseEventHandler(CellMouseDownHandler);</pre>
</div>
<p>具体的点击事件处理函数如下：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;"> 2</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> handle mouse down event of both single and double click event
</span><span style="color: #008080;"> 3</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;"> 4</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;param name="sender"&gt;&lt;/param&gt;</span>
<span style="color: #008080;"> 5</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;param name="e"&gt;&lt;/param&gt;</span>
<span style="color: #008080;"> 6</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">void</span> CellMouseDownHandler(<span style="color: #0000ff;">object</span><span style="color: #000000;"> sender, DataGridViewCellMouseEventArgs e)
</span><span style="color: #008080;"> 7</span> <span style="color: #000000;">        {
</span><span style="color: #008080;"> 8</span>             <span style="color: #0000ff;">int</span> rowIndex =<span style="color: #000000;"> e.RowIndex;
</span><span style="color: #008080;"> 9</span>             <span style="color: #0000ff;">if</span> (rowIndex &gt;= <span style="color: #800080;">0</span><span style="color: #000000;">)
</span><span style="color: #008080;">10</span> <span style="color: #000000;">            {
</span><span style="color: #008080;">11</span>                 RowClickInfo curRowClickInfo =<span style="color: #000000;"> rowClickInfoOfCurrentPage[rowIndex];
</span><span style="color: #008080;">12</span>                 <span style="color: #0000ff;">if</span> (!<span style="color: #000000;">curRowClickInfo.HasFirstClickHappened)
</span><span style="color: #008080;">13</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">14</span>                     <span style="color: #008000;">//</span><span style="color: #008000;"> triggered the first time</span>
<span style="color: #008080;">15</span>                     curRowClickInfo.HasFirstClickHappened = <span style="color: #0000ff;">true</span><span style="color: #000000;">;
</span><span style="color: #008080;">16</span> <span style="color: #000000;">                    curRowClickInfo.DoubleClickTimer.Start();
</span><span style="color: #008080;">17</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">18</span>                 <span style="color: #0000ff;">else</span>
<span style="color: #008080;">19</span> <span style="color: #000000;">                {
</span><span style="color: #008080;">20</span>                     <span style="color: #008000;">//</span><span style="color: #008000;"> triggered the second time and it must be double click, 
</span><span style="color: #008080;">21</span>                     <span style="color: #008000;">//</span><span style="color: #008000;"> because HasFirstClickHappened will change to false again if interval is too long</span>
<span style="color: #008080;">22</span>                     curRowClickInfo.IsDoubleClick = <span style="color: #0000ff;">true</span><span style="color: #000000;">;
</span><span style="color: #008080;">23</span> <span style="color: #000000;">                }
</span><span style="color: #008080;">24</span> <span style="color: #000000;">            }
</span><span style="color: #008080;">25</span>         }</pre>
</div>
<p>&nbsp;</p>