# Guitar on Linux

The audio interface being used is a `Native Instruments Komplete Audio 1`

The distribution being used is Fedora 32

### Hardware settings for the audio interface

* USB - Connected to computer
* IN1 - Disconnected - Gain set to zero
* IN2 - Connect guitar here - Gain set to zero (manage it in Guitarx)
* Mode switch - Set to `INST` for guitar
* 48V Phantom Power switch - Off, this is only used for EC Microphones
* Input / Host knob - Dial all the way to host
* Vol Jack - Connect headphones here and adjust volume with front knob (set to zero to begin and wind it up)
* Top Vol knob - Used for RCA out, set to zero if not using

### Packages

Fedora natively packages everything you need

* The JACK daemon and related tools are used for managing the inputs
* Guitarix is the virtual amp we are using
* Audacity is used for recording and audio editing

```
sudo dnf install guitarix audacity jack-audio-connection-kit qjackctl \
    alsa-plugins-jack pulseaudio-module-jack jack_capture
```

### Real time scheduling for JACK

Due to audio being latency sensitive, JACK requires real time scheduling.

This requires privileges due to the ability for an application to abuse it and negatively impact the system.

Fortunately it can be permitted per user/group via PAM.

**You do not require a real time Kernel**

The documentation for what you need to do is here: https://jackaudio.org/faq/linux_rt_config.html

I discovered the Fedora package already handled this though, all that is required is to add ourselves to a new group it created and re-log (or run `su - $USER` in the terminal where you will run JACK)

```
sudo usermod -a -G jackuser $USER
```

### Playing music

1. Connect everything to your Audio Interface
2. Run `qjackctl`
  1. Click Setup > Settings > Advanced > Set your Output and Input devices to your Audio Interface
  2. Click Setup >  Settings > Parameters > Set Sample Rate: 48000, Frames/Period: 256, Periods/Buffer: 2
  3. Apply all settings and close Setup window
  3. Click Start in the main `qjackctl` window
3. Run `guitarix`
4. In the main `qjackctl` window click Graph, you should see your Audio Interface inputs/outputs and the Guitarx components (amp and effects), wire them together:

In my case it went:

```
system capture_2 > gx_head_amp in_0
gx_head_amp out_0 > gx_head_fx in_0
gx_head_fx out_0 > system playback_1
gx_head_fx out_1 > system playback_2
```

Plug in and play!

### References

* https://www.schotty.com/EL_And_Fedora/Fedora_Plus_Electric_Guitar/
* https://fedoramagazine.org/configure-fedora-to-practise-and-compose-music/
