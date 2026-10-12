bbolt
=====

[![Go Report Card](https://goreportcard.com/badge/github.com/zxysilent/bbolt?style=flat-square)](https://goreportcard.com/report/github.com/zxysilent/bbolt)
[![Go Reference](https://pkg.go.dev/badge/github.com/zxysilent/bbolt.svg)](https://pkg.go.dev/github.com/zxysilent/bbolt)
[![Releases](https://img.shields.io/github/release/zxysilent/bbolt/all.svg?style=flat-square)](https://github.com/zxysilent/bbolt/releases)
[![LICENSE](https://img.shields.io/github/license/zxysilent/bbolt.svg?style=flat-square)](https://github.com/zxysilent/bbolt/blob/main/LICENSE)

**This repository is [zxysilent][owner]'s fork of [etcd-io/bbolt][upstream]**, an
embedded key/value store for Go. The fork is distributed as the Go module
[`github.com/zxysilent/bbolt`][module] and tracks upstream bbolt, with additional
changes maintained by zxysilent.

bbolt itself is a fork of [Ben Johnson's][gh_ben] [Bolt][bolt] key/value store, and
keeps the Bolt API and file format unchanged.

Full documentation of the underlying project (transactions, buckets, cursors, backup,
statistics, read-only mode, tuning, ...) lives in the upstream
[bbolt README][upstream_readme]; the API reference is on [pkg.go.dev][module].

## Usage

### Install the library

To start using this fork, install Go and run `go get`:

```sh
$ go get github.com/zxysilent/bbolt@latest
```

This will retrieve the library and update your `go.mod` and `go.sum` files.

### Install the bbolt command line utility

Install it into your `$GOBIN` path, which defaults to `$GOPATH/bin` or `$HOME/go/bin`
if the `GOPATH` environment variable is not set:

```sh
$ go install github.com/zxysilent/bbolt/cmd/bbolt@latest
```

Or run it without installing:

```sh
$ go run github.com/zxysilent/bbolt/cmd/bbolt@latest
```

### Use it in your code

```go
package main

import (
	"log"

	bolt "github.com/zxysilent/bbolt"
)

func main() {
	// Open the my.db data file in your current directory.
	// It will be created if it doesn't exist.
	db, err := bolt.Open("my.db", 0600, nil)
	if err != nil {
		log.Fatal(err)
	}
	defer db.Close()

	// Read-write transaction: create a bucket and store a key/value pair.
	if err := db.Update(func(tx *bolt.Tx) error {
		b, err := tx.CreateBucketIfNotExists([]byte("MyBucket"))
		if err != nil {
			return err
		}
		return b.Put([]byte("answer"), []byte("42"))
	}); err != nil {
		log.Fatal(err)
	}

	// Read-only transaction: retrieve the value.
	if err := db.View(func(tx *bolt.Tx) error {
		log.Printf("answer = %s", tx.Bucket([]byte("MyBucket")).Get([]byte("answer")))
		return nil
	}); err != nil {
		log.Fatal(err)
	}
}
```

For everything else (transactions in depth, key/value iteration, nested buckets,
database backups, statistics, read-only mode, memory tuning, ...) refer to the
upstream [bbolt documentation][upstream_readme].

## Acknowledgements

* Thanks to [Ben Johnson][gh_ben] for designing and developing [Bolt][bolt], the
  original embedded key/value store this project descends from.
* Thanks to the [etcd-io][etcd_io] maintainers and all contributors of
  [bbolt][upstream], which this fork is based on.

[owner]: https://github.com/zxysilent
[module]: https://pkg.go.dev/github.com/zxysilent/bbolt
[upstream]: https://github.com/etcd-io/bbolt
[upstream_readme]: https://github.com/etcd-io/bbolt#readme
[etcd_io]: https://github.com/etcd-io
[gh_ben]: https://github.com/benbjohnson
[bolt]: https://github.com/boltdb/bolt
