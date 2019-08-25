#1. JDBC

```
spring.datasource.url=jdbc:postgresql://127.0.0.1:5432/postgres
spring.datasource.username=postgres
spring.datasource.password=deb.deb
spring.datasource.driver-class-name=org.postgresql.Driver


@Service
public class UserService {
	@Autowired
	private JdbcTemplate jdbcTemplate;
	public List<User> getList(){
	    String sql = "SELECT id, name, password FROM test.user ";
	    return (List<User>) jdbcTemplate.query(sql, new RowMapper<User>(){
	      @Override
	      public User mapRow(ResultSet rs, int rowNum) throws SQLException {
	        User user = new User();
	        user.setId(rs.getInt("id"));
	        user.setName(rs.getString("name"));
	        user.setPassword(rs.getString("password"));
	        return user;
	      }
	 
	    });
	  }

}
```

#2. Mybatis
* **在mapper.xml中insert操作使用useGeneratedKeys="true"和keyProperty="id"时，需要提前在数据库中建表时设好自增键。Mybatis并不能自动建表？**
* 接口+xml方式
    * 处理业务和代码可服用性较好但存在繁琐的xml

```
mybatis.config-locations=classpath:mybatis/mybatis-config.xml
mybatis.mapper-locations=classpath:mybatis/mapper/*.xml
mybatis.type-aliases-package=com.main.model


@SpringBootApplication
@MapperScan("com.main.mapper")
public class SpringBootProgresqlApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootProgresqlApplication.class, args);
	}
}


public interface UserMapper {
	List<UserEntity> getAll();
	
	UserEntity getOne(Long id);

	void insert(UserEntity user);

	void update(UserEntity user);

	void delete(Long id);
}


<!-- mybatis-config.xml -->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<typeAliases>
		<typeAlias alias="Integer" type="java.lang.Integer" />
		<typeAlias alias="Long" type="java.lang.Long" />
		<typeAlias alias="HashMap" type="java.util.HashMap" />
		<typeAlias alias="LinkedHashMap" type="java.util.LinkedHashMap" />
		<typeAlias alias="ArrayList" type="java.util.ArrayList" />
		<typeAlias alias="LinkedList" type="java.util.LinkedList" />
	</typeAliases>
</configuration>


<!-- UserMapper.xml -->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.main.mapper.UserMapper" >
    <resultMap id="BaseResultMap" type="com.main.model.UserEntity" >
        <id column="id" property="id" jdbcType="BIGINT" />
        <result column="userName" property="userName" jdbcType="VARCHAR" />
        <result column="passWord" property="passWord" jdbcType="VARCHAR" />
        <result column="user_sex" property="userSex" jdbcType="VARCHAR"/>
        <result column="nick_name" property="nickName" jdbcType="VARCHAR" />
    </resultMap>
    
    <sql id="Base_Column_List" >
        id, userName, passWord, user_sex, nick_name
    </sql>

    <select id="getAll" resultMap="BaseResultMap"  >
       SELECT 
       <include refid="Base_Column_List" />
	   FROM test.userentity
    </select>

    <select id="getOne" parameterType="java.lang.Long" resultMap="BaseResultMap" >
        SELECT 
       <include refid="Base_Column_List" />
	   FROM test.userentity
	   WHERE id = #{id}
    </select>

    <insert id="insert" parameterType="com.main.model.UserEntity" >
       INSERT INTO 
       		test.userentity
       		(userName,passWord,user_sex) 
       	VALUES
       		(#{userName}, #{passWord}, #{userSex})
    </insert>
    
    <update id="update" parameterType="com.main.model.UserEntity" >
       UPDATE 
       		test.userentity
       SET 
       	<if test="userName != null">userName = #{userName},</if>
       	<if test="passWord != null">passWord = #{passWord},</if>
       	nick_name = #{nickName}
       WHERE 
       		id = #{id}
    </update>
    
    <delete id="delete" parameterType="java.lang.Long" >
       DELETE FROM
       		 test.userentity 
       WHERE 
       		 id =#{id}
    </delete>

</mapper>
```

* 使用mapper和provider
    * 简洁但处理复杂业务则代码可读性不好（如果用provider来拼sql其实也还好，比如直接写一长段sql好一点）
    * mapper包下面定义访问数据的方法，可以直接用sql语句，也可以指定provider类中的方法拼接sql，也可以使用xml文件
    * provider包下面相当于动态拼接sql语句，也类似于传统的mybatis的xml文件的作用

```
@Mapper
public interface EUserInfoMapper {

	 /**
     *用户登录
     * @param userName
     * @param pwd 
     * @return
     */
    @Select("select * from  `staff` where id=#{userName} and password=#{pwd}")
    EUser login(@Param("userName") String userName,@Param("pwd") String pwd);
    
     * @return
     */
    @SelectProvider(type = UserSqlProvider.class, method = "getUsersFilter")
    List<EUser> serachUsers(SerachUserVO serachUserVO);
   
}


public class UserSqlProvider {

    public String getUsersFilter(final SerachUserVO serachUserVO) {
        String sql =new SQL() {
            {
                SELECT("*");
                FROM("staff");
                WHERE("del_flag=0");
                if (StringUtil.isNotEmpty(serachUserVO.getUser_name())) {
                    WHERE("id=#{user_name}");
                }
                if (StringUtil.isNotEmpty(serachUserVO.getReal_name())) {
                	WHERE("name=#{real_name}");
                }
                if (StringUtil.isNotEmpty(serachUserVO.getMobile())) {
                	WHERE("cell_phone=#{mobile}");
                }
                ORDER_BY("id desc limit #{page_index},#{page_size}");
            }
        }.toString();
        return sql;
    }
}
```

```
@select("select * from  t_user ")
    public  list<User> findAll();
```

#3. JPA（Hibernate）
* **在model类中使用@Id和@GeneratedValue(strategy = GenerationType.AUTO)，可以在自动建表时定义自增列，不需要再自己操作数据定义表**