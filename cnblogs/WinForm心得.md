<ul>
<li>&nbsp;如果有UI部分可以复用，那么可以单独设置为一个<span style="color: #ff0000;"><strong>UserControl</strong>或者一个<strong>自定义子类控件</strong></span>
<ul>
<li>如果是单一一个控件，只是继承并修改了默认控件的样式行为，那么可以直接创建一个class并继承该控件，缺点是Designer中不可见</li>
<li>如果是一组控件或者说一个区域，可以创建一个UserControl，并且在Designer中自行做界面设计</li>
<li>即使做成了一个UserControl或自定义子类控件，<span style="color: #ff0000;">在外层Form等父控件中使用时，</span><span style="color: #ff0000;">Size还是要重新设置</span>，就像使用Winform自带的控件一样。因此在这个UserControl或自定义子类控件中，根据Size做的设置，都要注意一下自适应度了。</li>
</ul>
</li>
<li><span style="color: #000000;">WinForm中<strong><span style="color: #ff0000;">没有z-index的概念</span></strong>，同一级的控件直接根据位置大小进行覆盖</span>
<ul>
<li><span style="color: #000000;">小心动态调整布局或大小时，发生控件之间的遮罩情况</span></li>
</ul>
</li>
<li><span style="color: #ff0000;">利用<strong>Panel、Dock和Anchor</strong>属性进行自适应布局</span>
<ul>
<li>举个简单的例子来说，如果一个Form由上中下三个Panel组成，那么上下两个Panel可以设置为特定高度、Dock为Top和Bottom，然后中间的Panel要设置为Dock为Fill，那么中间的Panel就可以自适应高度了，但是在Designer中需要右击选择<strong>BringToFront</strong>，否则可能中间这个Panel的高度会包含上面或下面的Panel的高度。</li>
<li>Dock和Anchor属性可以通过位运算符&amp;等进行组合。</li>
</ul>
</li>
<li>做UI时，尽量<span style="color: #ff0000;"><strong>保证Designer模式是可见、可用的</strong></span>
<ul>
<li><strong>好处是方便进行总体布局、后期修改，不然有个东西显示不出来，都看不出来是为什么，比如被谁挡住了。。。否则只能通过设置背景色后再运行观察了</strong></li>
<li>保证Designer.cs中的代码符合vs的规则和顺序，免得自己直接改Designer.cs文件导致莫名其妙的问题，比如.BeginInit()、SuspendLayout()、((System.ComponentModel.ISupportInitialize)(this.dgvDataSourceSearchHistory)).EndInit();、this.ResumeLayout(false);this.PerformLayout();等的顺序</li>
<li>避免在Designer.cs中使用单例模式、自定义构造函数、泛型。前两个可以延迟到.cs文件中在构造函数调用InitializeComponent()后再做；泛型可以考虑用自定义类、object代替。</li>
<li>在Designer模式中修改，也比较容易看到默认的margin等东西、设置颜色代码等，也容易在不同控件间切换、选择（<strong>Esc</strong>键可以选中上一级控件）。</li>
<li>即使使用了多语言或者动态填充数据，也要保证空白控件在Designer模式可见，至少方便弄size、location、布局。</li>
</ul>
</li>
<li><span style="color: #ff0000;"><strong>控件之间的通信</strong></span>（一个父控件包含两个子控件为例）
<ul>
<li>父控件通知子控件：直接调用子控件函数</li>
<li>子控件通知父控件：子控件定义和在自身事件中调用该事件，父控件订阅和实现该事件，在父控件中进行事件绑定/订阅。</li>
<li>子控件之间：主动调用的控件定义和自身事件中调用该事件，另一个控件订阅和实现该事件，在父控件中进行事件绑定/订阅。</li>
<li>类似的，如果要使用子控件的子控件，直接将其设为public。</li>
</ul>
</li>
<li><span style="text-decoration: line-through;">如果有一些设置是通用的（比如某个颜色），那么如果想兼顾Designer可视化和代码可维护性，可以既在Designer中设置该值，也在代码中的构造函数中使用全局变量再设置一次该值？</span></li>
<li>一个Form中的各级子控件的<span style="color: #ff0000;"><strong>默认背景色</strong></span>（只是panel？），均继承自上级panel（form的背景色无效？）</li>
</ul>