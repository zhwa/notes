#### Set up IPython notebook and Pyspark

Helpful links:

 - [cloudera blog](http://blog.cloudera.com/blog/2014/08/how-to-use-ipython-notebook-with-apache-spark/)

 - [shell debug](https://unix.stackexchange.com/questions/155551/how-to-debug-a-bash-script)

###### Set Up IPython notebook

On AWS EC2 the notebook should listen to all IP addresses, and use a specific port number allowed by the instance

 - Set the EC2 instance to allow Custom TCP Rule on port 8889 (or others)
 - Start IPython notebook using:

		ipython notebook --no-browser --ip=* --port=8889

Then visit *http://remote ip:8889* in Chrome, and the IPython notebook will be shown.


##### Set Up Pyspark

Setting up for Pyspark and IPython notebook shall be easy, if the environment variable *$SPARK_HOME* is known to you. All the following works are similary to those in the [cloudera blog](http://blog.cloudera.com/blog/2014/08/how-to-use-ipython-notebook-with-apache-spark/). 

The major steps include:

 - create IPython profile for Pyspark:

	 	ipython profile create pyspark

 - configure notebook at *~/.ipython/profile_pyspark/ipython_notebook_config.py*:
 
		c = get_config()

		c.NotebookApp.ip = '*'
		c.NotebookApp.open_browser = False
		c.NotebookApp.port = 8889

 - create the file *~/.ipython/profile_pyspark/startup/00-pyspark-setup.py*:
 	
	The following code needs to be modified according to the Spark environment, especially the py4j part.

		import os
		import sys

		spark_home = os.environ.get('SPARK_HOME', None)
		if not spark_home:
			raise ValueError('SPARK_HOME environment variable is not set')
		sys.path.insert(0, os.path.join(spark_home, 'python'))
		sys.path.insert(0, os.path.join(spark_home, 'python/lib/py4j-0.8.2-src.zip'))
		execfile(os.path.join(spark_home, 'python/pyspark/shell.py'))

 - write the IPython notebook start shell:

		export SPARK_HOME=""
		export PYSPARK_SUBMIT_ARGS="--master yarn --executor-cores 2"   # optional
		ipython notebook --profile=pyspark


For the hortonworks sandbox, things become a little difficult: the *$SPARK_HOME* is unknown --- this environment variable is set in */usr/bin/pyspark* script whenever we run *pyspark*. Even worse, there are two Spark installed under the */usr/hdp* path.  In this case, the following trick is used: debug the *pyspark* script:

		bash -x /usr/bin/pyspark

Then the *$SPARK_HOME* will be dumped on the terminal, with the *py4j* version information.