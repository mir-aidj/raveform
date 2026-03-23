---
layout: distill
title: Raveform
permalink: /
description: A Dataset of Structure Annotations for EDM Tracks in DJ Mixes
# tags: distill formatting
date: 2026-03-23

authors:
  - name: Taejun Kim
    url: "https://taejun.kim"
    affiliations:
      name: Neutune
      url: "https://neutune.com"
  - name: Jongsoo Kim
    url: "https://white-df.github.io"
    affiliations:
      name: MAC Lab, KAIST
      url: "https://mac.kaist.ac.kr"
  - name: Hyungyu Kim
    # url: "https://en.wikipedia.org/wiki/Nathan_Rosen"
    affiliations:
      name: MLP Lab, KAIST
      url: "https://sites.google.com/view/aailab"
  - name: Juhan Nam
    url: "https://mac.kaist.ac.kr/~juhan/"
    affiliations:
      name: MAC Lab, KAIST
      url: "https://mac.kaist.ac.kr"

bibliography: raveform.bib

toc:
  - name: Dataset Structure
  - name: Dataset Components
    subsections:
      - name: DJ Mix Metadata
      - name: Track Metadata
      - name: Beats
      - name: Mix-to-Track Alignments
      - name: Structure Annotations
  - name: Segment Function Examples
    subsections:
      - name: Straightforward Example
      - name: Ambiguous Example
  - name: How to Annotate Structures

---

# Quick Links

<div style="margin-top: 1em;"></div>

