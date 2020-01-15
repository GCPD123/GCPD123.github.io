---
title: springAop
date: 2020-01-05 22:26:23
tags: spring
category: java框架
---

### 转账与事务问题

![](1.png)

转账的代码中调用了4次dao层的方法，而dao层的所有方法都需要使用queryRunner而其中又需要连接数据库的connection类，所有每用到一次dao就要连接一次数据库，如果出现1/0的异常，则之前的操作都提交了事务，此时是有事务的，没有事务则数据库任何操作都不能完成，该事务是自动开启的，默认都是自动开启。异常后面的代码就不会执行了，这就是事务不一致，因为事务是基于连接的，一个连接就是一个事务，一个提交是一个事务，事务不一致就是因为每次调用dao都是一个新的连接，无法统一操作。希望达到的目的是：所有的操作都用一个事务控制，即要成功一起成功，要失败一起失败即回滚操作。要达到此目的首先需要保证所有的操作都用一个数据库连接来完成，由此可以想到的是获取到一个连接之后用容器来保存，后面就都用容器中的连接这个容器就是线程。



目前的问题：需要有一个事务控制的类，在该类中保证连接的唯一性。所以需要transction和connectionutils两个工具类

```java
@Component
public class ConnectionUtils {

    private ThreadLocal<Connection> threadLocal = new ThreadLocal<Connection>();
    @Autowired
    private DataSource dataSource;

    public Connection getConnection(){
        Connection connection = threadLocal.get();
        if(connection == null){
            try {
                connection = dataSource.getConnection();
            } catch (SQLException e) {
                throw new RuntimeException(e);
            }
            threadLocal.set(connection);
        }
        return connection;
    }
    //移除线程上到连接
    public void removeConnection(){
        threadLocal.remove();
    }

}
```

由于连接是从连接池中取出的，调用close方法只是将其放回池中，但连接还和线程绑定，所以要和线程解绑

```java
@Component
public class transctionManager {
    @Autowired
    private ConnectionUtils connectionUtils;
    /*
    开启事务,先关闭自动提交
     */
    public void beginTransction(){
        try {
            connectionUtils.getConnection().setAutoCommit(false);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    /*
    提交事务
     */
    public void commit(){
        try {
            connectionUtils.getConnection().commit();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    /*
    回滚事务
     */
    public void rollback(){
        try {
            connectionUtils.getConnection().rollback();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    /*
    释放连接
     */
    public void release(){
        try {
            connectionUtils.getConnection().close();//还回到连接池,连接还在，只是不可用了，所以要解除绑定
            connectionUtils.removeConnection();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

有了transctionmanager类就可以进行事务控制了，用在每一个与数据库打交道的方法中，增删改需要事务，查询不需要，因为它不改变数据库中的内容。

修改后的业务层代码：

```java
@Service("accountService")
public class AccountServiceImpl implements IAcountService {
    @Autowired
    private IAccountDao accountDao;
    @Autowired
    private transctionManager tx;

    public void setAccountDao(IAccountDao accountDao) {

        this.accountDao = accountDao;
    }



    public List<Account> findAllAccount() {
        try {
            //开启事务
            tx.beginTransction();
            //执行操作
            List<Account> allAccount = accountDao.findAllAccount();
            //提交事务
            tx.commit();
            //返回结果
            return allAccount;
        } catch (Exception e) {
            //回滚事务
            tx.rollback();
            throw new RuntimeException(e);
        } finally {
            //释放连接
            tx.release();
        }

       

    }

    public Account findAccountById(Integer accountId) {
        try {
            //开启事务
            tx.beginTransction();
            //执行操作
            Account accountById = accountDao.findAccountById(accountId);
            //提交事务
            tx.commit();
            //返回结果
            return accountById;
        }catch (Exception e){
            //异常后回滚
            tx.rollback();
            throw new RuntimeException();
        }finally {
            //释放连接
            tx.release();
        }

       




    }

