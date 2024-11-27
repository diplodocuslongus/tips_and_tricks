Purpose: provide straight to the point solution to problems often (or rarely) encountered while coding, searching and working.


# ffmpeg

## video related

### convert video

### trim (in time) video

Fast, no re-encoding:
Crop from 1mn to 2mn, time format is hh:mm:ss

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

