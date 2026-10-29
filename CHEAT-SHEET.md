# OpenMontage Cheat Sheet

OpenMontage is not an app you click. You open this folder in Claude Code and
type what you want. Claude reads the instructions in this repo and builds the
video step by step. It stops after each step so you can approve or change it.

## 1. One-time setup (Windows, no WSL needed)

You need: **Python 3.10+**, **Node.js 22+**, **FFmpeg** (on your PATH), and Git.
Then, in PowerShell inside the OpenMontage folder:

```powershell
py -3 -m venv .venv; .\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
cd remotion-composer; npm install; cd ..        # if this errors: npx --yes npm install
python -m pip install piper-tts faster-whisper
python -m piper.download_voices en_US-lessac-medium   # free offline voice
Copy-Item .env.example .env
```

(`make setup` works on Mac/Linux, or on Windows only if you have `make` installed.
The PowerShell lines above do the same thing.)

## 2. Start a video: the prompt shape

```
Make a [LENGTH] [STYLE] about [TOPIC].
[FOOTAGE MODE]. [VOICE]. [MUSIC]. [PACING]. Free tools only, $0 budget.
```

Example:

```
Make a 30-second cinematic documentary montage about the Apollo moon landings.
Use real footage only. Piper narration, slow and calm. Royalty-free music,
word-level subtitles. Long, slow holds. Free tools only, $0 budget.
```

Claude will show you a plan, then pause at each step (idea, shot list, footage,
edit). Reply **"approve"** to keep going, or say what to change.

## 3. Real footage vs AI images

| You want | Say this | What it uses | Cost |
|---|---|---|---|
| Real footage | "documentary montage", "use real footage only" | Archive.org, NASA, Wikimedia (no key). Pexels/Pixabay/Unsplash need a **free** key | $0 |
| AI images | "animated explainer", "use AI images" | An image generator (FLUX, GPT Image, Imagen...) | **Paid key**: Claude must tell you the price and wait for your OK |
| AI video clips | "cinematic trailer with generated clips" | Kling, Veo, Seedance, Runway... | **Paid key** |

Free stock keys (Pexels etc.) go in `.env`, never in chat. Example line:
`PEXELS_API_KEY=...`

## 4. Change the voice, music, or pacing

- **Voice:** "use the Piper voice en_US-ryan-high". Get more voices with
  `python -m piper.download_voices <voice-name>` (list: rhasspy.github.io/piper-samples).
- **Speed of speech:** "speak slower" or "speak faster". Behind the scenes this
  is Piper's length setting: 1.2 is slower, 0.9 is faster.
- **No narration:** "no narration, music only".
- **Music:** drop MP3s you have the rights to into `music_library/`, then say
  "use cinematic_epic.mp3". Or say "find royalty-free music", or "no music".
- **Pacing:** "fast cuts, 1-2 seconds each" or "long slow holds". Also change
  the length: "make it 45 seconds".
- **Subtitles:** "word-level subtitles" or "no subtitles".

## 5. Where finished videos land

```
projects/<project-name>/renders/final.mp4
```

Everything for that video (footage, narration, music, subtitles, the plan) is
in `projects/<project-name>/`. The `projects/` folder is never committed to Git.
To watch the live storyboard while it works: `python -m backlot open <project-name>`.

## 6. Something failed? Re-run it

- Say: **"Resume project <project-name>."** Claude picks up from the last saved
  step. It does not start over.
- Only the render failed? Say: **"Re-run the compose stage for project <project-name>."**
- Want to redo one step? Say: **"Redo the edit stage for <project-name>, make the
  cuts faster."** Old versions are kept in `projects/<project-name>/history/`.
- Claude should always tell you which stage failed and why. If it doesn't, ask
  "which stage failed and why?"

## 7. Rules that keep you at $0

- Keys go **only** in `.env` (Git ignores this file). Never paste them in chat.
- Before any step that costs money, Claude must name the tool, the provider, and
  the price, then wait for your "yes".
- Say "free tools only" in your prompt to keep it strict.
