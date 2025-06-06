# Multi Flow - Concurrent Streaming Proxy

A CLI tool that accelerates media streaming by parallelizing range requests. Acts as a middleware to convert single-threaded streaming into multi-threaded transfers.

## Problem Solving

1. **Slow Streaming Playback**: Media players (mpv/VLC) use single-threaded streaming, unable to utilize full bandwidth.
2. **Multi-WAN Underutilization**: Users with multi-link ISPs can't benefit from aggregated bandwidth during streaming.

## How It Works

1. Starts a local HTTP server as proxy
2. Creates mapped URL for your media resource
3. Splits every incoming Range request into parallel chunks
4. Reassembles chunks in-order for seamless playback

## Features

- 🚀 Parallel chunk downloading
- 🔄 Automatic retry mechanism (5 retries/chunk)
- 🔗 Connection pooling & reuse
- 🧩 Dynamic chunk sizing (default: 1MB chunks)
- 🔄 Follows redirects (max 3 hops)
- 📦 Smart filename detection

## Installation

```bash
pip install mflow
```

## Usage

1. Start the proxy server:
```bash
mflow --port 8080 --connections 8
```

2. Use the mapped URL in your player:
```
http://localhost:8080/stream?url={YOUR_MEDIA_URL}
```

3. Play through any player supporting HTTP streaming:
```bash
mpv "http://localhost:8080/stream?url=https://example.com/video.mp4"
```

## Configuration Options

| Option | Description | Default |
|--------|-------------|---------|
| `-p/--port` | Listening port | 80 |
| `-c/--connections` | Max parallel connections per stream | 4 |
| `-s/--chunk-size` | Download chunk size (e.g., 1M, 512K) | 1M |
| `-r/--retry` | Max retries per chunk | 3 |
| `--log-level` | Logging verbosity | INFO |

## Example

Start server with 16 connections and 2MB chunks:
```bash
mflow --port 9000 --connections 16 --chunk-size 2M
```

Play 4K video through proxy:
```bash
mpv "http://localhost:9000/stream?url=https://media.example.com/4k-demo.mkv"
```

## Notes

1. Server acts as pass-through - no data is stored locally
2. Supports most HTTP servers (including HTTPS sources)
3. Connection pool is shared between all streaming sessions
4. Terminate with Ctrl+C when not needed

For debugging use:
```bash
mflow --log-level DEBUG
```