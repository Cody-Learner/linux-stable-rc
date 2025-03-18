This PKGBUILD, script and files work together to build the latest version of a Linux kernel you most likely wouldn't want to run, other than possibly for testing.

It's an -rc version of the current 'stable' release listed here: https://www.kernel.org/. <br>
Specifically: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/log/?h=linux-6.13.y <br> 
This kernel is a release canidate containing patches for testing and is not always available.<br> 
Note: Not to be confused with 'mainline' and 'longterm' versions -rc's.<br>
<br>

To build this kernel in Arch Linux:

* git clone https://github.com/Cody-Learner/linux-stable-rc.git
* cd linux-stable-rc
* makepkg -sr
* Wait for prompt to make selection.

<br>
<br>

Note: May have to update checksums as kernel being pulled is dynamic.<br>
&nbsp;&nbsp;&nbsp;&nbsp; ie: makepkg -g , copy paste -or- change to 'SKIP'<br>
&nbsp;&nbsp;&nbsp;&nbsp; Quote wiki\: https://wiki.archlinux.org/title/VCS_package_guidelines#Authentication_and_security<br>
>"Because the sources are not static, skip the checksum in sha256sums=() ...."<br>

<i>These kernels have no signing key for scripted integrity check AFAIK, so it's up to the user to establish integrity.<br>
Therefore, I'll leave the correct checksum from when the PKGBUILD was tested. </i>

----

**2025-03-18**

Greg K-H released -rc1 for Linux 6.13.8! I did a normal test build after having my script derust the config, and all went well.

Next I tried applying Arch's config and patches, but had mixed build results of 1 for 2 completing and was fighting issues all the way.
I ended up manually pulling a few patches out of Arch's patch file that seemed related to the rust issues I had chased down recently and installed them through a slightly modified PKGBUILD.
I'm running linux-stable-rc 6.13.8-rc1 with my two copy & pasted patches, using Arch's config out of the box, while typing this.

Have I mention that I'm having a blast with the new linux kernel and PKGBUILD obsession yet? This stuff's been some seriously enjoyably nerdly fun and a nice change of pace! lol
You all should try some...

Enjoy!

To review all future updates: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-16**

* Added placeholder config.
* Updated checksums.
* Added colored arrow for printed messages.
* Added printed message when an -rc release is unavailable.
* Reset '_verst' var when an -rc release is unavailable.

To review all future updates: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-12**

The PKGBUILD has a workaround implemented to be dynamic in the kernel versions it builds, while not using a VCS system or downloading a kernel git repo. 
It pulls the latest kernel source code, pulls the latest Arch 'linux' package kernel config, and my remove-rust script.

Running the PKGBUILD will stop and prompt the user for input after a few minutes downloading the source files.
The prompt verifies the kernel version being built and asks the user to choose between proceeding, exiting, or running the remove-rust script.

There's plenty of info about the remove-rust script printed to the user. It removes three rust related kernel config settings from the config file if present.
This is a last resort automated config step suggested if the kernel fails to build. I've had success with this for the last few builds.
It's also setup to use Graysky's moduled-db. Instructions are present to disable this feature should anyone want to build a full size kernel.

As for time to build, from pressing enter on makepkg, to downloading the source, building and producing finished linux kernel and modules packages takes a little under 30 min on my hardware.
The build hardware is an around 7yo HP mini desktop with a 35 watt Ryzen 2400GE 4 core, 16G ram, $100 refurb special.
Of course build time is highly variable, depending on both hardware speed and perhaps just as important, the amount of modules being build.

I took this project on as a way to learn a little more about kernels, patching them and PKGBUILDS. 
I've been using these for way over a decade, but never bothered diving in any deeper than the kiddy pool shallow end.

Will this ever make it to the AUR? No worries my fellow Archers, I'd never be able to make this pass the long list of rules for submission and have little interest in doing so with this one.
Was this possibly more of a warmup run for some other potential future AUR submissions. Who knows, I've never been one to be able to follow rules and regulations without a great deal of effort. 
I'll need to figure out how to get over that before seriously considering an AUR submission. In the mean time, I have everything I've made both worth sharing and not so much right here. 

So there you have it, my first effort at a PKGBUILD and kernel related stuff... Enjoy!