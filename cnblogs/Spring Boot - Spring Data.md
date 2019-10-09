* 使用JPA
    * 虽然JPA是一个标准，但spring中一般就是使用hibernate实现的
    * 使用JPA（Java Persistence API，Java持久化API，是个规范，其实是借助Hibernate实现的）操作数据库，使用其CrudRepository接口。
    * 数据库建好后，表会在使用时自动创建，具体逻辑要看spring.jpa.hibernate.ddl-auto的配置。
    * 依赖是通过Gradle的dependencies中加入compile('org.springframework.boot:spring-boot-starter-data-jpa')，会简介引用一些其他的包如spring.rabbit-1.7.8.RELEASE.jar等
        * 也可能是通过引入上层的配置
    * application.properties中配置数据库信息，如果是Cloud Foundry等平台，app部署后的环境变量中如果有同名参数则会覆盖properties文件
        * spring.jpa.show-sql可以控制是否打印执行的sql语句
        * spring.jpa.hibernate.ddl-auto
            * ddl-auto:create----每次运行该程序，没有表格会新建表格，表内有数据会清空
            * ddl-auto:create-drop----每次程序结束的时候会清空表
            * ddl-auto:update----每次运行程序，没有表格会新建表格，表内有数据不会清空，只会更新
            * ddl-auto:validate----运行程序会校验数据与数据库的字段类型是否相同，不同会报错
        * spring.jpa.hibernate.naming.physical-strategy
            * 关掉自动转小写、加下划线：org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImp

```
# postgresql
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/amp
spring.datasource.username=postgres
spring.datasource.password=deb.deb
spring.jpa.database=POSTGRESQL
spring.jpa.database-platform=org.hibernate.dialect.PostgresPlusDialect
spring.jpa.show-sql=false
spring.jpa.hibernate.ddl-auto=update

```

    * 定义用户POJO（算是数据库表的映射类）
        * 没有表、索引等时，会自动创建
        * 会用到一些数据库相关的注解，如@Entity(name = "assignment")、@Table、@Index、@NoArgsConstructor、@Id、@GeneratedValue(strategy = GenerationType.AUTO)、@Column(name = "tenant_id", nullable = false)
            * 可以用@Entity去指定对应的数据库表名
                * JPA在建表时会按他自己的逻辑去命名，比如转换大小写、加下划线什么的。如果想关掉这个配置，需要在application.properties文件中加上：**spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl**

```
@Entity(name = "assignment")
@Table(indexes = {
		@Index(name = AMPShiftCalendarConstants.IDX_TENANT_ID_ASSET_ID_ASSIGNMENT_START_DATE, columnList = "tenant_id, asset_id, assignment_start_date"),
		@Index(name = AMPShiftCalendarConstants.IDX_TENANT_ID_ASSIGNMENT_START_DATE, columnList = "tenant_id, assignment_start_date") })
@NoArgsConstructor
@Getter
@Setter
public class Assignment {

	@Id
	@GeneratedValue(strategy = GenerationType.AUTO)
	@Column(name = "assignment_id")
	private Long assignmentId;

	@Column(name = "tenant_id", nullable = false)
	private String tenantId;

	@Column(name = "asset_id", nullable = false)
	private String assetId;

	@Column(name = "content", columnDefinition = "TEXT")
	private String content;

	@Column(name = "assignment_start_date", columnDefinition = "timestamp with time zone", nullable = false)
	private Timestamp assignmentStartDate;

	@Column(name = "inserted_date", columnDefinition = "timestamp with time zone", nullable = false)
	private Timestamp insertedDate;

	@Enumerated(EnumType.STRING)
	@Column(name = "status", nullable = false)
	private AssignmentStatus status;

	public Assignment(String tenantId, String assetId, ShiftCalendar content, Timestamp assignmentStartDate, Timestamp insertedDate, AssignmentStatus status) {
		this.tenantId = tenantId;
		this.assetId = assetId;
		this.setShiftCalendar(content);
		this.assignmentStartDate = assignmentStartDate;
		this.insertedDate = insertedDate;
		this.status = status;
	}

	public ShiftCalendar getShiftCalendar() {
		return content != null ? Misc.deserializeSafeJson(content, ShiftCalendar.class) : null;
	}

	public void setShiftCalendar(ShiftCalendar template) {
		Check.notNull(template, "shiftCalendar");

		this.content = Misc.serializeSafeJson(template);
	}

	@Override
	public String toString() {
		return "Assignment [assignment_id=" + assignmentId + ", asset=" + assetId + ", assignmentStatus=" + status + ", content=" + content
				+ ", assignment_start_date=" + assignmentStartDate + "]";
	}
}

```

    * 自定义Repository类
        * 基于表及其映射类，也可以直接用CrudRepository
        * 用于数据库操作，有点类似于.NET的EF的DBContext类，基类已实现了部分通用简单的基于数据库表的操作，复杂的还是要自己拼sql（用xml或动态sql或者如下的方式）。
        * 其实还有其他更具体的功能更强的子类可以用，比如可以分页什么的。
        * getAllByTenantIdAndAssetId这种其实是利用了JPA的命名查询功能，可以指定通过哪些列来查询，还可以使用like功能。
        * @Query注解其实是JPA提供查询语言的JPQL，与Hibernate提供的HQL十分接近。
        * JPA（Hibernate）其实慢慢被mybatis替代了？？
        * 可以通过给Repo的函数传入Pageable参数进行分页，其可来自于接口调用

