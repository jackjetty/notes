<p>参考：</p>
<p>https://www.cnblogs.com/fuqiang88/p/5956363.html</p>
<p>https://www.cnblogs.com/zlingh/p/5887143.html</p>
<p>https://www.cnblogs.com/confach/p/10050437.html</p>
<p><img src="https://img2018.cnblogs.com/blog/106125/201811/106125-20181112145521874-1732725765.png" alt="" /></p>
<p>&nbsp;</p>
<ul>
<li>HTTP<br />
<ul>
<li>HTTP是<strong><span style="color: #ff0000;">非持久</span></strong>的协议
<ul>
<li>服务端不用维护客户端信息，减少工作量</li>
<li>不用维持tcp连接？</li>
<li>最初的目的是传输html，所以一问一答就足够了</li>
<li>但是有些情况需要保持状态，比如表单，可以通过cookie、session实现</li>



</ul>



</li>
<li>HTTP的生命周期通过&nbsp;<code>Request</code>&nbsp;来界定，也就是一个&nbsp;<code>Request</code>&nbsp;一个&nbsp;<code>Response。</code></li>
<li><strong><span style="color: #ff0000;">被动型</span></strong>：其实就是，服务端不能主动联系客户端，只能有客户端发起</li>
<li>1.0
<ul>
<li>在&nbsp;<code>HTTP1.0</code>&nbsp;中，这次HTTP请求就结束了。</li>




</ul>




</li>
<li>1.1
<ul>
<li>在HTTP1.1中进行了改进，使得有一个keep-alive（好像1.0也有，只是1.1默认开启了），也就是说，在一个HTTP连接中，可以发送多个Request，接收多个Response。但是请记住&nbsp;<code>Request = Response</code>， 在HTTP中永远是这样，也就是说一个request只能有一个response。而且这个response也是被动的，不能主动发起。</li>

</ul>

</li>
<li>2.0
<ul>
<li>参考Google的SPDY</li>
<li>二进制</li>
<li>安全性</li>
<li>连接复用等</li>

</ul>

</li>




</ul>




</li>
<li>HTML5
<ul>
<li>Html5是指的一系列新的API，或者说新规范，新技术。Http协议本身只有1.0和1.1，而且跟Html本身没有直接关系。通俗来说，可以用HTTP协议传输非Html数据。</li>




</ul>




</li>
<li>为了结局Http 1.0和1.1的问题（不能从协议层面复用tcp连接，导致每次都要握手和慢启动；安全性；）
<ul>
<li>为了持续、实时性、貌似主动性的交互需求，在Websocket之前还出现过两种：
<ul>
<li>ajax轮训</li>
<li>long poll</li>

</ul>

</li>
<li>Websocket
<ul>
<li>Websocket是一个持久化的协议</li>
<li>Websocket是基于HTTP协议的，或者说借用了HTTP的协议来完成一部分握手</li>
<li><code>Websocket</code>&nbsp;其实是一个新协议，跟HTTP协议基本没有关系，只是为了兼容现有浏览器的握手规范而已，也就是说它是HTTP协议上的一种补充</li>
<li><strong><span style="color: #ff0000;">必须客户端支持</span></strong></li>

</ul>

</li>
<li><span style="color: #000000;">SPDY和Http 2.0</span></li>

</ul>

</li>




</ul>
<p><span style="color: #4b4b4b; font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; font-size: 13px;">何为被动性呢，其实就是，服务端不能主动联系客户端，只能有客户端发起</span></p>
<p><span style="color: #ff0000;"><strong><span style="font-family: Verdana, Geneva, Arial, Helvetica, sans-serif; font-size: 13px;">所有的新协议，都要客户端和服务器端都支持</span></strong></span></p>
<ul>
<li>Http 2.0</li>

</ul>