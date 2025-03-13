The PKGBUILD and remove-rust script work together to build the latest version of a Linux kernel you most likely wouldn't want to run other than possibly for testing.

It's an -rc version of the current 'stable' release listed here: https://www.kernel.org/.
More specifically: https://web.git.kernel.org/pub/scm/linux/kernel/git/stable/linux-stable-rc.git/log/?h=linux-6.13.y
It's currently at Linux 6.13.7-rc2
This kernel is a release canidate containing patches for testing, not to be confused with 'mainline-rc' and all the other version -rc's.

The PKGBUILD has implemented a workaround to be dynamic in the kernel versions it builds, while not using a VCS system or downloading a kernel git repo. 
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

So there you have it, my first effort at a PKGBUILD and kernel related stuff... Enjoy!

Will this ever make it to the AUR? No worries my fellow Archers, I'd never be able to make this pass the long list of rules for submission and have little interest in doing so with this one.
Maybe this was more of a warmup run for some other potential future AUR submissions. That said, I've never been one to be able to willingly follow rules and regulations. I'd need to figure out how to get over that before anything AUR submission related. 