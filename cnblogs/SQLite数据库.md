<ul>
<li>SQLite
<ul>
<li>SQLite is a self-contained, high-reliability,&nbsp;<strong>embedded</strong>, full-featured,&nbsp;<strong>public-domain</strong>, SQL database engine.</li>
<li><a href="https://www.sqlite.org/index.html" target="_blank">https://www.sqlite.org/index.html</a></li>
<li>License:&nbsp;Public Domain, Open-Source, not Open-Contribution</li>
</ul>
</li>
<li>管理工具
<ul>
<li>VS插件-SQLite/SQL Server Compact Toolbox
<ul>
<li>新增了数据，自增id没有自动刷新出来</li>
<li>如果重命名了表名使view失效，会导致数据库数据错误，无法进行进一步操作如查看表数据，因此只能把表名改回去。可以考虑先删了view再重建。</li>
<li>UI上无法直接修改字段定义</li>
</ul>
</li>
</ul>
</li>
<li>基本使用
<ul>
<li>批量插入另一张表的数据
<ul>
<li>insert into nc_variable select id, area_id, module_id,name from nc_variable_1</li>
<li>列数不同时：insert into common_data(var_type, var_id) select 1, var_id from common_data1</li>
</ul>
</li>
<li>批量更新某一列的数据： <span style="color: #000000;">update info_t set info_t.name=desc_t.name from nc_variable_info_en_us info_t, nc_variable_desc_en_us desc_t where info_t.var_id=desc_t.var_id</span></li>
<li>创建索引
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('da0333d9-c0e9-465e-bfa9-424c0a60442a')"><img id="code_img_closed_da0333d9-c0e9-465e-bfa9-424c0a60442a" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_da0333d9-c0e9-465e-bfa9-424c0a60442a" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('da0333d9-c0e9-465e-bfa9-424c0a60442a',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_da0333d9-c0e9-465e-bfa9-424c0a60442a" class="cnblogs_code_hide">
<pre><span style="color: #000000;">CREATE INDEX idx_var_id
ON nc_variable_desc_en_us (var_id);CREATE INDEX idx_var_id
ON nc_variable_desc_en_us (var_id);</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
</li>
<li>特殊语法
<ul>
<li>SQLite<strong><span style="color: #ff0000;">不支持drop column语句</span></strong>，因此只能重建一个没有该列的表并把数据放进来</li>
<ul>
<li>同时创建表：create<span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">table</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">temp</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">as</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">select</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;recordId,&nbsp;customer,&nbsp;place,&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">time</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">from</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;record&nbsp;</span><span class="keyword" style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; color: #006699; background-color: inherit; font-weight: bold; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">where</span><span style="box-sizing: border-box; outline: 0px; margin: 0px; padding: 0px; overflow-wrap: break-word; border: none; background-color: inherit; font-family: Consolas, 'Courier New', Courier, mono, serif; font-size: 12px;">&nbsp;1&nbsp;= 1;</span></li>
</ul>
<li>限制查询数据数量：在语句结尾加<span style="color: #ff0000;"><strong>LIMIT x</strong></span></li>
<li>Sqlite的select语句中拼接字符串使用<span style="color: #ff0000;"><strong>||</strong></span></li>
</ul>
</li>
<li>密码/加密
<ul>
<li>SQLite有密码功能，并且<span style="color: #ff0000;">设置密码之后，数据库文件就会被加密<span style="color: #000000;">，可以使用notepad++的工具查看加密前后的文件内容，加密前都是明文，包括数据库元数据和数据等</span></span>。</li>
<li>SQLite for .NET (System.Data.SQLite)提供了<strong><span style="color: #ff0000;">在SQLiteConnection上</span><span style="color: #ff0000;"><strong>修改</strong>/设置密码</span></strong>的函数：public void <span style="color: #ff0000;"><strong>ChangePassword</strong></span>(string newPassword);</li>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('b3b0e3f2-2cbb-4bd0-85a9-f31de3b5674c')"><img id="code_img_closed_b3b0e3f2-2cbb-4bd0-85a9-f31de3b5674c" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_b3b0e3f2-2cbb-4bd0-85a9-f31de3b5674c" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('b3b0e3f2-2cbb-4bd0-85a9-f31de3b5674c',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_b3b0e3f2-2cbb-4bd0-85a9-f31de3b5674c" class="cnblogs_code_hide">
<pre><span style="color: #0000ff;">using</span> (SQLiteConnection connection = <span style="color: #0000ff;">new</span><span style="color: #000000;"> SQLiteConnection(dbFileConnectionString))
            {
                connection.Open();
                connection.ChangePassword(GenerateEncryptedDBPwd());
                connection.Close();
            }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
<li>为了信息安全，可以自行生成特殊密码
<ul>
<li>比如结合<strong><span style="color: #ff0000;">windows用户的sid</span></strong>和<span style="color: #ff0000;"><strong>程序自定义的一个密钥</strong></span>组合起来使用<span style="color: #ff0000;"><strong>AES加密</strong></span>得到密码。这样在客户端程序中，就可以为每个用户生成一个特定密码的数据库文件，那么不同用户的密码就不同了。同时保证了用户特殊性和程序特征。</li>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('6b23e996-0fb4-4d9a-8ec8-2d0a053bcc3a')"><img id="code_img_closed_6b23e996-0fb4-4d9a-8ec8-2d0a053bcc3a" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_6b23e996-0fb4-4d9a-8ec8-2d0a053bcc3a" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('6b23e996-0fb4-4d9a-8ec8-2d0a053bcc3a',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_6b23e996-0fb4-4d9a-8ec8-2d0a053bcc3a" class="cnblogs_code_hide">
<pre>            System.Security.Principal.WindowsIdentity currentUser =<span style="color: #000000;"> System.Security.Principal.WindowsIdentity.GetCurrent();
            </span><span style="color: #0000ff;">string</span> sid = currentUser.User.ToString();</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
</li>
<li>在代码中构造连接字符串（不用自己拼各个部分了）
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('c00e3595-13b9-4c4a-a2f2-93a2357a6393')"><img id="code_img_closed_c00e3595-13b9-4c4a-a2f2-93a2357a6393" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_c00e3595-13b9-4c4a-a2f2-93a2357a6393" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('c00e3595-13b9-4c4a-a2f2-93a2357a6393',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_c00e3595-13b9-4c4a-a2f2-93a2357a6393" class="cnblogs_code_hide">
<pre>            SQLiteConnectionStringBuilder builder = SQLiteFactory.Instance.CreateConnectionStringBuilder() <span style="color: #0000ff;">as</span><span style="color: #000000;"> SQLiteConnectionStringBuilder;

            </span><span style="color: #008000;">//</span><span style="color: #008000;"> set up connection string for user data db in code</span>
            builder.DataSource =<span style="color: #000000;"> userDataDBFilePath;
            builder.Password </span>=<span style="color: #000000;"> GenerateEncryptedDBPwd();
            userDataDB </span>= <span style="color: #0000ff;">new</span> UserDataContext(builder.ToString());</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
</ul>
<p><span style="color: #0000ff; font-weight: bold;">update</span> info_t <span style="color: #0000ff; font-weight: bold;">set</span> info_t.name=desc_t.name <span style="color: #0000ff; font-weight: bold;">from</span> nc_variable_info_en_us info_t, nc_variable_desc_en_us desc_t <span style="color: #0000ff; font-weight: bold;">where</span> info_t.var_id=desc_t.var_id</p>