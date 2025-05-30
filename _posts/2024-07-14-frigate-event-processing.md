---
title: Frigate Event Processing
date: 2024-07-14
categories: 
tags: 
image: /assets/img/posts/frigate.jpg
---
**Source code available on the [frigate-export-ffmpeg](https://github.com/benblustey/frigate-export-ffmpeg/) repository**

Now that the API has been flushed out to retrieve the proper events, a script is needed to download and begin the process of extracting the data from these events. This project was initially a study and practice of writing bash scripts, but has since been rewritten in Python for better performance and structure of execution.
### The Process
**Download the clips** `frigate_download.py`
* executable bash file executed via cron
* when no date provided, use current date
* allow for input of custom date or date ranges
* place these files in a directory for later processing

**Video Data** `process_files.py`
* iterate over a default or specific directory
* use `ffprobe` to obtain length
* output JSON for DB insert
* update file names to `<timestamp>.mp4`
* move files to new directory for processing

Notes: by using the epoch timestamp, a unique identifier is created allowing for quick checks during file creating, processing, and DB insert.

Example Output:
```json
{
	"length": 12.144,
	"src": "1719813362.mp4",
	"friendlyDate": "24-06-30--22-56-02",
	"epoch": 1719813362,
	"starred": false
}
```

**Process the Videos** `ffmpeg_process.py`
* iterated over a directory or JSON file
* crop, resize, mask blur video
* create png graphic of video for over

## Whats Next?
- Create a NodeJS API MongoDB endpoint
