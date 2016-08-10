# How I Learned to Stop Worrying and Love Ephemeral Storage - Silicon Valley Data Science

[Original URL](http://www.svds.com/learned-stop-worrying-love-ephemeral-storage/)

> There are many benefits to running data platforms in the cloud--elasticity of infrastructure, simplification of management and monitoring, and agility of deployment and expansion. While there...

There are many benefits to running data platforms in the cloud--elasticity of infrastructure, simplification of management and monitoring, and agility of deployment and expansion. While there are several good resources for deploying a Hadoop cluster on Amazon's cloud, such as Cloudera's [AWS Reference Architecture](http://www.cloudera.com/documentation/other/reference-architecture/PDF/cloudera_ref_arch_aws.pdf) and [AWS EMR Best Practices](https://media.amazonwebservices.com/AWS_Amazon_EMR_Best_Practices.pdf), the steps to storing data in a persistent and reliable data store are not as clear cut.

This post will show architects and developers how to set up Hadoop to communicate with S3, use Hadoop commands directly against S3, use distcp to perform transfers between Hadoop and S3, and how distcp can be used to update on a regular basis based only on differences. I've also included a quick look at other considerations to keep in mind when putting this all together.

At SVDS, one architecture pattern we use with our clients is to store HDFS data blocks in Hadoop clusters using local instance storage. Until recently this was the pattern recommended by Cloudera, however there is now also support for using EBS volumes for data nodes. Which pattern you choose will ultimately depend on the instance types you need to support and the tradeoffs between instance and EBS storage. For clusters that need to support interactive data science investigations, this deployment mode preserves data locality and offers better performance than querying directly against S3.

Note: this is not the only way to deploy Hadoop clusters; support to query against S3 directly is available from [EMR](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-plan-file-systems.html), and for tools such as [Impala](http://www.cloudera.com/documentation/enterprise/latest/topics/impala_s3.html), [Spark](http://www.cloudera.com/documentation/enterprise/latest/topics/spark_s3.html), [Hive](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/emr-hive-additional-features.html), and [Presto](http://techblog.netflix.com/2014/10/using-presto-in-our-big-data-platform.html). We will discuss more options in future posts.

The issue with using local instance storage is that it's ephemeral. If a server goes down, whether it is stopped or due to failure, data on instance storage is lost.

Yes, this is [true](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/InstanceStorage.html). However, using local instance storage is also how you can take advantage of physically attached storage on servers in AWS. In the case of Hadoop clusters, assuming data is replicated, all of the data nodes would need to go down for there to be data loss. Since this is possible (data center outage, for example), you must protect against the loss.

How do you do so? The short-answer is to use [distcp](http://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html). Distcp is used to [perform parallel copies](http://www.cloudera.com/documentation/enterprise/latest/topics/cdh_admin_distcp_data_cluster_migrate.html) between Hadoop clusters. In addition, it comes, out of the box, with [support](https://wiki.apache.org/hadoop/AmazonS3) to read and write from S3\. It is an efficient tool for data transfers as you can take advantage of the parallelism of the Hadoop cluster and of S3 to maximize throughput.

We'll start with how to setup Hadoop so it can communicate with S3.

## Setting Up Hadoop S3 configuration

There are a couple of [ways](http://www.cloudera.com/documentation/enterprise/latest/topics/spark_s3.html) to enable Hadoop to communicate with S3\. You can add the AWS Access Key and Secret Access Key to your Hadoop configuration by updating the core-site.xml with the following values:

```
&amp;lt;property&amp;gt;
 &amp;lt;name&amp;gt;fs.s3a.access.key&amp;lt;/name&amp;gt;
 &amp;lt;value&amp;gt;[access-key]&amp;lt;/value&amp;gt;
&amp;lt;/property&amp;gt;
&amp;lt;property&amp;gt;
 &amp;lt;name&amp;gt;fs.s3a.secret.key&amp;lt;/name&amp;gt;
 &amp;lt;value&amp;gt;[secret-access-key]&amp;lt;/value&amp;gt;
&amp;lt;/property&amp;gt;
```

Alternatively, you can pass the credentials with every command you make to S3:

```
hdfs dfs -ls s3a://[access-key]:[secret-access-key]@[bucket-name]/
```

Which way you choose to implement will depend on how you want to secure your keys and your specific environment setup. With updating the configuration, you run the risk that the keys could be checked into Git and accessible by others (don't do this). With running from the command line, you run the risk that the keys are logged on the terminal. Your choice ultimately depends on where you want to exercise your security constraints.

## Simple HDFS Commands

Let's assume you've created an S3 bucket called has-bucket. Here are some good [guidelines](http://docs.rightscale.com/faq/clouds/aws/What_are_valid_S3_bucket_names.html) to choosing a compliant name for your S3 bucket.

A good first test is to verify that Hadoop commands against S3 work. For example, you can access files in this bucket through HDFS fs commands:

```
hdfs dfs -ls -h s3a://has-bucket/
```

If this command goes through successfully and you are able to see the contents of your bucket, then you know that the Hadoop S3 configuration should be set.

Other commands you can try are:

```
hdfs dfs -put lorem_ipsum.txt s3a://has-bucket/
hdfs dfs -cat s3a://has-bucket/lorem_ipsum.txt
```

Some commands are supported and some don't have an effect. We won't focus on this as part of this post, but feel free to experiment in your own sandbox environment.

## Using Distcp for data transfers between HDFS and S3

Now that we have confirmed integration between Hadoop and S3, we can begin using distcp. In general, the format of distcp is as follows:

```
hadoop distcp [options] &amp;lt;src-url&amp;gt; &amp;lt;dest-url&amp;gt;
```

For example, if we want to copy a folder from HDFS to S3, we would use:

```
hadoop distcp hdfs://nameservice1/user/svds/walrus/ s3a://has-bucket/
```

This command copies the folder itself and all of its contents to the destination directory. Therefore, you would have the `walrus` folder in `s3://has-bucket/` when using the default distcp command.

You can also pass parameters to only update the destination directory based on the differences with source (i.e. -update) or completely overwrite the destination (i.e. -overwrite). However, the behavior of the command is different when using the -update or -overwrite parameters. In this case, the contents of the directory are copied, but not the folder itself.

If we run the following command:

```
hadoop distcp -update hdfs://nameservice1/user/svds/walrus/ s3a://has-bucket/
```

The _contents_ of the walrus directory are compared against has-bucket and whatever files are different are then copied over. If we were trying to compare against the `s3a://has-bucket/walrus` folder we previously copied over, this command would not work as expected and would instead copy the entire contents of the walrus directory to has-bucket, basically creating duplicates.

What we want is to run an update against the `walrus` directory itself. To do this we need to run the command as follows:

```
hadoop distcp -update hdfs://nameservice1/user/svds/walrus/ s3a://has-bucket/walrus/
```

This process will only copy the files that do not exist in the destination or those that have changed since the last copy. Note that this is not a sync operation; you need to flip the source and destination if you need to copy any changes made in S3 back down to your HDFS cluster.

## Setting up a job to regularly update data in S3

Now that we understand the behavior of distcp, we can schedule a regular job to update the data in S3 with changes made in the Hadoop cluster. This provides greater durability to the data and makes the data available to be used by other applications and cloud services through S3.

In the example below, we'll schedule it using a cron job, however you can also use Oozie or another workflow scheduler to trigger this job. First, I'll add all of my hadoop distcp commands to a shell script called hadoop_s3_backup.sh. Then, I can use cron to schedule to run this script every day at 2 AM.

```
0 2 data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md data.json data_ml developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.epub developing_a_street_basketball_game_part_i_getting_workflow_ready_—_whitestormjs_framework.md devops docker embedded from_zero_to_frontend_hero_part_1_—_free_code_camp.epub from_zero_to_frontend_hero_part_1_—_free_code_camp.md gaming gdb-example-ncurses.epub github go how_to_check_diskspace_with_the_ncdu_utility.epub how_to_check_diskspace_with_the_ncdu_utility.md html immutable_collections_for_javascript.epub immutable_collections_for_javascript.md javascript linux mobile node-hero-tutorials.epub png programming science social task_automation_with_npm_run.md tidy understanding_javascript_promises_pt_i_background__basics.epub understanding_javascript_promises_pt_i_background__basics.md understanding_object_streams.epub understanding_object_streams.md url_to_filename.csv web_dev writing_a_javascript_framework__execution_timing_beyond_settimeout.epub writing_a_javascript_framework__execution_timing_beyond_settimeout.md writing_a_javascript_framework__project_structuring.epub writing_a_javascript_framework__project_structuring.md /home/mauricio/hadoop_s3_backup.sh
```

## Other Considerations

Here are a few important things to consider when following this pattern:

- File permissions are not preserved when storing data in S3\. When transferring data from S3 to Hadoop, the permissions of the files being copied will be based on the user who runs the distcp command. Because of this, it is important that a process is created to update HDFS-based file permissions and ownership after the distcp step from S3 to HDFS.
- AWS provides a [VPC to S3 endpoint](https://aws.amazon.com/blogs/aws/new-vpc-endpoint-for-amazon-s3/) which should seriously be considered if the Hadoop cluster lives in a VPC. This provides a secure and reliable way to connect from Hadoop to S3 without needing to go through a NAT or the Internet Gateway, even if the Hadoop cluster resides in a private subnet.
- AWS provides a S3DistCp process that comes with EMR and optimizes transfers between Hadoop and S3\. S3DistCp should be considered as an alternative to distcp in this context.

## Conclusion

Hopefully this post helps provide a better idea of how to use distcp to regularly backup your data from ephemeral storage to S3\. This allows you to protect your data from disaster and decouples it from Hadoop so that it can be used by other tools and cloud services against S3 directly.

Have any questions or a different approach to this problem? Feel free to post them in the comments section below, or reach out to us via [Twitter](https://twitter.com/SVDataScience).

- Thanks to Rick Drushal for pointing me to the S3 VPC endpoint reference and for others who have helped edit and review this blog.*