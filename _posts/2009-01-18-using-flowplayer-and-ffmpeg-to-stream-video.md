---
layout: post
title: Using Flowplayer and ffmpeg to Stream Video
excerpt: How to convert videos using ffmpeg for in broswer streaming.
categories:
- Multimedia
- Programming
tags:
- Flash
- Multimedia
- PHP
status: publish
type: post
published: true
---
We recently had a client that had a need for hosting and streaming videos from their website. These videos were public
domain videos, some found on YouTube, NASA's website, and other resources, and the client was concerned that the videos
might be taken down or moved, so they were insistent on hosting the videos on their site. There was also a desire to
not require users to have to install Windows Media Player, Quick Time, Flash to be able to view all videos.

Our solution involved using [ffmpeg](http://ffmpeg.org/), an application that, among other things, converts audio and
video between file formats, to convert all uploaded videos to Flash format, and [Flowplayer](http://flowplayer.org/),
an open source Flash player, to stream the converted videos on demand.

This solution involved flagging all uploaded videos as `requires_conversion` in the database, and a cron script that
checks this table, and converts the videos one-by-one. Once available on the public website, Flowplayer takes over and
handles displaying the videos to visitors.

Since our application was written in PHP, we also used [ffmpeg-php](http://ffmpeg-php.sourceforge.net/) to pull the
duration from the videos, and also to generate thumbnails from the video at a random frame.

Some code samples:

**Pull Duration**

```php
<?php

$oMovie = new ffmpeg_movie( "/path/to/video.flv" );
echo $oMovie->getDuration(); // duration in seconds
```

**Get Thumbnail**

```php
<?php

$oMovie = new ffmpeg_movie( "/path/to/video.flv" );

// Grab the middle frame of the movie
$oFrame = $oMovie->getFrame( floor( $oMovie->getFrameCount() / 2 ) );
$oImage = $oFrame->toGDImage();

imagepng( $oImage, "/path/to/video.gif" );
imagedestroy( $oImage );
```
