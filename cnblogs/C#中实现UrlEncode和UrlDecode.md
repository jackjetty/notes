<ul>
<li>有时需要进行url编码、解码，比如从html中捞数据，有可能&gt;、&amp;等字符会被编码成&amp;gt;等。</li>
<li>WinForm中默认没有引入<strong>System.Web</strong>，因此要现在项目中引入依赖
<ul>
<li>System.Web.HttpUtility.HtmlEncode(str);</li>
<li>System.Web.HttpUtility.HtmlDecode(str);</li>
<li>System.Web.HttpUtility.UrlEncode(str);&nbsp;</li>
<li>System.Web.HttpUtility.UrlDecode(str);</li>
</ul>
</li>
<li><strong>编码、解码时可以指定编码</strong>，否则会看到乱码，如：<br />
<ul>
<li>System.Web.HttpUtility.UrlEncode(str,System.Text.Encoding.Unicode);&nbsp;</li>
<li>System.Web.HttpUtility.UrlEncode(str,System.Text.Encoding.UTF8);&nbsp;</li>
<li>System.Web.HttpUtility.UrlEncode(str,System.Text.Encoding.GetEncoding( "GB2312 "));&nbsp;</li>
<li>System.Web.HttpUtility.UrlDecode(str,System.Text.Encoding.Unicode);&nbsp;</li>
<li>System.Web.HttpUtility.UrlDecode(str,System.Text.Encoding.UTF8);&nbsp;</li>
<li>System.Web.HttpUtility.UrlDecode(str,System.Text.Encoding.GetEncoding( "GB2312 "));</li>
<li>等&nbsp;</li>

</ul>

</li>

</ul>