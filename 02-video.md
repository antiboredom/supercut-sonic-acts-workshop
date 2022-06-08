# Video

## Downloading videos with `yt-dlp`

Before we get into Videogrep, here's a brief intro to `yt-dlp`.

`yt-dlp` allows you to very easily download almost any video from the web. To install it, just run:

```bash
pip3 install yt-dlp
```

There are tons of things you can do with `yt-dlp`. I'm just going to cover the basics here, focusing on what I find the most useful for making supercuts.

### Basic usage

To download a video, just type `yt-dlp` and the URL of the video you want. This will work for YouTube, Vimeo, Twitter and _hundreds_ of other websites. For example:

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88"
```

_Note: I've surrounded the video URL in quotes to avoid problems that can occur if the URL contains special characters. This isn't always necessary, but I usually do it anyway._

You can also download an entire user, channel, playlist, or search query. For example this will download the entire White House channel (it will take a long time).

```bash
yt-dlp "https://www.youtube.com/c/shell/"
```

And this will download videos matching the search query "capitalism:"

```bash
yt-dlp "https://www.youtube.com/results?search_query=capitalism"
```

You can pretty much give yt-dlp any URL on YouTube, or any other video site.

### File formats

Websites like YouTube and Vimeo store videos in multiple file formats and sizes. By default, `yt-dlp` will download the highest quality video it can find, but frequently I like to work with smaller videos. To get a list of all available video formats simply add the `-F` option:

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88" -F
```

You can then select a file to download by passing the `-f` option along with an id (the number or characters in the first column). So if I want to download this video as a 1280x720 mp4, I would type:

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88" -f 22
```

_Note: some formats are video or audio only._

### Changing the video filename

By default `yt-dlp` will automatically name the downloaded video for you. This is useful, but sometimes it's convenient to name the file yourself. To do so, add the `-o` flag.

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88" -o shell.mp4
```

### Download subtitles

Finally, YouTube usually provides auto-generated subtitles for videos. These are fairly high quality, and are useful for a variety of reasons, including making supercuts!

