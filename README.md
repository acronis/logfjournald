# logf Appender and Encoder for systemd Journal

[![Go Reference](https://pkg.go.dev/badge/github.com/acronis/logfjournald.svg)](https://pkg.go.dev/github.com/acronis/logfjournald)
[![Build Status](https://github.com/acronis/logfjournald/actions/workflows/go.yml/badge.svg)](https://github.com/acronis/logfjournald/actions/workflows/go.yml)

Package `logfjournald` provides `logf` Appender and Encoder for systemd Journal. It supports structured logging and bulk operations with zero allocs.

## Example

The following example creates the new `logf` logger with `logfjournald` appender.

```go
package main

import (
    "runtime"

    "github.com/acronis/logf"
    "github.com/acronis/logfjournald"
)

func main() {
    // Create journald Appender with default journald Encoder.
    appender, appenderClose := logfjournald.NewAppender(logfjournald.NewEncoder.Default())
    defer appenderClose()

    // Create ChannelWriter with journald Encoder.
    writer, writerClose := logf.NewChannelWriter(logf.ChannelWriterConfig{
        Appender: appender,
    })
    defer writerClose()

    // Create Logger with ChannelWriter.
    logger := logf.NewLogger(logf.LevelInfo, writer)

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
