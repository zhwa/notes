### Java and Hadoop

##### 1. Hadoop commands

##### compile the java program

compile .java file:  two ways to make it: javac & hadoop

  - javac:  

		export HADOOP_CLASSPATH=$JAVA_HOME/lib/tools.jar
		javac -cp ${HADOOP_CLASSPATH} <classpath> <program_name>.java -d <build path>   
	
-d means directory, -cp means class path

  -  sun.tools.javac.Main:  javac could be invoked from a java program.
		
		export HADOOP_CLASSPATH=$JAVA_HOME/lib/tools.jar
		hadoop com.sun.tools.javac.Main <probram_name>.java -d <build path>


##### jar file

		jar -cvf <class_name>.jar  -C <build_path> .

-C means change the path during the jar procedure. It follows two paths: the first one contains the compiled file (class files, in the build path) the second one is the storage path. When all class files are in current directory, the command could also be:

		jar -cvf <class_name>.jar <class files to be archived>  
	
v means verbose, c means create, f means output goes to a file



##### Hadoop executation

		hadoop jar <jarfile> <class_name> <arguments>
	
Usually the class name is the same as the name of the jar file. The arguments should contains two directories: location of input file and output file in the HDFS. For example:
		
		hadoop jar WordCount.jar WordCount /tutorial/input /tutorial/output

##### HDFS operation

		hadoop fs -cmd

To list files:

		hadoop fs -ls
		hadoop fs -lsr /		# subdirectory

To make directory:

		hadoop fs -mkdir /user/chuck

To copy a file from local to HDFS:

		hadoop fs -put <local files> <HDFS path>

To copy a file from HDFS to local:

		hadoop fs -get <HDFS path> <local path>

To view a file:

		hadoop fs -cat <HDFS file path>
		hadoop fs -cat <HDFS file path> | head
		hadoop fs -tail <HDFS file path>

To delete a file:

		hadoop fs -rm <HDFS file path>
		hadoop fs -rm -r -f <HDFS path>  # delete the directory and subdirectory, -f will not display errors, -r will delete recursively

To merger files (usually merge output files of MapReduce):

		hadoop fs -getmerge /output/dir/on/hdfs /desired/local/output/file.txt

----
#### Hadoop classes

##### Data Types for keys & Values

 - Values: should be Writable
 - Keys: WritableComparable

Some Writable data types:

- BooleanWritable
- ByteWritable
- DoubleWritable
- FloatWritable
- IntWritable		: useful in counting
- LongWritable
- Text				: useful in word count. This class has a "set(String string)" method to record string/token, and "toString()"
- NullWritable		: useful when key is not needed



##### Major components in the MapReduce file

There are 4 major components in the java file:

 - import
	- app class:
		- mapper
		- reducer
		- main function


###### Mapper class
The mapper class should extend the Mapper<K1,V1,K2,V2> class. 

	public static class MyMapper extends Mapper<K1,V1,K2,V2> {}

- K1 and V1 are the data types for input keys and values
- K2 and V2 are the data types for output keys and values 

The map function is defined in this class

	public void map(K1 key, V1 value, Context context) {
		...
		context.write(key2, val2); // output for reduce
	}

The context data type is necessary in both mapper and reducer methods.

For example, in the word count problem:

 - K1 : Object
 - V1 : Text
 - K2 : Text
 - V2 : IntWritable


It seems that no attribute is required in the classes.  

###### Reducer class
The reducer class should extend the Reducer<K2,V2,K3,V3> class.

	public static class MyReducer extends reducer<K2,V2,K3,V3> {}

 - Output key is not necessarily the same as the input key
 - Output type of Mapper must be consistent with input type of Reducer

The reducer function is defined in this class

	public void reduce(K2 key, V2, value, context context) {
		...
		context.write(key3, val3);
	}

For example, in the word count problem:

 - K3 : Text
 - V3 : IntWritable


###### main function

Template for the main program:

	public static void main(String[] args) throws Exception {

        Job job = Job.getInstance(new Configuration(), "app name");		// create job, with config
		job.setJarByClass(app class name.class);						// name of class with main function

        job.setMapperClass(mapper name.class);
		job.setMapperOutputKeyClass(K2.class);
		job.setMapperOutputValueClass(V2.class);
		
        job.setReducerClass(reducer name.class);
		job.setOutputKeyClass(K3.class);
        job.setOutputValueClass(V3.class);

		job.setNumReduceTasks(2);										// set number of reducers

        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

		System.exit(job.waitForCompletion(true) ? 0 : 1);
	}


###### import

The following are almost necessary:

	import java.io.IOException;
	import java.util.*;

	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.*;										// include IntWritable, Text ...

	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;

	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;


or, simply code:

	import java.io.*
	import java.util.*
	import org.apache.hadoop.*


----

##### Useful data type for MapReduce

###### Array

The length of an array is established when the array is created. After creation, its length is fixed.  An array's type is written as type[], where type is the data type of the contained elements; the brackets are special symbols indicating that this variable holds an array. The size of the array is not part of its type (which is why the brackets are empty).

	int[] anArray;
	byte[] anArrayOfBytes;
	short[] anArrayOfShorts;
	long[] anArrayOfLongs;
	float[] anArrayOfFloats;
	double[] anArrayOfDoubles;
	boolean[] anArrayOfBooleans;
	char[] anArrayOfChars;
	String[] anArrayOfStrings;	


One way to create an array is with the *new* operator like *new int[10]*. Alternatively, you can use the shortcut syntax to create and initialize an array:

	int[] anArray = { 
    	100, 200, 300,
    	400, 500, 600, 
    	700, 800, 900, 1000
	};


###### ArrayList

ArrayList extends AbstractList and implements the List interface. ArrayList supports dynamic arrays that can grow as needed. It uses diamond inference syntax on the right side: *ArrayList<Type>*

	// Create new ArrayList.
	ArrayList<Integer> elements = new ArrayList<>();

	// Add three elements.
	elements.add(10);
	elements.add(15);
	elements.add(20);

	// Get size and display.
	int count = elements.size();
	System.out.println("Count: " + count);

Some important methods of ArrayList:

 - add(int index, Object element): Inserts the specified element at the specified position index
 - clear() : Removes all of the elements from this list
 - get(int index): Returns the element at the specified position in this list
 - remove(int index): 
 - size() :
 - toArray(): Returns an array containing all of the elements in this list in the correct order
 - Collections.sort(ArrayList) : We can sort an ArrayList with this method. We first import java.util.Collections into our program. This method sorts in ascending (low to high) order.
 
	
	import java.util.Collections;
	import java.util.ArrayList;
	public class Program {
	
    	public static void main(String[] args) {

			ArrayList<String> list = new ArrayList<>();
			list.add("cat");
			list.add("bird");
			list.add("ant");
			list.add("dog");

			// Sort the elements alphabetically.
			Collections.sort(list);

			for (String value : list) {
	    		System.out.println(value);
			}
    	}
	}




###### TreeSet


###### Pair

	class Pair<A extends Comparable<? super A>, B extends Comparable<? super B>> implements Comparable<Pair<A, B>>


###### simple for loop

	for (Pair<Integer, String> item: countToWordMap) {
		Text word = new Text(item.second);
		IntWritable value = new IntWritable(item.first);
		context.write(word, value);
	}

