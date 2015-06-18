---
layout: post
title: MapReduce应用
category: 技术
comments: true
---

1、MapReduce实现矩阵相乘

```java
#!/bin/bash
if [ $# -ne 3 ]
then
  echo "there must be 3 arguments to generate the two matries file!"
  exit 1
fi
cat /dev/null > M_$1_$2
cat /dev/null > N_$2_$3
for i in `seq 1 $1`
do
    for j in `seq 1 $2`
    do
        s=$((RANDOM%100))
        echo -e "$i,$j\t$s" >>M_$1_$2
    done
done
echo "we have built the matrix file M"
 
for i in `seq 1 $2`
    do
    for j in ` seq 1 $3`
    do
        s=$((RANDOM%100))
        echo -e "$i,$j\t$s" >>N_$2_$3 
    done
done
echo "we have built the matrix file N"

M_3_2:
1,1 81
1,2 13
2,1 38
2,2 46
3,1 0
3,2 2

N_2_4:
1,1 99
1,2 38
1,3 34
1,4 19
2,1 21
2,2 4
2,3 36
2,4 64
```

二. 计算

```java
public class Matrix {
 
    private static class MatrixMapper extends
            Mapper<LongWritable, Text, Text, Text> {
 
        private static int colN = 0;
        private static int rowM = 0;
 
        @Override
        protected void setup(
                Mapper<LongWritable, Text, Text, Text>.Context context)
                throws IOException, InterruptedException {
 
            Configuration configuration = context.getConfiguration();
            colN = configuration.getInt("colN", 0);
            rowM = configuration.getInt("rowM", 0);
 
        }
 
        @Override
        protected void map(LongWritable key, Text value,
                Mapper<LongWritable, Text, Text, Text>.Context context)
                throws IOException, InterruptedException {
 
            FileSplit fileSplit = (FileSplit) context.getInputSplit();
            String fileName = fileSplit.getPath().getName();
            String[] strings = value.toString().split(",");
            int i = Integer.parseInt(strings[0]);
            String[] ser = strings[1].split("\t");
            int j = Integer.parseInt(ser[0]);
            int val = Integer.parseInt(ser[1]);
 
            if (fileName.startsWith("M")) {
 
                for (int count = 1; count <= colN; count++) {
                    context.write(new Text(i + "," + count), new Text("M," + j
                            + "," + val + ""));
                }
 
            } else {
 
                for (int count = 1; count <= rowM; count++) {
                    context.write(new Text(count + "," + j), new Text("N," + i
                            + "," + val + ""));
                }
 
            }
        }
    }
 
    private static class MatrixReduce extends
            Reducer<Text, Text, Text, IntWritable> {
 
        private static int rowM = 0;
 
        @Override
        protected void setup(
                Reducer<Text, Text, Text, IntWritable>.Context context)
                throws IOException, InterruptedException {
 
            Configuration configuration = context.getConfiguration();
            rowM = configuration.getInt("rowM", 0);
 
        }
 
        @Override
        protected void reduce(Text key, Iterable<Text> values,
                Reducer<Text, Text, Text, IntWritable>.Context context)
                throws IOException, InterruptedException {
 
            int sumValue = 0;
            int[] m_Arr = new int[rowM + 1];
            int[] n_Arr = new int[rowM + 1];
 
            for (Text value : values) {
 
                String string = value.toString();
                String[] strings = string.split(",");
 
                if (strings[0].equals("M")) {
                    m_Arr[Integer.parseInt(strings[1])] = Integer
                            .parseInt(strings[2]);
                } else {
                    n_Arr[Integer.parseInt(strings[1])] = Integer
                            .parseInt(strings[2]);
                }
            }
 
            for (int i = 1; i < rowM + 1; i++) {
                sumValue += m_Arr[i] * n_Arr[i];
            }
 
            context.write(key, new IntWritable(sumValue));
        }
 
    }
 
    public static void main(String[] args) throws IllegalArgumentException,
            IOException, ClassNotFoundException, InterruptedException {
 
        Configuration configuration = HadoopConfig.getConfiguration();
        configuration.setInt("colN", 4);
        configuration.setInt("rowN", 2);
        configuration.setInt("colM", 2);
        configuration.setInt("rowM", 3);
 
        Job job = Job.getInstance(configuration, "矩阵相乘");
 
        job.setJarByClass(Sort.class);
        job.setMapperClass(MatrixMapper.class);
 
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
 
        job.setReducerClass(MatrixReduce.class);
        FileInputFormat.addInputPath(job, new Path("/matrix"));
        FileOutputFormat.setOutputPath(job, new Path("/matrixOutput"));
        job.waitForCompletion(true);
        System.exit(job.waitForCompletion(true) ? 0 : 1);
 
    }
 
}

1,1 8292
1,2 3130
1,3 3222
1,4 2371
2,1 4728
2,2 1628
2,3 2948
2,4 3666
3,1 42
3,2 8
3,3 72
3,4 128

```

