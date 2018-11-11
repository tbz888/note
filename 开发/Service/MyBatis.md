## 基本流程

1. 初始化配置SqlSessionFactory：jdbc驱动、连接字符串等
2. DaoFactory -> interface Dao -> DaoImpl
3. session = SqlSessionFactory.openSession()
4. mapper = session.getMapper(Mapper.class) -> interface Mapper -> Mapper.xml
5. Model(POJO) -> mapper.CRUD(params) 
6. session.commit()/rollback()