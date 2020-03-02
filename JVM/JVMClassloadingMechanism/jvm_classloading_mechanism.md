# JVM 类加载机制（JVM Classloading Mechanism）

Java 类`class`从被加载到 JVM 虚拟机至被卸载，整个生命周期包括几个阶段：

1. 加载 (Loading)
2. 验证 (Verification)
3. 准备 (Preparation)
4. 解析 (Resolution)
5. 初始化 (Initialization)
6. 使用 (Using)
7. 卸载 (Unloading)

![JVM-ClassloadingMechanism-1-ClassloadingFlow][JVM-ClassloadingMechanism-1-ClassloadingFlow]

> 图：Java 类加载流程

# 加载阶段

在加载阶段，JVM 完成以下三件事情：

- 通过一个类的**全限定名**获取类定义的**二进制字节流**，获取类字节流的途径可以是：Class 文件、Jar 包、网络、文件自动生成。

- 将类字节流的**静态存储结构**转化为方法区**运行时数据结构**。

- 在 JVM 堆中生成一个代表这个类的`java.lang.Class`对象，作为方法区类数据的访问入口。

[JVM-ClassloadingMechanism-1-ClassloadingFlow]: ../../images/JVM-ClassloadingMechanism-1-ClassloadingFlow.png

# 验证阶段

验证是为了确保加载的`Class`字节流格式正确，符合 JVM 虚拟机的要求，且不会损害虚拟机安全。

大致有几个验证步骤：

- 文件格式验证：保证类字节流可以被正确地解析，并被存储到方法区中。

- 元数据验证：确保元数据信息符合 Java 语法规范。

- 字节码验证：对类的方法体进行校验，确保运行时不会危害到虚拟机。

- 符号引用验证：对类自身外的引用符号匹配进行校验 (如：常量池中的符号引用)。




<!-- EOF -->