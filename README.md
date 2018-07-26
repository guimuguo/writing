## Config the Gminer on UAB Server
1. export GMINER_HOME=~/project/GMiner-master
2. Change all the mpi and HDFS path in the CMakeLists.txt to the UAB server's.
3. Configure all the parameter in gminer-conf.ini (attached)
4. Add all the slaves to the machines.cfg
5. Build the project:
>./auto-build.sh
6. Put the data on hdfs:
>mpiexec -n 1 /home/guimuguo/project/GMiner-master/release/put /home/guimuguo/project/data/youtube_sorted gminer_youtube
7. partition the data on hdfs and name the new data wiht dataset_input:
>mpiexec -n 2 -f machines.cfg /home/guimuguo/project/GMiner-master/release/partition gminer_toy  gminer_toy_input normal_hash
8. change the HDFS_INPUT_PATH in gminer-conf.ini to the HDFS/path/dataset_input
9. make a directory on the hdfs and change the HDFS_OUTPUT_PATH in the gminer-conf.ini
10. run the maxclique app:
>mpiexec -genv MPIR_CVAR_CH3_EAGER_MAX_MSG_SIZE=134217728 -n 3 -f /home/guimuguo/project/GMiner-master/machines.cfg /home/guimuguo/project/GMiner-master/release/mc

**Then I got the max-clique size is 10 which is not correct!**

The put, partition and execution report were attached.

----
## For the Normal Format:
Then I think it's may caused by the different data format between my youtube data and the normal_sample.adj

```
normal_sample.adj format:
{vertex_id} '\t'{num_neighbors}' '{neighbor1}' '{neighbor2} ...

youtube data format:
{vertex_id}' '{num_neighbors} '\t' {neighbor1}' '{neighbor2} ...
```
1. I change the code of data loading:
Change the strtok(line, "\t") to strtok(line, " \t") in partition/HashPartitioner.tpp and apps/maxclique.cpp.
I rebuilded the project and rerun it. While I still get the maximal size is 10.
2. I write a c++ application (code attached) to transfer the data as the normal_sample format. I rerun Gminer but still got the wrong result.
