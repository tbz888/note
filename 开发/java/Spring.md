问题

```
class Dao ｛
	private mySQLSession;
	Dao() {
        mySQLSession = new mySQLSession(); //如何解耦？成百上千的依赖关系能否集中管理？
	}
｝
```

**Inversion of Control / Dependency Injection**

- 对象间的依赖关系，由IoC容器集中管理被依赖对象的生命周期。
- 控制被反转之后，获得依赖对象的过程由自身管理变为由IoC容器动态注入。 

**Dependency inversion principle** 

- 上层模块不应该依赖底层模块，它们都应该依赖于抽象。
- 抽象不应该依赖于细节，细节应该依赖于抽象。

**implementation:**

- constructor
- setter
- 反射(XML/Annotation/Java Config)