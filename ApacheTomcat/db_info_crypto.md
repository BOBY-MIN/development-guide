
> jar 처리방식

```
-- server.xml
<Resource
        auth="Container"
        type="javax.sql.DataSource"
        username="user"
        password="password"
        driverClassName="com.mysql.cj.jdbc.Driver"
        factory="org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory"
        url="jdbc:mysql://ip:port/dbName?autoReconnect=true&amp;serverTimezone=UTC"
        maxTotal="500"
        maxIdle="50"
        minIdle="50"
        initialSize="50"
        maxWaitMillis="5000"
        validationQuery="SELECT 1"
        testOnBorrow="true"
        testWhileIdle="true"
        testOnReturn="false"
        minEvictableIdleTimeMillis="30000"
        numTestsPerEvictionRun="10"
        timeBetweenEvictionRunsMillis="30000"
        removeAbandonedOnBorrow="true"
        removeAbandonedOnMaintenance="true"
        removeAbandonedTimeout="60"
        logAbandoned="true"
        poolPreparedStatements="true"
        defaultTransactionIsolation="READ_COMMITTED"
        name="jdbc/jndiname"
        />
```

* 태그 내 factory="org.apache.tomcat.dbcp.dbcp2.BasicDataSourceFactory" 상속 받아 재구성
* 암호화 필요한 항목들 암호화 처리하여 server.xml에 기재
  <br> ex) username="암호화값"

