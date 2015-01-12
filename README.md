# Blockchain Analysis
Analyzing the blockchain.

## Parsing the Blockchain

### Download the whole blockchain and set it up for analysis
```bash
# download the blockchain bootstrap db to get a large number of transactions
# prepared by the bitcoin.org from here: https://bitcoin.org/en/download
wget https://bitcoin.org/bin/blockchain/bootstrap.dat.torrent

# now install rtorrent to pull this down (blazing fast!)
apt-get install rtorrent
rtorrent bootstrap.dat.torrent

# Now move this dump into the canonical location of reference implementation blockchain dat files
mkdir ~/.bitcoin
cp bootstrap.dat ~/.bitcoin/blk0001.dat
```

### Install the blockparser project so we can analyze
```bash
# Install the blockchain parser so we can read what's in here
sudo apt-get install -y libssl-dev build-essential g++-4.4 libboost-all-dev libsparsehash-dev git-core perl
git clone git://github.com/witoff/blockparser.git
cd blockparser
make
```

### (recommended) mount local instance store to max disk writes on local files
```
sudo -s
fdisk -l
mkfs.ext4 /dev/xvdb
mount -t ext4 /dev/xvdb /mnt
mount -a
mv blockparser /mnt
cd /mnt

# Move blockchain onto this volume and redefine home
mkdir .bitcoin
mv ~/.bitcoin/blk* .bitcoin/
export HOME=/mnt

# parse
./parser <option>
```

### Now dump to text
```bash

# export all balances
./parser allBalances > ~/all_balances.txt
# exports 5.8GB+ of data into the output text file

# export all transactions (custom to my fork)
./parser allTransactions > ~/all_tx.txt

# Run `./parser man` to see full docs.
```


Exporting balances looks like this:

| balance | Hash160 | Base58 | nbIn | lastTimeIn | nbOut | lastTimeOut |
| ------- | ------- | ------ | ---- | ---------- | ----- | ----------- |
| `144341.5` | `a0e6ca5444e...` | `1FfmbHf...` | 589 | Sun Apr  6 12:56:29 2014 | 0 | Thu Jan  1 00:00:00 1970 |
| `97831.5` | `1855055056b9...` | `13Df4x5...` | 63 | Wed Apr  2 21:02:53 2014 | 39 | Wed Mar 12 18:07:06 2014 |

