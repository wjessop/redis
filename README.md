# Redis client for Golang [![Build Status](https://travis-ci.org/go-redis/redis.png?branch=master)](https://travis-ci.org/go-redis/redis)

Supports:

- Redis 3 commands except QUIT, MONITOR, SLOWLOG and SYNC.
- [Pub/Sub](http://godoc.org/github.com/wjessop/redis#PubSub).
- [Transactions](http://godoc.org/github.com/wjessop/redis#Multi).
- [Pipelining](http://godoc.org/github.com/wjessop/redis#Client.Pipeline).
- [Scripting](http://godoc.org/github.com/wjessop/redis#Script).
- [Timeouts](http://godoc.org/github.com/wjessop/redis#Options).
- [Redis Sentinel](http://godoc.org/github.com/wjessop/redis#NewFailoverClient).
- [Redis Cluster](http://godoc.org/github.com/wjessop/redis#NewClusterClient).
- [Ring](http://godoc.org/github.com/wjessop/redis#NewRing).
- [Cache friendly](https://github.com/go-redis/cache).

API docs: http://godoc.org/github.com/wjessop/redis.
Examples: http://godoc.org/github.com/wjessop/redis#pkg-examples.

## Installation

Install:

    go get github.com/wjessop/redis

## Quickstart

```go
func ExampleNewClient() {
	client := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "", // no password set
		DB:       0,  // use default DB
	})

	pong, err := client.Ping().Result()
	fmt.Println(pong, err)
	// Output: PONG <nil>
}

func ExampleClient() {
	err := client.Set("key", "value", 0).Err()
	if err != nil {
		panic(err)
	}

	val, err := client.Get("key").Result()
	if err != nil {
		panic(err)
	}
	fmt.Println("key", val)

	val2, err := client.Get("key2").Result()
	if err == redis.Nil {
		fmt.Println("key2 does not exists")
	} else if err != nil {
		panic(err)
	} else {
		fmt.Println("key2", val2)
	}
	// Output: key value
	// key2 does not exists
}
```

## Howto

Please go through [examples](http://godoc.org/github.com/wjessop/redis#pkg-examples) to get an idea how to use this package.

## Look and feel

Some corner cases:

    SET key value EX 10 NX
    set, err := client.SetNX("key", "value", 10*time.Second).Result()

    SORT list LIMIT 0 2 ASC
    vals, err := client.Sort("list", redis.Sort{Offset: 0, Count: 2, Order: "ASC"}).Result()

    ZRANGEBYSCORE zset -inf +inf WITHSCORES LIMIT 0 2
    vals, err := client.ZRangeByScoreWithScores("zset", redis.ZRangeByScore{
        Min: "-inf",
        Max: "+inf",
        Offset: 0,
        Count: 2,
    }).Result()

    ZINTERSTORE out 2 zset1 zset2 WEIGHTS 2 3 AGGREGATE SUM
    vals, err := client.ZInterStore("out", redis.ZStore{Weights: []int64{2, 3}}, "zset1", "zset2").Result()

    EVAL "return {KEYS[1],ARGV[1]}" 1 "key" "hello"
    vals, err := client.Eval("return {KEYS[1],ARGV[1]}", []string{"key"}, []string{"hello"}).Result()

## Shameless plug

Check my [PostgreSQL client for Go](https://github.com/go-pg/pg).
