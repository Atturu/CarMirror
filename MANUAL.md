# Backseat — Manual

An offline video player that lives on your iPhone and fills the car screen through your
AirPlay mirror dongle. This manual covers install, the in-car routine, every control, what
happens with YouTube and streaming apps, and what to do when something goes wrong.

---

## 1. What it is, and what it isn't

**It is** a player for video files stored on your phone. You add files once, at home, on
Wi-Fi. From then on they play instantly with no signal, no account, and nothing leaving the
handset.

**It isn't** a streaming client. It has no YouTube integration, no search, no network calls
of any kind after the first load. That's deliberate — it's the reason it can't phone home,
and it's why it works in a tunnel. Section 7 covers what to do when you actually want
YouTube.

The whole app is five static files. If you ever want to check that it isn't talking to
anything, open `index.html` in a text editor. There are no dependencies and no minified
blobs to hide in.

---

## 2. One-time setup

You need to serve the files over HTTPS once so iOS will install it properly. GitHub Pages is
free and takes about five minutes.

1. Create a new **public** repo on GitHub.
2. Upload all five files to the root: `index.html`, `sw.js`, `manifest.webmanifest`,
   `icon-180.png`, `icon-512.png`.
3. Repo **Settings → Pages**. Under Source pick branch `main`, folder `/ (root)`. Save.
4. Wait a minute, then copy the URL it gives you (`https://yourname.github.io/reponame/`).
5. On the iPhone, open that URL in **Safari**. It has to be Safari — Chrome and Firefox on
   iOS can't install Home Screen apps.
6. Tap the Share button, scroll down, tap **Add to Home Screen**, then **Add**.
7. Close Safari. Open **Backseat** from the new Home Screen icon. You'll see the empty
   library. That first launch caches everything for offline use.

Also do this once, in iOS Settings:

- **Settings → Display & Brightness → Auto-Lock → Never** (or 5 minutes). The app holds a
  wake lock while playing, but this is belt and braces.
- Turn **off Low Power Mode** when you're going to mirror. It throttles the Wi-Fi radio the
  dongle depends on.

### If you'd rather not publish anything

On a laptop on your home Wi-Fi, `cd` into the folder and run `python3 -m http.server 8000`,
then open `http://<laptop-ip>:8000` on the phone. Everything works except Add to Home
Screen, which iOS gates behind HTTPS. You'll be using it as a Safari tab, with the address
bar eating the top of the car screen.

---

## 3. First run: adding your first video

Do this at home, plugged in, not in the car.

1. Open Backseat. The library is empty and says so.
2. Tap **Add videos** (top right, amber).
3. iOS opens its picker. Tap **Browse** at the bottom for the Files app, or the Photos tab
   if the video is in your camera roll. You can select several at once.
4. Tap **Open**. A message says "Adding 3 videos…".
5. Watch the tiles appear one at a time. For each file the app quietly seeks about 12% in,
   grabs a frame for the poster image, reads the duration, and copies the file into the
   phone's local database. A two-hour movie takes roughly 10–30 seconds.
6. When it says **Ready**, look at the top bar. It now reads something like
   `3 videos · 5.2 GB on device`.

That's it. Those videos are now on the phone permanently. Close the app, reboot, go to
airplane mode — they're still there.

**If a tile says "This session only"** in amber, iOS refused to save that file because
storage is tight. It'll play right now but disappears when you close the app. Delete
something else and re-add it.

**If a tile shows a big letter instead of a poster frame**, thumbnail extraction failed.
Harmless — it plays fine. Some HEVC files won't let Safari seek before they're fully loaded.

---

## 4. The in-car routine

Once set up, this is the whole thing, about twenty seconds:

1. Get in, plug the phone into the charger. Mirroring plus a lit screen drains fast.
2. **Rotate the phone to landscape and lock rotation** — swipe down for Control Centre, tap
   the padlock-with-arrow icon so it's *off*, turn the phone sideways, then turn the lock
   back *on*. If you skip this the car image will spin every time you pick the phone up.
3. Control Centre → **Screen Mirroring** → pick your dongle. The car screen goes to your
   Home Screen.
4. Tap the **Backseat** icon. It opens fullscreen with no address bar.
5. Tap a tile. It plays, resumes where you stopped, and the controls fade out after about
   four seconds.

Set the phone face-down or in a cradle after that. You don't need to look at it — everything
is on the car screen, and the phone's own display is just mirroring the same thing.

