# logf Appender and Encoder for systemd Journal

[![GoDoc](https://godoc.org/github.com/acronis/logfjournald?status.svg)](https://godoc.org/github.com/acronis/logfjournald)
[![Build Status](https://travis-ci.org/acronis/logfjournald.svg?branch=master)](https://travis-ci.org/acronis/logfjournald)
[![Go Report Status](https://goreportcard.com/badge/github.com/acronis/logfjournald)](https://goreportcard.com/report/github.com/acronis/logfjournald)
[![Coverage Status](https://coveralls.io/repos/github/acronis/logfjournald/badge.svg?branch=master)](https://coveralls.io/github/acronis/logfjournald?branch=master)

Package `logfjournald` provides `logf` Appender and Encoder for systemd Journal. It supports structured logging and bulk operations with zero allocs.

## Example

The following example creates the new `logf` logger with `logfjournald` appender.

```go
package main

import (
    "os"
    "runtime"

    "github.com/acronis/logf"
    "github.com/acronis/logfjournald"
)

func newLogger() (*logf.Logger, logf.ChannelCloser) {
    c := logf.SetFormatterConfigDefaults(&logf.FormatterConfig{})

    channel := logf.NewBasicChannel(logf.ChannelConfig{
        Appender:      logfjournald.NewAppender(logfjournald.NewEncoder(c, logf.NewJSONTypeMarshallerFactory(c))),
        ErrorAppender: logf.NewWriteAppender(os.Stderr, logf.NewJSONEncoder(c)),
    })

    return logf.NewLogger(logf.LevelInfo.Checker(), channel), channel
}

func main() {
    logger, channel := newLogger()
    defer channel.Close()

    logger.Info("got cpu info", logf.Int("count", runtime.NumCPU()))
}
```

The JSON representation of the journal entry this generates:

```json
{
  "TS": "2018-11-01T07:25:18Z",
  "PRIORITY": "6",
  "LEVEL": "info",
  "MESSAGE": "got cpu info",
  "COUNT": "4",
}
```
