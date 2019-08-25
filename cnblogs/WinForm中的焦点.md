<ul>
<li>窗口打开后默认的焦点在<strong>TabIndex</strong>为0的元素上，即使代码中在其他元素上设置了Focus()，也没用，所以初始状态最好通过TabIndex来控制。</li>
<li>WebForm中点其他如空白地方，之前的控件就会失去焦点，但WinForm中必须点到另一个控件上（貌似必须可见）。</li>
</ul>