**Audio** normally travels with the mirror through the dongle. Some cheaper dongles send
video over Wi-Fi and expect audio over Bluetooth separately; if you get picture but silence,
pair the phone to the car over Bluetooth as well.

**Volume** is the phone's side buttons or the car's knob. There is deliberately no on-screen
slider — Safari on iOS ignores any attempt to set volume from JavaScript, so a slider would
be a button that does nothing.

---

## 5. Player controls

Tap the video once to hide the controls, tap again to bring them back. They also come back
automatically whenever you pause.

| Control | Where | What it does |
|---|---|---|
| **Library** | top left | Saves your position and goes back to the grid |
| Title | top centre | The file you're watching |
| **Subtitles** | top right | Loads a subtitle file, or turns off the one that's loaded |
| **Speed** | top right | Opens the options panel |
| **10** ↺ | bottom left | Back ten seconds |
| **Play / Pause** | bottom centre, amber | The big one you can hit without aiming |
| **30** ↻ | bottom right | Forward thirty seconds |
| Scrub bar | bottom | Drag anywhere along it. The whole strip is a target, not just the dot |
| Left number | bottom | Elapsed |
| Right number | bottom | Remaining, counting down |

The scrub bar is deliberately about 64px tall even though the visible line is thin. On a
bumpy road you don't have to be precise.

### The options panel (Speed)

- **Playback speed** — 0.75× through 2×. Your choice is remembered across videos and
  relaunches.
- **Fit to screen** — *Fit whole frame* letterboxes and shows everything. *Fill screen*
  crops to eliminate black bars, useful for 4:3 or 2.35:1 content on a 16:9 head unit.
- **Done** closes the panel.

### Resuming

Any video you watched past three seconds and stopped before the last ten seconds remembers
its position. You'll see an amber line across the bottom of its poster in the library, and a
"Picking up where you left off" note when you open it. Watch a video to the end and its
position resets to zero.

---

## 6. Subtitles

1. Get the `.srt` or `.vtt` file onto the phone — AirDrop it, email it to yourself, or drop
   it in iCloud Drive. Save it in Files somewhere you can find it.
2. Start the video, tap **Subtitles**, pick the file.
3. `.srt` is converted to WebVTT automatically. The button turns amber and captions appear.

The subtitle is remembered for that specific video, so next time it loads by itself. Tap
**Subtitles** again to turn it off and forget it.

If captions load but show mojibake, the file isn't UTF-8. Open it in a text editor and
re-save it as UTF-8.

---

## 7. YouTube and streaming apps

This is the part that surprises people, so here it is plainly.

### YouTube: you don't need Backseat at all

Screen mirroring puts *whatever is on your phone* on the car display. So for YouTube, skip
Backseat entirely:

1. Mirror as usual (Control Centre → Screen Mirroring → your dongle).
2. Open the normal **YouTube app**.
3. Play a video and tap the fullscreen button.

It fills the car screen. This is exactly what the vendor app was doing for you, minus the
vendor app. That's the honest answer to "how do I run YouTube" — the dongle already does it,
because mirroring doesn't care which app is producing the pixels.

**Caveat:** this needs signal. YouTube streams; the phone has to have data. It'll stutter in
a dead zone and stop entirely in a tunnel. That's the whole reason Backseat exists — offline
video for the stretches with no bars.

### Can I put YouTube videos into Backseat for offline use?

Only for content you're allowed to have as a file:

- **Your own uploads.** YouTube Studio → your video → Download. That gives you an MP4 you
  can add to Backseat directly.
- **Creative Commons or explicitly downloadable content**, where the creator has offered a
  file.
- **Anything you already own** — your own recordings, ripped discs where that's legal where
  you live, purchased downloads.

**YouTube Premium's offline downloads won't work.** Those files are encrypted and locked
inside the YouTube app's sandbox. There is no way to export them, and no app running in
Safari could read them even if you got at them. If you have Premium, download in the YouTube
app and watch there — mirror it exactly as above, and it plays fine without signal. Premium
downloads plus mirroring is genuinely the best offline YouTube setup on iPhone.

Pulling arbitrary videos off YouTube as files is against their terms of service, so I'd
point you at Premium rather than a downloader.

### Netflix, Disney+, Prime Video, Apple TV+

These will show a **black screen with working audio** over an AirPlay mirror dongle. That's
not a bug in your dongle and not something any app can fix. Those services enforce HDCP
copy protection, iOS honours it, and a mirror to a non-certified receiver fails the check
so the video layer is blanked.

