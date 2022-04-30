## 给容器中注入组件的集几种方式

### 一、使用包扫描+组件注解

1. 开启包扫描 @ComponentScan/@ComponentScans
2. 使用组件注解：@Component/@Service/@Repository/@Controller

### 二、使用@Bean注解
方便用于导入第三方包内组件

### 三、使用@Import

#### 方式一：直接注入类

#### 方式二：使用ImportSelector

#### 方式三：使用ImportBeanDefinitionRigistrar

## 四、实现Spring的FactoryBean接口