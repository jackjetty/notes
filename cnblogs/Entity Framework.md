<ul>
<li>使用方式
<ul>
<li>DB FIrst
<ul>
<li>需要vs装了EF Tool才方便（默认应该是有的，不行就再去市场找）</li>
</ul>
</li>
<li>Model First</li>
<li>Code First
<ul>
<li>可在<span style="color: #ff0000;"><strong>OnModelCreating</strong></span>中取消默认的命名约束（实体类名是表名复数，DB First中在创建映射时也可以取消勾选）
<ul>
<li>modelBuilder.Conventions.Remove&lt;PluralizingTableNameConvention&gt;();</li>
</ul>
</li>
<li>可在<span style="color: #ff0000;"><strong>OnModelCreating</strong></span>中自行指定表映射关系
<ul>
<li>modelBuilder.Entity&lt;EntireInfo&gt;().ToTable(currentLanguageViewName);</li>
<li>其中modelBuilder.Entity&lt;EntireInfo&gt;是自定义的新建的mapping出的model构造器，还可以做其他操作
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('d4cd7332-bb4f-4131-92eb-61205ced6312')"><img id="code_img_closed_d4cd7332-bb4f-4131-92eb-61205ced6312" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_d4cd7332-bb4f-4131-92eb-61205ced6312" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('d4cd7332-bb4f-4131-92eb-61205ced6312',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_d4cd7332-bb4f-4131-92eb-61205ced6312" class="cnblogs_code_hide">
<pre>        <span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> OnModelCreating(ModelBuilder modelBuilder)
        {
            </span><span style="color: #0000ff;">base</span><span style="color: #000000;">.OnModelCreating(modelBuilder);
            EntityTypeBuilder</span>&lt;Category&gt; entityTypeBuilder = modelBuilder.Entity&lt;Category&gt;<span style="color: #000000;">();
            entityTypeBuilder.ToTable(</span><span style="color: #800000;">"</span><span style="color: #800000;">Category</span><span style="color: #800000;">"</span><span style="color: #000000;">);
            entityTypeBuilder.HasKey(e </span>=&gt;<span style="color: #000000;"> e.CategoryID);
            entityTypeBuilder.Property(e </span>=&gt;<span style="color: #000000;"> e.CategoryID).UseSqlServerIdentityColumn();
        }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
<li>还可参考：https://blog.csdn.net/zero_/article/details/6388807</li>
</ul>
</li>
<li>Database Initialization Strategies in EF 6 Code-First
<ul>
<li>没有的话，一般是Database<span class="pun" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #303336;">.</span><span class="typ" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #2b91af;">SetInitializer</span><span class="pun" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #303336;">&lt;</span><span class="typ" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #2b91af;">EntitiesName</span><span class="pun" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #303336;">&gt;(</span><span class="kwd" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #101094;">null</span><span class="pun" style="font-family: inherit; font-style: inherit; font-variant: inherit; font-weight: inherit; white-space: inherit; background-color: #eff0f1; font-size: 13px; margin: 0px; padding: 0px; border: 0px; font-stretch: inherit; line-height: inherit; vertical-align: baseline; box-sizing: inherit; color: #303336;">);</span></li>
</ul>
</li>
<li>消除no such table: __MigrationHistory和no such table: EdmMetadata错误</li>
<li>重写的<span style="color: #ff0000;"><strong>OnModelCreating</strong></span>事件中不能使用DbContext的Database，因为还没有创建好映射关系，所以直接用ADO.NET进行操作先。
<ul>
<li>否则会报错：The context cannot be used while the model is being createdThe context cannot be used while the model is being created</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li>判断表/视图是否存在（其实就是通过EF执行sql，也可以使用DbSet的FromSql或Database.ExecuteSqlCommand执行sql，也可以直接用ADO.NET，以SQLite为例）
<ul>
<li>
<div class="cnblogs_code" onclick="cnblogs_code_show('697a86cb-13ba-40a0-914c-455f918eec0a')"><img id="code_img_closed_697a86cb-13ba-40a0-914c-455f918eec0a" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_697a86cb-13ba-40a0-914c-455f918eec0a" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('697a86cb-13ba-40a0-914c-455f918eec0a',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_697a86cb-13ba-40a0-914c-455f918eec0a" class="cnblogs_code_hide">
<pre>            <span style="color: #0000ff;">bool</span> isExisted = presetDataDB.Database.SqlQuery&lt;<span style="color: #0000ff;">int</span>?&gt;(<span style="color: #800000;">@"</span><span style="color: #800000;">
                     select * from sqlite_master where type = 'table' and name = '{</span><span style="color: #800000;">"</span> + currentLanguageViewName + <span style="color: #800000;">"</span><span style="color: #800000;">}'</span><span style="color: #800000;">"</span><span style="color: #000000;">)
                     .SingleOrDefault() </span>!= <span style="color: #0000ff;">null</span>;</pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
</li>
</ul>
</li>
<li>EF中表/实体类的继承关系（一个实体对应多个表，反过来没必要特殊处理）
<ul>
<li>TPH</li>
<li>TPT</li>
<li>TPC</li>
<li>参考
<ul>
<li>https://social.msdn.microsoft.com/forums/azure/zh-tw/6ba06d00-004a-4828-98e7-1ac5387fead3/entity-framework-one-entity-mapping-multiple-tables</li>
<li>https://weblogs.asp.net/manavi/inheritance-mapping-strategies-with-entity-framework-code-first-ctp5-part-1-table-per-hierarchy-tph</li>
<li>http://blogs.microsoft.co.il/gilf/2010/01/24/table-per-hierarchy-inheritance-in-entity-framework/</li>
</ul>
</li>
</ul>
</li>
<li>EF自动生成的sql语句分析
<ul>
<li>工具</li>
<li>数据库端的监控，如Sql Server Profiler</li>
</ul>
</li>
<li>缓存？
<ul>
<li>有时后台新增了数据，并没有立刻取到</li>
</ul>
</li>
<li>异步</li>
<li>延迟更新？</li>
<li>注意
<ul>
<li>有时其他实体和表的字段等映射关系有问题，即使没用到这个表，执行不相关的表的操作也会报错，不过通常会指出什么错误，但可能没有具体位置。</li>
</ul>
</li>
</ul>