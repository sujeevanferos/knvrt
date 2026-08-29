# knvrt

A unified command-line file converter for documents, presentations, spreadsheets, images, audio, video, and subtitles.

`knvrt` provides a simple interface over established conversion tools, selecting the appropriate backend for the requested conversion. It is designed for terminal-based workflows, batch processing, scripting, and automation.

## Features

- Document, presentation, and spreadsheet conversion through LibreOffice.
- PDF conversion for supported office and document formats.
- Image conversion through ImageMagick.
- Audio and video conversion through FFmpeg.
- Subtitle embedding as selectable subtitle tracks.
- Subtitle burning directly into video.
- Batch conversion using shell glob patterns.
- Automatic backend selection based on the input and requested output format.
- Predictable output filenames based on the source filename.
- Command-line help and version information.
- No conversion engine bundled with `knvrt`; it uses existing system tools.

## How It Works

`knvrt` acts as a lightweight frontend. It does not implement codecs or document parsers itself.

```text
                         knvrt
                           |
                    Format Detection
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
     LibreOffice         FFmpeg        ImageMagick
          |                |                |
          v                v                v
   Documents, PDF     Audio, Video,      Images
   Presentations      Subtitles
   Spreadsheets
```

This approach keeps `knvrt` small while taking advantage of mature, widely used conversion software.

## Requirements

`knvrt` requires Bash and one or more conversion backends, depending on the formats you want to process.

| Tool | Used for |
|---|---|
| Bash | `knvrt` itself |
| LibreOffice | Documents, presentations, spreadsheets, PDF export |
| FFmpeg | Audio, video, subtitles |
| ImageMagick | Images |

You do not need to install every backend if you only need a subset of the supported functionality.

## Installation

`knvrt` is currently distributed only through this GitHub repository. There are no official package-manager packages or prebuilt installers at this time.

### Install knvrt from GitHub

Clone the repository:

```bash
git clone https://github.com/sujeevanferos/knvrt.git
cd knvrt
```

Make the program executable:

```bash
chmod +x knvrt
```

Run it directly:

```bash
./knvrt --help
```

### Install for the current user

To make `knvrt` available as a normal command:

```bash
mkdir -p "$HOME/.local/bin"
cp knvrt "$HOME/.local/bin/knvrt"
chmod +x "$HOME/.local/bin/knvrt"
```

Add `$HOME/.local/bin` to your `PATH` if it is not already present.

For Bash:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Verify the installation:

```bash
knvrt --version
```

## Dependency Installation

