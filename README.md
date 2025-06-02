Purpose: provide straight to the point solution to problems often (or rarely) encountered while coding, searching and working.

# Productivity (linux related)

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

## files, directories, management

### get number of processors

    nproc --all

build with max number of processors

    make -j$(nproc)

### replace string in files


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

### count files in directory

    tree

    ls | wc -l

    ls -1 | wc -l

-1: (that's a ONE) only one entry per line. Change it to -1a if you want hidden files too
wc: "wordcount"
-l: count lines.

only some files, ex all files starting w tof :

    ls tof* | wc -l

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


# ffmpeg

## video related

### repair video

To resolve the "moov atom not found" error in FFmpeg (faced in opencv from a corupted video), run the following command line:

    ffmpeg -i input_video_file.mp4 -vcodec copy -acodec copy -movflags faststart output_video_file.mp4

### convert video

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
