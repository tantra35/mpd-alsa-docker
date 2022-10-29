# mpd-alsa-docker

A Docker image for mpd with support for both Alsa (`alsa`) and PulseAudio (`pulse`).  
It also includes `mpdscribble`. In alternative, you can use [mpd-scrobbler-docker](https://github.com/GioF71/mpd-scrobbler-docker) as the scrobbler for this image.  
User mode is now possible with `alsa` mode, and of course it is mandatory (enforced) for `pulse` mode.

## Available Archs on Docker Hub

- linux/amd64
- linux/arm/v7
- linux/arm64/v8

## References

First and foremost, the reference to the awesome projects:

[Music Player Daemon](https://www.musicpd.org/)  
[MPDScribble](https://www.musicpd.org/clients/mpdscribble/)

## Links

Source: [GitHub](https://github.com/giof71/mpd-alsa-docker)  
Images: [DockerHub](https://hub.docker.com/r/giof71/mpd-alsa)

## Why

I prepared this Dockerfile because I wanted to be able to install mpd easily on any machine (provided the architecture is amd64 or arm). Also I wanted to be able to configure and govern the parameters easily, with particular and exclusive reference to the configuration of a single ALSA output. Configuring the container is easy through a webapp like Portainer.

## Prerequisites

You need to have Docker up and running on a Linux machine, and the current user must be allowed to run containers (this usually means that the current user belongs to the "docker" group).

You can verify whether your user belongs to the "docker" group with the following command:

`getent group | grep docker`

This command will output one line if the current user does belong to the "docker" group, otherwise there will be no output.

The Dockerfile and the included scripts have been tested on the following distros:

- Manjaro Linux with Gnome (amd64)
- Asus Tinkerboard
- Raspberry Pi 3 and 4, both 32bit and 64bit

As I test the Dockerfile on more platforms, I will update this list.

## Get the image

Here is the [repository](https://hub.docker.com/repository/docker/giof71/mpd-alsa) on DockerHub.

Getting the image from DockerHub is as simple as typing:

`docker pull giof71/mpd-alsa`

You might want to pull the `stable` image as opposed to the default `latest`.

## Usage

### Volumes

The following tables lists the volumes:

VOLUME|DESCRIPTION
:---|:---
/db|Where the mpd database is saved
/music|Where the music is stored. you might consider to mount your directory in read-only mode (`:ro`)
/playlists|Where the playlists are stored
/log|Where `mpd.log` is written
/app/scribble|Where `mpdscribble` will write its journals and its log file

### Environment Variables

The following tables lists all the currently supported environment variables:

VARIABLE|DEFAULT|NOTES
:---|:---:|:---
OUTPUT_MODE|alsa|Output mode, can be `alsa` or `pulse`. For `pulse` mode, running in `user` mode is required.
USER_MODE||Set to `Y` or `YES` for user mode. Case insensitive. See [User mode](#user-mode). Enforced when `OUTPUT_MODE` is set to `pulse`.
PUID||User id. Defaults to `1000`. The user/group will be created for `pulse` mode regardless of the `USER_MODE` variable.
PGID||Group id. Defaults to `1000`. The user/group will be created for `pulse` mode regardless of the `USER_MODE` variable.
PGID||Group id. Defaults to `1000`.
AUDIO_GID||`audio` group id from the host machine. Mandatory for `alsa` output in user mode. See [User mode](#user-mode).
MPD_AUDIO_DEVICE|default|The audio device. Common examples: `hw:DAC,0` or `hw:x20,0` or `hw:X20,0` for usb dac based on XMOS
ALSA_DEVICE_NAME|Alsa Device|Name of the Alsa Device
MIXER_TYPE|hardware|Mixer type
MIXER_DEVICE|default|Mixer device
MIXER_CONTROL|PCM|Mixer Control
MIXER_INDEX|0|Mixer Index
DOP|yes|Enables Dsd-Over-Pcm
ALSA_OUTPUT_FORMAT||Sets `alsa` output format. Example value: `192000:24:2`
ALSA_ALLOWED_FORMATS||Sets the `alsa` output allowed formats
REPLAYGAIN_MODE|0|ReplayGain Mode
REPLAYGAIN_PREAMP|0|ReplayGain Preamp
REPLAYGAIN_MISSING_PREAMP|0|ReplayGain mising preamp
REPLAYGAIN_LIMIT|yes|ReplayGain Limit
VOLUME_NORMALIZATION|no|Volume normalization
SAMPLERATE_CONVERTER||Configure `samplerate_converter`. Example value: `soxr very high`. Do not use in conjunction when `SOXR_PLUGIN_ENABLE` is set to enabled
SOXR_PLUGIN_ENABLE||Enable the `soxr` plugin. Do not use in conjunction with variable `SAMPLERATE_CONVERTER`
SOXR_PLUGIN_THREADS||The number of libsoxr threads. `0` means automatic. The default is `1` which disables multi-threading.
SOXR_PLUGIN_QUALITY||The quality of `soxr` resampler. Possible values: `very high`, `high` (the default), `medium`, `low`, `quick`, `custom`. When set to `custom`, the additional `soxr` parameters can be set.
SOXR_PLUGIN_PRECISION||The precision in bits. Valid values 16,20,24,28 and 32 bits.
SOXR_PLUGIN_PHASE_RESPONSE||Between the 0-100, where `0` is MINIMUM_PHASE and `50` is LINEAR_PHASE
SOXR_PLUGIN_PASSBAND_END||The % of source bandwidth where to start filtering. Typical between the 90-99.7.
SOXR_PLUGIN_STOPBAND_BEGIN||The % of the source bandwidth Where the anti aliasing filter start. Value 100+.
SOXR_PLUGIN_ATTENUATION||Reduction in dB’s to prevent clipping from the resampling process
SOXR_PLUGIN_FLAGS||Bitmask with additional option see soxr documentation for specific flags
QOBUZ_PLUGIN_ENABLED|no|Enables the Qobuz plugin
QOBUZ_APP_ID|ID|Qobuz application id
QOBUZ_APP_SECRET|SECRET|Your Qobuz application Secret
QOBUZ_USERNAME|USERNAME|Qobuz account username
QOBUZ_PASSWORD|PASSWORD|Qobuz account password
QOBUZ_FORMAT_ID|5|The Qobuz format identifier, i.e. a number which chooses the format and quality to be requested from Qobuz. The default is “5” (320 kbit/s MP3).
TIDAL_PLUGIN_ENABLED|no|Enables the Tidal Plugin. Note that it seems to be currently defunct: see the mpd official documentation.
TIDAL_APP_TOKEN|TOKEN|The Tidal application token. Since Tidal is unwilling to assign a token to MPD, this needs to be reverse-engineered from another (approved) Tidal client.
TIDAL_USERNAME|USERNAME|Tidal Username
TIDAL_PASSWORD|PASSWORD|Tidal password
TIDAL_AUDIOQUALITY|Q|The Tidal “audioquality” parameter. Possible values: HI_RES, LOSSLESS, HIGH, LOW. Default is HIGH.
LASTFM_USERNAME||Username for Last.fm.
LASTFM_PASSWORD||Password for Last.fm
LIBREFM_USERNAME||Username for Libre.fm
LIBREFM_PASSWORD||Password for Libre.fm
JAMENDO_USERNAME||Username for Jamendo
JAMENDO_PASSWORD||Password for Jamendo
PROXY||Proxy support for `mpdscribble`. Example value: `http://the.proxy.server:3128`
MPD_LOG_LEVEL||Can be `default` or `verbose`
STARTUP_DELAY_SEC|0|Delay before starting the application. This can be useful if your container is set up to start automatically, so that you can resolve race conditions with mpd and with squeezelite if all those services run on the same audio device. I experienced issues with my Asus Tinkerboard, while the Raspberry Pi has never really needed this. Your mileage may vary. Feel free to report your personal experience.

### Examples

#### Alsa Mode

You can start mpd-alsa in `alsa` mode by simply typing:

```text
docker run -d \
    --name=mpd-alsa \
    --rm \
    --device /dev/snd \
    -p 6600:6600 \
    -v ${HOME}/Music:/music:ro \
    -v ${HOME}/.mpd/playlists:/playlists \
    -v ${HOME}/.mpd/db:/db \
    giof71/mpd-alsa
```

Note that we need to allow the container to access the audio devices through `/dev/snd`. We need to give access to port `6600` so we can control the newly created mpd instance with our favourite mpd client.

#### Pulse Mode

You can start mpd-alsa in `pulse` mode by simply typing:

```text
docker run -d \
    --name=mpd-alsa \
    --rm \
    --device /dev/snd \
    -p 6600:6600 \
    -e OUTPUT_MODE=pulse \
    -v ${HOME}/Music:/music:ro \
    -v ${HOME}/.mpd/playlists:/playlists \
    -v ${HOME}/.mpd/db:/db \
    -v /run/user/1000/pulse:/run/user/1000/pulse \
    giof71/mpd-alsa
```

Note that we need to allow the container to access the pulseaudio by mounting `/run/user/$(id -u)/pulse`, which typically translates to `/run/user/1000/pulse`.  

## User mode

You can enable user-mode by specifying `USER_MODE` to `Y` or `YES`.  
For `alsa` mode, it is important that the container knows the group id of the host `audio` group. On my system it's `995`, however it is possible to verify using the following command:

```code
getent group audio
```

On my system, this commands outputs:

```text
audio:x:995:brltty,mpd,squeezelite
```

In any case, make sure to set the variable `AUDIO_GID` accordingly. The variable is mandatory for user mode with alsa output.  
Also, if your user/group id are not both `1000`, set `PUID` and `PGID` accordingly.  
It is possible to verify the uid and gid of the currently logged user using the following command:

```code
id
```

On my system this command outputs:

```text
uid=1000(giovanni) gid=1000(giovanni) groups=1000(giovanni),3(sys),90(network),98(power),957(autologin),965(docker),967(libvirt),991(lp),992(kvm),998(wheel)
```

## Support for Scrobbling

If at least one set of credentials for `Last.fm`, `Libre.fm` or `Jamendo` are provided through the environment variables, `mpdscribble` will be started and it will scrobble the songs you play.

## Run as a user-level systemd

When using a desktop system with PulseAudio, running a docker-compose with a `restart=unless-stopped` is likely to cause issues to the entire PulseAudio. At least that is what is systematically happening to me on my desktop systems.  
You might want to create a user-level systemd unit. In order to do that, move to the `pulse` directory of this repo, then run the following to install the service:

```code
./install.sh
```

After that, the service can be controlled using `./start.sh`, `./stop.sh`, `./restart.sh`.  
You can completely uninstall the service by running:

```code
./uninstall.sh`
```

## Build

You can build (or rebuild) the image by opening a terminal from the root of the repository and issuing the following command:

`docker build . -t giof71/mpd-alsa`

It will take very little time even on a Raspberry Pi. When it's finished, you can run the container following the previous instructions.  
Just be careful to use the tag you have built.

## Change History

Date|Major Changes
:---|:---
2022-10-29|PulseAudio user-level systemd service introduced
2022-10-26|Added support for `soxr` plugin
2022-10-26|Added support for `alsa` output format (`OUTPUT_FORMAT`)
2022-10-26|Added support for samplerate_converter
2022-10-26|Added support for PulseAudio mode
2022-10-26|Build mpd.conf at container runtime
2022-10-22|Add support for daily builds
2022-10-22|Add builds for ubuntu kinetic as well as for the current lts versions of ubuntu
2022-10-22|Fixed `AUDIO-GID` now effectively defaulting to `995`
2022-10-21|User mode support
2022-10-21|Add logging support
2022-10-20|Included `mpdscribble` for scrobbling support
2022-10-20|Multi-stage build
2022-10-05|Reviewed build process
2022-10-05|Add build from debian:bookworm-slim
2022-04-30|Rebased to mpd-base-images built on 2022-04-30
2022-03-12|Rebased to mpd-base-images built on 2022-03-12
2022-02-26|Rebased to mpd-base-images built on 2022-02-26
2022-02-25|Add README.md synchronization towards Docker Hub
2022-02-13|File `/etc/mpd.conf` is not overwritten. Using file `/app/conf/mpd-alsa.conf`. Launcher script moved to `/app/bin` in the container. Repository files reorganized.
2022-02-11|Automated builds thanks to [Der-Henning](https://github.com/Der-Henning/), Builds for arm64 also thanks to [Der-Henning](https://github.com/Der-Henning/), the README.md you are reading now is copied to the image under path `/app/doc/README.md`. Building from debian bullseye, debian buster and ubuntu focal. Created convenience script for local build.
