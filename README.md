# Supercuts: Sabotage by Montage

Workshop by Sam Lavigne for [Sonic Acts](https://sonicacts.com), June 9th, 2022.

## Prerequisites

For this workshop you'll need [python](https://python.org) (version 3.6 or greater), [ffmpeg](https://ffmpeg.org/), and [videogrep](https://antiboredom.github.io/videogrep/) installed on your computer.

### For Mac Users


#### Install `brew`

`brew` is an application that lets you install other applications. To get it on your computer, first open the `Terminal` application. It's your Applications folder, in folder called "Utilities".

Then copy and paste the following command *exactly* as shown below, and hit the return key:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Follow any instructions that appear.


#### Install ffmpeg

In the terminal, type (or copy/paste) the following and hit return:

```
brew install ffmpeg
```


#### Install Python

It's likely that you already have Python on your computer. To test, open the terminal, and then just type: `python3` and hit the return key.

If you see a message similar to this, then Python is installed:

```bash
Python 3.8.6 (default, May 16 2022, 18:46:03)
[Clang 12.0.5 (clang-1205.0.22.11)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

If you see something like this:

```bash
command not found python3
```

You will need to install Python. Just type:

```
brew install python3
```

#### Install Videogrep and Vosk

Once python is installed, in the terminal type:

```
pip3 install videogrep
```

Then type:

```
pip3 install vosk
```

NOTE: if you are on a new Mac, installing vosk may not work. Don't be alarmed.



### For Windows Users

1. Download and install Python from: https://www.python.org/downloads/
2. Download and install Git for Windows from: https://gitforwindows.org/
3. Open the Git BASH application, which should now appear in your start menu.
4. Type `pip3 install videogrep` and hit enter.
5. Type `pip3 install vosk` and hit enter.
6. Install ffmpeg: follow the instructions here: https://windowsloop.com/install-ffmpeg-windows-10/