2、MapReduce实现倒排索引

file1:
one fish
two bird
two monkey
 
file2:
two peach
three watermelon

```java
	public class InvertIndex {
 
    private static class InvertIndexMapper extends
            Mapper<LongWritable, Text, Text, Text> {
 
        @Override
        protected void map(LongWritable key, Text value,
                Mapper<LongWritable, Text, Text, Text>.Context context)
                throws IOException, InterruptedException {
 
            FileSplit fileSplit = (FileSplit) context.getInputSplit();
            String fileName = fileSplit.getPath().toString();
            String[] words = value.toString().split(" ");
            for (String string : words) {
                context.write(new Text(string), new Text(fileName + "#" + key.toString()));
            }
             
        }
 
    }
 
    private static class InvertIndexReduce extends
            Reducer<Text, Text, Text, Text> {
 
        @Override
        protected void reduce(Text key, Iterable<Text> values,
                Reducer<Text, Text, Text, Text>.Context context)
                throws IOException, InterruptedException {
             
            StringBuilder stringBuilder = new StringBuilder();
             
            for (Text text : values) {
                    stringBuilder.append(text.toString()).append(";");
            }
             
            context.write(key, new Text(stringBuilder.toString()));
        }
    }
 
    public static void main(String[] args) throws IOException,
    ClassNotFoundException, InterruptedException{
 
        Configuration configuration = HadoopConfig.getConfiguration();
        Job job = Job.getInstance(configuration, "倒排索引");
        job.setJarByClass(InvertIndex.class);
        job.setMapperClass(InvertIndexMapper.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(Text.class);
        job.setReducerClass(InvertIndexReduce.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
 
        FileInputFormat.addInputPath(job, new Path("/data"));
        FileOutputFormat.setOutputPath(job, new Path("/ouput"));
        job.waitForCompletion(true);
        System.exit(job.waitForCompletion(true) ? 0 : 1);
         
    }
    
bird    hdfs://127.0.0.1:8020/data/file1#9;
fish    hdfs://127.0.0.1:8020/data/file1#0;
monkey  hdfs://127.0.0.1:8020/data/file1#18;
one hdfs://127.0.0.1:8020/data/file1#0;
peach   hdfs://127.0.0.1:8020/data/file2#0;
three   hdfs://127.0.0.1:8020/data/file2#10;
two hdfs://127.0.0.1:8020/data/file2#0;hdfs://127.0.0.1:8020/data/file1#18;hdfs://127.0.0.1:8020/data/file1#9;
watermelon  hdfs://127.0.0.1:8020/data/file2#10;
```

3、MapReduce实现复杂倒排索引

file1:
one fish
two bird
two monkey
 
file2:
two peach
three watermelon

