## Problem description

When upgrade `byte-buddy` to 1.11.4 above, `hibernate` run in the war on tomcat
with security manager startup failed for `java.security.AccessControlException`.
Even grant `java.security.AllPermission` to all in the war.

<details>
  <summary>Stacktrace</summary>

```
Caused by: org.hibernate.MappingException: Could not get constructor for org.hibernate.persister.entity.SingleTableEntityPersister
	at org.hibernate.persister.internal.PersisterFactoryImpl.createEntityPersister(PersisterFactoryImpl.java:123)
	at org.hibernate.persister.internal.PersisterFactoryImpl.createEntityPersister(PersisterFactoryImpl.java:77)
	at org.hibernate.metamodel.internal.MetamodelImpl.initialize(MetamodelImpl.java:181)
	at org.hibernate.internal.SessionFactoryImpl.<init>(SessionFactoryImpl.java:312)
	at org.hibernate.boot.internal.SessionFactoryBuilderImpl.build(SessionFactoryBuilderImpl.java:471)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.build(EntityManagerFactoryBuilderImpl.java:1410)
	at org.springframework.orm.jpa.vendor.SpringHibernateJpaPersistenceProvider.createContainerEntityManagerFactory(SpringHibernateJpaPersistenceProvider.java:58)
	at org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean.createNativeEntityManagerFactory(LocalContainerEntityManagerFactoryBean.java:365)
	at org.springframework.orm.jpa.AbstractEntityManagerFactoryBean.buildNativeEntityManagerFactory(AbstractEntityManagerFactoryBean.java:409)
	... 39 more
Caused by: org.hibernate.HibernateException: Unable to instantiate default tuplizer [org.hibernate.tuple.entity.PojoEntityTuplizer]
	at org.hibernate.tuple.entity.EntityTuplizerFactory.constructTuplizer(EntityTuplizerFactory.java:91)
	at org.hibernate.tuple.entity.EntityTuplizerFactory.constructDefaultTuplizer(EntityTuplizerFactory.java:116)
	at org.hibernate.tuple.entity.EntityMetamodel.<init>(EntityMetamodel.java:432)
	at org.hibernate.persister.entity.AbstractEntityPersister.<init>(AbstractEntityPersister.java:612)
	at org.hibernate.persister.entity.SingleTableEntityPersister.<init>(SingleTableEntityPersister.java:126)
	at java.base/jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at java.base/jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.base/java.lang.reflect.Constructor.newInstance(Constructor.java:490)
	at org.hibernate.persister.internal.PersisterFactoryImpl.createEntityPersister(PersisterFactoryImpl.java:96)
	... 47 more
Caused by: java.lang.reflect.InvocationTargetException
	at java.base/jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
	at java.base/jdk.internal.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
	at java.base/java.lang.reflect.Constructor.newInstance(Constructor.java:490)
	at org.hibernate.tuple.entity.EntityTuplizerFactory.constructTuplizer(EntityTuplizerFactory.java:88)
	... 56 more
Caused by: java.lang.IllegalArgumentException: Could not create type
	at net.bytebuddy.TypeCache.findOrInsert(TypeCache.java:155)
	at net.bytebuddy.TypeCache.findOrInsert(TypeCache.java:175)
	at org.hibernate.bytecode.internal.bytebuddy.ByteBuddyState.load(ByteBuddyState.java:178)
	at org.hibernate.bytecode.internal.bytebuddy.ByteBuddyState.loadProxy(ByteBuddyState.java:102)
	at org.hibernate.proxy.pojo.bytebuddy.ByteBuddyProxyHelper.buildProxy(ByteBuddyProxyHelper.java:55)
	at org.hibernate.proxy.pojo.bytebuddy.ByteBuddyProxyFactory.postInstantiate(ByteBuddyProxyFactory.java:61)
	at org.hibernate.tuple.entity.PojoEntityTuplizer.buildProxyFactory(PojoEntityTuplizer.java:102)
	at org.hibernate.tuple.entity.AbstractEntityTuplizer.<init>(AbstractEntityTuplizer.java:160)
	at org.hibernate.tuple.entity.PojoEntityTuplizer.<init>(PojoEntityTuplizer.java:53)
	... 61 more
Caused by: java.security.AccessControlException: access denied ("java.lang.RuntimePermission" "defineClass")
	at java.base/java.security.AccessControlContext.checkPermission(AccessControlContext.java:472)
	at java.base/java.security.AccessController.checkPermission(AccessController.java:897)
	at java.base/java.lang.SecurityManager.checkPermission(SecurityManager.java:322)
	at java.base/java.lang.invoke.MethodHandles$Lookup.defineClass(MethodHandles.java:926)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.base/java.lang.reflect.Method.invoke(Method.java:566)
	at net.bytebuddy.utility.Invoker$Dispatcher.invoke(Unknown Source)
	at net.bytebuddy.utility.dispatcher.JavaDispatcher$Dispatcher$ForNonStaticMethod.invoke(JavaDispatcher.java:974)
	at net.bytebuddy.utility.dispatcher.JavaDispatcher$ProxiedInvocationHandler.invoke(JavaDispatcher.java:1098)
	at com.sun.proxy.$Proxy85.defineClass(Unknown Source)
	at net.bytebuddy.dynamic.loading.ClassInjector$UsingLookup.injectRaw(ClassInjector.java:1621)
	at net.bytebuddy.dynamic.loading.ClassInjector$AbstractBase.inject(ClassInjector.java:113)
	at net.bytebuddy.dynamic.loading.ClassLoadingStrategy$UsingLookup.load(ClassLoadingStrategy.java:492)
	at net.bytebuddy.dynamic.TypeResolutionStrategy$Passive.initialize(TypeResolutionStrategy.java:100)
	at net.bytebuddy.dynamic.DynamicType$Default$Unloaded.load(DynamicType.java:6154)
	at org.hibernate.bytecode.internal.bytebuddy.ByteBuddyState.lambda$load$0(ByteBuddyState.java:182)
	at net.bytebuddy.TypeCache.findOrInsert(TypeCache.java:153)
	... 69 more
```
</details>

## Environment

* Java: OpenJDK 1.8.0_302, OpenJDK 11.0.12
* Tomcat: 9.0.50
* Hibernate: 5.5.6.Final
* Byte Buddy: 1.11.10, 1.11.4
* Demo: [byte-buddy-hibernate-test](https://github.com/moonfruit/byte-buddy-hibernate-test)

## Steps

catalina.policy:
```
grant codeBase "file:${catalina.base}/webapps/demo/-" {
    permission java.security.AllPermission;
};
```

Command line:
```sh
# Build war
git clone https://github.com/moonfruit/byte-buddy-hibernate-test
cd byte-buddy-hibernate-test
./mvnw package
cd ..

# Download Tomcat
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.50/bin/apache-tomcat-9.0.50.tar.gz
tar xvf apache-tomcat-9.0.50.tar.gz

# Deploy war
cp byte-buddy-hibernate-test/target/demo.war apache-tomcat-9.0.50/webapps/

# Grant java.security.AllPermission
cat byte-buddy-hibernate-test/catalina.policy >> apache-tomcat-9.0.50/conf/catalina.policy

# Run
cd apache-tomcat-9.0.50
bin/catalina.sh run -security
```