    public void saveAccount(Account account) {
        try {
            //开启事务
            tx.beginTransction();
            //执行操作
            accountDao.saveAccount(account);
            //提交事务
            tx.commit();
            //返回结果

        }catch (Exception e){
            //异常后回滚
            tx.rollback();
            throw new RuntimeException();
        }finally {
            //释放连接
            tx.release();
        }

        

    }

    public void updateAccount(Account account) {
        try {
            //开启事务
            tx.beginTransction();
            //执行操作
            accountDao.updateAccount(account);
            //提交事务
            tx.commit();
            //返回结果

        }catch (Exception e){
            //异常后回滚
            tx.rollback();
            throw new RuntimeException();
        }finally {
            //释放连接
            tx.release();
        }

        

    }

    public void deleteAccount(Integer accountId) {
        try {
            //开启事务
            tx.beginTransction();
            //执行操作
            accountDao.deleteAccount(accountId);
            //提交事务
            tx.commit();
            //返回结果

        }catch (Exception e){
            //异常后回滚
            tx.rollback();
            throw new RuntimeException();
        }finally {
            //释放连接
            tx.release();
        }

        

    }
    /**
     * 一个dao就是一个事务，因为有连接操作数据库，所以就有事务，默认情况下是自动提交的事务
     * 现在的情况是每个dao都有一个单独的连接，所以是独立分开的事务，为了控制事务，所以要统一连接
     * @param sourceName
     * @param targetName
     * @param money
     */
    public void transfer(String sourceName, String targetName, Float money) {
        try {
            //开启事务
            tx.beginTransction();
            //获取转出账户
            Account sn = accountDao.getAccountByName(sourceName);
            //获取转入账户
            Account tn = accountDao.getAccountByName(targetName);
            //转出账户减钱
            sn.setMoney(sn.getMoney() - money);
            //转入用户加钱

            tn.setMoney(tn.getMoney() + money);
            //更新转出账户
            accountDao.updateAccount(sn);
              int i = 1/0;
            //更新转入账户
            accountDao.updateAccount(tn);
            //提交事务
            tx.commit();
            //返回结果

        }catch (Exception e){
            //异常后回滚
            tx.rollback();
            throw new RuntimeException();
        }finally {
            //释放连接
            tx.release();
        }

           

    }
}
```

此时业务层的所有方法都有了事务的控制，即要么同时成功，要么同时失败，这时由唯一的连接决定的。但是如此一来每次新增一个方法就要重复写上这些相同的代码，所有为了解决代码的重复性，就有了AOP，面向切面！

### 代理

使用代理可以解决重复的代码，代理的意义在于A代理B,则当B的方法执行时，会先执行A中增强的方法。实际上此时代理对象和被代理对象都是实现了同一个约束，所以此时直接用代理对象执行方法就是增强的方法，所以有没有增强还是看对象是代理还是非代理。

#### 常用动态代理分为两类

1. 基于借口的动态代理，使用JDK官方的Proxy类，**要求被代理者至少实现一个接口**
2. 基于子类的动态代理，使用第三方的CGLib库，**要求被代理类不能是final类**

使用基于接口的动态代理：

```java
接口名 新对象名 = (接口名)Proxy.newProxyInstance(
    被代理的对象.getClass().getClassLoader(),	// 被代理对象的类加载器,固定写法
    被代理的对象.getClass().getInterfaces(),	// 被代理对象实现的所有接口,固定写法
    new InvocationHandler() {	// 匿名内部类,通过拦截被代理对象的方法来增强被代理对象
        /* 被代理对象的任何方法执行时,都会被此方法拦截到
        	其参数如下:
                proxy: 代理对象的引用,不一定每次都用得到
                method: 被拦截到的方法对象
                args: 被拦截到的方法对象的参数
        	返回值:
        		被增强后的返回值
		*/
        @Override
        public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
            if("方法名".equals(method.getName())) {
            	// 增强方法的操作
                rtValue = method.invoke(被代理的对象, args);
                // 增强方法的操作
                return rtValue;
            }          
        }
    });


