# WebRTC For Node.js and Electron ( with WebSocket)

![Linux CI Build](https://github.com/krystofwoldrich-agent/node-datachannel/workflows/Build%20-%20Linux/badge.svg) ![Windows CI Build](https://github.com/krystofwoldrich-agent/node-datachannel/workflows/Build%20-%20Win/badge.svg) ![Mac x64 CI Build](https://github.com/krystofwoldrich-agent/node-datachannel/workflows/Build%20-%20Mac%20x64/badge.svg) ![Mac M1 CI Build](https://github.com/krystofwoldrich-agent/node-datachannel/workflows/Build%20-%20Mac%20M1/badge.svg)

> [!IMPORTANT]
> This is a fork of [murat-dogan/node-datachannel](https://github.com/murat-dogan/node-datachannel), published as `@krystofs/node-datachannel`. It is based on upstream node-datachannel v0.33.2 (`29fde0d`) and pins libdatachannel upstream `master` at [`51085b8de4e6185dc019e3705c88b87933d7c3f6`](https://github.com/paullouisageneau/libdatachannel/commit/51085b8de4e6185dc019e3705c88b87933d7c3f6). A one-line compatibility patch makes that revision's `std::seed_seq` construction compile with the supported GCC 10 baseline.

- Lightweight
  - No need to deal with WebRTC stack!
  - Small binary sizes (~8MB for Linux x64)
- Type infos for Typescript
- Integrated WebSocket Client & Server Implementation

This project is Node.js bindings for [libdatachannel](https://github.com/paullouisageneau/libdatachannel) library.

## Install

```sh
npm install @krystofs/node-datachannel
```

## Supported Platforms

`@krystofs/node-datachannel` targets N-API version 8 and supports Node.js **v18.20 and above** (including Node.js 20, 22, and 24+). Prebuilt binaries are distributed automatically via platform-specific npm packages (`optionalDependencies`).

| Architecture | Linux (glibc) | Linux (musl / Alpine) | macOS (Apple Silicon / Intel) | Windows | Android (Termux / Bionic) |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **x64** | `@krystofs-node-datachannel/linux-x64-gnu` | `@krystofs-node-datachannel/linux-x64-musl` | `@krystofs-node-datachannel/darwin-x64` | `@krystofs-node-datachannel/win32-x64-msvc` | - |
| **arm64** | `@krystofs-node-datachannel/linux-arm64-gnu` | `@krystofs-node-datachannel/linux-arm64-musl` | `@krystofs-node-datachannel/darwin-arm64` | `@krystofs-node-datachannel/win32-arm64-msvc` | `@krystofs-node-datachannel/android-arm64` |

## Electron

`@krystofs/node-datachannel` supports Electron.

Please check [electron demo](/examples/electron-demo)

## WebRTC Polyfills

WebRTC polyfills to be used for libraries like `simple-peer`.

Please check [here for more](/src/polyfill)

### web-platform-tests

Please check actual situation [here](/test/wpt-tests/)

## WebSocket Client & Server

Integrated WebSocket Client & Server is available, which can be used separately or for signaling.

For an example usage, [check here](/examples/websocket)

## Example Usage

```js
import nodeDataChannel from '@krystofs/node-datachannel';

// Log Level
nodeDataChannel.initLogger('Debug');

// Integrated WebSocket available and can be used for signaling etc
// const ws = new nodeDataChannel.WebSocket();

let dc1 = null;
let dc2 = null;

let peer1 = new nodeDataChannel.PeerConnection('Peer1', {
  iceServers: ['stun:stun.l.google.com:19302'],
});

peer1.onLocalDescription((sdp, type) => {
  peer2.setRemoteDescription(sdp, type);
});
peer1.onLocalCandidate((candidate, mid) => {
  peer2.addRemoteCandidate(candidate, mid);
});

let peer2 = new nodeDataChannel.PeerConnection('Peer2', {
  iceServers: ['stun:stun.l.google.com:19302'],
});

peer2.onLocalDescription((sdp, type) => {
  peer1.setRemoteDescription(sdp, type);
});
peer2.onLocalCandidate((candidate, mid) => {
  peer1.addRemoteCandidate(candidate, mid);
});
peer2.onDataChannel((dc) => {
  dc2 = dc;
  dc2.onMessage((msg) => {
    console.log('Peer2 Received Msg:', msg);
  });
  dc2.sendMessage('Hello From Peer2');
});

dc1 = peer1.createDataChannel('test');

dc1.onOpen(() => {
  dc1.sendMessage('Hello from Peer1');
});

dc1.onMessage((msg) => {
  console.log('Peer1 Received Msg:', msg);
});
```

## Examples

Please check [examples](/examples/) folder

## Test

```sh
npm run test                  # Unit tests
node test/connectivity.js     # Connectivity
```

## Build

Please check [here](/BULDING.md)

## API Docs

Please check [docs](/API.md) page

## Contributing

Contributions are welcome!

## Thanks

Thanks to [Streamr](https://streamr.network/) for supporting this project by being a Sponsor!
