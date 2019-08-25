<ul>
<li>SQLite for .NET (System.Data.SQLite)
<ul>
<li>introduction</li>
<ul>
<li>An ADO.NET provider for SQLite including EF and Linq functions.</li>
<li><a href="http://system.data.sqlite.org/index.html/doc/trunk/www/index.wiki" target="_blank">http://system.data.sqlite.org/index.html/doc/trunk/www/index.wiki</a></li>
<li>Developed by SQLite Development Team</li>
<li>License: Public Domain, Open-Source, not Open-Contribution</li>
</ul>
<li>how to use
<ul>
<li>Add references (Nuget to find &ldquo;System.Data.SQLite&rdquo;)
<ul>
<li>EntityFramework (can be upgraded to 6.2.0, already include EF and Linq)</li>
<li>System.Data.SQLite</li>
<li>System.Data.SQLite.Core</li>
<li>System.Data.SQLite.EF6 (can be removed)</li>
<li>System.Data.SQLite.Linq (can be removed)</li>
</ul>
</li>
<li>Setup configuration file (in App.config)
<ul>
<li>connection string</li>
<li>db provider</li>
</ul>
</li>
<li>Create custom context class</li>
</ul>
</li>
</ul>
</li>
</ul>
<p>在App.Config或Web.config文件中配置数据库的连接字符串及使用到的Provider：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span> &lt;?xml version=<span style="color: #800000;">"</span><span style="color: #800000;">1.0</span><span style="color: #800000;">"</span> encoding=<span style="color: #800000;">"</span><span style="color: #800000;">utf-8</span><span style="color: #800000;">"</span>?&gt;
<span style="color: #008080;"> 2</span> &lt;configuration&gt;
<span style="color: #008080;"> 3</span>   &lt;configSections&gt;
<span style="color: #008080;"> 4</span>     &lt;!-- For more information on Entity Framework configuration, visit http:<span style="color: #008000;">//</span><span style="color: #008000;">go.microsoft.com/fwlink/?LinkID=237468 --&gt;</span>
<span style="color: #008080;"> 5</span>     &lt;section name=<span style="color: #800000;">"</span><span style="color: #800000;">entityFramework</span><span style="color: #800000;">"</span> type=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089</span><span style="color: #800000;">"</span> requirePermission=<span style="color: #800000;">"</span><span style="color: #800000;">false</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;"> 6</span>   &lt;/configSections&gt;
<span style="color: #008080;"> 7</span>   &lt;connectionStrings&gt;
<span style="color: #008080;"> 8</span>     &lt;add name=<span style="color: #800000;">"</span><span style="color: #800000;">PresetDataConnectionString</span><span style="color: #800000;">"</span> connectionString=<span style="color: #800000;">"</span><span style="color: #800000;">data source=PresetData.db</span><span style="color: #800000;">"</span> providerName=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;"> 9</span>   &lt;/connectionStrings&gt;
<span style="color: #008080;">10</span>   &lt;entityFramework&gt;
<span style="color: #008080;">11</span>     &lt;defaultConnectionFactory type=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework</span><span style="color: #800000;">"</span>&gt;
<span style="color: #008080;">12</span>       &lt;parameters&gt;
<span style="color: #008080;">13</span>         &lt;parameter value=<span style="color: #800000;">"</span><span style="color: #800000;">v13.0</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;">14</span>       &lt;/parameters&gt;
<span style="color: #008080;">15</span>     &lt;/defaultConnectionFactory&gt;
<span style="color: #008080;">16</span>     &lt;providers&gt;
<span style="color: #008080;">17</span>       &lt;provider invariantName=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite</span><span style="color: #800000;">"</span> type=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite.EF6.SQLiteProviderServices, System.Data.SQLite.EF6</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;">18</span>     &lt;/providers&gt;
<span style="color: #008080;">19</span>   &lt;/entityFramework&gt;
<span style="color: #008080;">20</span>   &lt;system.data&gt;
<span style="color: #008080;">21</span>     &lt;DbProviderFactories&gt;
<span style="color: #008080;">22</span>       &lt;remove invariant=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;">23</span>       &lt;add name=<span style="color: #800000;">"</span><span style="color: #800000;">SQLite Data Provider</span><span style="color: #800000;">"</span> invariant=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite</span><span style="color: #800000;">"</span> description=<span style="color: #800000;">"</span><span style="color: #800000;">.NET Framework Data Provider for SQLite</span><span style="color: #800000;">"</span> type=<span style="color: #800000;">"</span><span style="color: #800000;">System.Data.SQLite.SQLiteFactory, System.Data.SQLite</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;">24</span>     &lt;/DbProviderFactories&gt;
<span style="color: #008080;">25</span>   &lt;/system.data&gt;
<span style="color: #008080;">26</span>   &lt;startup&gt;
<span style="color: #008080;">27</span>     &lt;supportedRuntime version=<span style="color: #800000;">"</span><span style="color: #800000;">v4.0</span><span style="color: #800000;">"</span> sku=<span style="color: #800000;">"</span><span style="color: #800000;">.NETFramework,Version=v4.6.2</span><span style="color: #800000;">"</span>/&gt;
<span style="color: #008080;">28</span>   &lt;/startup&gt;
<span style="color: #008080;">29</span> &lt;/configuration&gt;</pre>
</div>
<p>&nbsp;</p>
<p>自定义Context类，用于操作数据库：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;"> 1</span>     <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;"> 2</span>     <span style="color: #808080;">///</span><span style="color: #008000;"> Code First mode to use EF, on behalf of one DB
</span><span style="color: #008080;"> 3</span>     <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;"> 4</span>     <span style="color: #0000ff;">public</span> <span style="color: #0000ff;">class</span><span style="color: #000000;"> UserDataContext : DbContext
</span><span style="color: #008080;"> 5</span> <span style="color: #000000;">    {
</span><span style="color: #008080;"> 6</span>         <span style="color: #0000ff;">public</span> DbSet&lt;FavoriteData&gt; FavoriteDatas { <span style="color: #0000ff;">get</span>; <span style="color: #0000ff;">set</span><span style="color: #000000;">; }
</span><span style="color: #008080;"> 7</span> 
<span style="color: #008080;"> 8</span>         <span style="color: #0000ff;">public</span> UserDataContext(<span style="color: #0000ff;">string</span><span style="color: #000000;"> connectionString)
</span><span style="color: #008080;"> 9</span>             : <span style="color: #0000ff;">base</span>(<span style="color: #0000ff;">new</span> SQLiteConnection() { ConnectionString = connectionString }, <span style="color: #0000ff;">true</span><span style="color: #000000;">)
</span><span style="color: #008080;">10</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">11</span> 
<span style="color: #008080;">12</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">13</span> 
<span style="color: #008080;">14</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;">15</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> avoid "no such table error" of __MigrationHistory and EdmMetadata in EF Code-First Mode
</span><span style="color: #008080;">16</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;">17</span>         <span style="color: #0000ff;">static</span><span style="color: #000000;"> UserDataContext()
</span><span style="color: #008080;">18</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">19</span>             Database.SetInitializer&lt;PresetDataContext&gt;(<span style="color: #0000ff;">null</span><span style="color: #000000;">);
</span><span style="color: #008080;">20</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">21</span> 
<span style="color: #008080;">22</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;">23</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> dummy code so that the compiler would detect that the reference is being used.
</span><span style="color: #008080;">24</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> make sure projects like VisualXml and VisualXml.Test have related dlls in output bin folder
</span><span style="color: #008080;">25</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;">26</span>         <span style="color: #0000ff;">private</span> <span style="color: #0000ff;">static</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> FixProvidersNotAutoLoadProblem()
</span><span style="color: #008080;">27</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">28</span>             <span style="color: #0000ff;">var</span> _ = <span style="color: #0000ff;">typeof</span><span style="color: #000000;">(System.Data.SQLite.EF6.SQLiteProviderFactory);
</span><span style="color: #008080;">29</span>             <span style="color: #0000ff;">var</span> __ = <span style="color: #0000ff;">typeof</span><span style="color: #000000;">(System.Data.SQLite.SQLiteFactory);
</span><span style="color: #008080;">30</span>             <span style="color: #008000;">//</span><span style="color: #008000;">var ___ = typeof(System.Data.Entity.SqlServer.SqlProviderServices);</span>
<span style="color: #008080;">31</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">32</span> 
<span style="color: #008080;">33</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;summary&gt;</span>
<span style="color: #008080;">34</span>         <span style="color: #808080;">///</span><span style="color: #008000;"> remove restriction of DbSet field names based on table names
</span><span style="color: #008080;">35</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;/summary&gt;</span>
<span style="color: #008080;">36</span>         <span style="color: #808080;">///</span> <span style="color: #808080;">&lt;param name="modelBuilder"&gt;&lt;/param&gt;</span>
<span style="color: #008080;">37</span>         <span style="color: #0000ff;">protected</span> <span style="color: #0000ff;">override</span> <span style="color: #0000ff;">void</span><span style="color: #000000;"> OnModelCreating(DbModelBuilder modelBuilder)
</span><span style="color: #008080;">38</span> <span style="color: #000000;">        {
</span><span style="color: #008080;">39</span>             modelBuilder.Conventions.Remove&lt;PluralizingTableNameConvention&gt;<span style="color: #000000;">();
</span><span style="color: #008080;">40</span> 
<span style="color: #008080;">41</span>             <span style="color: #008000;">//</span><span style="color: #008000;">modelBuilder.Configurations.AddFromAssembly(typeof(DefaultContext).Assembly);
</span><span style="color: #008080;">42</span>             <span style="color: #008000;">//</span><span style="color: #008000;">modelBuilder.Configurations.AddFromAssembly(Assembly.GetExecutingAssembly());</span>
<span style="color: #008080;">43</span> <span style="color: #000000;">        }
</span><span style="color: #008080;">44</span>     }</pre>
</div>
<p>&nbsp;</p>
<p>也可以动态设置数据库连接字符串（动态往自定义的Context类的构造函数中传入拼接的连接字符串，userDataDBFilePath为db文件物理路径）：</p>
<div class="cnblogs_code">
<pre><span style="color: #008080;">1</span>             SQLiteConnectionStringBuilder builder = SQLiteFactory.Instance.CreateConnectionStringBuilder() <span style="color: #0000ff;">as</span><span style="color: #000000;"> SQLiteConnectionStringBuilder;
</span><span style="color: #008080;">2</span> 
<span style="color: #008080;">3</span>             <span style="color: #008000;">//</span><span style="color: #008000;"> set up connection string for user data db in code</span>
<span style="color: #008080;">4</span>             builder.DataSource =<span style="color: #000000;"> userDataDBFilePath;
</span><span style="color: #008080;">5</span>             builder.Password =<span style="color: #000000;"> GenerateEncryptedDBPwd();
</span><span style="color: #008080;">6</span>             UserDataContext userDataDB = <span style="color: #0000ff;">new</span> UserDataContext(builder.ToString());</pre>
</div>
<p>&nbsp;</p>
<p>也可以直接使用ADO.NET操作数据库：</p>
<div class="cnblogs_code" onclick="cnblogs_code_show('36876299-127a-45f8-91c3-751ee11cf933')"><img id="code_img_closed_36876299-127a-45f8-91c3-751ee11cf933" class="code_img_closed" src="http://images.cnblogs.com/OutliningIndicators/ContractedBlock.gif" alt="" /><img id="code_img_opened_36876299-127a-45f8-91c3-751ee11cf933" class="code_img_opened" style="display: none;" onclick="cnblogs_code_hide('36876299-127a-45f8-91c3-751ee11cf933',event)" src="http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif" alt="" />
<div id="cnblogs_code_open_36876299-127a-45f8-91c3-751ee11cf933" class="cnblogs_code_hide">
<pre>                SQLiteConnectionStringBuilder builder = SQLiteFactory.Instance.CreateConnectionStringBuilder() <span style="color: #0000ff;">as</span><span style="color: #000000;"> SQLiteConnectionStringBuilder;
                builder.DataSource </span>= <span style="color: #800000;">"</span><span style="color: #800000;">PresetData.db</span><span style="color: #800000;">"</span><span style="color: #000000;">;
                </span><span style="color: #0000ff;">using</span> (SQLiteConnection conn = <span style="color: #0000ff;">new</span><span style="color: #000000;"> SQLiteConnection(builder.ToString()))
                {
                    conn.Open();
                    </span><span style="color: #0000ff;">string</span> currentLanguageViewName = <span style="color: #800000;">"</span><span style="color: #800000;">vw_nc_variable_entire_info_</span><span style="color: #800000;">"</span> + Thread.CurrentThread.CurrentUICulture.Name.ToLowerInvariant().Replace(<span style="color: #800000;">'</span><span style="color: #800000;">-</span><span style="color: #800000;">'</span>, <span style="color: #800000;">'</span><span style="color: #800000;">_</span><span style="color: #800000;">'</span><span style="color: #000000;">);
                    </span><span style="color: #0000ff;">string</span> sql = <span style="color: #800000;">"</span><span style="color: #800000;">select count(*) from sqlite_master where type = 'table' and name = '{</span><span style="color: #800000;">"</span> + currentLanguageViewName + <span style="color: #800000;">"</span><span style="color: #800000;">}'</span><span style="color: #800000;">"</span><span style="color: #000000;">;
                    SQLiteCommand cmd </span>= <span style="color: #0000ff;">new</span><span style="color: #000000;"> System.Data.SQLite.SQLiteCommand();
                    cmd.CommandText </span>=<span style="color: #000000;"> sql;
                    cmd.Connection </span>=<span style="color: #000000;"> conn;
                    </span><span style="color: #0000ff;">int</span> tableInfoCount =<span style="color: #000000;"> Convert.ToInt32(cmd.ExecuteScalar());
                    </span><span style="color: #0000ff;">if</span> (tableInfoCount &gt; <span style="color: #800080;">0</span><span style="color: #000000;">)
                    {
                        modelBuilder.Entity</span>&lt;EntireInfo&gt;().ToTable(<span style="color: #800000;">"</span><span style="color: #800000;">vw_nc_variable_entire_info_</span><span style="color: #800000;">"</span> + Thread.CurrentThread.CurrentUICulture.Name.ToLowerInvariant().Replace(<span style="color: #800000;">'</span><span style="color: #800000;">-</span><span style="color: #800000;">'</span>, <span style="color: #800000;">'</span><span style="color: #800000;">_</span><span style="color: #800000;">'</span><span style="color: #000000;">));
                    }
                    </span><span style="color: #0000ff;">else</span><span style="color: #000000;">
                    {
                        modelBuilder.Entity</span>&lt;EntireInfo&gt;().ToTable(<span style="color: #800000;">"</span><span style="color: #800000;">vw_nc_variable_entire_info_en_us</span><span style="color: #800000;">"</span><span style="color: #000000;">);
                    }
                    conn.Close();
                }</span></pre>
</div>
<span class="cnblogs_code_collapse">View Code</span></div>
<p>&nbsp;</p>