```

使用工厂产生代理对象，然后使用增强后的方法：

```java
package com.itheima.factory;

import com.itheima.service.IAcountService;
import com.itheima.service.impl.AccountServiceImpl;
import com.itheima.utils.transctionManager;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @author Xue
 * @date 2020/1/2 - 8:13 下午
 * 使用工厂产生代理对象
 */
@Component
public class BeanFactory {
    @Resource
    private  IAcountService accountService;
    @Autowired
    private transctionManager tx;

    /**
     * 获取代理对象
     * @return
     */
    @Bean(name = "proxyService")
    public IAcountService getAccountServiceProxy(){
        return (IAcountService) Proxy.newProxyInstance(AccountServiceImpl.class.getClassLoader(), AccountServiceImpl.class.getInterfaces()
                , new InvocationHandler() {

                    /**
                     * 添加事务的支持
                     * @param proxy 代理对象本身也就是proxyService
                     * @param method 执行的方法，此处是用了反射，将被代理对象中的方法提取出来
                     * @param args 执行方法时候的参数
                     * @return
                     * @throws Throwable
                     */
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        Object invoke = null;
                        try {
                            //开启事务
                            tx.beginTransction();
                            //执行操作
                            System.out.println("代理对象方法运行了");
                             invoke = method.invoke(accountService, args);
                            //提交事务
                            tx.commit();
                            //返回结果
                            return invoke;
                        } catch (Exception e) {
                            //回滚事务
                            tx.rollback();
                            throw new RuntimeException(e);
                        } finally {
                            //释放连接
                            tx.release();
                        }

                    }
                });
    }

}

```

getAccountServiceProxy()方法就是为了获取代理后的对象，顺便在产生的过程中把如何加强被代理对象的方法也确定了，即先执行什么再干什么。其中 invoke = method.invoke(accountService, args);**表示直接执行拦截到的原方法，包括拦截到的方法参数一起执行**，此时要知名执行的对象，因为一个类会有多个对象，各个状态不一样（成员变量）此时的method类是通过反射创建Class对象中成员变量method所以自带invoke方法

测试工厂类产生代理对象：

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfiguar.class)
public class BeanFactoryTest {

    @Autowired
    private BeanFactory factory;
    @Test
    public void getAccountServiceProxy() {

        factory.getAccountServiceProxy().findAllAccount();
    }
}
```

以后用代理对象执行即可完成方法的加强，在业务层中只需要业务代码而不需要手动增加事务控制了！

### Spring 中的AOP

####相关术语

- `Joinpoint`(`连接点`): 被拦截到的方法.

- `Pointcut`(`切入点`): 我们对其进行增强的方法.

- `Advice`(`通知`/`增强`): 对切入点进行的增强操作

  包括`前置通知`,`后置通知`,`异常通知`,`最终通知`,`环绕通知`

- `Weaving`(`织入`): 是指把增强应用到目标对象来创建新的代理对象的过程。

- `Aspect`(`切面`): 是切入点和通知的结合

#### 使用步骤

1. 引入约束

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd">
	
    <!--通知类-->
	<bean id="logger" class="cn.maoritian.utils.Logger"></bean>
</beans>


```

2. 使用``标签声明AOP配置,所有关于AOP配置的代码都写在``标签内

```java
<aop:config>
	<!-- AOP配置的代码都写在此处 -->
</aop:config>


```

3. 使用<aop:aspect>标签配置切面,其属性如下

```java
<aop:config>
	<aop:aspect id="logAdvice" ref="logger">
    	<!--配置通知的类型要写在此处-->
    </aop:aspect>
</aop:config>


```

4. 使用<aop:pointcut>标签配置切入点表达式,指定对哪些方法进行增强,其属性如下
   - `id`: 指定切入点表达式的`id`
   - `expression`: 指定切入点表达式