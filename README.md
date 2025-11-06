# Mac Video Compression Quick Action

Add right-click video compression to macOS Finder using FFmpeg. This macOS Quick Action integrates FFmpeg into your Finder workflow, letting you compress videos with a simple right-click. Perfect for developers, content creators, and anyone who needs to quickly reduce video file sizes.


## Features

- 🎬 Compress videos directly from Finder's context menu
- 🔒 Works completely offline - your files never leave your Mac
- ⚡ Batch process multiple videos at once
- 🎯 Customizable compression settings
- 📱 Supports MP4, MOV, and other video formats

## Prerequisites

Install FFmpeg via Homebrew:
```bash
brew install ffmpeg
```

## Installation

### Step 1: Open Automator

1. Press `Cmd + Space` and type "Automator"
2. Click on Automator to open it
3. Select **Quick Action**

### Step 2: Configure Workflow Settings

At the top of the Automator window:
- **Workflow receives current:** `movie files`
- **in:** `Finder`

### Step 3: Add Shell Script Action

1. Search for **Run Shell Script** in the actions library & drag it to the workflow area (or double click).
3. Set **Shell:** `/bin/bash`
4. Set **Pass input:** `as arguments`

### Step 4: Add the Compression Script

Paste this script into the text area:
```bash
# Path to ffmpeg (adjust for your system)
FFMPEG="/opt/homebrew/bin/ffmpeg"

# Loop through each selected video
for input_file in "$@"
do
    # Get directory and filename
    dir=$(dirname "$input_file")
    filename=$(basename "$input_file")
    name="${filename%.*}"
    ext="${filename##*.}"
    
    # Create output filename
    output_file="${dir}/${name}_compressed.${ext}"
    
    # Compress video
    "$FFMPEG" -i "$input_file" \
        -c:v libx264 \
        -crf 23 \
        -preset medium \
        -c:a aac \
        -b:a 128k \
        -movflags +faststart \
        "$output_file" \
        -y
    
    # Show notification when done
    osascript -e "display notification \"Compressed: $filename\" with title \"Video Compression Complete\""
done
```

### Step 5: Save the Quick Action

1. Press `Cmd + S` or go to File → Save
2. Name it: **Shrink Video**
3. It will save to `~/Library/Services/`

## Usage

1. Right-click any video file in Finder
2. Navigate to **Quick Actions**
3. Click **Shrink Video**
4. The compressed video will appear in the same folder with `_compressed` suffix



## Troubleshooting

### FFmpeg Not Found

Find your FFmpeg location:
```bash
which ffmpeg
```

Update the `FFMPEG=` line in the script with the correct path.

### Intel Mac Users

If you're on an Intel Mac, change the path to:
```bash
FFMPEG="/usr/local/bin/ffmpeg"
```

### No Notification Appears

The script will still work - notifications are optional feedback. Check your output folder for the compressed file.


## Supported Formats

- MP4
- MOV
- AVI
- MKV
- WebM
- And most other video formats supported by FFmpeg

## Uninstallation

Delete the file from `~/Library/Services/` in Finder.

## Alternative Solution

If you prefer a GUI app without terminal setup, check out [TinyFast](https://tinyfast.app). It's a simple drag in, drag out Mac app for compressing videos, images, PDFs, SVGs, MP3 & more. I discovered this method while creating this app and decided to share it here.

## License

This script is provided as-is for personal and commercial use.

## Contributing

Feel free to submit issues or pull requests with improvements!

---

**Note:** Compression time depends on video length, settings, and your Mac's hardware. Larger videos may take several minutes to process. The automator does not provide feedback (progress bar) while compressing, but only a notification once complete.