To download subtitles, just add `--write-auto-sub`.

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88" --write-auto-sub
```

You can also combine this with `-f`, and `-o`, like so:

```bash
yt-dlp "https://www.youtube.com/watch?v=rZhJzTJFu88" --write-auto-sub -f 22 -o shell.mp4
```

This will download the video as a 1280x720 mp4 with the filename shell.mp4, along with it's subtitles in .vtt format.

### Updating

Always be sure that your version of yt-dlp is updated. To keep it fresh, use pip's update command:

```
pip3 install -U yt-dlp
```

---

## Videogrep

`videogrep` is a command line program that allows you to create supercuts of videos based audio transcriptions. Like `grep` but for video!

Videogrep uses the timestamps in subtitle files to make supercuts of videos. It will automatically look for a few different types of subtitle files, including `.srt` files (the most common subtitle format), `.vtt` files (a common web-based subtitle format), or specially formatted `.json` files that videogrep can generate itself (more on this later). 

*Note: the subtitle file must have the same name as the video file. So, if you have a video called `meta.mp4` you'd also need a subtitle file, in the same folder, called `meta.vtt` or `meta.srt`.*

We'll start using `.vtt` files that are provided with almost every YouTube video.

If you haven't already done so, go ahead and download a YouTube video with `yt-dlp`, and make sure to pass along the `--write-auto-sub` option to also download the video subtitle file.

Once you have a video to work with, we can use `videogrep` to make a supercut of it. First, install `videogrep` with `pip`:

```bash
pip3 install videogrep
```

### Usage

To use `videogrep` give it input video or video files with the `--input` argument, and a search term with the `--search` argument.


```bash
videogrep --input shell.mp4 --search 'emission'
```

This will save a video called `supercut.mp4` in the same folder that you ran the script from. The video will be made only of clips containing the word "emission".

To change the name of the output video, add the `--output` or `-o` option:

```bash
videogrep --input shell.mp4 --search 'emission' --output emission.mp4
```

Notice that the video doesn't just contain the search word "emission" but also a few words before and after the keyword. By default `videogrep` will try to match whole sentences or phrases. A "sentence" is determined by the transcript file associated with the video.

If you want to just cut to individual words you can change the `--search-type` argument from the default (`sentence`) to `fragment`:

```bash
videogrep --input meta.mp4 --search 'emission' --search-type fragment --output emission2.mp4
```

*Note: `--search-type fragment` requires that subtitle files have timestamps for each word. Frequently `.vtt` files will have these timestamps, but `.srt` files will not.*

You may notice that some of the timing seems a bit off. In this example, videogrep is using timestamps in the `.vtt` file that YouTube generated and sometimes these aren't 100% accurate. If you want to you can adjust how videogrep makes cuts with either the `--padding` argument, or the `--resyncsubs` argument.

`--padding SECS` will add some padding time to the start and end of every clip, and `--resyncsubs SECS` will shift the time of the entire subtitle file.

In this example, pushing everything back by about 1/10th of a second seems to increase the accuracy:

```bash
videogrep --input shell.mp4 --search 'emission' --search-type fragment --resyncsubs 0.1 --output emission3.mp4
```

### Frequent words and phrases

If you want to learn what the most common words and phrases inside a video are, you can use the `--ngrams N` argument. For example, to print out the most common single words in our Meta video:

```bash
videogrep --input shell.mp4 --ngrams 1
```

To print out the most common two word phrases:

```bash
videogrep --input shell.mp4 --ngrams 2
```

The output would be:

```bash
que three 7
our customers 7
by twenty 7
billion dollars 7
net zero 5
of our 5
energy and 5
low carbon 5
twenty twenty 5
transforming our 5
absolute emissions 4
...
```

Let's use this to make a video:

```bash
videogrep --input meta.mp4 --search 'billion dollars' --search-type fragment --resyncsubs 0.1 --output dollars.mp4
```

### Regular Expressions

Videogrep uses Python's regular expression engine for the searches you specify with the `--search` argument. Regular expressions are a long and complicated topic that I won't try to fully cover here, but I will provide a few useful pointers for getting started.

#### What is a "regular expression engine??"

A regular expression engine takes a set of characters (some of which have special meaning) and uses them to try to find matches in a text. The input you give the engine (in this case, videogrep) is called a "regular expression". The most simple type of regular expression is just a set of normal characters, like `ing`.

```bash
videogrep --input shell.mp4 --search 'ing' --search-type fragment
```

This will look for any word that contains the characters `ing`, regardless of where they appear in the word. For example `making`, `building` and `things`

In addition to just searching for characters chunks, regular expressions can also contain characters that aren't matched literally but have a special meaning.

For example:

* `.` is a wildcard that will match with any other character. The regular expression `h.t` will match "hot", "hit", "hat" and so on.
* `$` signifies the end of the word or sentence: `y$` will find anything that ends with the character "y".
* `^` signifies the start of a word or sentence
* `|` means "or" - it let's you search for multiple things.

Here's an example of matching multiple words with `|`:

```bash
videogrep --input shell.mp4 --search 'zero|dollars' --search-type fragment --output zero_dollars.mp4
```

#### Very important note

If you want videogrep to match exactly what you enter, surround your search with `^` and `$`. For example, to match the word "the" you would use:


```bash
videogrep --input shell.mp4 --search '^the$' --search-type fragment --output the.mp4
```

If you'd like to learn a bit more about regular expressions, I recommend Allison Parrish's [Regular Expressions: A Gentle Introduction](https://github.com/aparrish/rwet/blob/master/regular-expressions-a-gentle-introduction.ipynb).


### Transcribing

If you don't have a subtitle file for your video (for instance if you've recorded it yourself), you can use videogrep to transcribe the video for you. To do this you'll also need to install [vosk](https://alphacephei.com/vosk/install) and [ffmpeg](https://www.ffmpeg.org/download.html).

*Note: this does not currently work on M1 Macs without some hacking around.*

To install `vosk`:

```bash
pip3 install vosk
```

To install ffmpeg on a Mac, first install [brew](https://brew.sh/), and then:

```bash
brew install ffmpeg
```

Then, just pass the `--transcribe` option to videogrep:

```bash
videogrep --input shell.mp4 --transcribe
```

After some time passes you should see a new file in the same directory as your video with a `.json` extension. You can now use videogrep on that video. By default the transcription only works in English, but you can download [other language models from vosk](https://alphacephei.com/vosk/models) and use the `--model` option to indicate which model to use.


### Export options

Videogrep can export any file type that [moviepy](https://zulko.github.io/moviepy/) can handle. In addition, it can export `edl` files compatible with the wonderful command line video player `mpv`, `xml` files that can be imported into Final Cut, Premiere, and Resolve, and it can also save individual clips rather than a concatenated supercut.

To save a supercut as individual clips, just pass the `--export-clips` option. 

```bash
videogrep --input shell.mp4 --search experience --output experience.mp4 --export-clips
```

To save a supercut for import into another video editor, give the output file a `.xml` extension:

```bash
videogrep --input shell.mp4 --search experience --output experience.xml
```

You can now drag the xml file directly into Adobe Premiere, or import it into Davinci Resolve.


### Multiple files

Videogrep can take multiple input videos. For example, to run it on every `mp4` in your current directly, just pass it `*.mp4` as an input:

```bash
yt-dlp "https://www.youtube.com/results?search_query=capitalism" --write-auto-sub

