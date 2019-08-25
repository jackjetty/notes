<p>&nbsp;key的位数固定为32个字符，也就是512位</p>
<p>注意对末尾为0的要特殊处理，否则会少一位？？</p>
<p>&nbsp;</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('9fa48598-9c1e-4421-99ec-e6fe2fcbad10')"><img id="code_img_closed_9fa48598-9c1e-4421-99ec-e6fe2fcbad10" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_9fa48598-9c1e-4421-99ec-e6fe2fcbad10" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('9fa48598-9c1e-4421-99ec-e6fe2fcbad10',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_9fa48598-9c1e-4421-99ec-e6fe2fcbad10" class="cnblogs_code_hide">
<pre>        <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">string</span> AesEncrypt(<span style="color: #0000ff;">string</span> str, <span style="color: #0000ff;">string</span><span style="color: #000000;"> key)
        {
            </span><span style="color: #0000ff;">if</span> (<span style="color: #0000ff;">string</span>.IsNullOrEmpty(str)) <span style="color: #0000ff;">return</span> <span style="color: #0000ff;">null</span><span style="color: #000000;">;
            Byte[] toEncryptArray </span>=<span style="color: #000000;"> Encoding.UTF8.GetBytes(str);

            RijndaelManaged rm </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> RijndaelManaged
            {
                Key </span>=<span style="color: #000000;"> Encoding.UTF8.GetBytes(key),
                Mode </span>=<span style="color: #000000;"> CipherMode.ECB,
                Padding </span>=<span style="color: #000000;"> PaddingMode.PKCS7
            };

            ICryptoTransform cTransform </span>=<span style="color: #000000;"> rm.CreateEncryptor();
            Byte[] resultArray </span>= cTransform.TransformFinalBlock(toEncryptArray, <span style="color: #800080;">0</span><span style="color: #000000;">, toEncryptArray.Length);

            </span><span style="color: #0000ff;">return</span> Convert.ToBase64String(resultArray, <span style="color: #800080;">0</span><span style="color: #000000;">, resultArray.Length);
        }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>&nbsp;</p>