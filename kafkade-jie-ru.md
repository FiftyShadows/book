####生产者
```go
package main

import (
	"fmt"
	"time"

	"github.com/Shopify/sarama"
)

func main() {

	config := sarama.NewConfig()
	config.Producer.RequiredAcks = sarama.WaitForAll
	config.Producer.Partitioner = sarama.NewRandomPartitioner
	config.Producer.Return.Successes = true

	client, err := sarama.NewSyncProducer([]string{"127.0.0.1:9092"}, config)
	if err != nil {
		fmt.Println("producer close, err:", err)
		return
	}

	defer client.Close()
	for {
		msg := &sarama.ProducerMessage{}
		msg.Topic = "shen"
		msg.Value = sarama.StringEncoder("this is a good test, my message is good")

		pid, offset, err := client.SendMessage(msg)
		if err != nil {
			fmt.Println("send message failed,", err)
			return
		}

		fmt.Printf("pid:%v offset:%v\n", pid, offset)
		time.Sleep(10 * time.Millisecond)
	}
}
```
####消费者
```go
package main

import (
	"fmt"
	"strings"
	"sync"

	"github.com/Shopify/sarama"
	"time"
)

var (
	wg sync.WaitGroup
)

func main() {

	consumer, err := sarama.NewConsumer(strings.Split("127.0.0.1:9092", ","), nil)
	if err != nil {
		fmt.Println("Failed to start consumer: %s", err)
		return
	}
	partitionList, err := consumer.Partitions("shen")
	if err != nil {
		fmt.Println("Failed to get the list of partitions: ", err)
		return
	}
	fmt.Println(partitionList)
	for partition := range partitionList {
		pc, err := consumer.ConsumePartition("shen", int32(partition), sarama.OffsetNewest)
		if err != nil {
			fmt.Printf("Failed to start consumer for partition %d: %s\n", partition, err)
			return
		}
		defer pc.AsyncClose()
		go func(pc sarama.PartitionConsumer) {
			wg.Add(1)
			for msg := range pc.Messages() {
				fmt.Printf("Partition:%d, Offset:%d, Key:%s, Value:%s", msg.Partition, msg.Offset, string(msg.Key), string(msg.Value))
				fmt.Println()
			}
			wg.Done()
		}(pc)
	}
	time.Sleep(time.Hour)
	wg.Wait()
	consumer.Close()
}
```