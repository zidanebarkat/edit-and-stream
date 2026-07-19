# Edit & Stream Pipeline

All-in-one GitHub Actions workflow: **edit video + process audio + overlay + stream** to any platform.

## What it does

1. Takes a source video/stream URL
2. Edits video: cycles between N minutes of content and N minutes of black screen with overlay text
3. Processes audio: high-pitched voice + 60Hz buzzing background
4. Adds transparent frame overlay (optional)
5. Adds reaction image in webcam position (optional)
6. Adds fingerprint watermark + video ID text
7. Streams the result live to your platform

## Platforms

| Platform | RTMP URL (auto-filled) |
|---|---|
| Facebook | `rtmps://live-api-s.facebook.com:443/rtmp/` |
| TikTok | `rtmp://push.tiktokv.com/live/` |
| YouTube | `rtmp://a.rtmp.youtube.com/live2/` |
| Twitch | `rtmp://live.twitch.tv/app/` |
| Kick | `rtmp://fa723fc1b7b7.entrypoint.livekick.tv/app/` |
| Instagram | `rtmps://live-api-s.facebook.com:443/rtmp/` |
| Custom RTMP | Your own URL |

## Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `platform` | yes | — | Target platform |
| `action` | yes | `start` | `start` or `stop` |
| `source_url` | yes | — | Source video/stream URL |
| `rtmp_url` | no | auto | RTMP URL (auto-filled for known platforms) |
| `stream_key` | yes | — | Stream key |
| `video_id` | yes | — | Video ID (displayed as text overlay) |
| `fingerprint` | yes | — | Browser fingerprint string (watermark + anti-detect) |
| `overlay_text` | yes | — | Text shown during black screen segments |
| `reaction_image` | no | — | URL to reaction image (webcam spot) |
| `overlay_frame` | no | — | URL to transparent frame PNG |
| `video_mins` | no | `5` | Minutes of source video per cycle |
| `black_mins` | no | `5` | Minutes of black screen per cycle |
| `pitch` | no | `1.5` | Audio pitch multiplier (1.0 = normal) |
| `buzz_volume` | no | `0.15` | Background buzz volume (0.0 = off) |
| `cookies_b64` | no | — | Base64 cookies for YouTube etc |
| `self_reboot_token` | no | — | GitHub PAT for auto-restart |

## Quick start

1. Go to **Actions** > **Edit & Stream Pipeline** > **Run workflow**
2. Fill in:
   - `platform`: your target
   - `source_url`: the video to restream
   - `stream_key`: your platform stream key
   - `video_id`: unique identifier
   - `fingerprint`: your fingerprint string
   - `overlay_text`: text for black screen
3. Click **Run workflow**
4. To stop: run again with `action: stop`

## Video cycle example

With `video_mins=5` and `black_mins=3`:
- 0:00 - 5:00 → source video plays
- 5:00 - 8:00 → black screen with overlay text
- 8:00 - 13:00 → source video plays
- 13:00 - 16:00 → black screen with overlay text
- ... repeats

## Long streams

GitHub Actions has a 6-hour limit. To stream longer:
1. Provide a `self_reboot_token` (GitHub PAT with `repo` scope)
2. The workflow auto-restarts itself ~5.8 hours in
3. Creates an indefinite chain of 6-hour runs

## Audio effects

- **Pitch**: `asetrate=44100*pitch` raises the voice. `1.5` = noticeably higher, `2.0` = chipmunk
- **Buzz**: 60Hz sine wave mixed in. `0.15` = subtle hum, `0.3` = obvious buzz

## Stop stream

Run the workflow with `action: stop` — kills all FFmpeg processes on the runner.