Install the dependencies appropriate for your operating system. You only need the tools required by the conversions you intend to perform.

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install libreoffice ffmpeg imagemagick
```

### Ubuntu on WSL

The same Debian/Ubuntu packages work in WSL:

```bash
sudo apt update
sudo apt install libreoffice ffmpeg imagemagick
```

### Debian

```bash
sudo apt update
sudo apt install libreoffice ffmpeg imagemagick
```

### Fedora

```bash
sudo dnf install libreoffice ffmpeg ImageMagick
```

On some Fedora installations, FFmpeg packages may be provided through RPM Fusion rather than the default repositories. Follow Fedora/RPM Fusion documentation if `ffmpeg` is unavailable from the configured repositories.

### RHEL / Rocky Linux / AlmaLinux

LibreOffice and ImageMagick are available through the standard package ecosystem, while FFmpeg availability depends on the enabled repositories.

Typical package installation is:

```bash
sudo dnf install libreoffice ImageMagick
```

Install FFmpeg from an enabled repository appropriate for your distribution.

### Arch Linux / Manjaro

```bash
sudo pacman -S libreoffice-fresh ffmpeg imagemagick
```

For Arch-based systems, package names can vary with repository and distribution choices. Use the package manager to search when necessary:

```bash
pacman -Ss libreoffice ffmpeg imagemagick
```

### openSUSE

```bash
sudo zypper install libreoffice ffmpeg ImageMagick
```

### Alpine Linux

```bash
sudo apk add libreoffice ffmpeg imagemagick
```

Availability of the full LibreOffice package and individual FFmpeg codecs can vary by Alpine release and repository configuration.

### macOS

The recommended approach is Homebrew.

Install Homebrew from the official Homebrew documentation, then:

```bash
brew install --cask libreoffice
brew install ffmpeg imagemagick
```

Clone and install `knvrt` from GitHub as described above.

### Windows

`knvrt` is currently intended primarily for Unix-like environments. On Windows, the recommended option is WSL.

Install WSL and an Ubuntu distribution, then follow the Debian/Ubuntu instructions inside WSL:

```bash
sudo apt update
sudo apt install libreoffice ffmpeg imagemagick
```

Then clone and install `knvrt` inside the WSL environment.

Native Windows support may be considered in a future release.

## Checking Dependencies

Check that the backend programs are available:

```bash
command -v libreoffice
command -v ffmpeg
command -v magick
```

Depending on the ImageMagick package, the executable may also be exposed as `convert` rather than `magick`.

You can check the installed versions with:

```bash
libreoffice --version
ffmpeg -version
magick --version
```

## Usage

Basic syntax:

```text
knvrt <input> <output-format>
```

Examples:

```bash
knvrt document.docx pdf
knvrt presentation.pptx pdf
knvrt spreadsheet.xlsx pdf
knvrt image.webp jpg
knvrt audio.wav flac
knvrt video.mkv mp4
```

The generated file is normally written next to the source file using the source filename with the new extension.

## Batch Conversion

Shell glob patterns can be used to process multiple files.

Convert all Word documents to PDF:

```bash
knvrt "*.docx" pdf
```

Convert all PowerPoint presentations to PDF:

```bash
knvrt "*.pptx" pdf
```

Convert all WebP images to JPEG:

```bash
knvrt "*.webp" jpg
```

Convert all WAV files to FLAC:

```bash
knvrt "*.wav" flac
```

Convert all MKV files to MP4:

```bash
knvrt "*.mkv" mp4
```

Multiple file types can also be processed in a shell loop. For example, to convert both DOCX and PPTX files to PDF:

```bash
for file in *.docx *.pptx; do
    [ -f "$file" ] || continue
    knvrt "$file" pdf
