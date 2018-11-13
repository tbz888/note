## 基本流程

1. 初始化配置SqlSessionFactory：jdbc驱动、连接字符串等// 只初始化一次，进程作用域。

2. DaoFactory -> interface Dao -> DaoImpl

3. session = SqlSessionFactory.openSession() // 非线程安全，不能共享，确保被关闭。

4. mapper = session.getMapper(Mapper.class) -> interface Mapper -> Mapper.xml

5. Model(POJO) -> mapper.CRUD(params) 

6. session.commit()/rollback()

7. session.close()

## Mapper.xml

```mybatis
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

## sql



## 参数(Parameters)

- #{}生成PreparedStatement的安全参数（相当于jdbc的?）
- ${}只是替换不转义的字符串，可能有sql注入