---
layout: post
title: Hadoop整合Mongdb
category: 技术
comments: true
---

## 1、确保已经安装了mongdb

![ui-demo](http://static.oschina.net/uploads/space/2015/0528/220943_Ma0w_1863482.png)

## 2、下载jar包

![ui-demo](http://static.oschina.net/uploads/space/2015/0528/221052_Z8kp_1863482.png)

<font color="red">注意：列出了两个重要的jar包，其他的hadoop的jar包同样需要！</font>

## 3、准备

数据格式是这种：

![ui-demo](http://static.oschina.net/uploads/space/2015/0528/221314_aole_1863482.png)

创建数据库和集合

![ui-demo](http://static.oschina.net/uploads/space/2015/0528/221402_0j6o_1863482.png)

## 4、代码实现

```java
	public class CityDis {
 
    private static class CityDisMapper extends
            Mapper<LongWritable, Text, Text, IntWritable> {
 
        @Override
        protected void map(LongWritable key, Text value,
                Mapper<LongWritable, Text, Text, IntWritable>.Context context)
                throws IOException, InterruptedException {
 
            if( value.toString().split(",")[5].contains(" ")){
                String outKey = value.toString().split(",")[5].split(" ")[1];
                context.write(new Text(outKey), new IntWritable(1));
            }
 
        }
    }
 
    private static class CityDisReduce extends
            Reducer<Text, IntWritable, NullWritable, BSONWritable> {
 
        @Override
        protected void reduce(Text key, Iterable<IntWritable> values,
                Reducer<Text, IntWritable, NullWritable, BSONWritable>.Context context)
                throws IOException, InterruptedException {
 
            int count = 0;
            for (IntWritable value : values) {
                count += value.get();
            }
             
            BasicBSONObject out = new BasicBSONObject();
             
            out.put("name",key.toString());
            out.put("value",count);
             
            context.write(NullWritable.get(), new BSONWritable(out));
 
        }
 
    }
 
    public static void main(String[] args) throws IOException,
            ClassNotFoundException, InterruptedException {
 
        Configuration configuration = HadoopConfig.getConfiguration();
         
        Job job = Job.getInstance(configuration, "地区分布");
 
        job.setJarByClass(CityDis.class);
        job.setMapperClass(CityDisMapper.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        job.setOutputKeyClass(NullWritable.class);
        job.setOutputValueClass(BSONWritable.class);
        job.setReducerClass(CityDisReduce.class);
 
        FileInputFormat.addInputPath(job, new Path("/data/user.txt"));
        job.setOutputFormatClass(MongoOutputFormat.class);      
        System.exit(job.waitForCompletion(true) ? 0 : 1);
 
    }
```

```java
	mongo-defaults.xml：
 
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <property>
    <!-- run the job verbosely ? -->
    <name>mongo.job.verbose</name>
    <value>false</value>
  </property>
  <property>
    <!-- Run the job in the foreground and wait for response, or background it? -->
    <name>mongo.job.background</name>
    <value>false</value>
  </property>
  <property>
      <!-- The field to pass as the mapper key. Defaults to _id if blank -->
      <name>mongo.input.key</name>
      <value></value>
  </property>
   
  <!--我们可以从这里来设置读取mongdb数据的位置-->
  <!--<property>
    <name>mongo.input.uri</name>
    <value>mongodb://127.0.0.1/bigdata.test</value>    
  </property>-->
   
  <property>
    <!-- If you are writing to mongo, the URI -->
    <name>mongo.output.uri</name>
    <value>mongodb://127.0.0.1/bigdata.city_weibo</value>
  </property>
  <property>
    <!-- The query, in JSON, to execute [OPTIONAL] -->
    <name>mongo.input.query</name>
    <value></value>
  </property>
  <property>
    <!-- The fields, in JSON, to read [OPTIONAL] -->
    <name>mongo.input.fields</name>
    <value></value>
  </property>
  <property>
    <!-- A JSON sort specification for read [OPTIONAL] -->
    <name>mongo.input.sort</name>
    <value></value>
  </property>
  <property>
    <!-- The number of documents to limit to for read [OPTIONAL] -->
    <name>mongo.input.limit</name>
    <value>0</value> <!-- 0 == no limit -->
  </property>
  <property>
    <!-- The number of documents to skip in read [OPTIONAL] -->
    <!-- TODO - Are we running limit() or skip() first? -->
    <name>mongo.input.skip</name>
    <value>0</value> <!-- 0 == no skip -->
  </property>
  <property>
    <!-- IF you want to control the split size for input, set it here.
      Should be a long indicating # of docs per split
      Affects # of mappers so be careful what you do -->
    <name>mongo.input.split_size</name>
  </property>
  <!-- These .job.* class defs are optional and only needed if you use the MongoTool baseclass -->
  <property>
    <!-- Class for the mapper -->
    <name>mongo.job.mapper</name>
    <value></value>
  </property>
  <property>
    <!-- Reducer class -->
    <name>mongo.job.reducer</name>
    <value></value>
  </property>
  <property>
    <!-- InputFormat Class -->
    <name>mongo.job.input.format</name>
    <!-- <value>com.mongodb.hadoop.MongoInputFormat</value> -->
    <value></value>
  </property>
  <property>
    <!-- OutputFormat Class -->
    <name>mongo.job.output.format</name>
    <!-- <value>com.mongodb.hadoop.MongoOutputFormat</value> -->
    <value></value>
  </property>
  <property>
    <!-- Output key class for the output format -->
    <name>mongo.job.output.key</name>
    <value></value>
  </property>
  <property>
    <!-- Output value class for the output format -->
    <name>mongo.job.output.value</name>
    <value></value>
  </property>
  <property>
    <!-- Output key class for the mapper [optional] -->
    <name>mongo.job.mapper.output.key</name>
    <value></value>
  </property>
  <property>
    <!-- Output value class for the mapper [optional] -->
    <name>mongo.job.mapper.output.value</name>
    <value></value>
  </property>
  <property>
    <!-- Class for the combiner [optional] -->
    <name>mongo.job.combiner</name>
    <value></value>
  </property>
  <property>
    <!-- Partitioner class [optional] -->
    <name>mongo.job.partitioner</name>
    <value></value>
  </property>
  <property>
    <!-- Sort Comparator class [optional] -->
    <name>mongo.job.sort_comparator</name>
    <value></value>
  </property>
</configuration>
```
## 5、运行效果

![ui-demo](http://static.oschina.net/uploads/space/2015/0528/221806_ZI6F_1863482.png)