videogrep --input *.mp4 --search capitalism
```

---

## FFMPEG

`ffmpeg` is a command line tool for manipulating video.

### Installation

```
brew install ffmpeg
```

### Basics

Convert one file format into another:

```
ffmpeg -i input.mkv output.mp4
```

Extract audio:

```
ffmpeg -i input.mkv output.mp3
```

Turn a video into choppier but smaller gif: (-r 3 will make it 3 frames per second)

```
ffmpeg -i input.mp4 -r 3 output.gif
```

Better gifs here: https://engineering.giphy.com/how-to-make-gifs-with-ffmpeg/

```
ffmpeg -ss 61.0 -t 2.5 -i input.mp4 -filter_complex "[0:v] fps=12,scale=480:-1,split [a][b];[a] palettegen [p];[b][p] paletteuse" output.gif
```

extract frames

```
ffmpeg -i input.mp4 frame%08d.jpg
```

images to video:

```
ffmpeg -r 1 -pattern_type glob -i 'test_*.jpg' -c:v libx264 -pix_fmt yuv420p out.mp4
```

images to video, with padding to fix different sizes:

```
ffmpeg -r 20 -pattern_type glob -i 'samfaces/*.jpg' -vf "scale=w=1280:h=720:force_original_aspect_ratio=1,pad=1280:720:(ow-iw)/2:(oh-ih)/2" -c:v libx264 -pix_fmt yuv420p out.mp4
```

increase speed:

```
ffmpeg -i input.mp4 -vf "setpts=0.5*PTS" fastvideo.mp4
```

Decrease speed:

```
ffmpeg -i input.mp4 -vf "setpts=2.0*PTS" slowvid.mp4
```

Resize:

```
ffmpeg -i input.mp4 -c:v libx264 -s:v 100x100 -c:a copy output.mp4
```

Trim:

```
ffmpeg -ss 00:01:30 -i input.mp4 -c:v copy -c:a copy -t 5 output.mp4
```

put all videos in a folder together

```
for f in *.mp4 ; do echo file \'$f\' >> list.txt; done && ffmpeg -f concat -safe 0 -i list.txt -s 1280x720 -crf 24 stitched-video.mp4 && rm list.txt
```

Making a mirror effect:

```
ffmpeg -i input.mp4 -vf "crop=iw/2:ih:0:0,split[left][tmp];[tmp]hflip[right];[left][right] hstack" output.mp4
```

Edge detection:

```
ffmpeg -i input.mp4 -vf "edgedetect=low=0.1:high=0.4" output.mp4
```

Fading in:

```
ffmpeg -i input.mp4 -vf "fade=in:0:30" output.mp4
```

Slow down a video with frame interpolation:

```
ffmpeg -i input.mp4 -vf ""[0:v]minterpolate=fps=120:mi_mode=mci[out];[out]setpts=5*PTS"" -y output.mp4
```

Overlay audio on to video:

```
ffmpeg -i input.mp4 -i input.mp3 -c copy -map 0:v:0 -map 1:a:0 output.mp4
```

Chromakey overlay:

```
ffmpeg -i background.mp4 -i foreground.mp4 -filter_complex "[1]colorkey=0xffffff:0.5:0.1[fg];[0][fg]overlay[out]" -map "[out]" together.mp4
```

Record from a webcam (on mac) for 10 seconds and save to file

```
ffmpeg -f avfoundation -pixel_format yuyv422 -framerate 30 -video_size 1280x720 -i 0:0 -t 00:00:10 -y webcamrecord.mp4
```
