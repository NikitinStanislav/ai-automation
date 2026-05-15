# Fully automated AI workflow: from Reddit’s r/todayilearned fact to complete YouTube Short

An n8n automation that scrapes trending facts from Reddit, rewrites them with AI, generates a voiceover, overlays everything onto a Minecraft gameplay clip, and posts the result to YouTube daily.

Ran for a couple weeks before API credits ran out. [📺 Channel →](https://youtube.com/@todayyoulearnedafact?si=RgLGzC5wZO96_GE8)

---

## What it does

The workflow runs on a daily trigger and processes up to 6 facts per day through a loop. 

Step-by-step:

1. **Fetch facts** – Pulls the top 25 posts of the day from `r/todayilearned` via RSS, converts XML to JSON, and extracts titles.  
2. **Trim to 6** – Limits the batch to 6 facts per run to control API costs.  
3. **AI expansion** – Sends each raw Reddit title to **GPT-4.1 mini**, which rewrites it into a clear, engaging 2–3 sentence "Did you know that..." narration script.  
4. **Text-to-Speech** – The narration is sent to **ElevenLabs** (Turbo v2, custom voice) and returned as a high-quality MP3 at 1.4× speaking rate.  
5. **Audio analysis** – The MP3 is uploaded to **AssemblyAI**, which transcribes it and extracts the exact audio duration in milliseconds.  
6. **Random video trim** – A JavaScript node calculates a random start time within a Minecraft gameplay video stored on Cloudinary, ensuring the trimmed clip is exactly as long as the voiceover. The clip is trimmed via **Replicate** and uploaded back to **Cloudinary**.  
7. **Merge audio \+ video** – The TTS audio and trimmed video clip are merged using a **Replicate** video-compose model. The result is uploaded back to Cloudinary.  
8. **Subtitle burn-in** – The merged video is passed to another **Replicate** model that auto-generates and burns in animated word-by-word subtitles (Poppins ExtraBold, white text with black stroke, yellow highlight, bottom-positioned).  
9. **Upload to YouTube** – The finished Short is uploaded to YouTube via a custom API integration running on Railway. After posting, the workflow waits 4 hours before processing the next fact in the loop.  
10. **Counter tracking** – A counter stored in Cloudinary tracks how many videos have been posted, used to generate sequential titles and avoid re-processing.

---

## Services & APIs

| Service | Purpose |
| :---- | :---- |
| **n8n** | Workflow orchestration |
| **Reddit RSS** | Source facts (`r/todayilearned`) |
| **OpenAI GPT-4.1 mini** | Expand fact titles into narration scripts |
| **ElevenLabs** | Text-to-speech voiceover |
| **AssemblyAI** | Audio transcription \+ duration extraction |
| **Replicate** | Video trim, audio/video merge, subtitle burn-in |
| **Cloudinary** | Media hosting and file passing between steps |
| **YouTube Data API** | Posting finished Shorts |
| **Railway** | Hosting the YouTube upload service |

---

## Note

* fact\_factory.json is a workflow template. Replace all credential values before running.   
* On a daily cadence this runs to a few dollars per month depending on video length. The workflow paused when credits ran out, but all the logic is intact and ready to resume. 

