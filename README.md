Purpose: provide straight to the point solution to problems often (or rarely) encountered while coding, searching and working.

# Productivity (linux related)

## clean up space in /usr

### clean up /usr/share/doc

https://askubuntu.com/questions/129566/remove-documentation-to-save-hard-drive-space
Create a file /etc/dpkg/dpkg.cfg.d/01_nodoc which specifies the desired filters. Example:

cd /etc/dpkg/dpkg.cfg.d

sudo vi 01_nodoc

This is mine, on the gonze:
 (I kept the man)

    # purpose: exclude documentation installation 
    path-exclude /usr/share/doc/*
    # we need to keep copyright files for legal reasons
    path-include /usr/share/doc/*/copyright
    # I keep the man page can be usefull for CLI
    # path-exclude /usr/share/man/*
    path-exclude /usr/share/groff/*
    path-exclude /usr/share/info/*
    # lintian stuff is small, but really unnecessary
    path-exclude /usr/share/lintian/*
    path-exclude /usr/share/linda/*


Then:

find /usr/share/doc -depth -type f ! -name copyright|sudo xargs rm || true
du -h doc
find /usr/share/doc -empty|sudo xargs rmdir || true
sudo rm -rf /usr/share/groff/* /usr/share/info/*
sudo rm -rf /usr/share/lintian/* /usr/share/linda/*


Doc was reduced from ~600MB down to ~200MB (not sure why I couldn't shrink it further  though)

## CLI Command Line 

## copy - paste

Primary buffer
Secondary buffer

    xclip

    sudo apt install xclip

Using xclip has no effect (shft-ctl-v does nothing) on RPi, use wp-clipboard

https://github.com/bugaevc/wl-clipboard

There is also copyQ but it's not pure CLI.

https://github.com/hluk/CopyQ

### get number of processors

    nproc --all

build with max number of processors

    make -j$(nproc)

### history

Clear history and don't show it has been cleaned:

    history -c && history > /dev/null

Delete content of ~/.bash_history

Delete a given line in the history:

Edit .bash_history or 
history -d line_number


## files, directories, management

### count files in directory

    tree

    ls | wc -l

    ls -1 | wc -l

-1: (that's a ONE) only one entry per line. Change it to -1a if you want hidden files too
wc: "wordcount"
-l: count lines.

only some files, ex all files starting w tof :

    ls tof* | wc -l



### replace string in files

#### specific string in all directories and sub

First search to make sure of what will be replaced:

Will search and show all instances of speed

    find . -type d -name "third-party" -prune -o -type f \( -name "*.cpp" -o -name "*.h" \) -exec grep --color=always -Hn "speed" {} +

Also:
search for any word with speed in it
grep -r -n --include=\*.{cpp,h} "speed" .

for single word (TODO check it's correct)
grep -r -n -w --include=\*.{cpp,h} "speed_" .

Replace e.g.:

speedalign with velocityalign

    find . -type f \( -name "*.cpp" -o -name "*.h" \) -print0 | xargs -0 sed -i 's/speedalign/velocityalign/g'

####

Non-recursive, non-hidden files in this directory only:

    perl -i -pe 's/foo/bar/g' ./* 
    sed -i 's/foo/bar/g' ./*

Recursive, regular files (including hidden ones) in this and all subdirectories

    find . -type f -exec perl -i -pe 's/foo/bar/g' {} +
    find . -type f -exec sed -i 's/foo/bar/g' {} +

Replace only if the file name matches another string / has a specific extension / is of a certain type etc:

    Non-recursive, files in this directory only:

    sed -i -- 's/foo/bar/g' *baz*    ## all files whose name contains baz
    sed -i -- 's/foo/bar/g' *.baz    ## files ending in .baz

    (using the -- option delimiter rather than ./ prefix works here as the filenames are guaranteed not to be - and makes the argument list shorter, less likely to break the system limit on the size of the arguments)

    Recursive, regular files in this and all subdirectories

    find . -name '*baz*' -type f -exec sed -i 's/foo/bar/g' {} +


Replace only if the string is found in a certain context

    Replace foo with bar only if it's followed by baz:

    sed -i 's/foo\(.*baz\)/bar\1/' file

Replace foo with bar only on the 4th line:

    sed -i '4s/foo/bar/g' file
    gawk -i /usr/share/awk/inplace.awk 'NR==4{gsub(/foo/,"baz")};1' file
    perl -i -pe 's/foo/bar/g if $.==4' file


 Multiple replace operations: replace with different strings

    You can combine sed commands:

    sed -i 's/foo/bar/g; s/baz/zab/g; s/Alice/Joan/g' file

    Be aware that order matters (sed 's/foo/bar/g; s/bar/baz/g' will substitute foo with baz).

    or Perl commands

    perl -i -pe 's/foo/bar/g; s/baz/zab/g; s/Alice/Joan/g' file

    If you have a large number of patterns, it is easier to save your patterns and their replacements in a sed script file:

    #!/usr/bin/sed -f
    s/foo/bar/g
    s/baz/zab/g

Multiple replace operations: replace multiple patterns with the same string

    Replace any of foo, bar or baz with foobar

    sed -Ei 's/foo|bar|baz/foobar/g' file

    or

    perl -i -pe 's/foo|bar|baz/foobar/g' file

Replace File paths in multiple files

    Another use case of using different delimiter:

    sed -i 's|path/to/foo|path/to/bar|g' ./*

    or

    perl -i -pe 's|path/to/foo|path/to/bar|g' ./*

More: https://unix.stackexchange.com/a/112024/209363

### rename files

ex: change space to _ (underscore) for all python files

    rename -n 's/ /_/g' ./*.py

to see the change without any action taken, use the -n switch.

    rename -n 's/ /_/g' ./*.py

### limit / prune number of files shown with tree

use filelimit:

    $ tree /usr --filelimit 10
    /usr
    ├── bin [3260 entries exceeds filelimit, not opening dir]
    ├── include [1110 entries exceeds filelimit, not opening dir]

### search string in files

Use ripgrep.

Limit search to specific files (extension)

Search for a string in c and python files ('c' includes .h, .c but not cpp)

    rg search_string --type t c --type py

This works also (no spaec):

    rg search_string -tc -tpy

Exclude file types:

    rg search_string --type-not cpp

or: 
    
    rg search_string -Tcpp

All file types:

    rg --type-list




# ffmpeg

## video related

### detect, remove black frames

Application: start OBS recording before the actual application to record begins results in blcak frames at the beginning.

Detect them with:

ffmpeg -i WP_nav_4pts.mp4 -vf blackframe=amount=98:threshold=32 -an -f null -

Will show the amount of black for each frame:

[Parsed_blackframe_0 @ 0x5c669f63c080] frame:1 pblack:100 pts:1 t:0.033333 type:B last_keyframe:0
[Parsed_blackframe_0 @ 0x5c669f63c080] frame:2 pblack:100 pts:2 t:0.066667 type:B last_keyframe:0
[Parsed_blackframe_0 @ 0x5c669f63c080] frame:3 pblack:100 pts:3 t:0.100000 type:B last_keyframe:0
[...]
[Parsed_blackframe_0 @ 0x5c669f63c080] frame:69 pblack:99 pts:69 t:2.300000 type:B last_keyframe:0
[Parsed_blackframe_0 @ 0x5c669f63c080] frame:70 pblack:99 pts:70 t:2.333333 type:B last_keyframe:0
[Parsed_blackframe_0 @ 0x5c669f63c080] frame:71 pblack:99 pts:71 t:2.366667 type:B last_keyframe:0

Non black frame starts at frame #69 and time 2.3.

This can be used with ffplay as well, to play the video while also outputint the above info in the console:

    ffplay -i input.mp4 -vf blackframe=amount=98:threshold=32

Use these (frame # or time) to either remove the black frame from the input or convert directly to another file.

Trim the begining (containing blackframe but it can be something else to trim):

    ffmpeg -i WP_nav_4pts.mp4 -ss 1.8 -c:v libx264 -c:a aac output.mp4

Or save as ogv:
(with audio)

    ffmpeg -ss 2.1 -i WP_nav_4pts.mp4 -c:v libtheora -c:a libvorbis WP_nav_4pts.ogv


(without audio)

    ffmpeg -ss 2.1 -i WP_nav_4pts.mp4 -c:v libtheora -an WP_nav_4pts.ogv


### repair video

To resolve the "moov atom not found" error in FFmpeg (faced in opencv from a corupted video), run the following command line:

    ffmpeg -i input_video_file.mp4 -vcodec copy -acodec copy -movflags faststart output_video_file.mp4

### convert video

Convert to ogv:

(with audio)

    ffmpeg -ss 2.1 -i input.mp4 -c:v libtheora -c:a libvorbis output.ogv

(without audio)

    ffmpeg -ss 2.1 -i input.mp4 -c:v libtheora -an output.ogv

### trim (in time) video



Fast, no re-encoding:
Crop from 1mn, duration 2mn, time format is hh:mm:ss

    ffmpeg -ss 00:01:00 -i input.mkv -to 00:02:00 -c copy output.mp4

ss: seek start: begining of where to trim
to: time where to stop the trim (not a duration)

Otherways: TODO: organize:
use the trim filter
https://www.bannerbear.com/blog/how-to-trim-a-video-using-ffmpeg/

https://superuser.com/questions/138331/using-ffmpeg-to-cut-up-video

script:
https://gist.github.com/ceremcem/4c7b44edd6c21c873f7f27bdaf7f2baa
https://stackoverflow.com/questions/18444194/cutting-multimedia-files-based-on-start-and-end-time-using-ffmpeg
https://www.arj.no/2018/05/18/trimvideo/


### extract image

extract 1 frame at the 4th second.

    ffmpeg -ss 00:00:04 -i input.mp4 -frames:v 1 screenshot.png

check...

    ffmpeg -i <input> -vf "select=eq(n\,34)" -vframes 1 out.png

https://ffmpeg.org/ffmpeg-filters.html#select_002c-aselect

extract several frame from list:

https://superuser.com/questions/1707609/ffmpeg-extract-several-individual-frames-from-a-video-given-a-list-of-timestamp


### crop video

## audio

### extract audio

## Others

### Get frame number from timestamp

https://stackoverflow.com/questions/69653703/how-to-get-frame-number-given-a-timestamp-using-ffmpeg

    ffmpeg -t 01:05 -i input.mp4 -nostats -vcodec copy -y -f rawvideo /dev/null 2>&1  |\
       grep frame |\
       awk '{print $2}'

### overlay frame number

https://stackoverflow.com/questions/15364861/frame-number-overlay-with-ffmpeg

# vim

## buffers

Open several files at once, type `:buffers` to see all the files in the vim buffers.

example:

    vi README.md


Then from within vim, `:e another_file` and again `:e yet_another_file`

The current visible file will be `yet_another_file`.

See all the files in the buffer: `:buffers`

Open a given buffer (here the buffer number 1 corresponding to README.md)  with:

    :b1[enter]

# libraries, packages

## opencv

### get version

    pkg-config --modversion opencv4
