1.Using ami launch the instances under t2-medium 
ami-0178d42118c1f7677

2.Change the security group rules 
add all TCP at port IP - 0.0.0.0/0
add all ICMP - IPV4 scource - 0.0.0.0/0

3.Environment variables of master and slaves
~/.bash_profile

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/home/ubuntu/hadoop-2.6.5
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export M2_HOME=/home/ubuntu/apache-maven-3.5.3
export PATH=$PATH:$M2_HOME/bin
export OOZIE_HOME=/home/ubuntu/oozie-4.1.0
export OOZIE_CONFIG=$OOZIE_HOME/conf
export CLASSPATH=$CLASSPATH:$OOZIE_HOME/bin

4.To start the oozie and hadoop

Hadoop - start-all.sh or start-yarn.sh and start-dfs.sh
Oozie - bin/oozied.sh start
Historyserver - sbin/mr-jobhistory-daemon.sh start historyserver

5.Check for all the node using jps
jps

we should get
Bootstrap 
SecondaryNameNode
Jps
NameNode
JobHystoryServer
ResourceManager

6.Upload input file to HDFS:
$ hdfs dfs -mkdir -p input
$ hdfs dfs -put input/* input

7. Upload oozie's share file to HDFS:
$ cd $OOZIE_HOME
$ sudo tar xvf oozie-sharelib-4.3.0.tar.gz #change the sharelib name to your local sharelib name
$ cd $HADOOP_HOME
$ hdfs dfs -put $OOZIE_HOME/share share

8. Upload workflow.xml to HDFS:
$ hdfs dfs -mkdir project
$ hdfs dfs -put workflow.xml project

9. Compile the java files and make a jar file and upload the jar file to HDFS project/lib:
$ hadoop com.sun.tools.javac.Main *.java
$ jar cf project.jar *.class
$ hdfs dfs -mkdir project/lib
$ hdfs dfs -put project.jar project/lib

10. Initialize the database of oozie:
$ $OOZIE_HOME/bin/ooziedb.sh create -sqlfile oozie.sql -run

11.check for status
oozie-4.1.0/bin/oozie admin --oozie http://localhost:11000/oozie -status

12.open the chrome and launch the ec2 address with the extention as follows to connect it to oozie and haddop 
oozie port- :11000
Hadoop port- :8088

13.Change the job.properties file accordingly
nano job.properties

nameNode=hdfs://master:9000
jobTracker=master:8032
queueName=default
examplesRoot=cs644project
inputFilePath=/user/ubuntu/input/1987.csv.bz2,/user/ubuntu/input/1988.csv.bz2,/user/ubuntu/input/1989.csv.bz2,/user/ubuntu/input/1990.csv.bz2,/user/ubuntu/input/1991.csv.bz2,/user/ubuntu/input/1992.csv.bz2,/user/ubuntu/input/1993.csv.bz2,/user/ubuntu/input/1994.csv.bz2,/user/ubuntu/input/1995.csv.bz2,/user/ubuntu/input/1996.csv.bz2,/user/ubuntu/input/1997.csv.bz2,/user/ubuntu/input/1998.csv.bz2,/user/ubuntu/input/1999.csv.bz2,/user/ubuntu/input/2000.csv.bz2,/user/ubuntu/input/2001.csv.bz2,/user/ubuntu/input/2002.csv.bz2,/user/ubuntu/input/2003.csv.bz2,/user/ubuntu/input/2004.csv.bz2,/user/ubuntu/input/2005.csv.bz2,/user/ubuntu/input/2006.csv.bz2,/user/ubuntu/input/2007.csv.bz2,/user/ubuntu/input/2008.csv.bz2

oozie.wf.application.path=${nameNode}/user/${user.name}/${examplesRoot}/workflow.xml

14.get the job ID using
cd ~/oozie-4.1.0
bin/oozie job -oozie  http://localhost:11000/oozie -config examples/apps/map-reduce/job.properties -run

15.Check if the job ID is same that is running on oozie then you are good to go 

For adding more VMS

1.Using ami launch the instances under t2-medium and same security group as previous
ami-0178d42118c1f7677

2.Connect to the terminal using
ssh -i "pro.pem" ubuntu@ec2-100-26-121-254.compute-1.amazonaws.com
chmod 400 "pro.pem"

3.Gernerate the key 
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub

4.Add the key in all the instances created
nano ~/.ssh/authorized_keys

5. Add one more slave IP address in this filesudo 
nano /etc/hosts #172.31.60.55 slave2

6.Edit the bash profile according to master
nano ~/.bash_profile

7.Add slave
nano slaves #slave2

8.copy all the files using scp
scp -r hadoop-2.6.5 slave2:~

9.Now you have added another VM 




