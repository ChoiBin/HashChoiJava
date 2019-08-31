## 1、MyBatis定义的接口，怎么找到实现类的

- Mapper接口在初始化SqlSessionFactory注册
- Mapper接口注册在名为MapperRegistry类的HashMap中，key值为Mapper的类型，value为创建当前Mapper的工厂
- Mapper注册后，可以从sqlsession中get
- SqlSeesion.getMapper运用了JDK动态代理，产生了目标Mapper接口的代理对象
- 动态代理的代理类是MapperProxy，在这个代理类中最终完成了增删改查的调用