```
package pakage;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.Enumeration;
import java.util.Hashtable;
import java.util.LinkedHashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;

import javax.naming.Context;
import javax.naming.Name;
import javax.naming.RefAddr;
import javax.naming.Reference;

import org.apache.juli.logging.Log;
import org.apache.juli.logging.LogFactory;
import org.apache.tomcat.dbcp.pool2.impl.BaseObjectPoolConfig;
import org.apache.tomcat.dbcp.pool2.impl.GenericObjectPoolConfig;


public class CryptoDataSourceFactory extends BasicDataSourceFactory{

	private static final Log log = LogFactory.getLog(BasicDataSourceFactory.class);

	private static final String PROP_DEFAULT_AUTO_COMMIT = "defaultAutoCommit";
    private static final String PROP_DEFAULT_READ_ONLY = "defaultReadOnly";
    private static final String PROP_DEFAULT_TRANSACTION_ISOLATION = "defaultTransactionIsolation";
    private static final String PROP_DEFAULT_CATALOG = "defaultCatalog";
    private static final String PROP_DEFAULT_SCHEMA = "defaultSchema";
    private static final String PROP_CACHE_STATE = "cacheState";
    private static final String PROP_DRIVER_CLASS_NAME = "driverClassName";
    private static final String PROP_LIFO = "lifo";
    private static final String PROP_MAX_TOTAL = "maxTotal";
    private static final String PROP_MAX_IDLE = "maxIdle";
    private static final String PROP_MIN_IDLE = "minIdle";
    private static final String PROP_INITIAL_SIZE = "initialSize";
    private static final String PROP_MAX_WAIT_MILLIS = "maxWaitMillis";
    private static final String PROP_TEST_ON_CREATE = "testOnCreate";
    private static final String PROP_TEST_ON_BORROW = "testOnBorrow";
    private static final String PROP_TEST_ON_RETURN = "testOnReturn";
    private static final String PROP_TIME_BETWEEN_EVICTION_RUNS_MILLIS = "timeBetweenEvictionRunsMillis";
    private static final String PROP_NUM_TESTS_PER_EVICTION_RUN = "numTestsPerEvictionRun";
    private static final String PROP_MIN_EVICTABLE_IDLE_TIME_MILLIS = "minEvictableIdleTimeMillis";
    private static final String PROP_SOFT_MIN_EVICTABLE_IDLE_TIME_MILLIS = "softMinEvictableIdleTimeMillis";
    private static final String PROP_EVICTION_POLICY_CLASS_NAME = "evictionPolicyClassName";
    private static final String PROP_TEST_WHILE_IDLE = "testWhileIdle";
    private static final String PROP_PASSWORD = "password";
    private static final String PROP_URL = "url";
    private static final String PROP_USER_NAME = "username";
    private static final String PROP_VALIDATION_QUERY = "validationQuery";
    private static final String PROP_VALIDATION_QUERY_TIMEOUT = "validationQueryTimeout";
    private static final String PROP_JMX_NAME = "jmxName";
    private static final String PROP_CONNECTION_FACTORY_CLASS_NAME = "connectionFactoryClassName";

    /**
     * The property name for connectionInitSqls. The associated value String must be of the form [query;]*
     */
    private static final String PROP_CONNECTION_INIT_SQLS = "connectionInitSqls";
    private static final String PROP_ACCESS_TO_UNDERLYING_CONNECTION_ALLOWED = "accessToUnderlyingConnectionAllowed";
    private static final String PROP_REMOVE_ABANDONED_ON_BORROW = "removeAbandonedOnBorrow";
    private static final String PROP_REMOVE_ABANDONED_ON_MAINTENANCE = "removeAbandonedOnMaintenance";
    private static final String PROP_REMOVE_ABANDONED_TIMEOUT = "removeAbandonedTimeout";
    private static final String PROP_LOG_ABANDONED = "logAbandoned";
    private static final String PROP_ABANDONED_USAGE_TRACKING = "abandonedUsageTracking";
    private static final String PROP_POOL_PREPARED_STATEMENTS = "poolPreparedStatements";
    private static final String PROP_CLEAR_STATEMENT_POOL_ON_RETURN = "clearStatementPoolOnReturn";
    private static final String PROP_MAX_OPEN_PREPARED_STATEMENTS = "maxOpenPreparedStatements";
    private static final String PROP_CONNECTION_PROPERTIES = "connectionProperties";
    private static final String PROP_MAX_CONN_LIFETIME_MILLIS = "maxConnLifetimeMillis";
    private static final String PROP_LOG_EXPIRED_CONNECTIONS = "logExpiredConnections";
    private static final String PROP_ROLLBACK_ON_RETURN = "rollbackOnReturn";
    private static final String PROP_ENABLE_AUTO_COMMIT_ON_RETURN = "enableAutoCommitOnReturn";
    private static final String PROP_DEFAULT_QUERY_TIMEOUT = "defaultQueryTimeout";
    private static final String PROP_FAST_FAIL_VALIDATION = "fastFailValidation";

    /**
     * Value string must be of the form [STATE_CODE,]*
     */
    private static final String PROP_DISCONNECTION_SQL_CODES = "disconnectionSqlCodes";

    /*
     * Block with obsolete properties from DBCP 1.x. Warn users that these are ignored and they should use the 2.x
     * properties.
     */
    private static final String NUPROP_MAX_ACTIVE = "maxActive";
    private static final String NUPROP_REMOVE_ABANDONED = "removeAbandoned";
    private static final String NUPROP_MAXWAIT = "maxWait";

    /*
     * Block with properties expected in a DataSource This props will not be listed as ignored - we know that they may
     * appear in Resource, and not listing them as ignored.
     */
    private static final String SILENT_PROP_FACTORY = "factory";
    private static final String SILENT_PROP_SCOPE = "scope";
    private static final String SILENT_PROP_SINGLETON = "singleton";
    private static final String SILENT_PROP_AUTH = "auth";

    private static final String[] ALL_PROPERTIES = {PROP_DEFAULT_AUTO_COMMIT, PROP_DEFAULT_READ_ONLY,
            PROP_DEFAULT_TRANSACTION_ISOLATION, PROP_DEFAULT_CATALOG, PROP_DEFAULT_SCHEMA, PROP_CACHE_STATE,
            PROP_DRIVER_CLASS_NAME, PROP_LIFO, PROP_MAX_TOTAL, PROP_MAX_IDLE, PROP_MIN_IDLE, PROP_INITIAL_SIZE,
            PROP_MAX_WAIT_MILLIS, PROP_TEST_ON_CREATE, PROP_TEST_ON_BORROW, PROP_TEST_ON_RETURN,
            PROP_TIME_BETWEEN_EVICTION_RUNS_MILLIS, PROP_NUM_TESTS_PER_EVICTION_RUN, PROP_MIN_EVICTABLE_IDLE_TIME_MILLIS,
            PROP_SOFT_MIN_EVICTABLE_IDLE_TIME_MILLIS, PROP_EVICTION_POLICY_CLASS_NAME, PROP_TEST_WHILE_IDLE, PROP_PASSWORD,
            PROP_URL, PROP_USER_NAME, PROP_VALIDATION_QUERY, PROP_VALIDATION_QUERY_TIMEOUT, PROP_CONNECTION_INIT_SQLS,
            PROP_ACCESS_TO_UNDERLYING_CONNECTION_ALLOWED, PROP_REMOVE_ABANDONED_ON_BORROW, PROP_REMOVE_ABANDONED_ON_MAINTENANCE,
            PROP_REMOVE_ABANDONED_TIMEOUT, PROP_LOG_ABANDONED, PROP_ABANDONED_USAGE_TRACKING, PROP_POOL_PREPARED_STATEMENTS,
            PROP_CLEAR_STATEMENT_POOL_ON_RETURN,
            PROP_MAX_OPEN_PREPARED_STATEMENTS, PROP_CONNECTION_PROPERTIES, PROP_MAX_CONN_LIFETIME_MILLIS,
            PROP_LOG_EXPIRED_CONNECTIONS, PROP_ROLLBACK_ON_RETURN, PROP_ENABLE_AUTO_COMMIT_ON_RETURN,
            PROP_DEFAULT_QUERY_TIMEOUT, PROP_FAST_FAIL_VALIDATION, PROP_DISCONNECTION_SQL_CODES, PROP_JMX_NAME,
            PROP_CONNECTION_FACTORY_CLASS_NAME };

    /**
     * Obsolete properties from DBCP 1.x. with warning strings suggesting new properties. LinkedHashMap will guarantee
     * that properties will be listed to output in order of insertion into map.
     */
    private static final Map<String, String> NUPROP_WARNTEXT = new LinkedHashMap<>();

    static {
        NUPROP_WARNTEXT.put(NUPROP_MAX_ACTIVE,
                "Property " + NUPROP_MAX_ACTIVE + " is not used in DBCP2, use " + PROP_MAX_TOTAL + " instead. "
                        + PROP_MAX_TOTAL + " default value is " + GenericObjectPoolConfig.DEFAULT_MAX_TOTAL + ".");
        NUPROP_WARNTEXT.put(NUPROP_REMOVE_ABANDONED,
                "Property " + NUPROP_REMOVE_ABANDONED + " is not used in DBCP2," + " use one or both of "
                        + PROP_REMOVE_ABANDONED_ON_BORROW + " or " + PROP_REMOVE_ABANDONED_ON_MAINTENANCE + " instead. "
                        + "Both have default value set to false.");
        NUPROP_WARNTEXT.put(NUPROP_MAXWAIT,
                "Property " + NUPROP_MAXWAIT + " is not used in DBCP2" + " , use " + PROP_MAX_WAIT_MILLIS + " instead. "
                        + PROP_MAX_WAIT_MILLIS + " default value is " + BaseObjectPoolConfig.DEFAULT_MAX_WAIT_MILLIS
                        + ".");
    }

    /**
     * Silent Properties. These properties will not be listed as ignored - we know that they may appear in JDBC Resource
     * references, and we will not list them as ignored.
     */
    private static final List<String> SILENT_PROPERTIES = new ArrayList<>();

    static {
        SILENT_PROPERTIES.add(SILENT_PROP_FACTORY);
        SILENT_PROPERTIES.add(SILENT_PROP_SCOPE);
        SILENT_PROPERTIES.add(SILENT_PROP_SINGLETON);
        SILENT_PROPERTIES.add(SILENT_PROP_AUTH);

    }

	@Override
	public Object getObjectInstance(Object obj, Name name, Context nameCtx, Hashtable<?, ?> environment)
			throws Exception {

		// We only know how to deal with <code>javax.naming.Reference</code>s
        // that specify a class name of "javax.sql.DataSource"
        if (obj == null || !(obj instanceof Reference)) {
            return null;
        }
        final Reference ref = (Reference) obj;
        if (!"javax.sql.DataSource".equals(ref.getClassName())) {
            return null;
        }

        // Check property names and log warnings about obsolete and / or unknown properties
        final List<String> warnings = new ArrayList<>();
        final List<String> infoMessages = new ArrayList<>();
        validatePropertyNames(ref, name, warnings, infoMessages);
        for (final String warning : warnings) {
            log.warn(warning);
        }
        for (final String infoMessage : infoMessages) {
            log.info(infoMessage);
        }

        final Properties properties = new Properties();
        for (final String propertyName : ALL_PROPERTIES) {
            final RefAddr ra = ref.get(propertyName);
            if (ra != null) {
                final String propertyValue = ra.getContent().toString();

                // 암호화 되어있는 항목들에 대해서 복호화 함수 적용
                // 암복호화 모듈은 별도로 구현해야 함.
                if(propertyName.equals(PROP_USER_NAME) || propertyName.equals(PROP_PASSWORD)) {
                	properties.setProperty(propertyName, 복호화 함수(propertyValue));
                }else {
                	properties.setProperty(propertyName, propertyValue);
                }
            }
        }

        return super.createDataSource(properties);
	}

	/**
     * Collects warnings and info messages. Warnings are generated when an obsolete property is set. Unknown properties
     * generate info messages.
     *
     * @param ref
     *            Reference to check properties of
     * @param name
     *            Name provided to getObject
     * @param warnings
     *            container for warning messages
     * @param infoMessages
     *            container for info messages
     */
    private void validatePropertyNames(final Reference ref, final Name name, final List<String> warnings,
            final List<String> infoMessages) {
        final List<String> allPropsAsList = Arrays.asList(ALL_PROPERTIES);
        final String nameString = name != null ? "Name = " + name.toString() + " " : "";
        if (NUPROP_WARNTEXT != null && !NUPROP_WARNTEXT.isEmpty()) {
            for (final String propertyName : NUPROP_WARNTEXT.keySet()) {
                final RefAddr ra = ref.get(propertyName);
                if (ra != null && !allPropsAsList.contains(ra.getType())) {
                    final StringBuilder stringBuilder = new StringBuilder(nameString);
                    final String propertyValue = ra.getContent().toString();
                    stringBuilder.append(NUPROP_WARNTEXT.get(propertyName)).append(" You have set value of \"")
                            .append(propertyValue).append("\" for \"").append(propertyName)
                            .append("\" property, which is being ignored.");
                    warnings.add(stringBuilder.toString());
                }
            }
        }

        final Enumeration<RefAddr> allRefAddrs = ref.getAll();
        while (allRefAddrs.hasMoreElements()) {
            final RefAddr ra = allRefAddrs.nextElement();
            final String propertyName = ra.getType();
            // If property name is not in the properties list, we haven't warned on it
            // and it is not in the "silent" list, tell user we are ignoring it.
            if (!(allPropsAsList.contains(propertyName) || NUPROP_WARNTEXT.containsKey(propertyName)
                    || SILENT_PROPERTIES.contains(propertyName))) {
                final String propertyValue = ra.getContent().toString();
                final StringBuilder stringBuilder = new StringBuilder(nameString);
                stringBuilder.append("Ignoring unknown property: ").append("value of \"").append(propertyValue)
                        .append("\" for \"").append(propertyName).append("\" property");
                infoMessages.add(stringBuilder.toString());
            }
        }
    }

}

```

* 위 클래스 정의한 프로젝트 jar로 구성하여 $CATALINA_HOME/lib 에 추가