- 📦 [Dataset](https://huggingface.co/datasets/taejunkim/raveform/resolve/main/raveform.zip)
- 💻 [Code](https://github.com/mir-aidj/raveform)
- 🎵 [Structure Analysis Model](https://github.com/mir-aidj/all-in-one) (v2 will be released soon)
- 📄 Paper (TBA)

<div style="margin-top: 2em;"></div>

# Dataset Structure

```shell
dataset 
├─ mixes.jsonl                 # DJ mix metadata including tracklists
├─ tracks.jsonl                # Individual track metadata
├─ mixesdb_genres.jsonl        # All genres of DJ mixes collected from MixesDB
├─ beats/                         
│  ├─ mixes/
│  │  └─ <MIX_ID>.beat.json    # Beats of the mix
│  └─ tracks/
│     └─ <TRACK_ID>.beat.json  # Beats of the track
├─ alignments/                 # Mix-to-track alignments
│  └─ <MIX_ID>.align.jsonl     # including estimated mix points
└─ structures/                 # Human-annotated structure annotations
   ├─ beats/                   
   │  └─ <TRACK_KEY>.beat.csv  # Beats and downbeats
   └─ segments.json          # Tempo and functional segment boundaries with labels
```
- Track IDs are YouTube video IDs. You can access the video with `https://www.youtube.com/watch?v=<TRACK_ID>`.
- `*.jsonl` files are JSON Lines files, each containing one JSON object per line.



---

# Dataset Components

## DJ Mix Metadata

- File name: `mixes.jsonl`
- Retrieved from [MixesDB](https://www.mixesdb.com/).

### Schema

| Field                     | Type     | Description                                                                                                   |
|---------------------------|----------|---------------------------------------------------------------------------------------------------------------|
| `id`                      | string   | Unique identifier for the DJ mix assigned by this dataset.                                                    |
| `title`                   | string   | Title of the DJ mix from [MixesDB](https://www.mixesdb.com/).                                                 |
| `url`                     | string   | URL of the DJ mix on [MixesDB](https://www.mixesdb.com/).                                                     |
| `audio_source`            | string   | Source of the audio file (`soundcloud`, `mixcloud`, `youtube`, or `html_audio`).                              |
| `audio_url`               | string   | URL of the audio file.                                                                                        |
| `duration`                | number   | Duration of the DJ mix in seconds.                                                                            |
| `num_identified_tracks`   | integer  | Number of tracks in the DJ mix that have been identified by [MixesDB](https://www.mixesdb.com/).              |
| `frac_identified_tracks`  | number   | Fraction of identified tracks (`num_identified_tracks` divided by `num_total_tracks`).                        |
| `num_total_tracks`        | integer  | Total number of tracks in the DJ mix, according to [MixesDB](https://www.mixesdb.com/).                       |
| `num_available_transitions` | integer| Number of available transitions in the DJ mix.                                                                |
| `num_timestamps`          | integer  | Number of timestamps in the DJ mix, parsed from `[NN]` patterns in track titles. About 38% of mixes have timestamps. |
| `tracklist`               | array    | List of tracks in the DJ mix. Each entry has:<br>&nbsp;&nbsp;`id`: YouTube video ID (string or null)<br>&nbsp;&nbsp;`title`: Track title from [MixesDB](https://www.mixesdb.com/). |
| `genres`                  | array    | Genres extracted by filtering genre tags from `tags`.                                                         |
| `tags`                    | array    | List of tags from [MixesDB](https://www.mixesdb.com/).                                                        |


### Example

<!-- mix3225 -->
<iframe width="100%" height="166" scrolling="no" frameborder="no" allow="autoplay" src="https://w.soundcloud.com/player/?url=https%3A//api.soundcloud.com/tracks/soundcloud%253Atracks%253A280552861&color=%234c4f56&auto_play=false&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true"></iframe><div style="font-size: 10px; color: #cccccc;line-break: anywhere;word-break: normal;overflow: hidden;white-space: nowrap;text-overflow: ellipsis; font-family: Interstate,Lucida Grande,Lucida Sans Unicode,Lucida Sans,Garuda,Verdana,Tahoma,sans-serif;font-weight: 100;"><a href="https://soundcloud.com/djborisnyc" title="BORIS" target="_blank" style="color: #cccccc; text-decoration: none;">BORIS</a> · <a href="https://soundcloud.com/djborisnyc/transmissions-140-with-joey" title="Transmissions 140 with Joey Beltram" target="_blank" style="color: #cccccc; text-decoration: none;">Transmissions 140 with Joey Beltram</a></div>

- Below is an example of the metadata for the DJ mix shown above:

```json
{
  "id": "mix3225",
  "title": "2016-08-30 - Joey Beltram - Transmissions 140",
  "url": "https://www.mixesdb.com/w/2016-08-30_-_Joey_Beltram_-_Transmissions_140",
  "audio_source": "soundcloud",
  "audio_url": "https://soundcloud.com/djborisnyc/transmissions-140-with-joey",
  "duration": 3600.065306,
  "num_identified_tracks": 20,
  "frac_identified_tracks": 0.8695652174,
  "num_total_tracks": 23,
  "num_available_transitions": 16,
  "num_timestamps": 0,
  "tracklist": [
    {
      "id": "nCICZPgEhAk",
      "title": "Pig&Dan - Chez Dre [Drumcode]"
    },
    {
      "id": null,
      "title": "Nigel Richards & GhettoBlaster Feat. Robert Armani - Bang The Box (Joey Beltram Remix)"
    },
    {
      "id": "HjMuAdaYIMc",
      "title": "D-Unity - Slippin [1605]"
    },
    {
      "id": "fKRHlYJQibw",
      "title": "Boris - Language (Carlo Lio Remix) [Transmit]"
    },
    {
      "id": "aEASTKctDJo",
      "title": "Gary Beck - Timeline (P\u00e4r Grindvik Remix) [BEK]"
    },
    {
      "id": null,
      "title": "Slam - Significance"
    },
    {
      "id": "M0hjKPRgVqk",
      "title": "Ian Pooley - Celtic Cross (Len Faki Remix) [Figure]"
    },
    {
      "id": "rREATieGXgY",
      "title": "Adam Beyer & Mark Reeve - Nine Of You [Drumcode]"
    },
    {
      "id": "bC3xQwua0ao",
      "title": "Umek - Inhumane Visions"
    },
    {
      "id": "wV5A2yqRMh8",
      "title": "A. Mochi - C2M [Figure]"
    },
    {
      "id": "8MiSjl9XULo",
      "title": "Simone Tavazzi - Nitro (Joey Beltram Remix) [Unknown Territory]"
    },
    {
      "id": "t8u3hCYrkrg",
      "title": "Owen Offset - Inhale (Joey Beltram Remix)"
    },
    {
      "id": "Jr4Vk8ZcoQc",
      "title": "Oliver Deutschmann - Asylum [Slim]"
    },
    {
      "id": "5WvMql1Ejzs",
      "title": "Secret Cinema & Egbert - Maximaal [Drumcode]"
    },
    {
      "id": "UtF88HgRJ2g",
      "title": "Bart Skils - Voodoo Child [Drumcode]"
    },
    {
      "id": "bfuutcmHbGM",
      "title": "Nick & Danny Chatelain - Acid (Coyu Remix) [Suara]"
    },
    {
      "id": "AoWKSZpZKQ0",
      "title": "Advent & Industrialyzer - Evil Bee [Kombination Research]"
    },
    {
      "id": "rpHv3LT592I",
      "title": "Carlo Lio - Welcome To The Flipside [Suara]"
    },
    {
      "id": "lrrHQn69Ngg",
      "title": "DJ Murphy - Out Cold (Joey Beltram Remix) [Dolma]"
    },
    {
      "id": "TUxODzHrU94",
      "title": "Egbert - Dieper [GEM]"
    },
    {
      "id": null,
      "title": "Boris - Can You Hear Me [Alleanza]"
    },
    {
      "id": "-9AzxrzSEbU",
      "title": "Sian & Luigi Madonna - Royal Oak [Octopus]"
    },
    {
      "id": "cpVUZKZL3Ho",
      "title": "Kaiserdisco - Bonkers [Drumcode]"
    }
  ],
  "genres": [
    "Techno",
    "Tech House"
  ],
  "tags": [
    {
      "key": "Category:Joey Beltram",
      "url": "https://www.mixesdb.com/w/Category:Joey_Beltram"
    },
    {
      "key": "Category:Transmissions",
      "url": "https://www.mixesdb.com/w/Category:Transmissions"
    },
    {
      "key": "Category:Techno",
      "url": "https://www.mixesdb.com/w/Category:Techno"
    },
    {
      "key": "Category:Tech House",
      "url": "https://www.mixesdb.com/w/Category:Tech_House"
    }
  ]
}
```

---


## Track Metadata

- File name: `tracks.jsonl`
- Aggregated from `mixes.jsonl`.


### Schema

| Field       | Type    | Description                                                                        |
| ----------- | ------- | ---------------------------------------------------------------------------------- |
| `id`        | string  | Track ID (YouTube video ID).                                                       |
| `play_count`| integer | Total occurrences of this track in the dataset.                                    |
| `title`     | string  | Chosen track title (aggregated from [MixesDB](https://www.mixesdb.com/) listings). |
| `duration`  | number  | Track duration in seconds.                                                         |
| `played_at` | array   | List of mix IDs in which this track appears.                                       |



### Example

<!-- Track 0019.5WvMql1Ejzs -->
<iframe style="display: block; margin: 0 auto 20px auto;" id="youtube-player-5WvMql1Ejzs" width="560" height="315" src="https://www.youtube.com/embed/5WvMql1Ejzs?start=289" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

- The track above is the 14th track in `mix3225`. Its metadata is shown below:

```json
{
  "id": "5WvMql1Ejzs",
  "play_count": 10,
  "title": "Secret Cinema & Egbert - Maximaal [Drumcode - DC159]",
  "duration": 525.288,
  "played_at": [
    "mix2872",
    "mix3185",
    "mix3195",
    "mix3210",
    "mix3223",
    "mix3225",
    "mix3488",
    "mix3639",
    "mix3724",
    "mix4477"
  ]
}
```

---


## Beats

- Beat files for mixes: `beats/mixes/<MIX_ID>.beat.json`
- Beat files for tracks: `beats/tracks/<TRACK_ID>.beat.json`

### Schema

| Field   | Description |
| ------- | :---------- |
| `track_id` | Track ID. |
| `beats` | List of beat times in seconds. |

### Example

- Beats for the example mix above `mix3325`:
```json
{
  "mix_id": "mix3325", 
  "beats": [1.17, 1.66, 2.15, ... , 5470.35, 5470.82, 5471.29]
}
```

- Beats for the example track above `5WvMql1Ejzs`:
```json
{
  "track_id": "5WvMql1Ejzs",
  "beats": [0.17, 0.64, 1.11, ... , 509.72, 510.19, 510.66]
}
```

---


## Mix-to-Track Alignments

- File name: `alignments/<MIX_ID>.align.jsonl`
- Each file contains the beat-level alignment between the DJ mix and the track, performed using Dynamic Time Warping (DTW) on spectral features<d-cite key="taejun2020djmix"></d-cite>.
- Mix-in and mix-out points are extracted following the method in <d-cite key="taejun2020djmix"></d-cite>.
- Mix points are indicated from both perspectives: when they occur in the mix `*_mix` and when they occur in the track `*_track`.
- Each mix point is specified in both beat index `*_beat_*` and time in seconds `*_time_*`.

### Schema

| Field   | Description |
| ------- | :---------- |
| `mix_id` | DJ mix ID. |
| `track_id` | Track ID. |
| `mixin_time_mix` | Mix-in time in seconds (in the mix). |
| `mixout_time_mix` | Mix-out time in seconds (in the mix). |
| `mixin_time_track` | Mix-in time in seconds (in the track). |
| `mixout_time_track` | Mix-out time in seconds (in the track). |
| `mixin_beat_mix` | Mix-in beat index (in the mix). |
| `mixout_beat_mix` | Mix-out beat index (in the mix). |
| `mixin_beat_track` | Mix-in beat index (in the track). |
| `mixout_beat_track` | Mix-out beat index (in the track). |
| `match_rate` | Proportion of correctly aligned beats relative to the total number of beats in the track. |
| `matched_beats` | Number of correctly aligned beats. |
| `matched_time_mix` | Correctly aligned time in seconds (in the mix). |
| `matched_time_track` | Correctly aligned time in seconds (in the track). |
| `cost` | DTW cost. |
| `wp` | Warping path: list of `[track_beat, mix_beat]` index pairs from DTW. |

### Example

- Alignment between the example mix above `mix3225` and the example track above `5WvMql1Ejzs`:
```json
{
  "mix_id": "mix3225",
  "track_id": "5WvMql1Ejzs",
  "mixin_time_mix": 2359.72,
  "mixout_time_mix": 2460.8,
  "mixin_time_track": 267.83,
  "mixout_time_track": 368.14,
  "mixin_beat_mix": 4975,
  "mixout_beat_mix": 5189,
  "mixin_beat_track": 571,
  "mixout_beat_track": 785,
  "match_rate": 0.1917930419,
  "matched_beats": 215,
  "matched_time_mix": 101.08,
  "matched_time_track": 100.31,
  "cost": 0.4504659436,
  "wp": [
    [ 785, 5189 ],
    [ 784, 5188 ],
    [ 783, 5187 ],
    [ 782, 5186 ],
    [ 781, 5185 ],
    ...
    [ 575, 4979 ],
    [ 574, 4978 ],
    [ 573, 4977 ],
    [ 572, 4976 ],
    [ 571, 4975 ]
  ]
}
```
- The track starts at 39:20 (`mixin_time_mix=2359.72`) in the mix and at 4:28 (`mixin_time_track=267.83`) in the track.
- The track ends at 41:01 (`mixout_time_mix=2460.8`) in the mix and at 6:08 (`mixout_time_track=368.14`) in the track.


---

## Structure Annotations

- A subset of 1,423 tracks includes human-annotated structure annotations: tempo, beats, downbeats, and segment boundaries with labels.
- In this subset, track keys have an additional prefix (e.g., `0019.5WvMql1Ejzs` instead of `5WvMql1Ejzs`).
- Tracks for manual structure annotation are selected based on play count, prioritizing those most frequently played in the dataset.
- Functional segment annotations are provided in `structures/segments.json`, while beats and downbeats are available in `structures/beats/<TRACK_KEY>.beat.csv`.
- Definitions of segment functions and annotation procedures are in the paper.


### Schema

**Schema for `segments.json`**

| Field   | Description |
| ------- | :---------- |
| `key` | Unique identifier for the subset with structure annotations. |
| `index` | Track index in the subset. |
| `id` | Track ID in the entire dataset, which is the YouTube video ID. |
| `title` | Track title from `tracks.jsonl`. |
| `fold` | Fold index for 8-fold cross-validation of structure analysis model. |
| `genre` | Genre annotated manually referencing Beatport. |
| `duration` | Duration in seconds. |
| `play_count` | Number of times the track has been played in this dataset from `tracks.jsonl`. |
| `average_bpm` | Average tempo in beats per minute. |
| `tempos` | List of tempos in beats per minute. If multiple tempos exist, the tempo changes, the tempo applies from `start` to the next tempo. `beat_position_in_bar` is the beat index in the bar, `1` indicates the downbeat. |
| `sections` | List of sections. |
| `played_at` | List of DJ mix IDs where the track has been played. |

<br>
**Schema for `<TRACK_KEY>.beat.csv`**

| Field      | Description                                          |
|------------|------------------------------------------------------|
| `time`     | Time of the beat in seconds.                         |
| `downbeat` | Beat position in the bar; `1` denotes the downbeat.  |
| `section`  | Functional segment label for the beat.               |



### Example

Entry for the example track `5WvMql1Ejzs` in `segments.json`:
```json
{
  "key": "0019.5WvMql1Ejzs",
  "index": 19,
  "id": "5WvMql1Ejzs",
  "title": "Secret Cinema & Egbert - Maximaal [Drumcode - DC159]",
  "fold": 2,
  "genre": "Techno",
  "duration": 525.25861678,
  "play_count": 10,
  "average_bpm": 128.0,
  "tempos": [
    {
      "start": 0.179,
      "bpm": 128.0,
      "beat_position_in_bar": 1
    }
  ],
  "sections": [
    {
      "name": "intro",
      "start": 0.179,
      "end": 15.179
    },
    {
      "name": "buildup",
      "start": 15.179,
      "end": 60.179
    },
    {
      "name": "buildup",
      "start": 60.179,
      "end": 105.179
    },
    {
      "name": "cooldown",
      "start": 105.179,
      "end": 135.179
    },
    {
      "name": "breakdown",
      "start": 135.179,
      "end": 180.179
    },
    {
      "name": "drop",
      "start": 180.179,
      "end": 240.179
    },
    {
      "name": "cooldown",
      "start": 240.179,
      "end": 270.179
    },
    {
      "name": "breakdown",
      "start": 270.179,
      "end": 300.179
    },
    {
      "name": "drop",
      "start": 300.179,
      "end": 360.179
    },
    {
      "name": "cooldown",
      "start": 360.179,
      "end": 480.178
    },
    {
      "name": "outro",
      "start": 480.178,
      "end": 495.178
    },
    {
      "name": "altoutro",
      "start": 495.178,
      "end": 525.25861678
    }
  ],
  "played_at": [
    "mix2872",
    "mix3185",
    "mix3195",
    "mix3210",
    "mix3223",
    "mix3225",
    "mix3488",
    "mix3639",
    "mix3724",
    "mix4477"
  ]
}
```

<br>
Beats for track `5WvMql1Ejzs` (`5WvMql1Ejzs.beat.csv`):
```csv
time,downbeat,section
0.179,1,intro
0.6478,2,intro
1.1165,3,intro
1.5853,4,intro
2.054,1,intro
2.5228,2,intro
2.9915,3,intro
...
```




---
# Segment Function Examples

Music is hard to explain with text alone. Hearing the structure is often clearer than describing it.  
Below we provide two audio examples: one track with a straightforward structure, and another with an ambiguous structure that led us to exclude it from the annotated subset.


## Straightforward Example

<iframe style="display: block; margin: 0 auto 10px auto;" id="youtube-player-0EWbonj7f18" width="560" height="315" src="https://www.youtube.com/embed/0EWbonj7f18" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

| Timestamp | Function  | Description                                                |
|-----------|-----------|------------------------------------------------------------|
| [0:00](#) | Intro     | Percussive opening.                                        |
| [0:15](#) | Buildup   | Gradual increase in energy.                                |
| [0:45](#) | Breakdown | Sudden drop in energy; strings heighten tension further.   |
| [1:17](#) | Drop      | First full-energy drop.                                    |
| [1:32](#) | Drop      | Consecutive drop; new synth enters, so marked as boundary. |
| [1:47](#) | Drop      | Consecutive drop; rhythmic pattern changes, marked again.  |
| [2:02](#) | Breakdown | Second breakdown.                                          |
| [2:34](#) | Drop      | Second drop begins.                                        |
| [2:49](#) | Drop      | Consecutive drop; new synth/voice enters, marked boundary. |
| [3:04](#) | Drop      | Consecutive drop; rhythmic shift similar to prior.         |
| [3:19](#) | Outro     | Percussive closing.                                        |

**NOTE:** Clicking timestamps will jump to the corresponding time in the video.

<script>
(function() {
  const iframe = document.getElementById('youtube-player-0EWbonj7f18');
  const timestamps = [0, 15, 45, 77, 92, 107, 122, 154, 169, 184, 199];
  
  const listItems = iframe.nextElementSibling.querySelectorAll('a');
  listItems.forEach((link, index) => {
    link.addEventListener('click', function(e) {
      e.preventDefault();
      const seconds = timestamps[index];
      const baseUrl = 'https://www.youtube.com/embed/0EWbonj7f18';
      iframe.src = baseUrl + '?start=' + seconds + '&autoplay=1';
    });
  });
})();
</script>


---

## Ambiguous Examples

This section describes two ambiguous examples that led us to exclude them since it is difficult to establish consistent annotation criteria across all tracks and all annotators. We have organized the musical cues to illustrate why this is challenging.

### Ambiguous Example 1

Most excluded tracks correspond to this case. This track is difficult to segment because instruments enter and exit gradually. Additionally, the energy remains almost constant throughout the track.

<iframe style="display: block; margin: 0 auto 10px auto;" id="youtube-player-XGqydwBHlqA" width="560" height="315" src="https://www.youtube.com/embed/XGqydwBHlqA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

| Timestamp | Description |
|-----------|-------------|
| [0:00](#) | Intro. |
| [0:43](#) | Kick drum fades in. |
| [1:35](#) | Short percussive synth fades in. |
| [1:50](#) | Vocal ("step back") fades in. |
| [2:00](#) | Bass synth fades in. |
| [2:45](#) | Synth added. |
| [2:59](#) | Ride added. |
| [3:13](#) | Ride removed. |
| [3:42](#) | Breakdown. |
| [4:25](#) | Second part begins. |
| [4:39](#) | Ride removed. |
| [5:08](#) | Ride returns. |
| [5:45](#) | Synths removed; track begins fading out. |
| [6:19](#) | Ambient outro. |

**NOTE:** Clicking timestamps will jump to the corresponding time in the video.

<div style="margin-bottom: 20px;"></div>

#### Why is this difficult to annotate?
- The energy peaks just before (3:00) and after (4:30) the breakdown. However, since instruments enter and exit gradually, it is difficult to determine the start of drops. For the same reason, it is difficult to determine the boundaries of intro, buildup, and outro.

<script>
(function() {
  const iframe = document.getElementById('youtube-player-XGqydwBHlqA');
  const timestamps = [0, 43, 95, 110, 120, 165, 179, 193, 222, 265, 279, 308, 345, 379];
  
  // Find the table after the iframe
  let element = iframe.nextElementSibling;
  while (element && element.tagName !== 'TABLE') {
    element = element.nextElementSibling;
  }
  
  if (element) {
    const links = element.querySelectorAll('a');
    links.forEach((link, index) => {
      link.addEventListener('click', function(e) {
        e.preventDefault();
        const seconds = timestamps[index];
        const baseUrl = 'https://www.youtube.com/embed/XGqydwBHlqA';
        iframe.src = baseUrl + '?start=' + seconds + '&autoplay=1';
      });
    });
  }
})();
</script>



### Ambiguous Example 2

This track was excluded due to its highly repetitive structure.

<iframe style="display: block; margin: 0 auto 10px auto;" id="youtube-player-U1hBVOutacI" width="560" height="315" src="https://www.youtube.com/embed/U1hBVOutacI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

| Timestamp | Description |
|-----------|-------------|
| [0:00](#) | Intro. |
| [0:46](#) | Clap added. |
| [1:17](#) | Vocal added. |
| [1:48](#) | Vocal removed; new synth added. |
| [2:19](#) | Synth removed; vocal returns. |
| [2:35](#) | Synth returns; both synth and vocal playing. |
| [2:46](#) | Short 2-bar breakdown. |
| [2:50](#) | Vocal removed; synth continues. |
| [3:07](#) | Vocal returns; synth continues. |
| [3:21](#) | Synth removed. |
| [3:52](#) | Vocal removed; synth returns. Rhythmic pattern changes. |
| [4:23](#) | New bass synth fades in. |
| [4:31](#) | Synth removed; bass synth starts fading out. |
| [4:50](#) | Synth returns. |
| [4:54](#) | Ambient outro. |

**NOTE:** Clicking timestamps will jump to the corresponding time in the video.

<div style="margin-bottom: 20px;"></div>

#### Why is this difficult to annotate?
- Too repetitive.
- Instruments come in and out randomly.
- The energy is almost constant throughout the track except for the breakdown. However, even the breakdown is too short, making it arguable whether it should be labeled as a segment. It could be treated as transition bars before moving to the next segment.
- It's difficult to locate drops. We may be able to identify them when both vocals and synths are playing together (2:35), but the energy difference is too small. If we label these as drops, the remaining segments would be classified as intro, outro, or buildup, but they don't exhibit the gradual energy increase (intro or buildup) or decrease (outro) that these functions typically show.
- This track consists of consecutive chunks with small variations alternating throughout the track.

<script>
(function() {
  const iframe = document.getElementById('youtube-player-U1hBVOutacI');
  const timestamps = [0, 46, 77, 108, 139, 155, 166, 170, 187, 201, 232, 263, 271, 290, 294];
  
  // Find the table after the iframe
  let element = iframe.nextElementSibling;
  while (element && element.tagName !== 'TABLE') {
    element = element.nextElementSibling;
  }
  
  if (element) {
    const links = element.querySelectorAll('a');
    links.forEach((link, index) => {
      link.addEventListener('click', function(e) {
        e.preventDefault();
        const seconds = timestamps[index];
        const baseUrl = 'https://www.youtube.com/embed/U1hBVOutacI';
        iframe.src = baseUrl + '?start=' + seconds + '&autoplay=1';
      });
    });
  }
})();
</script>




---
# How to Annotate Structures

This section describes how to annotate track structures using [Rekordbox](https://rekordbox.com/) and convert the annotations to JSON format. The image below shows an example screenshot of Rekordbox annotating `0004.0EWbonj7f18`. We use Rekordbox's memory cues to annotate segments, assisted by Rekordbox's track analysis for beat and downbeat annotation.

{% include figure.liquid loading="eager" path="assets/img/rekordbox.png" class="img-fluid rounded z-depth-1" zoomable=true %}


### Detailed Steps

<ol>
<li>Open the track you want to annotate in Rekordbox. We do not recommend <code>mp3</code> files since their time offset can vary depending on the decoder.</li>
<li>Use Rekordbox's "Analyze Track" button to get the beat grid. Tip: It may work better with the "Analyze Track (Dynamic)" option if the BPM changes.</li>
<li>Check beats and downbeats and adjust if necessary by clicking the "GRID" button.</li>
<li>Set memory cues and label segment functions. If you have more than 10 segments, you can also use hot cues.</li>
<li>In the track browser, you can use the "Comments" column as shown in the image below. We used it to filter out tracks and leave comments for discussion later. For example, if the comment starts with "pass.", we excluded the track automatically when running the python script.
{% include figure.liquid loading="eager" path="assets/img/rekordbox_comments.png" class="img-fluid rounded z-depth-1" zoomable=true %}</li>
<li>Go to "File" and click "Export Collection in XML format".</li>
<li>Run <code>python convert_rekordbox_xml_to_json.py</code> to convert the XML file to JSON format.</li>
</ol>
