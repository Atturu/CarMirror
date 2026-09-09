# Backseat

An offline video player for an iPhone mirrored to a car screen. No account, no server, no
telemetry. Your videos are stored on the phone and never leave it.

Files: `index.html`, `sw.js`, `manifest.webmanifest`, `icon-180.png`, `icon-512.png`.
That's the whole thing. There is no build step and no dependencies.

---

## Getting it onto the phone

It has to be served over HTTPS once, so iOS will let you install it to the Home Screen and
cache it for offline use. Any static host works. GitHub Pages is the least effort:

1. Make a new public repo and upload all five files to the root.
2. Settings → Pages → Source: `main` branch, `/ (root)`. Wait a minute for the URL.
3. Open that URL in **Safari** on the iPhone. It must be Safari — Chrome on iOS can't
   install Home Screen apps.
4. Share button → Add to Home Screen.
5. Open it from the Home Screen icon at least once with a connection. After that it works
   with the phone in airplane mode.

Launching from the Home Screen icon matters: that's what gives you the fullscreen view with
no Safari address bar, which is what you want filling the car display.

If you'd rather not publish anything, run `python3 -m http.server 8000` on a laptop on your
home Wi-Fi and open `http://<laptop-ip>:8000` on the phone. You lose Home Screen install
(needs HTTPS), but everything else works.

## Using it

- **Add videos** pulls from Files or Photos. Each one gets a poster frame and is copied into
  the phone's local database, so the library survives relaunches.
- Tap a tile to play. It remembers where you stopped and offers to resume.
- Tap the video to hide the controls. Tap again to bring them back.
- **Subtitles** takes a `.srt` or `.vtt`; `.srt` is converted automatically and remembered
  for that video.
- **Speed** also holds a fit toggle, for pillarboxed content on a wide head unit.
- **Edit** turns on the delete buttons.

## Things worth knowing before you load it up

**Formats.** iOS decodes MP4 / M4V / MOV with H.264 or HEVC. It will not play MKV, AVI,
WMV, or anything with VP9 or DTS audio, and no web app can change that — the decoder isn't
exposed. If a file refuses to load, remux or convert it first:

```
ffmpeg -i input.mkv -c:v libx264 -crf 20 -preset slow -c:a aac -b:a 192k -movflags +faststart output.mp4
```

HandBrake's "Apple 1080p30 Surround" preset does the same thing with a GUI.

**Volume** is the phone's hardware buttons or the car's knob. Safari ignores any attempt to
set volume from JavaScript on iOS, so there's deliberately no on-screen slider.

**Rotation.** Lock the phone to landscape before you mirror, or the car image will rotate
with the handset.

**AirPlay routing.** The `<video>` element is flagged `x-webkit-airplay="deny"` and
`disableremoteplayback`. That's the fix for the common dongle bug where iOS grabs the video
and streams it separately from the mirror, leaving you with a black car screen and audio in
the wrong place. Don't remove those attributes.

**Storage.** Safari gives a Home Screen app a few GB and can evict it if the phone gets
tight. The app calls `navigator.storage.persist()` to ask iOS not to, but that's a request,
not a guarantee. If a file is too big to save, it still plays for that session and the tile
says so. Keep the originals somewhere.

**Battery.** Mirroring plus a screen wake lock drains fast. Keep the phone on the charger.

**Motion.** Meant for passengers or a parked car. In a lot of places a video visible from
the driver's seat while moving is a ticket.
