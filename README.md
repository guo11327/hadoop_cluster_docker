# 介绍
实验项目,基于docker搭建Hadoop集群

# Q&A

## pdsh
### ERROR: pdsh@stretch: localhost: connect: Connection refused
### ANSWER
adding 
``` 
PDSH_RCMD_TYPE=ssh
```
in $HADOOP_HOME/etc/hadoop/hadoop-env.sh

### REFERENCE
[pdsh](https://stackoverflow.com/questions/48189954/hadoop-start-dfs-sh-connection-refused)

## jar version conflict
### ERROR: 
```
root@675582378ef3:/etc# hive -version
```
>SLF4J: Class path contains multiple SLF4J bindings.  
SLF4J: Found binding in [jar:file:/usr/local/hive/lib/log4j-slf4j-impl-2.10.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]  
SLF4J: Found binding in [jar:file:/usr/local/hadoop/share/hadoop/common/lib/slf4j-log4j12-1.7.25.jar!/org/slf4j/impl/StaticLoggerBinder.class]  
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.  
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]  
Exception in thread "main" java.lang.NoSuchMethodError: com.google.common.base.Preconditions.  checkArgument(ZLjava/lang/String;Ljava/lang/Object;)V  
        at org.apache.hadoop.conf.Configuration.set(Configuration.java:1380)  
        at org.apache.hadoop.conf.Configuration.set(Configuration.java:1361)  
        at org.apache.hadoop.mapred.JobConf.setJar(JobConf.java:536)  
        at org.apache.hadoop.mapred.JobConf.setJarByClass(JobConf.java:554)  
        at org.apache.hadoop.mapred.JobConf.<init>(JobConf.java:448)  
        at org.apache.hadoop.hive.conf.HiveConf.initialize(HiveConf.java:5141)  
        at org.apache.hadoop.hive.conf.HiveConf.<init>(HiveConf.java:5099)  
        at org.apache.hadoop.hive.common.LogUtils.initHiveLog4jCommon(LogUtils.java:97)  
        at org.apache.hadoop.hive.common.LogUtils.initHiveLog4j(LogUtils.java:81)  
        at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:699)  
        at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:683)  
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)  
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)   
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)  
        at java.lang.reflect.Method.invoke(Method.java:498)  
        at org.apache.hadoop.util.RunJar.run(RunJar.java:323)
### ANSWER
This issue happened because some jar version is different in Hive lib folder and Hadoop shared folder.  
To fix this, we need to ensure the versions are consistent.
Here, we need to deal these jars. the version could be different.
- guava
- log4j-slf4j-impl

```
/usr/local/hive/lib# mv log4j-slf4j-impl-2.10.0.jar log4j-slf4j-impl-2.10.0.jar.bak
/usr/local/hive/lib# mv guava-19.0.jar guava-19.0.jar.bak
/usr/local/hadoop/share/hadoop/common/lib# cp guava-27.0-jre.jar /usr/local/hive/lib
```
### REFERENCE
* [https://kontext.tech/column/hadoop/415/hive-exception-in-thread-main-javalangnosuchmethoderror-comgooglecommon](https://kontext.tech/column/hadoop/415/hive-exception-in-thread-main-javalangnosuchmethoderror-comgooglecommon)
