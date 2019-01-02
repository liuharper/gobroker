# gobroker
wrapper for all (to-be) kinds of message brokers.

## Supported message brokers & patterns
### PubSub
- RabbitMQ (*fanout*)
- Google Cloud Pub/Sub

## Intentions & Features
- Generic terms & functions to use message brokers
- Auto reconnection
- Limit & requeue messages
- Concurrent subscribers
- Support for mockgen unit-testing

## Usage
Complete examples are provided in `examples` folder/ package
### RabbitMQ
```
// initialize publisher RabbitMQ
p := pubsub.NewPublisher(gobroker.RabbitMQ, pubsub.RabbitMQAMPQ("amqp://guest:guest@localhost:5672/", "vhost"))

p.Publish("test.fanout", "msg"+t.String())
```
```
// register RabbitMQ subscriber(s) & run it
s := pubsub.NewSubscriber(gobroker.RabbitMQ, []*pubsub.SubHandler{
    {
        Name:       "test",
        Topic:      "test.fanout",
        Handler:    testRMQ,
        MaxRequeue: 10,
    },
}, pubsub.RabbitMQAMPQ("amqp://guest:guest@localhost:5672/", "vhost"))

s.Start()
```
### Google
```
// initialize publisher Google
p := pubsub.NewPublisher(gobroker.Google, pubsub.GoogleJSONFile("gcp-project-id", "/path/to/google/application/credentials/cred.json"))

p.Publish("test", "msg"+t.String())
```
```
// register Google subscriber(s) & run it
s := pubsub.NewSubscriber(gobroker.Google, []*pubsub.SubHandler{
		{
			Name:       "consumer-test",
			Topic:      "test",
			Handler:    testGoogle,
			MaxRequeue: 10,
		},
	},
		pubsub.GoogleJSONFile("gcp-project-id", "/path/to/google/application/credentials/cred.json"))
		
s.Start()
```
### Creating subcriber/ consumer
```
// subcriber function format
// return nil will ack the message as success
// return error will requeue based on config

func testRMQ(msg *gobroker.Message) error {
	log.Println("consume rabbitmq", string(msg.Body))
	return nil
}
func testGoogle(msg *gobroker.Message) error {
	log.Println("consume google pubsub", string(msg.Body))
	return errors.New("requeue msg")
}
```

## Contributing
Please use a fork to create a pull request

## Offline Contributors
[ichsanrp](https://github.com/ichsanrp)
