
---
title: "My thoughts on Cosmo Communicator OTA availability"
date: "2023-08-15"
---

## DISCLAIMER: As I have signed an NDA with Planet Computers, this post is of my own views, and NOT those of Planet Computers. They should not be contacted for comment.
## I am restricted in the information I can release, so please, no emails or DMs asking for information.

# UPDATE:

I've found a copy of `UpdatePackage.zip`. which I've published on GitHub. More
soon.

# Synposis

Just a little blog post to address recent posts I've seen on
FB/Discord/OESF/etc about Cosmo Communicator OTA updates.

## Keywords:

First of all, it's necessary to define key terms regarding the Cosmo:

- Recovery:
    Used for multi-boot, factory reset, bootloader access, and applying ZIP
    files via `adb sideload` or SD card - but not from the Planet website. What
    can be applied are things like the Magisk ZIP installer, but that's not
    recommended.

- 'Rooted firmware'
    This is a bit of a misnomer. The Android data is shared between the
    'trusted' boot slot (i.e, the `boot` partition, not the other four slots
    (maybe more if we get LVM support merged)), and the 'untrusted' boot slot,
    which you would generally flash a root image to via recovery.

    Planet needs to clarify this. The firmware is merely a 'Android Boot
    Image', or 'kernel+ramdisk'. It has the Magisk-specific parts applied and
    only runs when you select this boot slot. *However*, it is worth noting
    that if you reboot to non-rooted Android, traces of being 'rooted' are
    still present, and software like 'SafetyNet' and 'Play Integrity' are
    perfectly capable of detecting these traces.

## So, what's going on with V25?

I'm not employed by Planet Computers, but as an embedded developer, my educated
guess is that the ODM (Original Design Manufacturer; essentially the factory,
and company that provides the Android base ROM, as well as sourcing chipsets)
has pulled the update from their servers.

Long-term members of the community may remember @Ninji's two posts on the
Cosmo OTA updater ([here][ninji_1] and [here][ninji_2]).

Whilst these posts are concerning, it's not necessarily unusual. In any case,
my conclusions draw upon the responsible disclosure that Ninji made to Planet
Computers, and combined with the fact that the ODM contract has been terminated
(no, I don't know which ODM), that those factors have contributed towards the
ODM taking down the OTA service for Cosmo.

However, seeing as it didn't happen immediately, maybe it's related to the
contract being terminated (as mentioned in the Astro Slide update on IGG). Who
knows. Speculating probably isn't very helpful for any of us right now.

When I did some reverse engineering of the new ODM OTA updater on the Astro, I
noted there's some sort of.. compression, encryption, maybe, in the payloads.

Couldn't tell *what* was being sent, but I am a firm believer that OTA software
should send as minimal data as possible.

## Next steps

In terms of moving forward, my suggestion to Planet is to make the V25 ZIP
patch update file available, either on S3/Drive/Dropbox/their wiki, for
downloading. The SystemFOTA app supports installing a 'UpdatePackage.zip' file,
located in the root internal storage of the phone. This would work as a
solution for now.

There's not much the community can do to get this going. Of course, if any
community members extracted the 'UpdatePackage.zip' from storage for the V25
firmware, then that could work. But I doubt anyone made a backup,
unfortunately.

### Side note: More OTA thoughts:

I know that Planet did write their own OTA updater for Astro. 

The ODM's FOTA updater is still active - remember the posts about 'deejay-dota'?
It's actually the updater. I have it on my phone, but not enabled as software.

I am currently working on a replacement AIO OTA platform for Planet. I call it
'[planet_ota][]', and it uses cloud tooling to scale the server, serve updates
from S3, and store metadata in a hosted PostgreSQL database. It's not fully
complete yet, but the idea is for it to serve as a way for the XR-series
machines, Gemini, Cosmo, and Astro (and future projects) to update themselves,
but without straining the current server for OTA.

It's also privacy-respecting. Instead of sending one's IMEI, you can subscribe
to an 'update channel', such as 'beta', where you can try out the latest
updates without sending personal data.

## What about rooting?

OK, so rooting is a tricky one. The current rooted Android Boot Image
(kernel+ramdisk) is for V25. You can achieve the same for V23, by using my
[scripts][] to root the `boot.img` for V23, and then flash these via the
recovery scripts. I have created a GitHub repository where you can download a
pre-built image for a untrusted boot slot [here][v23_rooted]

The [current][rooted_cosmo_v25] firmware can be used as normal, **IF**, and
this is a BIG if, you are on firmware V25. If you're not, or on older firmware,
you must not apply this update. It is not compatible.

## Conclusion

I can understand the frustration with this situation. Hopefully, things can work again.

However, emailing Planet asking for the V25 ZIP won't get anywhere right now.
It's best to hang tight.

[ninji_1]: https://wuffs.org/blog/pulling-apart-the-cosmos-systemfota-updater
[ninji_2]: https://wuffs.org/blog/digitime-tech-fota-backdoors
[planet_ota]: https://github.com/shymega/planet_ota.git
[scripts]: https://github.com/PC-LineageOS-Ports/magisk-boot-patch-ci-tool
[v23_rooted]: https://github.com/shymega/cosmo-v23-rooted-android
[rooted_cosmo_v25]: https://support.planetcom.co.uk/index.php/Rooted_Android_For_Cosmo
