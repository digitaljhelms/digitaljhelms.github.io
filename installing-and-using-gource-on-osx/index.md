# Installing & Using Gource on OS X

*Published on November 10, 2011*

> Gource is a software version control visualization tool.
> 
> Software projects are displayed by Gource as an animated tree with the root directory of the project at its centre. Directories appear as branches with files as leaves. Developers can be seen working on the tree at the times they contributed to the project.

http://code.google.com/p/gource/

## Installing Gource Manually (w/out MacPorts or Homebrew)

Not for the faint of heart...

### Install SDL

```language-bash
$ hg clone -u SDL-1.2 http://hg.libsdl.org/SDL SDL-1.2
$ cd SDL-1.2
$ ./autogen.sh
$ ./configure && make && sudo make install
```

### Install SDL Image

```language-bash
$ hg clone -u release-1.2.10 http://hg.libsdl.org/SDL_image SDL_Image-1.2.10
$ cd SDL_Image-1.2.10
$ ./autogen.sh
$ ./configure && make && sudo make install
```

### Install PCRE3

```language-bash
$ svn co svn://vcs.exim.org/pcre/code/trunk pcre
$ cd pcre
$ ./autogen.sh
$ ./configure && make && sudo make install
```

### Install GLEW

Note: Installing from the public Git repository fails on OS X.

```language-bash
$ curl -L -O http://sourceforge.net/projects/glew/files/glew/1.7.0/glew-1.7.0.tgz
$ tar xvfz glew-1.7.0.tgz
$ rm glew-1.7.0.tgz
$ cd glew-1.7.0
$ make && sudo make install
```

### Install Gource

```language-bash
$ git clone git://github.com/acaudwell/Gource.git
$ cd Gource
$ git co gource-0.37
$ git submodule init
$ git submodule update
$ autoreconf -f -i
$ ./configure && make && sudo make install
```

### Usage

```language-bash
$ gource ./ -s 0.5 -b 000000 -1280x720
```

## Optional Features/Installs

### Install FFmpeg

This will enable outputting to video.

#### Yasm (requirement)

```language-bash
$ git clone git://github.com/yasm/yasm.git
$ cd yasm
$ git co v1.2.0
$ ./autogen.sh
$ ./configure && make && sudo make install
```

#### LAME (optional requirement)

_Optional: This allows laying an audio track over the visualization._

```language-bash
$ curl -L -O http://sourceforge.net/projects/lame/files/lame/3.99/lame-3.99.1.tar.gz
$ tar xvfz lame-3.99.1.tar.gz
$ rm lame-3.99.1.tar.gz
$ cd lame-3.99.1
$ ./configure && make && sudo make install
```

#### x264 (requirement)

```language-bash
$ curl -L -O ftp://ftp.videolan.org/pub/x264/snapshots/last_x264.tar.bz2
$ tar xvfj last_x264.tar.bz2
$ rm last_x264.tar.bz2
$ cd x264-snapshot-[datestamp]-[timestamp]
$ ./configure --enable-shared
$ make && sudo make install
```

#### Xvid (requirement)

```language-bash
$ curl -L -O http://downloads.xvid.org/downloads/xvidcore-1.3.2.tar.gz
$ tar xvfz xvidcore-1.3.2.tar.gz
$ rm xvidcore-1.3.2.tar.gz
$ cd xvidcore/build/generic/
$ ./configure && make && sudo make install
```

#### FFmpeg

```language-bash
$ curl -L -O http://ffmpeg.org/releases/ffmpeg-0.8.6.tar.gz
$ tar xvfz ffmpeg-0.8.6.tar.gz
$ rm ffmpeg-0.8.6.tar.gz
$ cd ffmpeg-0.8.6
$ ./configure --enable-shared --enable-libmp3lame --enable-gpl --enable-libx264 --enable-libxvid
$ make && sudo make install
```

_Note: If LAME was not installed, exclude the `--enable-libmp3lame` argument from the `configure` command._

#### Usage

The command below will create a video at 28fps using the x264 codec in an mp4 container with an audio track over the top -- the output will be the length of the video/audio track, whichever is longer.

```language-bash
$ gource ./ -s 0.5 -b 000000 -1280x720 --output-ppm-stream - | ffmpeg -y -b 3000K -r 28 -f image2pipe -vcodec ppm -i - -i audio.mp3 -vcodec libx264 -preset slow -crf 28 -threads 0 output.mp4
```

_Note: If LAME was not installed, exclude the `-i audio.mp3` argument/value from the piped `ffmpeg` command._

### Gravatar Support

Gource supports user images; here is a Perl script that will pull images from Gravatar based on email addresses found in `git-log`.

<!-- https://gist.github.com/digitaljhelms/1359047/raw/gistfile1.pl -->
```language-clike
#!/usr/bin/perl
#fetch Gravatars
#http://code.google.com/p/gource/wiki/GravatarExample

use strict;
use warnings;

use LWP::Simple;
use Digest::MD5 qw(md5_hex);

my $size       = 90;
my $output_dir = '.git/avatar';

die("no .git/ directory found in current path\n") unless -d '.git';

mkdir($output_dir) unless -d $output_dir;

open(GITLOG, q/git log --pretty=format:"%ae|%an" |/) or die("failed to read git-log: $!\n");

my %processed_authors;

while(<GITLOG>) {
    chomp;
    my($email, $author) = split(/\|/, $_);

    next if $processed_authors{$author}++;

    my $author_image_file = $output_dir . '/' . $author . '.png';

    #skip images we have
    next if -e $author_image_file;

    #try and fetch image

    my $grav_url = "http://www.gravatar.com/avatar/".md5_hex(lc $email)."?d=404&size=".$size;

    warn "fetching image for '$author' $email ($grav_url)...\n";

    my $rc = getstore($grav_url, $author_image_file);

    sleep(1);

    if($rc != 200) {
        unlink($author_image_file);
        next;
    }
}

close GITLOG;
```

#### Usage

```language-bash
$ gource --user-image-dir .git/avatar/
```