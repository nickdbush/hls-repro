# HLS.js / Firefox decode error

This is a minimal reproduction for a bug in showing HLS video in Firefox using the hls.js library. Under specific circumstances, the video never loads due to a decoding error and calling `hls.recoverMediaError()` causes an endless loading loop.

- **HLS.js**: 1.6.2
- **MacOS**: Sequoia 15.4
- **Firefox**: 137.0.1 (aarch64)

## Reproduction steps

1. Checkout this Git repo.
1. Start a local server with `npx http-server --cors` or similar.
1. Open `http://localhost:8080` in Firefox and observe the console output (see _console.txt_ for the logs from my machine).

## Notes

- Removing the second video (with or without the discontinuity) makes the video load ✅.
- Removing the first video (with or without the discontinuity) makes the video load ✅.
- Removing the last segment from video one (`/media/v1_s5.mp4`) makes the video load ✅.
- Removing random segments from video one also makes the video load (e.g. `/media/v1_s3.mp4`) ✅.

## Console output (non-debug mode)

```
Media resource blob:http://localhost:8080/033587e3-d7d8-45db-9805-ad47b1e08d0f could not be decoded.
```

```
DOMException: An attempt was made to use an object that is not, or is no longer, usable
    appendExecutor buffer-controller.ts:1643
    execute buffer-controller.ts:845
    executeNext buffer-operation-queue.ts:102
    append buffer-operation-queue.ts:35
    append buffer-controller.ts:1727
    onBufferAppending buffer-controller.ts:933
    emit index.js:182
    emit hls.ts:382
    trigger hls.ts:393
    _bufferInitSegment stream-controller.ts:1492
    _bufferInitSegment stream-controller.ts:1488
    _handleTransmuxComplete stream-controller.ts:1205
    handleTransmuxComplete transmuxer-interface.ts:447
    onWorkerMessage transmuxer-interface.ts:384
    TransmuxerInterface transmuxer-interface.ts:92
    _handleFragmentLoadProgress stream-controller.ts:812
    progressCallback base-stream-controller.ts:489
    result base-stream-controller.ts:934
    promise callback*_doFragLoad base-stream-controller.ts:932
    _loadFragForPlayback base-stream-controller.ts:492
    loadFragment base-stream-controller.ts:467
    loadFragment stream-controller.ts:409
    doTickIdle stream-controller.ts:387
    doTick stream-controller.ts:234
    tick task-loop.ts:109
    completeInitSegmentLoad base-stream-controller.ts:693
    _loadInitSegment base-stream-controller.ts:670
    promise callback*_loadInitSegment base-stream-controller.ts:623
    loadFragment stream-controller.ts:402
    doTickIdle stream-controller.ts:387
    doTick stream-controller.ts:234
    tick task-loop.ts:109
    onLevelLoaded stream-controller.ts:730
    emit index.js:203
    emit hls.ts:382
    trigger hls.ts:393
    handlePlaylistLoaded playlist-loader.ts:747
    handleTrackOrLevelPlaylist playlist-loader.ts:563
    onSuccess playlist-loader.ts:360
    readystatechange xhr-loader.ts:232
    openAndSendXhr xhr-loader.ts:153
    loadInternal xhr-loader.ts:125
    load xhr-loader.ts:83
    load playlist-loader.ts:393
    onManifestLoading playlist-loader.ts:158
    emit index.js:203
    emit hls.ts:382
    trigger hls.ts:393
    loadSource hls.ts:520
    <anonymous> (index):14
```

## `Hls.Events.ERROR` data

```jsonc
{
  "type": "mediaError",
  "parent": "main",
  "details": "bufferAppendError",
  "sourceBufferName": "video",
  "frag": {
    "_byteRange": null,
    "_url": "http://localhost:8080/media/v2_init.mp4",
    "_stats": {
      "aborted": false,
      "loaded": 868,
      "retry": 0,
      "total": 868,
      "chunkCount": 0,
      "bwEstimate": null,
      "loading": {
        "start": 259,
        "first": 263,
        "end": 263
      },
      "parsing": {
        "start": 263,
        "end": 263
      },
      "buffering": {
        "start": 263,
        "first": 0,
        "end": 263
      }
    },
    "_streams": null,
    "base": {
      "url": "http://localhost:8080/stream.m3u8"
    },
    "relurl": "/media/v2_init.mp4",
    "stats": {
      "aborted": false,
      "loaded": 868,
      "retry": 0,
      "total": 868,
      "chunkCount": 0,
      "bwEstimate": null,
      "loading": {
        "start": 259,
        "first": 263,
        "end": 263
      },
      "parsing": {
        "start": 263,
        "end": 263
      },
      "buffering": {
        "start": 263,
        "first": 0,
        "end": 263
      }
    },
    "_decryptdata": null,
    "_programDateTime": null,
    "_ref": null,
    "rawProgramDateTime": null,
    "tagList": [["DIS"]],
    "duration": 0,
    "sn": "initSegment",
    "type": "main",
    "loader": null,
    "keyLoader": null,
    "level": 0,
    "cc": 1,
    "start": 48.715382,
    "playlistOffset": 48.715382,
    "data": {
      /* ... */
    },
    "bitrateTest": false,
    "title": null,
    "initSegment": null,
    "urlId": 0
  },
  "part": null,
  "chunkMeta": {
    "level": 0,
    "sn": 5,
    "part": -1,
    "id": 1,
    "size": 2728070,
    "partial": false,
    "transmuxing": {
      "start": 453,
      "executeStart": 261,
      "executeEnd": 261,
      "end": 453
    },
    "buffering": {
      "audio": {
        "start": 0,
        "executeStart": 0,
        "executeEnd": 0,
        "end": 0
      },
      "video": {
        "start": 453,
        "executeStart": 453,
        "executeEnd": 0,
        "end": 0
      },
      "audiovideo": {
        "start": 0,
        "executeStart": 0,
        "executeEnd": 0,
        "end": 0
      }
    }
  },
  "error": {},
  "err": {},
  "fatal": true
}
```
