# Big_Data_EX_05

# EX_05 - Implement Word Count / Frequency Program Using MapReduce

## Aim:

To implement a MapReduce program in Java for counting the frequency of words from input text using Hadoop MapReduce framework.

## Algorithm:

1. Mapper:

- Reads input line by line.

- Splits each line into words.

- Emits each word as a key and 1 as the value.

2. Reducer:

- Receives all values for a given word (key).

- Sums up all counts (values) for the word.

- Emits the word and its total count.

3. Driver:

- Configures the MapReduce job.

- Specifies input and output paths.

- Sets Mapper, Reducer classes, output key and value types.

- Submits the job and waits for completion.

## Program:
```
DEVELOPED BY : NIRAUNJANA GAYATHRI G R
REGISTER NO. : 212222230096
```

### 1. Mapper Class: WCMapper.java

```
import java.io.IOException;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.Mapper;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reporter;

public class WCMapper extends MapReduceBase implements Mapper<LongWritable, Text, Text, IntWritable> {

    public void map(LongWritable key, Text value, OutputCollector<Text, IntWritable> output, Reporter rep) throws IOException {
        String line = value.toString();

        for (String word : line.split(" ")) {
            if (word.length() > 0) {
                output.collect(new Text(word), new IntWritable(1));
            }
        }
    }
}

```

### 2. Reducer Class: WCReducer.java

```
import java.io.IOException;
import java.util.Iterator;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.MapReduceBase;
import org.apache.hadoop.mapred.OutputCollector;
import org.apache.hadoop.mapred.Reducer;
import org.apache.hadoop.mapred.Reporter;

public class WCReducer extends MapReduceBase implements Reducer<Text, IntWritable, Text, IntWritable> {

    public void reduce(Text key, Iterator<IntWritable> values, OutputCollector<Text, IntWritable> output, Reporter rep) throws IOException {
        int count = 0;
        while (values.hasNext()) {
            count += values.next().get();
        }
        output.collect(key, new IntWritable(count));
    }
}

```

### 3. Driver Class: WCDriver.java

```
import java.io.IOException;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapred.FileInputFormat;
import org.apache.hadoop.mapred.FileOutputFormat;
import org.apache.hadoop.mapred.JobClient;
import org.apache.hadoop.mapred.JobConf;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

public class WCDriver extends Configured implements Tool {

    public int run(String args[]) throws IOException {
        if (args.length < 2) {
            System.out.println("Please provide input and output paths");
            return -1;
        }

        JobConf conf = new JobConf(WCDriver.class);
        FileInputFormat.setInputPaths(conf, new Path(args[0]));
        FileOutputFormat.setOutputPath(conf, new Path(args[1]));

        conf.setMapperClass(WCMapper.class);
        conf.setReducerClass(WCReducer.class);

        conf.setMapOutputKeyClass(Text.class);
        conf.setMapOutputValueClass(IntWritable.class);

        conf.setOutputKeyClass(Text.class);
        conf.setOutputValueClass(IntWritable.class);

        JobClient.runJob(conf);
        return 0;
    }

    public static void main(String[] args) throws Exception {
        int exitCode = ToolRunner.run(new WCDriver(), args);
        System.out.println("Job exited with code: " + exitCode);
    }
}

```

## Execution Steps:

1. Compile the Java files and package them into a JAR file.

2. Run the Hadoop job from the terminal:
   
```
   hadoop jar WordCount.jar WCDriver /input/path /output/path

```

3. Check output by running:

```
hdfs dfs -cat /output/path/part-00000

```

## Sample Input:

![image](https://github.com/user-attachments/assets/41ce4f92-7607-4e90-891a-03aadfd5f05c)

## Expected Output:

![image](https://github.com/user-attachments/assets/328e1042-3387-4e70-afe5-209e53aeac87)

## Result:

Successfully executed MapReduce word count program on Hadoop, which counted and displayed the frequency of each word in the input file(s).



