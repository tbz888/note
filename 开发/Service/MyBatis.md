## 基本流程
1. 初始化配置SqlSessionFactory：jdbc驱动、连接字符串等// 只初始化一次，进程作用域。
2. DaoFactory -> interface Dao -> DaoImpl
3. session = SqlSessionFactory.openSession() // 非线程安全，不能共享，确保被关闭。
4. mapper = session.getMapper(Mapper.class) -> interface Mapper -> Mapper.xml
5. Model(POJO) -> mapper.CRUD(params) 
6. session.commit()/rollback()
7. session.close()

## Mapper.xml
```xml
<select id="selectPerson" parameterType="int" resultType="hashmap">
  SELECT * FROM PERSON WHERE ID = #{id}
</select>

<insert id="insertAuthor">
  insert into Author (id,username,password,email,bio)
  values (#{id},#{username},#{password},#{email},#{bio})
</insert>

<update id="updateAuthor">
  update Author set
    username = #{username},
    password = #{password},
    email = #{email},
    bio = #{bio}
  where id = #{id}
</update>

<delete id="deleteAuthor">
  delete from Author where id = #{id}
</delete>
```
- id：标识
- parameterType：参数类型
- resultMap：返回值的key-value
- resultType：返回值类型，不能与resultMap同时使用
- timeout：设置超时
- statementType ：STATEMENT 或 PREPARED（默认） 或 CALLABLE
- flushCache：清空缓存（insert、update、delete = true，select = false）

## 参数(Parameters)
- #{}生成PreparedStatement的安全参数（相当于jdbc的?）
- ${}只是替换不转义的字符串，可能有sql注入

## 控制结构
- if 选择性包含 
```xml
<select id="findActiveBlogWithTitleLike" resultType="Blog"> 
    SELECT * FROM BLOG 
    WHERE state = 'ACTIVE'
    <if test="title != null"> 
    	AND title like #{title} 
    </if> 
</select> 
```

- choose-when-otherwise 分支性包含 
```xml
<select id="findActiveBlogLike" resultType="Blog"> 
	SELECT * FROM BLOG WHERE state = 'ACTIVE'
    <choose> 
        <when test="title != null"> 
        	AND title like #{title} 
        </when> 
        <when test="author != null and author.name != null"> 
        	AND author_name like #{author.name} 
        </when> 
        <otherwise> 
        	AND featured = 1 
        </otherwise> 
    </choose> 
</select> 
```

- where 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，where 元素也会将它们去除。 
```xml
<select id="findActiveBlogLike" resultType="Blog"> 
    SELECT * FROM BLOG 
    <where> 
        <if test="title != null"> 
        	AND title like #{title} 
        </if> 
    </where> 
</select> 
```

- foreach 遍历容器 
```xml
<update id="batchUpdateStatus"> 
    UPDATE TEST 
    SET UPDATE_TIME = #{updateTime}, ENABLE = #{enable} 
    WHERE ID IN 
    <foreach collection="ids" item="id" index="index" 
    	open="(" close=")" separator=","> 
    	#{id} 
    </foreach> 
</update> 
```