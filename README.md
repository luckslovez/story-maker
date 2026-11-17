# story-maker

Turns photos and videos into social posts you can publish later. You point it at folders of stills and video of any length. It splits the video into moments that stand on their own, and groups each moment with the photos that belong to it. The result is a small set of finished posts: a Reel, a Story, or a carousel.

Two modules, with a folder as the boundary between them:

| Module | What it does | Status |
| --- | --- | --- |
| Compose | Reads photos and videos. Writes post folders. | This is the program to build. |
| Publish | Schedules, uploads, and attaches a platform sound. | Later. It reads a finished post folder and does not cut media. |

The program is not built yet. Compose does not post anywhere.

The module contracts are `story-maker.sdd` and the `.sdd` files in `src/`. Compose is a Rust terminal UI built with ratatui and crossterm. ffmpeg finds the scene cuts and encodes what you save. A model is available only when the advisor is on, and that call goes to whatever OpenAI-compatible server you name.

## Compose

```sh
story-maker \
  --clips /path/to/clips \
  --photos /path/to/photos \
  --out /path/to/posts
```

The folders and the advisor are remembered in a settings file outside this repo. The terminal has a settings screen that edits and saves it. A flag overrides the saved value for one launch and does not change the file.

On macOS the file is `~/Library/Application Support/story-maker/settings.toml`. On Linux it is `~/.config/story-maker/settings.toml`. `--settings` points at a different file, and that is the one the screen writes. The file is readable only by you, because it can hold an API key.

| Field | What it stores |
| --- | --- |
| `clips` | Video of any length. Mp4 or mov. |
| `photos` | Stills. Jpeg, png, or heic. |
| `out` | Where post folders are written. |
| `advise` | Whether the suggestion key is available. |
| `api_base` | The provider's OpenAI-compatible root, the URL that has `/chat/completions` under it. |
| `model` | That provider's model name. |
| `api_key` | Sent as a bearer token. Leave it empty for a server that does not check a key. |

Either media folder may be empty. A path that does not exist is an error that names it. Both media folders empty is an error. If a folder field is blank, the program opens settings before it scans. Nothing is read from this repo.

A post is one situation. Its cards share a subject, a place, or a continuous action, and they are in the order a viewer should see them. A long video can yield several posts, one per moment. The rest of the file stays unused. Unrelated clips are not combined to fill out a carousel.

```text
posts/
  01-chorus-look/
    kind            # reel, story, or carousel
    caption.txt
    01/
      post.mp4      # or post.jpg for a photo
      slice.txt     # video cards only
    02/             # further cards, stories and carousels only
  02-crowd-hit/
```

A Reel is one card. A Story is one to five cards. A carousel is two to ten slides. A photo card is `post.jpg` and has no `slice.txt`.

`slice.txt` names the source file, the start and end inside it, and whatever note you typed. A Story card and a carousel video slide are at most 60 seconds, and shorter when the moment is shorter. A Reel is as long as its moment, within the length Instagram will accept. Story cards and Reels are 9:16. Every slide in a carousel uses the frame of the first slide, so a 4:5 opening keeps the video slides 4:5 too. `post.mp4` is H.264 and keeps the audio that was recorded with the picture.

The terminal lists every scene in the clips, plus the photos. You trim a scene, group the pieces into one post, pick Reel, Story, or carousel, and write the caption. Saving encodes that post. One key writes or removes an empty `chosen` file on a saved post. Another opens it in the Mac viewer. The terminal does not play video.

Turn the advisor on from the settings screen and fill in the server and the model. One key then asks for a suggestion: a grouping, a kind, and a caption. Accepting loads that suggestion into the post you have not saved yet. You can change it. It is encoded only when you save. Dismissing it leaves your post as it was. If the server or the model is blank, that key opens settings and makes no request. With the advisor off, the program makes no network call.

The model has to accept images. If the call fails, the suggestion is dropped and your unsaved post stays as it was. `STORY_MAKER_API_BASE`, `STORY_MAKER_MODEL`, and `STORY_MAKER_API_KEY` override the file for one launch. A key that came only from the environment is not written back when you save.

## macOS

Compose runs on the Mac that has the files. Install Rust and ffmpeg there. Nothing is installed on a server. Quote a path that contains a space:

```sh
story-maker \
  --clips "/Users/you/Band Media/Clips" \
  --photos "/Users/you/Band Media/Photos" \
  --out "/Users/you/Band Media/Posts"
```

iPhone photos (`.heic`) and videos (`.mov`) are fine as inputs. If a folder lives in iCloud, download the files first. A cloud placeholder fails the run. An external drive works while it is plugged in.

## Publish, later

Scheduling, auto-upload, and attaching the band's DistroKid song belong here. That module takes a post folder as it was written and does not re-cut the video. The song is added from Instagram's library at publish time, which is what gives the post the official label. Masters stay out of Compose.

## What stays out of the repo

This repo can be public. It holds these instructions and, later, the program. It does not hold clips, photos, post folders, captions, or account tokens. Pass the folders at launch.
