<ul>
<li>官网：http://lua-users.org/wiki/</li>
<li>用户社区wiki：http://lua-users.org/wiki/</li>
<li>库：<a href="http://luaforge.net/">LuaForge</a>，<a href="http://luabinaries.sourceforge.net/">LuaBinaries</a>（统一的预编译的各种二进制包的库），<a href="http://luarocks.org/">LuaRocks</a>，<a href="https://github.com/LewisJEllis/awesome-lua">Awesome Lua</a></li>
<li>网友翻译的中文手册：http://cloudwu.github.io/lua53doc/</li>
<li>tips
<ul>
<li>pronounced&nbsp;<strong>LOO-ah</strong></li>
<li><strong>不要全大写</strong></li>
</ul>
</li>
<li>特点
<ul>
<li>open source</li>
<li>may be used for any purpose, including commercial purposes</li>
<li>free, no cost for any purpose</li>
</ul>
</li>
<li>License</li>
<ul>
<li>not in the public domain</li>
<li><span style="color: #ff0000;">MIT license</span></li>
<li>compatible with GPL</li>
<li>no GNU-like "copyleft" restrictions</li>
</ul>
<li>使用建议
<ul>
<li>if you do use Lua, then you should give us credit by <span style="color: #ff0000;">including the copyright notice somewhere in your product or its documentation</span></li>
<li><span style="color: #000000;">&nbsp;A nice, but optional, way to give us further credit is to </span><span style="color: #ff0000;">include a&nbsp;<a href="http://www.lua.org/images/"><span style="color: #ff0000;">Lua logo</span></a>&nbsp;and a&nbsp;<a href="http://www.lua.org/"><span style="color: #ff0000;">link to our site</span></a>&nbsp;in a web page for your product.</span></li>
</ul>
</li>
<li><span style="color: #000000;">优点</span>
<ul>
<li><span style="color: #000000;">Lua is a proven, robust language</span></li>
<li><span style="color: #000000;">Lua is fast</span></li>
<li><span style="color: #000000;">Lua is portable</span></li>
<li><span style="color: #000000;">Lua is embeddable</span></li>
<li><span style="color: #000000;">Lua is powerful (but simple)</span></li>
<li><span style="color: #000000;">Lua is small</span></li>
<li><span style="color: #000000;">Lua is free</span></li>
</ul>
</li>
<li><span style="color: #000000;">使用</span>
<ul>
<li><span style="color: #000000;">自行编译</span></li>
<li><span style="color: #000000;">现成的二进制包</span></li>
</ul>
</li>
<li><span style="color: #000000;">用途</span>
<ul>
<li><span style="color: #000000;">1</span>
<ul>
<li><span style="color: #000000;">程序热更新（代码级）<br /></span></li>
<li><span style="color: #000000;">可以用做数据描述（DSL）</span></li>



</ul>



</li>
<li><span style="color: #000000;">2</span>
<ul>
<li><span style="color: #000000;">作为配置文件：灵活强大，可以加入计算，甚至复杂逻辑</span></li>
<li><span style="color: #000000;">作为插件脚本：实时更新，不需要编译环节，比如Nginx</span></li>
<li><span style="color: #000000;">开发简单DSL：快速小巧，语法简洁，比如跨平台UI布局描述</span></li>
<li><span style="color: #000000;">开发顶层项目：并无不可，无缝结合C，更强大，比如Kong</span></li>



</ul>



</li>
<li><span style="color: #000000;">3：lua脚本语言，运行依托于宿主语言，可以是c++，c#，golang等，只要实现了lua解析器就可以。</span>
<ul>
<li><span style="color: #000000;">比如游戏引擎领域的cocos2d-x、unity，由于引擎自身使用的c++或者c#作为编写语言，游戏前端发布后，发现bug或者发布新逻辑，都需要重新出包，周期过长，lua作为脚本语言，可以以资源的形式下载，重新加载运行，周期快，且效率损失有限</span></li>
<li><span style="color: #000000;">服务端使用，比较火的也就是nginx+lua的形式，后来有人做了框架openresty，基本也是利用nginx的高性能+lua脚本的灵活性，逻辑修改之后只需要触发重新加载脚本就可以，开发运行效率都比较高，相比传统c++、java等需要重新编译部署，开发效率高很多</span></li>



</ul>



</li>
<li><span style="color: #000000;">4：<a href="https://en.wikipedia.org/wiki/List_of_applications_using_Lua" rel="nofollow noreferrer" target="_blank">List of applications using Lua - Wikipedia</a></span>
<ul>
<li>
<p>openresty （nginx 的 lua 模块）</p>


</li>
<li>
<p>Awesome、Scite 的配置</p>


</li>
<li>
<p>Redis 支持服务端 Lua 脚本</p>


</li>
<li>
<p>PostgreSQL 支持服务端 Lua 脚本（以及其它一些语言）</p>


