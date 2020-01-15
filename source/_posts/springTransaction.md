---
title: springTransaction
date: 2020-01-08 21:27:35
tags: spring
category: java框架
---

### spring中的事务

spring中的事务都是基于AOP的，spring为我们提供了一组事务的接口，在spring-tx包中，注意是接口定义，其中一个接口是：PlatformTransactionManager，其中一个实现类DataSourceTransactionManager在spring-jdbc包中，所以使用的时候要导入着两个包。

> 注意：spring的事务管理只对spring-jdbc包中的JdbcTemplate有用，对QueryRunner都没用

### 配置步骤

1. 在bean.xml中导入tx的约束
2. 配置事务管理器也就是DataSourceTransactionManager类，将生成实例放入spring容器中,在配置类中配置

```java
public class TransctionConfiguration {

    @Bean(name = "transactionManager")
    public PlatformTransactionManager createTransctionManager(DataSource dataSource){
           return new DataSourceTransactionManager(dataSource);
    }
}
```

其中数据源

```java
package com.itheima.configure;

import com.mchange.v2.c3p0.ComboPooledDataSource;
import com.mchange.v2.c3p0.DataSources;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;

import javax.sql.DataSource;
import java.beans.PropertyVetoException;

/**
 * @author Xue
 * @date 2020/1/7 - 8:46 下午
 */

public class JdbcConfiguration {
    @Value("${username}")
    private String username;
    @Value("${password}")
    private String password;
    @Value("${url}")
    private String url;
    @Value("${driver}")
    private String driver;



    @Bean(name = "dataSource")
    public DataSource createDataSource(){
        try {

        ComboPooledDataSource dataSource = new ComboPooledDataSource();
           dataSource.setDriverClass(driver);
            dataSource.setUser(username);
            dataSource.setPassword(password);
            dataSource.setJdbcUrl(url);

            return dataSource;
        }catch (Exception e){
            throw new  RuntimeException(e);
        }


    }
    @Bean("jdbcTemplate")
    public JdbcTemplate createJdbcTemplate(DataSource dataSource){
        return new JdbcTemplate(dataSource);

    }

}

```

注意：DataSoureTransactionManager和JdbcTemplate都需要数据源也就是连接池！但是都是spring处理的，所以可以保证都是使用的相同的连接，只有这样才会有事务的控制，所以也就解释了为什么queryRunner无法被这个事务管理器控制，因为连接不相同。

3. 在主配置文件中声明事务注解开启，支持事务的注解配置

```java
package com.itheima.configure;

import org.springframework.context.annotation.*;
import org.springframework.transaction.annotation.EnableTransactionManagement;

/**
 * @author Xue
 * @date 2020/1/7 - 8:45 下午
 */
@Configuration
@ComponentScan("com.itheima")
@Import({JdbcConfiguration.class,TransctionConfiguration.class})
@PropertySource("jdbc.properties")
@EnableTransactionManagement
public class SpringConfiguration {


}

```

4. 可以在想要使用的方法上使用注解来开启事务控制了

```java
package com.itheima.service.impl;

import com.itheima.dao.IAccountDao;
import com.itheima.domain.Account;
import com.itheima.service.IAccountService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

/**
 * @author Xue
 * @date 2020/1/7 - 9:01 下午
 */
@Service("accountService")
public class AccountService implements IAccountService {
    @Autowired
    @Qualifier("accountDao1")
    private IAccountDao accountDao;

    @Transactional
    public List<Account> findAllAccount() {
        return accountDao.findAllAccount();

    }

    @Transactional
    public Account findAccountById(Integer accountId) {
        return accountDao.findAccountById(accountId);
    }

    @Transactional
    public void saveAccount(Account account) {

        accountDao.saveAccount(account);
    }

    @Transactional
    public void updateAccount(Account account) {
        accountDao.updateAccount(account);
    }

    @Transactional
    public void deleteAccount(Integer accountId) {

        accountDao.deleteAccount(accountId);
    }

    @Transactional
    public void transfer(String sourceName, String targetName, Float money) {

        Account source = accountDao.getAccountByName(sourceName);
        Account target = accountDao.getAccountByName(targetName);
        source.setMoney(source.getMoney()-money);
        target.setMoney(target.getMoney()+money);
        accountDao.updateAccount(source);
        int i = 1/0;
        accountDao.updateAccount(target);
    }
}

```

### 事务的xml配置

```java
<!-- 事务管理器 -->
	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<!-- 数据源 -->
		<property name="dataSource" ref="dataSource" />
	</bean>
	<!-- 通知 -->
	<tx:advice id="txAdvice" transaction-manager="transactionManager">
		<tx:attributes>
			<!-- 传播行为 -->
			<tx:method name="save*" propagation="REQUIRED" />
			<tx:method name="insert*" propagation="REQUIRED" />
			<tx:method name="add*" propagation="REQUIRED" />
			<tx:method name="create*" propagation="REQUIRED" />
			<tx:method name="delete*" propagation="REQUIRED" />
			<tx:method name="update*" propagation="REQUIRED" />
			<tx:method name="find*" propagation="SUPPORTS" read-only="true" />
			<tx:method name="select*" propagation="SUPPORTS" read-only="true" />
			<tx:method name="get*" propagation="SUPPORTS" read-only="true" />
		</tx:attributes>
	</tx:advice>
	<!-- 切面 -->
	<aop:config>
		<aop:advisor advice-ref="txAdvice"
			pointcut="execution(* com.taotao.service.*.*(..))" />
	</aop:config>
```