Your options are: a factory head unit with certified CarPlay (most still block video), an
HDMI input with an HDCP-compliant path, or download-and-watch on the phone itself. There
isn't a software workaround, and anything advertising one is either lying or doing something
you shouldn't install.

Notably, **YouTube does not enforce HDCP**, which is why it mirrors fine and Netflix
doesn't.

---

## 8. Getting video into the right format

iOS decodes **H.264** and **HEVC** inside **.mp4 / .m4v / .mov**. That's the list. MKV, AVI,
WMV, VP9, and DTS audio will not play, and no web app can change that because Safari doesn't
expose any other decoder.

If Backseat says it can't decode a file, convert it:

```bash
# General purpose, good quality, wide compatibility
ffmpeg -i input.mkv -c:v libx264 -crf 20 -preset slow \
       -c:a aac -b:a 192k -movflags +faststart output.mp4

# Already H.264 inside an MKV? Just repackage — takes seconds, no quality loss
ffmpeg -i input.mkv -c copy -movflags +faststart output.mp4

# Shrink a big file for a small car screen (720p is plenty)
ffmpeg -i input.mp4 -vf scale=-2:720 -c:v libx264 -crf 23 \
       -c:a aac -b:a 160k -movflags +faststart output.mp4
```

Prefer a GUI? **HandBrake**, preset **Apple 1080p30 Surround**, does the same job.

Try the second command first. A lot of MKVs are already H.264 and just need the container
swapped, which is instant and lossless.

`-movflags +faststart` moves the index to the front of the file. Without it, playback can
take several seconds to begin.

---

## 9. Managing the library

- **Edit** (top bar) puts a red × on every tile. Tap one to delete that video and its saved
  position. Tap **Done** when finished.
- The top bar shows the video count and how much space the library occupies.
- Deleting from Backseat doesn't touch the original in Files or Photos.

Safari allows a Home Screen app a few gigabytes and *can* evict it if the phone runs short
on space. The app asks iOS not to, via `navigator.storage.persist()`, but that's a request
rather than a promise. **Keep your originals somewhere.** Treat the phone library as a
convenient copy, not the only copy.

---

## 10. Troubleshooting

| What you see | What's happening | Fix |
|---|---|---|
| Black car screen, audio still playing, Netflix/Disney+ | HDCP copy protection | Not fixable. See section 7 |
| Black car screen with Backseat specifically | iOS tried to route the video separately from the mirror | The `x-webkit-airplay="deny"` and `disableremoteplayback` attributes on the video tag prevent this. If you edited `index.html`, put them back |
| "iPhone can't decode this file" | Wrong codec or container | Convert it — section 8 |
| Safari address bar eating the top of the car screen | You opened a bookmark, not the Home Screen app | Launch it from the Home Screen icon |
| Car image rotates when you pick the phone up | Rotation not locked | Section 4, step 2 |
| Screen dims or locks mid-film | Wake lock lost, or iOS Auto-Lock | Set Auto-Lock to Never; keep the app in the foreground |
| Mirror keeps dropping out | Dongle Wi-Fi, or Low Power Mode throttling the radio | Turn off Low Power Mode, keep the phone charging, keep it out of the glovebox |
| Library empty after a few weeks | iOS evicted the storage under space pressure | Re-add from your originals; free up space on the phone |
| Tile shows a letter, no poster image | Thumbnail extraction failed on that codec | Cosmetic only, it still plays |
| Subtitles show garbled characters | File isn't UTF-8 | Re-save the `.srt` as UTF-8 |
| Video plays but stutters | Very high bitrate 4K source | Downscale to 720p — third ffmpeg command in section 8 |
| App won't open with no signal | It was never opened from the Home Screen icon while online | Open it once on Wi-Fi so the service worker can cache the shell |

---

## 11. Privacy

The app makes exactly one kind of network request: fetching its own five files, once, the
first time you open it. After that the service worker serves everything from cache and there
is no code path that contacts a server. No analytics, no crash reporting, no account, no
device ID.

Your videos are held in the browser's IndexedDB, sandboxed to this app on this phone.
Deleting the Home Screen icon deletes them along with it.

---

## 12. Safety

This is built for passengers and parked cars. In most jurisdictions a moving vehicle with
video visible from the driver's seat is a fineable offence, and a good number of factory
head units block video above a few km/h for exactly that reason. Your mirror dongle won't,
because it doesn't know the car's speed — so that judgement is on you.
