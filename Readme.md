# pomsky - lightweight webshell
[![Build Status](https://travis-ci.org/mperlet/pomsky.svg?branch=master)](https://travis-ci.org/mperlet/pomsky)
![pylint Score](https://mperlet.github.io/pybadge/badges/4.81.svg)

## What is pomsky?

![Pomsky](http://www.wallpapermaiden.com/image/2017/03/08/dog-yawning-tongue-muzzle-fluffy-animal-14137-thumb.jpg)

> Pomskies are robust like siberian huskies and small like pomeranians.

Pomsky is a very simple and rudimentary service to call programms via http.
To share files with python you can run `python -m SimpleHTTPServer`.
To execute programms or functions call `python pomsky.py`.

Pomsky executes user defined programms. The second main feature is the workingfile.
You can edit the workingfile via pomsky and use it as input for your scripts or
programms (See *What can i do with pomsky?*).   

#### Warning

There is **no security** logic in pomsky and it is not at all suitable for production!
So if you run pomsky as root (not recommended) all commands will be executed as root.  

## Design Goals

### Dependency Free
You need no extra packages, no easy_install no pip. Pomsky use only standard python librarys.

### Python Version Independent
Pomsky is tested from python version 2.3 to 3.6. See `Dockerfile` or `.travis.yml` for more information.

### Lightweight
Pomsky currently has a file size of 12KB.

## What do i need?

> Just python. Install via Github.

There is no dependency hell with pomksy. You only need python on your system.

## How to try?

```
curl https://raw.githubusercontent.com/mperlet/pomsky/master/pomsky.py -o pomsky.py
```

### Help

```
python pomsky.py -h

pomsky v0.0.1
usage: python pomsky.py [options]
Available options are:
  -h        prints help
  -w<FILE>  sets the workingfile, default: /tmp/pomsky.txt
  -p<PORT>  changes the port, default: 8888
  -d<DEBUG> sets the debug command, default: "du -h *"
  -a#<CMD>  sets a command a0 to a9 are free slots to define commands
            default: "ls > /dev/null"
  -v        verbose mode

Example:
    python pomsky.py -w"top.dat" -a0"ps -ax > top.dat" -a1"ls > top.dat"
```

## What can i do with pomsky?

Here some examples what you can do with pomsky.

### Video-Downloader

```
python pomsky.py -w"video_links.txt" -a0"youtube-dl --batch-file video_links.txt"
```

That creates a simple web ui for [youtube-dl](https://github.com/rg3/youtube-dl).

### Start, stop and edit a service

```
python pomsky.py -w"/etc/systemd/my_service.service" -a0"systemctl start my_service.service" -a0"systemctl stop my_service.service"
```

## Where are the tests?

The tests are based on curl requests/responses. So you need curl ¯\_(ツ)_/¯.

```
./test.sh && echo "OK" || echo "FAIL"
```

You can test against multiple python versions with docker.

```
./TestAllPythonVersionsWithDocker.sh
```

## Fun

### Generate a binary with cython
Generate a binary with cython via:

```
# create C source-code
cython3 --embed pomsky.py

# compile with clang or gcc
clang $(python3-config --cflags --ldflags) pomsky.c -o pomsky

# run pomsky
./pomsky
```
## Example setup with caddy and autossh

### pomsky and autossh on raspberry pi at home

Start pomsky on the local machine
```
python pomsky.py -p8888 -w"video_links.txt" -a0"youtube-dl --batch-file video_links.txt"
```
Start `autossh` to open a tunnel on a remote server
```
autossh -f -M 9999 -p2222 -NC -R 8888:localhost:8888 my@remoteServer.org
```

### caddy on the remote servier to make pomsky available with https
[caddy](https://caddyserver.com/)

Caddyfile for pomsky
```
pomksy.remoteServer.org {
    proxy / localhost:8888
}
```

Start caddy
```
/usr/local/bin/caddy -conf=/etc/caddy/Caddyfile
```

## TODO-List

- [ ] solve encoding problems
- [ ] enhance test cases
- [ ] support for [grumpy](https://github.com/google/grumpy)
- [ ] HTML in workingfile is not escaped (bug or feature?)

## License

MIT License

Copyright (c) 2017 Mathias Perlet

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
