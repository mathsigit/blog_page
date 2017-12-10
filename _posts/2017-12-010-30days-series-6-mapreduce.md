---
layout:     post
title:      "30天系列 Day 6-MapReduce實作"
subtitle:   "30 Days Series Day 6 - Example of MapReduce."
date:       2017-12-10 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# MapReduce實作

前面介紹完了HDFS的基本操作與指令介紹後，接下來要來簡介MapReduce分散式運算的實作，終於可以寫點程式啦！

由於Hadoop原始碼是由Java所撰寫，當然MapReduce也需要使用Java來實作囉。本篇將會實作MapReduce界中的`Hello World`，word count。

## WordCount的範例程式
```java
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class HadoopWordCountSample {
    public static class WordCountMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        //計數使用，設定為1。每當找到相同的字就會+1。
        private final static IntWritable plugOne  = new IntWritable(1);
        private Text word = new Text();

        @Override
        public void map(Object key, Text value, Context context
        ) throws IOException, InterruptedException {
            //使用StringTokenizer效能會比使用split好。預設使用空白、tab或是換行當作分隔符號。
            StringTokenizer st = new StringTokenizer(value.toString());
            while (st.hasMoreTokens()) {
                word.set(st.nextToken());
                context.write(word, plugOne);
            }
        }
    }

    public static class WordCountReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {

        private IntWritable result = new IntWritable();

        @Override
        public void reduce(Text key, Iterable<IntWritable> values,
                           Context context
        ) throws IOException, InterruptedException {
            int reduceSum = 0;
            for (IntWritable val : values) {
                reduceSum += val.get();
            }
            result.set(reduceSum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration config = new Configuration();
        Job job = Job.getInstance(config, "hadoop word count example");
        job.setJarByClass(HadoopWordCountSample.class);
        job.setReducerClass(WordCountReducer.class);
        job.setMapperClass(WordCountMapper.class);
        //設定setCombinerClass後，每個mapper會在sorting後，對結果先做一次reduce
        job.setCombinerClass(WordCountReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        //執行程式時，第一個參數（agrs[0]）為欲計算檔案路徑
        FileInputFormat.addInputPath(job, new Path(args[0]));
        //第二個參數（agrs[1]）為計算結果存放路徑
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

## 使用說明

* **Step1. 複製程式碼：**
  * 使用筆記本或是`vi`、`vim`等指令將上列程式碼複製貼上後存擋。假設檔案名稱為：`HadoopWordCountSample.java`
* **Step2. 設定環境。**
  * 假設儲存程式碼的環境是安裝hadoop的其中一台node，已經設定完`JAVA_HOME`與`HADOOP_HOME`等環境變數，接下來只要設定`HADOOP_CLASSPATH`即可。
  ```bash
  #編輯~/.bashrc檔案
  sudo vi ~/.bashrc
  #加入HADOOP_CLASSPATH。注意！記得放在JAVA_HOME後面得行數，否則會出錯。
  export HADOOP_CLASSPATH=${JAVA_HOME}/lib/tools.jar
  ```
* **Step3. 編譯並打包程式碼。**
```bash
#Compile
hadoop com.sun.tools.javac.Main HadoopWordCountSample.java
#Package
jar cf hwcs.jar HadoopWordCountSample*.class
```
* **Step4. 準備要計算的字數的檔案，並上傳至HDFS。**    
寫好了word count程式碼，那也要有計算目標。為了讓MapReduce發揮淋漓盡致，需要將要計算的檔案上傳至HDFS，有了分散式儲存再加上分散式運算，真是天作之合啊！(威！離題了！)    
  * 首先使用先在本機機器產生兩個檔案：`wordcount_target1`與`wordcount_target2`，內容分別為：    
    * **wordcount_target1**
    ```bash
    I am Jack
    I am the king of the world
    ```
    * **wordcount_target2**
    ```bash
    I am Rose
    I am looking for Jack
    ```

  * 接下來在HDFS上建立一個資料夾，並且將這兩個檔案上傳到資料夾內：    
    ```bash
    #建立資料夾
    hadoop fs -mkdir -p /tmp/wordcount_target
    #上傳
    hadoop fs -put wordcount_target1 wordcount_target2 /tmp/wordcount_target
    ```
* Step5. 在Hadoop上執行程式碼
```bash
hadoop jar hwcs.jar HadoopWordCountSample /tmp/wordcount_target /tmp/wordcount_result
#說明
hwcs.jar:              要執行計算的MapReduce jar檔案路徑（需要包含的檔案名稱）
HadoopWordCountSample: 欲執行的class name
/tmp/wordcount_target: 欲計算檔案路徑
/tmp/wordcount_result: 計算結果存放路徑
```
* Step6. 查看結果：
```bash
hadoop fs -cat /tmp/wordcount_result/part-r-00000
#結果
am 4
for 1
king 1
looking 1
of 1
the 2
world 1
I 4
Jack 2
Rose 1
```
