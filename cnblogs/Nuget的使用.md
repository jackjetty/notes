<ul>
<li>VS中的Project是根据是否存在<strong><span style="color: #ff0000;">packages.config</span></strong>文件及其内容，来判断是否以及存在那些Nuget依赖的，<span style="color: #ff0000;">即使packages.config文件并未include到project中</span>。</li>
<li>即使引用的dll是自己指定的目录（csproj文件中的路径），如果存在packages.config及相关信息（csproj中的引用写法也不同），还是会当做丢失的包去下载或者去全局目录拷贝到项目根目录生成packages目录</li>
<li>对Nuget引用的包的操作，最好都是可视化的Nuget界面进行管理，比如在其中删除所有依赖时，会同时帮你删除packages.config文件</li>
<li>Nuget引入的包在.csproj文件中的内容与直接引用dll不太相同，多出了版本、key等信息
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('e3a2b033-d51e-462c-a47a-5fbf5378e413')"><img id="code_img_closed_e3a2b033-d51e-462c-a47a-5fbf5378e413" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_e3a2b033-d51e-462c-a47a-5fbf5378e413" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('e3a2b033-d51e-462c-a47a-5fbf5378e413',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_e3a2b033-d51e-462c-a47a-5fbf5378e413" class="cnblogs_code_hide">
<pre><span style="color: #008080;">1</span>     &lt;Reference Include=<span style="color: #800000;">"</span><span style="color: #800000;">Microsoft.VisualStudio.TestPlatform.TestFramework.Extensions</span><span style="color: #800000;">"</span>&gt;
<span style="color: #008080;">2</span>       &lt;HintPath&gt;..\MSTest.TestFramework.<span style="color: #800080;">1.1</span>.<span style="color: #800080;">11</span>\lib\net45\Microsoft.VisualStudio.TestPlatform.TestFramework.Extensions.dll&lt;/HintPath&gt;
<span style="color: #008080;">3</span>     &lt;/Reference&gt;
<span style="color: #008080;">4</span> 
<span style="color: #008080;">5</span>     &lt;Reference Include=<span style="color: #800000;">"</span><span style="color: #800000;">Microsoft.VisualStudio.TestPlatform.TestFramework.Extensions, Version=14.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a, processorArchitecture=MSIL</span><span style="color: #800000;">"</span>&gt;
<span style="color: #008080;">6</span>       &lt;HintPath&gt;..\MSTest.TestFramework.<span style="color: #800080;">1.1</span>.<span style="color: #800080;">11</span>\lib\net45\Microsoft.VisualStudio.TestPlatform.TestFramework.Extensions.dll&lt;/HintPath&gt;
<span style="color: #008080;">7</span>     &lt;/Reference&gt;</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
<li>Nuget下载缺失的包时（VS的Tools菜单下面有Nuget的设置项，可以修改是否下载/restore）
<ul>
<li>先去默认的C盘的全局Nuget包目录找（也能改位置）</li>
<li>再去用户的目录</li>
<li>再去项目的目录</li>
</ul>
</li>
</ul>