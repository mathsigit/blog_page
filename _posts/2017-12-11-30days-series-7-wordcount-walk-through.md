---
layout:     post
title:      "30天系列 Day 7-MapReduce WordCount流程介紹"
subtitle:   "30 Days Series Day 7 - Walk-through of WordCount."
date:       2017-12-11 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

## MapReduce WordCount 流程

承接上篇的程式碼，這次計算的檔案有兩個：**wordcount_target1** 與 **wordcount_target2**，由於檔案容量小於HDFS一個block的大小(預設為64mb)，這兩個檔案被上傳至HDFS後分別會被儲存至兩個block。    

程式開始執行運算時，在這個範例中首先會先執行**map**，預設會啟動**2**個 (與block數量相同) map來執行分散運算，最後**reduce** 會將所有的**map**運算結果進行最後的加總。

### Map

在map階段會執行下列的程式碼：
```java
@Override
public void map(Object key, Text value, Context context
) throws IOException, InterruptedException {
    StringTokenizer st = new StringTokenizer(value.toString());
    while (st.hasMoreTokens()) {
        word.set(st.nextToken());
        context.write(word, plugOne);
    }
}
```
Map方法在處理資料時，只會一次處理檔案內的一行資料並且當成一個字串。首先會透過`StringTokenizer`將讀取進來字串，使用空白當作分割字串，並且組成鍵值配對(key-value pair)後傳出。    

如果**wordcount_target1**被map讀取後執行，結果就會變這樣：
```bash
< I, 1>
< am, 1>
< Jack, 1>
< I, 1>
< am, 1>
< the, 1>
< king, 1>
< of, 1>
< the, 1>
< world, 1>
```

另一個map執行**wordcount_target2**檔案的結果就會如下列所示：
```bash
< I, 1>
< am, 1>
< Rose, 1>
< I, 1>
< am, 1>
< looking, 1>
< for, 1>
< Jack, 1>
```

---

### Reduce

Map階段完成處理並送出key-value的結果後，接下來reduce就會接收所有map處理完的資料進行最後的運算。

```java
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
```

在此階段reduce會把**wordcount_target1**與**wordcount_target2**經過map運算後key-value，再針對相同的key進行一次加總並且根據key排序，就是最後運算出來的結果：
```bash
< am, 4>
< for, 1>
< king, 1>
< looking, 1>
< of, 1>
< the, 2>
< world, 1>
< I, 4>
< Jack, 2>
< Rose, 1>
```

---

### 效能調整

如果有注意主程式(**main**)的話，可以看到有一個行程式碼：
```java
job.setCombinerClass(WordCountReducer.class);
```

**setCombinerClass** 主要是用來讓map可以再進行一次運算。上面的程式碼指定了使用**WordCountReducer.class**，表示map會把運算結果進行一次**reduce**的運算。    

**wordcount_target1** 經過**CombinerClass**的運算後，結果就會變這樣：
```bash
< am, 2>
< king, 1>
< of, 1>
< the, 2>
< world, 1>
< I, 2>
< Jack, 1>
```

而**wordcount_target2** 運算結果就變這樣：
```bash
< am, 2>
< for, 1>
< looking, 1>
< I, 2>
< Jack, 1>
< Rose, 1>
```

可以注意到這2個map結果，經過**CombinerClass**處理後，key-value個數一共減少了5個，換算成百分比就是少了5/18(27.8%)。當資料量非常大(數兆筆資料)的時候，運算次數減少27.8%是非常可觀的。所以善用**CombinerClass**可以加快mapreduce程式的運算時間！

---

### 最後

經過這一連串的介紹，想必大家對於Hadoop應該有更深一步的了解。Hadoop的篇章也到這邊告一個段落，接下來將邁入Big Data的資料庫：`HBase`。
