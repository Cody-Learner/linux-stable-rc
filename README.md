This PKGBUILD builds the latest 'stable'(*) release '-rc' version of the Linux kernel for testing.

(*) Linux stable listed here  : https://www.kernel.org/ <br>
Latest stable -rc listed here : [https://web.git.kernel.org](https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/log/?h=linux-6.15.y) <br>
Download link listed here: [https://web.git.kernel.org](https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/commit/?h=linux-6.15.y)

**Note:** Not to be confused with 'mainline' or 'longterm' release -rc's.<br>

This PKGBUILD is dynamic in that the version is not defined until the latest kernel source is downloaded. 
The source download takes a few min, after which the current kernel version will be read from the compressed archive.

You'll be promped with notification of the current version, along with making a selection to: proceed, remove rust, or exit.

The entire build process takes slightly less than 30min on a 7yo HP mini desktop with a Ryzen 2400GE 4 core processor,
or about 6.5 min on a newer Ryzen 8745HS 8 core processor.

Build time is highly dependant on the number of modules being built. Build time will increases dramatically without AUR `modprobd-db`.

To build this kernel:

    git clone https://github.com/Cody-Learner/linux-stable-rc.git
    cd linux-stable-rc
    makepkg -sr
    Wait for prompt to make selection.

Alternatively:

    wget https://raw.githubusercontent.com/Cody-Learner/linux-stable-rc/refs/heads/main/PKGBUILD
    makepkg -sr
    Wait for prompt to make a selection.

<br>

This PKGBUILD may use **Graysky's AUR modprobed-db** to save build time among other benefits.<br>
For additional info: https://wiki.archlinux.org/title/Modprobed-db <br>
It handles modprobed-db without user intervention. If it's available it's used, if not the script proceeds normally.<br>

To facilitate clean chroot builds and building for different hardware, the modprobed-db may optionally be placed in the `root-build-dir` with the PKGBUILD.
The `root-build-dir/modprobed-db` takes precedence over `$HOME/.config/modprobed-db` if both are available.

If modprobed-db is not available in either location, all the kernel modules will be built. In any case, a message is printed announcing which locations db will be used or if db was not found.

<br>

This PKGBUILD also handles **clean chroot builds**. <br>
If this is new to you, install `devtools` pkg and something like the following should get you started: <br>
`makechrootpkg -u -r $HOME/path/to/chroot/extra-x86_64/ -d $(pwd) -- -rs --noconfirm --clean`


<br>

**Note:** The kernel `config` from source is an empty place holder file. By default, it gets overwritten by Arch's current `linux` package `config`. 
To use your config, just replace the existing `config` with your custom config before running makepkg.

<br>

**Note:** You may have to update checksums as kernel and config file being pulled are dynamic.<br>
There are several options for updating checksums including: <br>
* For a list of checksums, run                 : `makepkg -g`  <br>
* For a list of checksums with file names, run : `sha256sum *`  <br>
Copy / paste updated checksums into PKGBUILD.  <br>


Arch wiki quote\: https://wiki.archlinux.org/title/VCS_package_guidelines#Authentication_and_security<br>
>"Because the sources are not static, skip the checksum in sha256sums=() ...."<br>

**Note:** These kernels have no signing key for scripted integrity check. <br>
It's up to the user to establish integrity if updates to checksums are required.<br>
Therefore, I'll leave the correct checksum from when the PKGBUILD was last tested.

<br>

If all goes well, the results will be something like this after installing the packages:

    $ pacman -Q linux-stable-rc
    linux-stable-rc 6.13.8rc-1
    $ uname -rs
    Linux 6.13.8-rc1

<br>
<br>
<br>

----
**UPDATES, ANNOUNCEMENTS:**
<br>
<br>
<br>

**2025-06-08**

	pacman -Q linux-stable-rc ; uname -rs
	linux-stable-rc 6.15.2rc-1
	Linux 6.15.2-rc1

PKGBUILD:<br>
Made changes required to build Linux stable 6.15 latest release candidate.

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/<br>

----

**2025-04-12**

    pacman -Q linux-stable-rc ; uname -rs
    linux-stable-rc 6.13.12rc-1
    Linux 6.13.12-rc1

PKGBUILD:<br>
Switched fetching the latest Arch 'linux' pkg kernel version from html to using the JSON interface.<br>
Changed the edits made to Arch 'linux' pkg kernel patch.<br>
Edited to stay on the 6.13 stable kernel as both 6.13 and 6.14 are currently listed as stable releases.<br>
Updated checksums.<br>
Function prepare(), changed location of 'test if source dir is present and delete if true'.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/<br>

----

**2025-04-06**

    pacman -Q linux-stable-rc ; uname -rs
    linux-stable-rc 6.13.10rc-1
    Linux 6.13.10-rc1

PKGBUILD:<br>
Added script comments.<br>
Changed kernel source from commit hash to kernel git branch 'tip/head'.<br>
Updated kernel source checksum.<br>
Removed newline from '_dirname' variable definition, 'find' command.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/<br>

----

**2025-04-05**

    pacman -Q linux-stable-rc ; uname -rs
    linux-stable-rc 6.13.10rc-1
    Linux 6.13.10-rc1

PKGBUILD:<br>
Commented out packages used for documentation in makedepends array.<br>
Temp edited kernel source from tag to commit hash.<br>
Updated kernel source checksum.<br>
Added '_basedir' var to handle either kernel source from tag or commit hash.<br>
Edited '_verst' var to handle either kernel source from tag or commit hash.<br>
Changed 'dirname' var to '_dirname' to comply with PKGBUILD added vars.<br>
Edited '_dirname' to handle either kernel source from tag or commit hash.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/<br>

-----

**2025-03-30**

    pacman -Q linux-stable-rc ; uname -rs
    linux-stable-rc 6.13.9rc-1
    Linux 6.13.9-rc1

PKGBUILD:<br>
Reverted temp edits made in last release.<br>
Edited '_version' used from www.kernel.org/finger_banner.<br>
Added parameter expansion on '_version' use to remove 3rd decimal, trailing numbers.<br>
Fixed escape sequences in '_ptr' (colored arrow) variable.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-28**

Version: linux-stable-rc  6.13.9rc-1

PKGBUILD:<br>
Added code to fetch/edit Arch's latest version of the linux patch file.<br>
Make temporary edits to deal with the kernel transition from 6.13 to 6.14 .<br>
Added code to prevent rerunning patch apply code in 'prepare()' function.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-22**

PKGBUILD:<br>
Added code to automatically handle modprobed-db. See above for details on this feature add.<br>
Fixed the PKGBUILD to build in a clean chroot.<br>
Added `_basedir` variable to replace an existing 'cd ..' command and used in new code additions. The 'cd ..' command was causing issues in a clean chroot build.<br>
Added 'kmod' to makedepends array to eliminate warning.<br>
Updated checksums to match Arch's updated kernel config.<br>

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-20**

After spending ~~some~~ <i>'a lot of'</i> time on Arch's `linux` package patch file, I'm finally adding it to this package.<br>
For full transparency on mods to the patch, it's currently removal of the 'Makefile patch' from the group of patches.

I'll keep the option to remove rust from the config as a fallback in case the rapidly moving peices of this PKGBUILD become incompatable.

This update consolidates and sanitizes things a bit.

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-18**

Greg K-H released -rc1 for Linux 6.13.8! I did a normal test build after having my script derust the config, and all went well.

Next I tried applying Arch's config and patches, but had mixed build results of 1 for 2 completing and was fighting issues all the way.
I ended up manually pulling a few patches out of Arch's patch file that seemed related to the rust issues I had chased down recently and installed them through a slightly modified PKGBUILD.
I'm running linux-stable-rc 6.13.8-rc1 with my two copy & pasted patches, using Arch's config out of the box, while typing this.

Have I mention that I'm having a blast with the new linux kernel and PKGBUILD obsession yet? This stuff's been some seriously enjoyably nerdly fun and a nice change of pace! lol
You all should try some...

Enjoy!

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

----

**2025-03-16**

* Added placeholder config.
* Updated checksums.
* Added colored arrow for printed messages.
* Added printed message when an -rc release is unavailable.
* Reset '_verst' var when an -rc release is unavailable.

Update details: https://github.com/Cody-Learner/linux-stable-rc/commits/main/

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