</li>
<li>
<p>LuaTeX，基于 TeX 的排版引擎</p>


</li>
<li>
<p>迅雷也在用（发现了 DLL）</p>


</li>
<li>
<p>游戏开发：云风的 skynet</p>


</li>
<li>
<p>MediaWiki（维基百科所使用的软件）大量使用 Lua 编写的模块，用于生成特定的页面内容</p>


</li>
<li>
<p>Vim、WeeChat 也支持 Lua 脚本编写插件（以及其它一些语言）</p>


</li>
<li>
<p>Wireshark 支持 Lua 脚本编写插件</p>


</li>
<li>
<p>&hellip;&hellip;（太多了）</p>


</li>


</ul>


</li>
<li>5：
<ul>
<li>首先<a class="baidu-highlight" href="https://www.baidu.com/s?wd=%E8%84%9A%E6%9C%AC%E8%AF%AD%E8%A8%80&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">脚本语言</a>的共同优点就是学习容易，繁琐的细节少。<br /><br />而Lua的解释器又是预编译性质的，明显比其他的<a class="baidu-highlight" href="https://www.baidu.com/s?wd=%E8%84%9A%E6%9C%AC%E8%AF%AD%E8%A8%80&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">脚本语言</a>快许多（是javascript、perl等几倍到十倍吧），所以<a class="baidu-highlight" href="https://www.baidu.com/s?wd=%E8%84%9A%E6%9C%AC%E8%AF%AD%E8%A8%80&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">脚本语言</a>性能上的劣势lua相对较小。<br /><br />而且lua很适合作为&ldquo;<a class="baidu-highlight" href="https://www.baidu.com/s?wd=%E8%83%B6%E6%B0%B4%E8%AF%AD%E8%A8%80&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">胶水语言</a>&rdquo;，用于连接不同语言编写的项目。<br /><br />许多游戏（比如<a class="baidu-highlight" href="https://www.baidu.com/s?wd=%E9%AD%94%E5%85%BD%E4%B8%96%E7%95%8C&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">魔兽世界</a>）的脚本就是lua，事实上现在绝大多数游戏的剧情、流程都是某种脚本，基本不会有人傻到用<a class="baidu-highlight" href="https://www.baidu.com/s?wd=C%2FC%2B%2B&amp;tn=SE_PcZhidaonwhc_ngpagmjz&amp;rsv_dl=gh_pc_zhidao" target="_blank">C/C++</a>的if、else去写RPG的流程了。</li>


</ul>


</li>
<li>6：转载自https://www.jb51.net/article/65152.htm
<ul>
<li>1. 在很多时候，我们可以将Lua直接嵌入到我们的应用程序中，如游戏、监控服务器等。这样的应用方式对于程序的最终用户而言是完全透明的，但是对于程序本身，其扩展性将会得到极大的增强。<br /><br />　　2. 将Lua视为一种独立的脚本语言，通过它来帮助我们完成一些软件产品的辅助性工具的开发。比如在我们之前的数据分析产品中，我们通过编写Lua脚本，将每个用户不同格式的数据重新格式化为我们的软件平台能够读取的格式，之后再将格式化的后的数据加载到数据库中，或者是写入我们的分析引擎可以识别的数据分析文件中。这其中Lua仅仅用于文件格式的规格化过程，至于此后的操作，都是通过Lua调用我们的C语言导出函数来完成的。<br /><br />　　3. 将Lua应用于应用程序的动态配置部分。比如移动智能设备或嵌入式设备，它们的显示分辨率在很多情况下都是非标准的，如果我们为每一款设备都维护一套相关的配置信息，这无疑会加大我们程序的维护开销，如果我们将这段动态配置逻辑交由Lua脚本完成，那么这对于程序配置的灵活性而言，将会得到很大的提高。甚至可以是这样，运行在移动终端设备上的应用程序，在启动主窗体之前先和服务器建立连接，在服务器确认设备的各种参数后，再将和该设备显示相关的Lua脚本发送给设备客户端，这样客户端在得到Lua脚本之后，就可以立刻执行它以得到最新的动态配置信息。</li>

</ul>

</li>
<li>7：转载自https://blog.csdn.net/unsv29/article/details/50157179
<ul>
<li>
<p>获得的好处<br />&bull; 隔离底层，让系统更健壮<br />&bull; 降低开发成本<br />&bull; 应付多变的需求<br />&bull; 热更新系统<br />&bull; 数据描述更方便</p>
<p>&bull;&nbsp;能做到单一语言难以做到的</p>
<p>&nbsp;</p>
<p>易出现的问题<br />&bull; 多语言构建的系统更为复杂<br />&bull; 层次划分不清，职责混乱<br />&bull; 把 &nbsp;Lua &nbsp;当成 &nbsp;C/C++ &nbsp;来用<br />&bull; 定义不恰当的 &nbsp;DSL</p>

</li>

</ul>

</li>



</ul>



</li>



</ul>