done
```

## Document Conversion

LibreOffice is used for supported office and document conversions.

### DOCX to PDF

```bash
knvrt report.docx pdf
```

### PPTX to PDF

```bash
knvrt presentation.pptx pdf
```

### XLSX to PDF

```bash
knvrt spreadsheet.xlsx pdf
```

Supported document families include formats such as:

```text
doc  docx  docm  odt  rtf  txt  html  htm
```

## Presentation Conversion

Supported presentation families include:

```text
ppt  pptx  pptm  odp
```

Example:

```bash
knvrt slides.pptx pdf
```

## Spreadsheet Conversion

Supported spreadsheet families include:

```text
xls  xlsx  xlsm  ods  csv
```

Example:

```bash
knvrt data.xlsx pdf
```

## Image Conversion

ImageMagick is used for image conversions.

Examples:

```bash
knvrt image.webp jpg
knvrt image.png webp
knvrt image.jpg png
knvrt image.tiff webp
```

Common image formats include:

```text
jpg  jpeg  png  webp  gif  bmp  tiff  tif  svg  ico  avif  heic  heif
```

Actual format support depends on the installed ImageMagick build and its delegates.

## Audio Conversion

FFmpeg is used for audio conversion.

Examples:

```bash
knvrt song.wav mp3
knvrt song.mp3 flac
knvrt song.flac ogg
knvrt song.m4a wav
```

Common audio formats include:

```text
mp3  wav  flac  ogg  oga  opus  aac  m4a  wma  alac
```

The available codecs depend on the installed FFmpeg build.

## Video Conversion

FFmpeg is used for video conversion.

Examples:

```bash
knvrt video.mkv mp4
knvrt video.mp4 webm
knvrt video.avi mp4
knvrt video.mov mkv
```

Common video containers include:

```text
mp4  mkv  webm  avi  mov  flv  wmv  mpeg  mpg  m4v  ts  3gp
```

The available codecs and encoders depend on the installed FFmpeg build.

## Subtitle Operations

### Add selectable subtitles

Add an SRT subtitle file as a separate subtitle stream:

```bash
knvrt --subtitle video.mp4 subtitles.srt output.mp4
```

The subtitle remains selectable in compatible media players.

### Burn subtitles into the video

Render subtitles permanently into the video image:

```bash
knvrt --burn-subtitle video.mp4 subtitles.srt output.mp4
```

Burned subtitles cannot be disabled during playback because they become part of the video frames.

## Command-Line Help

Show the complete command usage information:

```bash
knvrt --help
```

or:

```bash
knvrt -h
```

Show the installed version:

```bash
knvrt --version
```

or:

```bash
knvrt -v
```

## Output Behaviour

By default, `knvrt` writes the converted file to the same directory as the source file.

For example:

```text
documents/
├── report.docx
└── report.pdf
```

and:

```text
images/
├── image.webp
└── image.jpg
```

Conversion may overwrite an existing destination file because some backend commands are configured to allow replacement. Take care when performing bulk conversions.

## Limitations

`knvrt` is a frontend to external conversion software. Its supported formats therefore depend on the installed versions and builds of LibreOffice, FFmpeg, and ImageMagick.

Not every format can be converted to every other format. Container formats, codecs, document features, fonts, subtitle formats, and backend-specific capabilities can affect conversion results.

For advanced codec options, quality controls, hardware acceleration, or format-specific features, the underlying backend may provide more control than the current `knvrt` interface.

## Project Status

`knvrt` is currently distributed directly from GitHub and is under active development.

The command-line interface and supported operations may evolve before a stable `1.0.0` release.

## Roadmap

Potential future improvements include:

- Multiple input patterns in one command.
- Recursive directory conversion.
- Custom output directories.
- Dependency checking and diagnostics.
- Format listing and capability discovery.
- Quality and bitrate presets.
- Video resolution and codec options.
- More advanced subtitle controls.
- PDF manipulation operations.
- Archive handling.
- Markdown/HTML conversion through additional backends.
- Parallel batch processing.
- Progress reporting.
- Shell completion for Bash, Zsh, and Fish.
- Installation through Linux and macOS package managers.
- Native Windows support.

## Development

Clone the repository:

```bash
git clone https://github.com/sujeevanferos/knvrt.git
cd knvrt
```

Run the program directly:

```bash
./knvrt --help
```

When modifying the shell script, ShellCheck is recommended:

```bash
shellcheck knvrt
```

## Contributing

Contributions, bug reports, and feature requests are welcome.

For changes, please:

1. Fork the repository.
2. Create a dedicated branch.
3. Make and test the changes.
4. Run ShellCheck where applicable.
5. Update the documentation if behaviour or supported formats change.
6. Open a pull request with a clear description of the change.

For substantial feature changes, opening an issue before implementation is recommended so that the design can be discussed first.

## Security Considerations

`knvrt` executes external conversion programs against user-provided files.

Use normal caution when converting files from untrusted sources, particularly office documents and media files that may be deliberately crafted to exercise parser or decoder vulnerabilities in the underlying tools.

`knvrt` does not currently provide a sandbox around LibreOffice, FFmpeg, or ImageMagick.

## License

`knvrt` is distributed under the MIT License. See [LICENSE](LICENSE) for the full license text.

Copyright (c) 2026 Sujeevan Feros.

## Maintainer

Maintained by [sujeevanferos](https://github.com/sujeevanferos).

Repository:

https://github.com/sujeevanferos/knvrt
