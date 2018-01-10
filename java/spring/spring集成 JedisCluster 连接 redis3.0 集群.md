# spring集成 JedisCluster 连接 redis3.0 集群


## 客户端

采用最新的jedis 2.8

### 1.pom文件配置

maven依赖：

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.8</version>
</dependency>
```


### 2.增加spring 配置

	<bean name="genericObjectPoolConfig" class="org.apache.commons.pool2.impl.GenericObjectPoolConfig" >
			<property name="maxWaitMillis" value="-1" />
			<property name="maxTotal" value="1000" />
			<property name="minIdle" value="8" />
			<property name="maxIdle" value="100" />
	</bean>

	<bean id="jedisCluster" class="xxx.JedisClusterFactory">
		<property name="addressConfig">
			<value>classpath:connect-redis.properties</value>
		</property>
		<property name="addressKeyPrefix" value="address" />   <!--  属性文件里  key的前缀 -->
		
		<property name="timeout" value="300000" />
		<property name="maxRedirections" value="6" />
		<property name="genericObjectPoolConfig" ref="genericObjectPoolConfig" />
	</bean>
	


### 3.增加connect-redis.properties配置文件

这里配置了6个节点

```
address1=172.16.23.27:6379
address2=172.16.23.27:6380
address3=172.16.23.27:6381
address4=172.16.23.27:6382
address5=172.16.23.27:6383
address6=172.16.23.27:6384
```


### 4. 增加java类

```java
import java.util.HashSet;
import java.util.Properties;
import java.util.Set;
import java.util.regex.Pattern;

import org.apache.commons.pool2.impl.GenericObjectPoolConfig;
import org.springframework.beans.factory.FactoryBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.core.io.Resource;

import redis.clients.jedis.HostAndPort;
import redis.clients.jedis.JedisCluster;

public class JedisClusterFactory implements FactoryBean<JedisCluster>, InitializingBean {

	private Resource addressConfig;
	private String addressKeyPrefix ;

	private JedisCluster jedisCluster;
	private Integer timeout;
	private Integer maxRedirections;
	private GenericObjectPoolConfig genericObjectPoolConfig;
	
	private Pattern p = Pattern.compile("^.+[:]\\d{1,5}\\s*$");

	@Override
	public JedisCluster getObject() throws Exception {
		return jedisCluster;
	}

	@Override
	public Class<? extends JedisCluster> getObjectType() {
		return (this.jedisCluster != null ? this.jedisCluster.getClass() : JedisCluster.class);
	}

	@Override
	public boolean isSingleton() {
		return true;
	}



	private Set<HostAndPort> parseHostAndPort() throws Exception {
		try {
			Properties prop = new Properties();
			prop.load(this.addressConfig.getInputStream());

			Set<HostAndPort> haps = new HashSet<HostAndPort>();
			for (Object key : prop.keySet()) {

				if (!((String) key).startsWith(addressKeyPrefix)) {
					continue;
				}

				String val = (String) prop.get(key);

				boolean isIpPort = p.matcher(val).matches();

				if (!isIpPort) {
					throw new IllegalArgumentException("ip 或 port 不合法");
				}
				String[] ipAndPort = val.split(":");

				HostAndPort hap = new HostAndPort(ipAndPort[0], Integer.parseInt(ipAndPort[1]));
				haps.add(hap);
			}

			return haps;
		} catch (IllegalArgumentException ex) {
			throw ex;
		} catch (Exception ex) {
			throw new Exception("解析 jedis 配置文件失败", ex);
		}
	}
	
	@Override
	public void afterPropertiesSet() throws Exception {
		Set<HostAndPort> haps = this.parseHostAndPort();
		
		jedisCluster = new JedisCluster(haps, timeout, maxRedirections,genericObjectPoolConfig);
		
	}
	public void setAddressConfig(Resource addressConfig) {
		this.addressConfig = addressConfig;
	}

	public void setTimeout(int timeout) {
		this.timeout = timeout;
	}

	public void setMaxRedirections(int maxRedirections) {
		this.maxRedirections = maxRedirections;
	}

	public void setAddressKeyPrefix(String addressKeyPrefix) {
		this.addressKeyPrefix = addressKeyPrefix;
	}

	public void setGenericObjectPoolConfig(GenericObjectPoolConfig genericObjectPoolConfig) {
		this.genericObjectPoolConfig = genericObjectPoolConfig;
	}

}
```



5.使用时，直接注入即可， 如下所示：

```java
@Autowired
JedisCluster jedisCluster;
```