---
title: youtube-dl
date: 2021-01-14 18:00:00 +01:00
tags: [script, youtube-dl]
description: youtube-dl
---

Best and easy way to download an video/audo is to use [youtube-dl](https://github.com/ytdl-org/youtube-dl) tool. This tool is available in [Homebrew](https://brew.sh/) and it works for multiple resources, not only youtube.

Install `brew install ffmpeg` if you need to extract the audio.

E.g. of this: [Descargar Un pais para contarlo](https://github.com/jchuerva/un-pais-para-escucharlo)

## Commands

### Download in `mp4` format:
```
youtube-dl -o "test.mp4" --merge-output-format mp4 https://www.rtve.es/alacarta/videos/un-pais-para-escucharlo/pais-para-escucharlo-programa-1/4969918/
```

### Download the mp4 and extract the audio in a mp3 file:
```
youtube-dl -o "test2.%(ext)s" --merge-output-format mp4 --extract-audio --audio-format mp3 https://www.rtve.es/alacarta/videos/un-pais-para-escucharlo/pais-para-escucharlo-programa-1/4969918/
```

### Download audio for a video

Install `brew install ffmpeg`


```
youtube-dl -x --audio-format mp3 https://www.youtube.com/watch?v=7E-cwdnsiow
```

### Download from text file
```
youtube-dl -a url.txt
```

### Download best/worse audio/video
```
youtube-dl -f best https://www.youtube.com/watch?v=7E-cwdnsiow
youtube-dl -f bestaudio https://www.youtube.com/watch?v=7E-cwdnsiow
youtube-dl -f worstvideo https://www.youtube.com/watch?v=7E-cwdnsiow
$ youtube-dl -f bestvideo+bestaudio https://www.youtube.com/watch?v=7E-cwdnsiow
```