```java
public class ComplexInvertIndex {
 
    private static class FileNameRecordReader extends RecordReader<Text, Text> {
 
        LineRecordReader lineRecordReader = new LineRecordReader();
        String fileName;
 
        @Override
        public void initialize(InputSplit split, TaskAttemptContext context)
                throws IOException, InterruptedException {
            lineRecordReader.initialize(split, context);
            fileName = ((FileSplit) split).getPath().getName();
        }
 
        @Override
        public boolean nextKeyValue() throws IOException, InterruptedException {
            return lineRecordReader.nextKeyValue();
        }
 
        @Override
        public Text getCurrentKey() throws IOException, InterruptedException {
            return new Text(fileName);
        }
 
        @Override
        public Text getCurrentValue() throws IOException, InterruptedException {
            return lineRecordReader.getCurrentValue();
        }
 
        @Override
        public float getProgress() throws IOException, InterruptedException {
            return lineRecordReader.getProgress();
        }
 
        @Override
        public void close() throws IOException {
            lineRecordReader.close();
        }
 
    }
 
    private static class FileNameInputFormat extends
            FileInputFormat<Text, Text> {
 
        @Override
        public RecordReader<Text, Text> createRecordReader(InputSplit split,
                TaskAttemptContext context) throws IOException,
                InterruptedException {
            FileNameRecordReader fileNameRecordReader = new FileNameRecordReader();
            fileNameRecordReader.initialize(split, context);
            return fileNameRecordReader;
        }
 
    }
 
    private static class ComplexInvertIndexMapper extends
            Mapper<Text, Text, Text, IntWritable> {
 
        @Override
        protected void map(Text key, Text value,
                Mapper<Text, Text, Text, IntWritable>.Context context)
                throws IOException, InterruptedException {
 
            String[] strs = value.toString().split(" ");
            for (String string : strs) {
                context.write(new Text( string+"#"+key.toString() ),new IntWritable(1));
            }
 
        }
 
    }
 
    private static class ComplexInvertIndexCombiner extends
            Reducer<Text, IntWritable, Text, IntWritable> {
 
        @Override
        protected void reduce(Text key, Iterable<IntWritable> values,
                Reducer<Text, IntWritable, Text, IntWritable>.Context context)
                throws IOException, InterruptedException {
 
            int sum = 0;
            for (IntWritable value : values) {
                sum += value.get();
            }
            context.write(key,new IntWritable(sum));
            System.out.println(key.toString() + sum +"");
        }
 
    }
 
    //把key的前面字段聚合，排序
    private static class InvertIndexPartitioner extends
            HashPartitioner<Text, IntWritable> {
 
        @Override
        public int getPartition(Text key, IntWritable value, int numReduceTasks) {
            String[] strs = key.toString().split("#");
            return super.getPartition(new Text(strs[0]), value, numReduceTasks);
        }
 
    }                
 
    private static class ComplexInvertIndexReduce extends
            Reducer<Text, IntWritable, Text, Text> {
 
        static Map<String, String> map = new HashMap<String, String>();
         
        @Override
        protected void reduce(Text key, Iterable<IntWritable> values,
                Reducer<Text, IntWritable, Text, Text>.Context context)
                throws IOException, InterruptedException {
 
            String[] strings = key.toString().split("#");
            String word = strings[0];
            String doc = strings[1];
            int sum = 0;
            for(IntWritable value : values){
                sum = sum + value.get();
            }
            if( map.get(word) == null ){
                map.put(word," ("+doc+","+sum+") ");
            }else{
                map.put(word,map.get(word)+" ("+doc+","+sum+") ");
            }
            
        }
         
        @Override
        protected void cleanup(
                Reducer<Text, IntWritable, Text, Text>.Context context)
                throws IOException, InterruptedException {
            for(String key:map.keySet()){
                context.write(new Text(key), new Text(map.get(key)));
            }
        }
 
    }
 
    public static void main(String[] args)throws IOException,
    ClassNotFoundException, InterruptedException{
 
        Configuration configuration = HadoopConfig.getConfiguration();
        Job job = Job.getInstance(configuration, "复杂倒排索引");
        job.setJarByClass(ComplexInvertIndex.class);
        job.setInputFormatClass(FileNameInputFormat.class);
        job.setMapperClass(ComplexInvertIndexMapper.class);
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        job.setCombinerClass(ComplexInvertIndexCombiner.class);
        job.setReducerClass(ComplexInvertIndexReduce.class);
        job.setPartitionerClass(InvertIndexPartitioner.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
         
        FileInputFormat.addInputPath(job, new Path("/data"));
        FileOutputFormat.setOutputPath(job, new Path("/ouputdata"));
        job.waitForCompletion(true);
        System.exit(job.waitForCompletion(true) ? 0 : 1);
         
    }

monkey   (file1,1) 
bird     (file1,1) 
fish     (file1,1) 
one  (file1,1) 
peach    (file2,1) 
watermelon   (file2,1) 
three    (file2,1) 
two  (file1,2)  (file2,1)

```
