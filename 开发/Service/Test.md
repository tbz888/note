> 基本思路：像Online Judge有预期结果判定

## Unit Test
- 对功能点的自动化假设检验
- UT > Service > API > UI

## Integration Test
- 整合所有真实依赖后，对完整业务流程的假设检验

### 工具
- 每个Java Class附带一个public static void main(String[] args)作为UT入口
- JUnit

### FAQ
Q:如何测试private的变量和函数？
A: Testing private methods may be an indication that those methods should be moved into another class to promote reusability.  But if you must...  If you are using JDK 1.3 or higher, you can use reflection to subvert the access control mechanism with the aid of the PrivilegedAccessor.
  
