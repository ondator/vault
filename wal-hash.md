# Write-Ahead Log

Imagine you want to create your own key-value database. I think that something like this will be a good start:
```scala
object Naive{
  import java.io._
  def get[T](key: String)(implicit walName: String): Option[String] = {
    val file = new File(walName)
    val bw = new BufferedReader(new FileReader(file))
    val line = bw
                .lines()
                .filter(x=>x.startsWith(s"$key,"))
                .findFirst()
                .orElse(null)
    
    bw.close()
    Option(line).map(x=>x.split(",").last)
  }

  def set(key: String, value: String)(implicit walName: String){
    val file = new File(walName)
    if(!file.exists()) file.createNewFile()
    val bw = new BufferedWriter(new FileWriter(file, true))
    bw.write(s"$key,$value\n")
    bw.close()
  }
}
```
This is realy nice implementation because it's very tolerate to writes as far as it is append-only. But what about reads? 
In order to speedup reads we create a simple index: a HashMap with our database keys and offset as value:
```scala
object Naive {
  import java.io._
  def get[T](key: String)(implicit walName: String, index: mutable.Map[String, Long]): Option[String] = {
    val file = new File(walName)
    val offset = index get key
    offset.flatMap(o => {
      val bw = new BufferedReader(new FileReader(file))
      bw.skip(o)
      var line = bw.readLine()
      bw.close()
      Option(line).map(x => x.split(",").last)
    })
  }

  def set(key: String, value: String)(implicit walName: String, index: mutable.Map[String, Long]) {
    val file = new File(walName)
    if (!file.exists()) file.createNewFile()
    val offset = file.length()
    val bw = new BufferedWriter(new FileWriter(file, true))
    bw.write(s"$key,$value\n")
    bw.close()
    index.addOne((key, offset))
  }
}
``` 
So, here we have a simple key-value database with write-ahead log and simple index. Really it's quite similar to [Bitcask](https://riak.com/assets/bitcask-intro.pdf).

In order to keep this article simple I skip `delete` operation and concurrency checks, but you can find complete code on [github]() 

## Compaction

After long usage of our database our wal file will be very large. In order to avoid this we should introduce compaction.

First off all we will split our wal to segments: when our current segment will achieve size N we will create new segment(file)

Then as you can see in our segments we can have multiple records for same key but we need only one(latest). So from time to time we will clean our archived segments from unused database.

And finaly from time to time we can merge few small segments in larger one also in background