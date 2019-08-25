<ul>
<li>自定义样式
<ul>
<li><span style="color: #ff0000;">先要清除系统风格影响：this.<strong>FlatStyle</strong> =&nbsp;FlatStyle.Flat;</span>
<ul>
<li><span style="color: #000000;">FlatStyle.Flat</span></li>
<li><span style="color: #000000;">FlatStyle.System</span></li>
<li><span style="color: #000000;">FlatStyle.Standard</span></li>
<li><span style="color: #000000;">FlatStyle.Popup</span></li>
</ul>
</li>
<li>位置：this.<strong>Location</strong> = new System.Drawing.Point(this.sidePadding + this.btnWidth, 0);</li>
<li>大小：this.<strong>Size</strong> = new System.Drawing.Size(btnWidth, btnHeight);</li>
<li>前景色：this.<strong>ForeColor</strong> = Color.White;</li>
<li>背景色（字体颜色）：this.<strong>BackColor</strong> = Color.FromArgb(35, 135, 170);</li>
<li>背景图片：this.<strong>BackgroundImage</strong> = newGradientBackImg;</li>
<li>字体：this.<strong>Font</strong> =&nbsp;new Font(GlobalParams.SiemensFontFamily, GlobalParams.FontSize, FontStyle.Bold, GraphicsUnit.Pixel, 1);</li>
<li>边框
<ul>
<li>清除：this.<strong>FlatAppearance.BorderSize</strong> = 0;</li>
<li>颜色：this.<strong>FlatAppearance.BorderColor</strong> = Color.FromArgb(173, 190, 203);</li>
</ul>
</li>
</ul>
</li>
<li>事件/效果
<ul>
<li>悬停时显示手形鼠标
<ul>
<li>this.btnTabCommonData.Click += new EventHandler(btnTabCommonData_Click);
<p> private void btn_MouseEnter(object sender, EventArgs e)<br />        {<br />            ((Button)sender).Cursor = Cursors.Hand;<br />        }</p>

　　</li>

</ul>

</li>

</ul>

</li>

</ul>