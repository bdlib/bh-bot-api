# bh-bot-api

BeHappy Bot API server — a Telegram-Bot-API-compatible HTTP front-end
for the BeHappy messaging platform.

Forked from [tdlib/telegram-bot-api](https://github.com/tdlib/telegram-bot-api)
with the only structural change being that the underlying TDLib has been
swapped for [bdlib/bhlib](https://github.com/bdlib/bhlib), which speaks
the MVSy protocol instead of MTProto and connects to BeHappy servers.

## Architecture

```
HTTP Bot client (aiogram / pyrogram / etc.)
        |  HTTP (port 8081)
        v
   bh-bot-api  ── translates Bot API ──► bhlib
        |                                 |
        |                                 |  MVSy / TLS (port 10443)
        |                                 v
        └─── webhooks back to client ── BeHappy server
```

## Build

```sh
git clone --recursive https://github.com/bdlib/bh-bot-api.git
cd bh-bot-api
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
cmake --build . --target behappy-bot-api -j$(nproc)
```

### Dependencies

- OpenSSL
- zlib
- gperf
- CMake 3.10+
- C++17 compiler (GCC 7+, Clang 5+)

## Usage

```sh
./behappy-bot-api \
  --api-id=1 \
  --api-hash=behappy \
  --http-port=8081 \
  --dir=./data \
  --local
```

### Pointing at a different deployment

`bh-bot-api` connects to `mvsy.behappy.rest:10443` by default. Set the
`BH_MVSY_HOST` env var before launch to override:

```sh
BH_MVSY_HOST=localhost ./behappy-bot-api --api-id=1 --api-hash=behappy --local
```

## Docker

```sh
docker build -t bh-bot-api:latest .
docker run --rm -p 8081:8081 \
  -e BH_MVSY_HOST=mvsy.behappy.rest \
  -v $(pwd)/data:/data \
  bh-bot-api:latest \
  --api-id=1 --api-hash=behappy --http-port=8081 --dir=/data --local
```

## License

Boost Software License 1.0 — same as upstream tdlib/telegram-bot-api.
