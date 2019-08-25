<ul>
<li>&nbsp; 使用BeginInvoke或Invoke
<ul>
<li>作用
<ul>
<li>在自己创建的非UI线程中，进行UI操作，比如更新UI上控件的状态。</li>
<li>Windows 窗体中的控件被绑定到特定的线程，不具备线程安全性。因此，如果从另一个线程调用控件的方法，那么必须使用控件的一个 Invoke 方法来将调用封送到适当的线程。</li>
<li>如果已经创建控件的句柄，则除了 InvokeRequired 属性以外，控件上还有四个可以从任何线程上安全调用的方法，它们是： Invoke、BeginInvoke、 EndInvoke 和 CreateGraphics。 在后台线程上创建控件的句柄之前调用 CreateGraphics 可能会导致非法的跨线程调用。 对于所有其他方法调用，当从另一个线程进行调用时，应使用这些 Invoke 方法之一。如果控件句柄尚不存在，则 InvokeRequired 沿控件的父级链搜索，直到它找到有窗口句柄的控件或窗体为止。 如果找不到合适的句柄， InvokeRequired 方法将返回 false。</li>
</ul>
</li>
<li>也可以new一个Thread，在其中再使用BeginInvoke或Invoke
<ul>
<li>这其实就是在自己创建的非UI线程中，又有一部分逻辑是要修改主UI线程的显示，那么就要用BeginInvoke来实现</li>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('72f67f47-62df-4099-a062-b3daf876392a')"><img id="code_img_closed_72f67f47-62df-4099-a062-b3daf876392a" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_72f67f47-62df-4099-a062-b3daf876392a" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('72f67f47-62df-4099-a062-b3daf876392a',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_72f67f47-62df-4099-a062-b3daf876392a" class="cnblogs_code_hide">
<pre>Thread t = <span style="color: #0000ff;">new</span> Thread(() =&gt;<span style="color: #000000;">
            {
                </span><span style="color: #008000;">//</span><span style="color: #008000;"> business logic</span>

                <span style="color: #0000ff;">this</span>.BeginInvoke(<span style="color: #0000ff;">new</span> Action(<span style="color: #0000ff;">delegate</span><span style="color: #000000;"> ()
                {
                   </span><span style="color: #008000;">//</span><span style="color: #008000;"> ui logic</span>
<span style="color: #000000;">                }));

                </span><span style="color: #008000;">//</span><span style="color: #008000;"> business logic</span>

                <span style="color: #0000ff;">this</span>.BeginInvoke(<span style="color: #0000ff;">new</span> Action(<span style="color: #0000ff;">delegate</span><span style="color: #000000;"> ()
                {
                   </span><span style="color: #008000;">//</span><span style="color: #008000;"> ui logic</span>
<span style="color: #000000;">                }));
            });
            t.Start();</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>&nbsp;</p>
</li>
</ul>
</li>
<li>每个控件都有new、active、disposed三个状态</li>
<li>调用前的检查
<ul>
<li>否则可能会导致异常：<span style="color: #ff0000;">Invoke or BeginInvoke cannot be called on a control until the window handle has been created. （在创建窗口句柄之前,不能在控件上调用 Invoke 或 BeginInvoke）</span></li>
<li>因此需要一些属性用于确定是否必须调用 Invoke 方法，当不知道什么线程拥有控件时这很有用
<ul>
<li><strong><span style="color: #ff0000;">InvokeRequired</span></strong>
<ul>
<li><span style="color: #000000;">最好在调用的外层套一个InvokeRequired来判断当前是否必须使用Invoke或者BeginInvoke来做动作，为false时即可以直接做动作<br /></span></li>
<li><span style="color: #000000;">这意味着如果不需要 Invoke（调用发生在同一线程上），或者如果控件是在另一个线程上创建的但尚未创建控件的句柄，则InvokeRequired 可以返回 false。如果尚未创建控件的句柄，您就不能简单地在控件上调用属性、方法或事件。这可能导致在后台线程上创建控件的句柄，从而隔离不带消息泵的线程上的控件并使应用程序不稳定。</span></li>


</ul>


</li>
<li><strong><span style="color: #ff0000;">IsHandleCreated</span></strong>
<ul>
<li><span style="color: #000000;">谨慎起见，还可以使用IsHandleCreated来判断该控件是否已经创建了关联的句柄</span></li>
<li><span style="color: #000000;">当 InvokeRequired 在后台线程上返回 false 时，您也可以通过检查 IsHandleCreated 的值来避免上面这种情况。 如果尚未创建控件句柄，您必须等到控件句柄已创建，才能调用 Invoke 或 BeginInvoke。 通常，仅当在应用程序主窗体的构造函数中创建了后台线程时（如同在 Application.Run(new MainForm()) 中），在已经显示窗体或取消 Application.Run 之前，才会发生这种情况</span></li>


</ul>


</li>
<li><span style="color: #000000;"><span style="color: #ff0000;">单从检查的角度来说，事实上IsHandleCreate比InvokeRequired要严格</span>，前者为true时，后者肯定是true<span style="color: #ff0000;">？？？</span>；InvokeRequired为false的原因可能是IsHandleCreate为false或者根本不需要Invoke等。</span></li>
<li><span style="color: #ff0000;">两者的用途不太一样，一个用于判断有没有必要使用Invoke等，一个用于判断控件是否有句柄，虽然两者存在上述关系，所以</span><strong><span style="color: #ff0000;">最好两个属性都进行判断，先后顺序个人感觉无所谓</span></strong></li>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('b14aadaf-2373-4cb1-be6d-f48b51e19c32')"><img id="code_img_closed_b14aadaf-2373-4cb1-be6d-f48b51e19c32" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_b14aadaf-2373-4cb1-be6d-f48b51e19c32" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('b14aadaf-2373-4cb1-be6d-f48b51e19c32',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_b14aadaf-2373-4cb1-be6d-f48b51e19c32" class="cnblogs_code_hide">
<pre><span style="color: #008080;"> 1</span> <span style="color: #0000ff;">if</span>(control.IsDisposed || (!control.IsHandleCreated &amp;&amp; !<span style="color: #000000;">control.FindForm().IsHandleCreated))
</span><span style="color: #008080;"> 2</span> <span style="color: #000000;">{
</span><span style="color: #008080;"> 3</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> some exceptional condition:
</span><span style="color: #008080;"> 4</span>     <span style="color: #008000;">//</span><span style="color: #008000;"> handle in whatever way is appropriate for your app</span>
<span style="color: #008080;"> 5</span>     <span style="color: #0000ff;">return</span><span style="color: #000000;">;
</span><span style="color: #008080;"> 6</span> <span style="color: #000000;">}
</span><span style="color: #008080;"> 7</span> 
<span style="color: #008080;"> 8</span> <span style="color: #0000ff;">if</span><span style="color: #000000;">(control.InvokeRequired)
</span><span style="color: #008080;"> 9</span> <span style="color: #000000;">{
</span><span style="color: #008080;">10</span> <span style="color: #000000;">    control.Invoke(action);
</span><span style="color: #008080;">11</span> <span style="color: #000000;">}
</span><span style="color: #008080;">12</span> <span style="color: #0000ff;">else</span>
<span style="color: #008080;">13</span> <span style="color: #000000;">{
</span><span style="color: #008080;">14</span> <span style="color: #000000;">    action();
</span><span style="color: #008080;">15</span> }</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
</li>
<li><span style="color: #ff0000;">&nbsp;<span style="color: #000000;">参考</span></span>
<ul>
<li><span style="color: #ff0000;"><span style="color: #000000;">https://www.cnblogs.com/slyzly/articles/2121436.html</span><br /></span></li>
<li><span style="color: #ff0000;"><span style="color: #000000;">https://blog.csdn.net/dyllove98/article/details/9105555</span></span></li>



</ul>



</li>




</ul>




</li>




</ul>