```
@Repository
public interface AssignmentRepository extends CrudRepository<Assignment, Long> {

	List<Assignment> getAllByTenantId(String tenantId);

	List<Assignment> getAllByTenantIdAndAssetId(String tenantId, String assetId);

	@Query(value = "" +
			"  SELECT " +
			"DISTINCT a.asset_id AS asset_id" +
			"    FROM assignment a " +
			"   WHERE a.tenant_id = :tenant_id" +
			"     AND a.assignment_start_date < :end_date" +
			"     AND a.status='ACTIVE'",
			nativeQuery = true)
	List<String> getAllAssetsWithActiveWorkplan(
			@Param("tenant_id") String tenantId,
			@Param("end_date") Timestamp endDate);
}

public interface MissingWorkPieceRepository extends CrudRepository<MissingWorkPiece, Long> {

	List<MissingWorkPiece> getAllByAssetIdAndTenantIdAndDateBetweenOrderByDateDesc(String assetId, String tenantId, Timestamp from, Timestamp to);
	
	Optional<MissingWorkPiece> getByAssetIdAndTenantIdAndDate(String assetId, String tenantId, Timestamp date);
	
	@Transactional	
	void deleteByAssetIdAndTenantIdAndDate(String assetId, String tenantId, Timestamp date);
	
}
```

```
https://dimcdev-mmmui-dimcdev.cn1.mindsphere-in.cn/v3/alarms?from=1570377600000&to=1570511445993&size=9007199254740991


    @RequestMapping(value = "", method = RequestMethod.POST, produces = APPLICATION_JSON_VALUE)
    @ApiOperation("Get alarms for agents by time range.")
    @ResponseStatus(HttpStatus.OK)
    @ApiResponses(value = { @ApiResponse(code = SC_OK, message = "OK", response = AlarmProtocol.class),
            @ApiResponse(code = SC_BAD_REQUEST, message = Messages.BAD_REQUEST_MESSAGE, response = String.class),
            @ApiResponse(code = SC_UNAUTHORIZED, message = Messages.UNAUTHORIZED_MESSAGE, response = String.class),
            @ApiResponse(code = SC_FORBIDDEN, message = Messages.FORBIDDEN_MESSAGE, response = String.class),
            @ApiResponse(code = SC_NOT_FOUND, message = Messages.NOT_FOUND_MESSAGE, response = String.class) })
    // @PreAuthorize("#oauth2.hasScope(this.readRole)")
    public List<AlarmProtocol> getAlarmsByAgentIdsAndTimeRange(
            @PageableDefault(size = Integer.MAX_VALUE) final Pageable pageable,
            @ApiParam(required = true)  @RequestBody final List<String> agentIds,
            @RequestParam(required = true) Long from,
            @RequestParam(required = true) Long to)


    @Query("SELECT a from alarm_protocol a WHERE a.agentId in ?1 "
                + "AND a.stStartTime <= ?3 "
                + "AND (a.stEndTime >= ?2 OR a.stEndTime IS NULL) "
                + "ORDER BY a.stStartTime DESC")
    List<AlarmProtocol> findAlarmsByAgentIdsAndTimeRange(Pageable pageable, List<String> agentIds, Long from, Long to);
```