**where:**<br />
* `balance` is the outstanding net balance on this address<br />
* `hash160` is the directly listed public key hash output of any transaction going to this address (=RIPEMD160(SHA256(x)))<br />
* `bash58` is the base58 encoded public key (more [here](https://en.bitcoin.it/wiki/Base58Check_encoding))<br />
* `nbIn` is the number of transactions sending *to* this address<br />
* `lastTimeIn` is the last time a tx was received here<br />
* `nbOut` is the number of transactions sent *from* this address<br />
* `lastTimeOut` is the last time a tx was broadcast pulling from this address.<br />


<!-- 
actual output: 
---------------------------------------------------------------------------------------------------------------------------------------------------------------------
                 Balance                                  Hash160                             Base58   nbIn lastTimeIn                 nbOut lastTimeOut
---------------------------------------------------------------------------------------------------------------------------------------------------------------------
         144341.53121235 a0e6ca5444e4d8b7c80f70237f332320387f18c7 1FfmbHfnpaZjKFvyi1okTjJJusN455paPH    589 Sun Apr  6 12:56:29 2014       0 Thu Jan  1 00:00:00 1970
          97831.54800760 1855055056b9c6de07fc66eaa30dc40232867710 13Df4x5nQo7boLWHxQCbJzobN5gUNT65Hh     63 Wed Apr  2 21:02:53 2014      39 Wed Mar 12 18:07:06 2014
          79957.05650837 a0b0d60e5991578ed37cbda2b17d8b2ce23ab295 1FeexV6bAHb8ybZjqQMjJrcCrHGW9sb6uF     42 Wed Apr  2 10:09:18 2014       0 Thu Jan  1 00:00:00 1970
          78693.00000000 9c5a3e66a134bd33d99fba091a398e0f47ba7e68 1FFiXAwoeLDvzPmcifNTDiGhdJM8GkvZAg      1 Sat Apr  5 18:02:09 2014       0 Thu Jan  1 00:00:00 1970
          69471.09859854 b3dd79fb3460c7b0d0bbb8d2ed93436b88b6d89c 1HQ3Go3ggs8pFnXuHVHRytPCq5fGG8Hbhx     32 Thu Mar 27 09:26:09 2014       0 Thu Jan  1 00:00:00 1970
          53880.05281692 a82d63de690d6e71f98a2991249310f6a3d9cb88 1GLEtzJ1H2zoGrUA4RMbRJam5UJSdrk6T2     29 Thu Mar 27 09:26:09 2014       3 Fri Feb  1 06:04:48 2013
          53000.05276694 3d9e561f21d312f9b8b46e74169263e2452d5591 16cou7Ht6WjTzuFyDBnht9hmvXytg6XdVT     37 Thu Mar 27 09:56:05 2014       9 Sun May 13 12:13:16 2012
          48652.88535736 f33a3af3695f041e2ea6271f1fe6309abac4dd58 1PB4xXUFyy4kSNqroCBVaQuCuw9VcN3be4    104 Mon Apr  7 20:53:55 2014       0 Thu Jan  1 00:00:00 1970
          48566.14013045 27ed35ebcf72744ec3b4a0f4dbb83d7620acf443 14e7XAZbepQp9MXXzjNG3fNLoAUpaBAXHW     61 Mon Apr  7 00:54:45 2014       0 Thu Jan  1 00:00:00 1970
          48547.09910638 3d03002dbed5cb1dc10fc6bcb0886d2df32f2838 16ZbpCEyVVdqu8VycWR8thUL2Rd9JnjzHt    106 Sun Apr  6 23:53:24 2014       0 Thu Jan  1 00:00:00 1970

-->


And exporting transactions look like this:

| time | address | txId | txAmount |
| ---- | ------- | ---- | -------- |
| Sat Jan  3 18:15:05 2009 | `62e907..` | `4a5e1e...` | 50 |


## Spark Cluster

From [here](http://ampcamp.berkeley.edu/3/exercises/launching-a-bdas-cluster-on-ec2.html#log-into-your-cluster)

AWS Tokens (*.*):
* key: ...
* secret: ...

```bash
# N.B. Work in US-East

# setup keys
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...

# clone bdas setup scripts
git clone git://github.com/witoff/bdas-scripts.git

# Launch Cluster
cd training-scripts

# launch cluster with ganglia, using my keys
./spark-ec2 -i ~/.ssh/bdas.pem -k bdas -w 240 -g -t m3.xlarge --root-vol-size 31 --copy launch amplab-training

# ssh into master e.g.
ssh -i ~/.ssh/bdas.pem root@ec2-54-242-218-80.compute-1.amazonaws.com

# slaves
ssh -i ~/.ssh/bdas.pem root@ec2-54-91-66-241.compute-1.amazonaws.com
ssh -i ~/.ssh/bdas.pem root@ec2-204-236-205-194.compute-1.amazonaws.com
ssh -i ~/.ssh/bdas.pem root@ec2-54-237-224-182.compute-1.amazonaws.com
ssh -i ~/.ssh/bdas.pem root@ec2-54-205-75-198.compute-1.amazonaws.com
ssh -i ~/.ssh/bdas.pem root@ec2-54-237-241-220.compute-1.amazonaws.com
```

Spark Example:
```bash
/root/spark/bin/spark-shell

# load data from hdfs
val pagecounts = sc.textFile("/wiki/pagecounts")

# look at some datafiles and print each on a line
pagecounts.take(10).map(println)
# each line in data contains stats for one page.  Schema is:
# <date_time> <project_code/language> <page_title> <num_hits for hour> <page_size in bytes>
# 20090505-000000 aa Main_Page 2 9980

# count number of lines
pagecounts.count

# get all english pages and cache rdd
val enPages = pagecounts.filter(_.split(" ")(1) == "en").cache

# count en pages from the cache
enPages.count

# count number of visits on each day
val enTuples = enPages.map(_.split(" "))
val enKeyValuePairs = enTuples.map(tuple => (tuple(0).substring(0, 8), tuple(3).toInt))
enKeyValuePairs.reduceByKey(_+_, 1).collect
# Array((20090505,207698578), (20090506,204190442), (20090507,202617618))

enPages.map( line => (line.substring(0,8), line.split(" ")(3).toInt)).reduceByKey(_ + _).collect
# Array((20090505,207698578), (20090506,204190442), (20090507,202617618))

# find the biggest pages
enPages.map(_.split(" ")).map(tuple => (tuple(2), tuple(3).toInt)).reduceByKey(_+_).filter(el => el._2 > 200000).collect.map(println)

enPages.map(l => l.split(" ")).map(l => (l(2), l(3).toInt)).reduceByKey(_+_, 40).filter(x => x._2 > 200000).map(x => (x._2, x._1)).collect.foreach(println)

# finished up here: http://ampcamp.berkeley.edu/3/exercises/data-exploration-using-spark.html
```

Full Scala API [here](http://www.cs.berkeley.edu/~pwendell/strataconf/api/core/index.html#spark.RDD)
Full Python API [here](http://www.cs.berkeley.edu/~pwendell/strataconf/api/pyspark/index.html)

clear the tachyon cache with `./tachyon/bin/tachyon clearCache`

Shark / Spark SQL
```bash
```

```bash
# copying in btc data
cd /ampcamp-data/
wget https://s3.amazonaws.com/witoff-bitcoin/all_balances.tar.gz
gunzip all_balances.tar.gz
mv all_balances.tar all_balances.txt

# copy data into HDFS
/root/ephemeral-hdfs/bin/hadoop fs -copyFromLocal /ampcamp-data/all_balances.txt /all_balances.txt

val data = sc.textFile("/all_balances.txt")
data.take(10).map(println)
data.count
# 32994774

val balances = data.filter(x => x.length>0 && x(0) !='-')
balances.count


# Balance   Hash160  Base58   nbIn  lastTimeIn                nbOut   lastTimeOut
# 144341.5  a0e6...  1Ffmb... 589   Sun Apr  6 12:56:29 2014  0       Thu Jan  1 00:00:00 1970
# 97831.54  1855...  13Df4... 63    Wed Apr  2 21:02:53 2014  39      Wed Mar 12 18:07:06 2014

val parts = balances.map(_.trim).filter(_(0) != 'B').map(_.split("\\s+"))

val biggest = parts.map(_(0).toFloat).filter(_ > 1000)
biggest.collect().map(println)

# sum of all mined blocks
val sum = parts.map(_(0).toFloat).reduce(_+_)
# sum: Float = 1.2573022E7

val tx_sum = parts.map(x => x(3).toInt + x(9).toInt).reduce(_+_)
# 176,227,748

val hashes = parts.map(x => x(2).toLowerCase())
hashes.filter(_.contains("d")).count

# save as a text file

nums.saveAsTextFile("hdfs:///nums")
./ephemeral-hdfs/bin/hadoop fs -copyToLocal /nums.txt/part-00000 /root/nums
cat /root/nums
# DONE!
```

GraphX Notes From [here](http://ampcamp.berkeley.edu/big-data-mini-course/graph-analytics-with-graphx.html#getting-started-again)
```bash

```
