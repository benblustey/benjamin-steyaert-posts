---
title: Firework Detection
date: 2024-07-10
categories: frigate, unraid, webdev, fireworks
tags:
  - frigate
  - hardware
  - docker
  - unraid
  - bash
  - python
image: /assets/img/posts/frigate.jpg
---
Livermore has one of the highest concentration of veterans in the Bay Area. Based on census data of 4.6% of residents. Half of these veterans are from Vietnam, Gulf, Afganistan, and Iraq wars. Our neighborhood has been experiencing a large amount of illegal fireworks being detonated overhead. These events have been occurring year round at all times of the day with no sense of association with holidays. Knowing that my NVR (network video recorder) is pretty sophisticated, I decided to see if there was a way to utilize the tools present in my homelab, to provided data for the city to acknowledge. It seems a lot of people in our neighborhood have contacted the city, but many officials seem this is a minor inconvenience for a small neighborhood.
Frigate is an amazing open source NVR platform that provides and large amount of configuration and ability to be hosted at home with no cloud dependencies. This allows for proper privacy by only allowing the cameras to be accessed by the NVR and not outside the network.
## Goal
- Utilize the Frigate NVR platform to detect large explosions and fireworks
- Download the video clips to a local storage
- Analyze the data from the clips
  - timestamp/date of video
  - length of clip
- Process the clips to obfuscate the location of the residence but clearly show the timestamp
- upload the data into a database for easy rendering and analysis
- Automate the whole process from event to user interaction
## Audio Detection
Within the Frigate configuration each camera can be configured with specific objects or audio detection. A simple change to the cameras configuration to listen to explosions, and fireworks is as simple as below. The main to parts to be aware are the `ffmpeg > inputs > source > roles` define under the ffmpeg inputs and the define audio models under `audio > llisten`.

``` yml
front_yard:
  ffmpeg:
    inputs:
    - path: rtsp://<front-yard-camera-ip>/front_yard_sub
      input_args: preset-rtsp-restream
      roles:
      - audio
      - detect
    - path: rtsp://<front-yard-camera-ip>/front_yard_main
      hwaccel_args: preset-intel-qsv-h265
      roles:
      - record
      output_args:
      record: preset-record-generic-audio-copy
  audio:
    enabled: true
    min_volume: 300
    listen:
      - fireworks
      - explosion
```

## Frigate API
Frigates documentation is fantastic. The section on [API](https://docs.frigate.video/integrations/api/) includes many examples that allow for easy understanding. Below is the URL used to search the front yard camera events with the fireworks and explosion labels.

```
https://<frigate-ip>/api/events?camera=front_yard&label=fireworks,explosion
```

Adding additional filters allowed for a more specific request.
```
&after=<epoch-time>&before=<epoch-time>
```

Knowing the additional filters would allow me to write a script that would take in specific date ranges or set defaults within the script its sell.

Using PostMan below is example response.

```json
{
  "box":null,
  "camera":"front_yard",
  "data":{
    "score":0.890625,
    "top_score":0.890625,
    "type":"audio"
  },
  "end_time":1727706940.117359,
  "false_positive":null,
  "has_clip":true,
  "has_snapshot":true,
  "id":"1727706933.692818-mvnlgz",
  "label":"fireworks",
  "plus_id":null,
  "retain_indefinitely":false,
  "start_time":1727706928.692818,
  "sub_label":null,
  "top_score":null,
  "zones":[]
}
```

## Whats Next?
- Create a script executed by CRON or event
- Massage data for DB input
- Process Images for presentation
- Create a static site and pipeline
