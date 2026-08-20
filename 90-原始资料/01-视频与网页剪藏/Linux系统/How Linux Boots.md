---
title: "How Linux Boots"
url: "https://www.youtube.com/watch?v=EjrAzulPsT4"
videoId: "EjrAzulPsT4"
channel: "Joe Collins (EzeeLinux)"
channelId: "UCTfabOKD7Yty6sDF4POBVqA"
duration: "3360"
views: "280083"
isLive: false
isPrivate: false
---

#joe-collins-ezeelinux

![How Linux Boots](https://www.youtube.com/watch?v=EjrAzulPsT4)

[0:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=0s) Greetings and salutations.
[0:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=4s) Welcome to a very cool video, or at
[0:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=7s) least I hope it will be. We are going to
[0:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=9s) look at how Linux boots.
[0:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=13s) This is a very complex subject. A viewer
[0:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=17s) asked me a few weeks ago what appears to
[0:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=20s) be a simple question. What happens
[0:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=22s) between the time you press the power
[0:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=24s) button and the time you get to a login
[0:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=26s) screen? My short answer was a lot. and I
[0:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=30s) went off and started doing some
[0:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=31s) research, refreshing my memory on how
[0:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=33s) all of this works and learning how the
[0:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=36s) boot process works in Linux all over
[0:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=38s) again. And I'm going to share what I
[0:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=40s) learned with you today. I am joined as
[0:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=43s) usual by Maurice the cat. And we may get
[0:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=46s) a visit from Carly the dog. So, if you
[0:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=49s) hear any strange dog noises, that's why
[0:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=52s) and where they are coming from.
[0:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=55s) If you like this sort of video, please
[0:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=58s) give it a like. A big thumbs up helps
[1:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=60s) YouTube to put videos like this out in
[1:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=63s) front of more people and it lets them
[1:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=65s) know that folks do actually like to see
[1:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=67s) long form content. And if you are not
[1:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=70s) already, please subscribe to the
[1:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=72s) channel. That helps a lot. And thank you
[1:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=75s) to everybody who has subscribed lately.
[1:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=78s) If you are somebody who knows a great
[1:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=80s) deal about Linux already and maybe you
[1:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=82s) know a whole lot about the boot process,
[1:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=84s) you're going to find in this video that
[1:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=87s) I am generalizing quite a bit and I'm
[1:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=89s) skipping over some things that happen.
[1:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=92s) If I actually went into great detail
[1:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=95s) about what happens during the boot
[1:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=96s) process, we could very easily have four
[1:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=100s) or five videos of this length on this
[1:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=102s) subject. And I don't think most folks
[1:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=104s) really care that much. Alan Pope who
[1:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=108s) worked for Auntu for many years. He was
[1:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=110s) once the community manager there and he
[1:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=113s) was also the uh UI designer said of this
[1:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=118s) whole process that it is plumbing and
[2:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=121s) nobody really cares. Well, I kind of
[2:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=124s) care. But it is interesting to think
[2:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=127s) that
[2:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=129s) all of this happens in five to 10
[2:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=130s) seconds when we boot up our machine. And
[2:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=134s) we're just worried about getting logged
[2:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=135s) in and getting what we need to get done
[2:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=137s) done and we don't think twice about it.
[2:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=139s) And most of the time we don't interact
[2:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=141s) with any of the stuff that we're going
[2:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=143s) to be looking at today.
[2:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=146s) So let's get started with a simplified
[2:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=149s) view of the boot process and then we're
[2:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=152s) going to jump into a couple of important
[2:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=154s) aspects of it and uh dig a little deeper
[2:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=157s) of course. So the first thing that
[2:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=159s) happens when you push the power button
[2:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=161s) on your computer is the post. That is
[2:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=164s) the power on self- test and it is run by
[2:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=167s) the computer itself. It's a diagnostic
[2:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=169s) procedure where the computer goes and
[2:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=172s) checks out the CPU and the memory and
[2:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=174s) the hard drives and any other
[2:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=176s) peripherals that are hooked to it to
[2:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=177s) make sure everything is working the way
[2:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=179s) it's supposed to. In years past, when
[3:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=182s) you would turn on a computer, the post
[3:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=184s) would end with a nice little beep. These
[3:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=187s) days, that doesn't happen anymore. The
[3:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=189s) only time that you will hear a beep from
[3:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=191s) a computer is if there's something
[3:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=193s) wrong. It will usually stop during the
[3:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=195s) post and start beeping profusely and it
[3:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=199s) will not boot and then you know you have
[3:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=201s) some sort of hardware issue that you
[3:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=203s) have to go figure out.
[3:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=206s) After the post one of two things happen.
[3:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=210s) We either have the BIOS start working
[3:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=212s) which is basic input output system. This
[3:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=215s) is the older way of booting a computer
[3:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=218s) or UEFI unified extensible firmware
[3:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=222s) interface. Now all that does is to find
[3:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=225s) and start the boot loader. The
[3:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=227s) bootloadader is when booting actually
[3:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=231s) begins. So in Linux there are several
[3:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=234s) bootloadaders that are available, but
[3:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=237s) most distributions ship with Grub by
[4:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=241s) default. So we're going to concentrate
[4:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=243s) on Grub in this video. And Grub stands
[4:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=245s) for Grand Unified Boot Loader. It's been
[4:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=248s) around for a very long time. It gives
[4:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=251s) users a chance to choose an operating
[4:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=253s) system. So if you have a dual boot
[4:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=255s) machine that has Linux and Windows on
[4:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=257s) it, Grub will stop and let you choose
[4:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=260s) that from a menu. And it also offers the
[4:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=263s) ability to run older kernels. So most of
[4:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=266s) the time with Linux, you'll have the
[4:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=269s) newest kernel running and then the older
[4:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=271s) kernel is still on the machine just in
[4:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=273s) case something goes wrong and you need
[4:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=275s) to boot to a known stable kernel. You
[4:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=278s) can do that with Grub as well. Uh it
[4:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=281s) also has a recovery mode and memory
[4:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=283s) testing is available sometimes in grub
[4:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=286s) especially on the original BIOS version
[4:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=289s) of Grub and it also has a shell. So Grub
[4:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=293s) itself has a command line that you can
[4:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=295s) use to troubleshoot problems if the
[4:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=296s) machine is not booting properly or you
[4:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=299s) want to change a configuration or
[5:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=300s) something like that. Uh Grub usually
[5:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=303s) loads the latest Linux kernel and the
[5:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=306s) init RAM FS automatically that goes with
[5:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=308s) it. We'll talk about what that is
[5:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=310s) shortly. It starts the kernel and exits.
[5:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=313s) It's done its job and it goes away.
[5:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=318s) The next thing that happens is the Linux
[5:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=320s) kernel is loaded into memory and then it
[5:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=323s) is started with the boot command which
[5:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=325s) comes from the grub boot loader. It
[5:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=328s) initializes devices and loads drivers,
[5:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=331s) kernel, modules and the init program
[5:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=333s) from an init ram fs. A NIT RAM FS is
[5:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=339s) sort of like a RAM drive, but not
[5:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=341s) exactly in the sense that we think of
[5:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=342s) creating a RAM drive on a Linux system
[5:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=345s) where it's a file system that's living
[5:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=348s) in memory. Init RAM FS
[5:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=352s) is an archive of sorts and it's loaded
[5:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=355s) along with the kernel and what it does
[5:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=357s) is it acts as the kernel's root file
[6:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=361s) system its environment that it needs to
[6:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=364s) work in. Now BIOS can read hard drives
[6:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=370s) block storage devices. It can do it but
[6:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=372s) it doesn't do it in a particularly
[6:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=375s) efficient way. It uses something called
[6:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=377s) LBA. And LBA is kind of clumsy. So if we
[6:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=380s) can load this archive into memory as the
[6:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=383s) kernel starts, it will mean that we'll
[6:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=385s) have a more robust initialization of the
[6:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=388s) kernel. We're not worrying about the
[6:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=390s) kernel maybe getting some sort of
[6:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=392s) garbage from the uh hard drive that
[6:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=394s) isn't there because we're not using the
[6:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=396s) more efficient drivers. And also of
[6:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=398s) course it makes it faster because it's
[6:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=400s) reading it from RAM.
[6:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=402s) So while it is doing that uh it mounts
[6:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=405s) the root file system toward the end of
[6:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=407s) the initialization of the kernel itself
[6:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=410s) and then it switches over from a nit RAM
[6:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=412s) FS to the actual physical drive and then
[6:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=415s) it starts a process with an ID of one
[6:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=419s) called init and this is where user space
[7:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=422s) begins. If you've ever listened to Lenus
[7:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=425s) Torvalds talk about developing the
[7:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=427s) kernel, he'll talk about how the biggest
[7:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=429s) rule in kernel development is to stay
[7:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=432s) out of user space, not affect anything
[7:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=434s) in user space. That's what he's talking
[7:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=436s) about. The kernel itself is its own
[7:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=439s) little contained environment. And after
[7:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=441s) the system is up and running, of course,
[7:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=443s) it is the interface between all of the
[7:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=446s) programs and processes that we have on
[7:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=448s) our machine and the actual hardware
[7:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=450s) itself. But user space begins with init
[7:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=454s) and that is a process ID of one.
[7:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=458s) Init these days in Linux is systemd.
[7:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=462s) It's uh it it's found on just about
[7:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=465s) every major distribution. There are a
[7:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=468s) couple that are still using the old uh
[7:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=470s) CIS 5 from years ago. We'll talk about
[7:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=472s) that more later on in this video, but
[7:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=475s) pretty much everybody has switched to
[7:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=477s) systemd. And what it does is it starts
[8:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=480s) and manages essential services such as
[8:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=483s) udevd and sys logd. These are things
[8:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=486s) that you absolutely need to have
[8:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=488s) running. Uh it sets up network
[8:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=490s) configuration and starts highle services
[8:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=493s) like cron for timing things and cups for
[8:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=496s) printing. And once the services are
[8:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=499s) running it then starts Getty for a user
[8:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=502s) login. That's the program that asks you
[8:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=505s) for your username if you do not have
[8:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=508s) some sort of
[8:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=511s) graphic interface running on your
[8:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=512s) machine. So if you don't have a desktop
[8:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=514s) installed, it opens up Getty, which
[8:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=517s) starts the login process. If you do then
[8:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=520s) it opens up something like GDM which is
[8:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=523s) the Gnome desktop manager, KDM which is
[8:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=526s) the KDE desktop manager or light DM
[8:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=530s) which is the one that is used these days
[8:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=532s) by Linux Mint. Uh light DM was used by
[8:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=534s) Iuntu for many years as well. It's a
[8:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=536s) great desktop manager. The net program
[8:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=539s) is also used to perform an orderly
[9:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=542s) computer shutdown. So this runs all the
[9:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=545s) time. Init does a lot. init takes care
[9:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=548s) of all of the services running in the
[9:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=551s) background that you need to make your
[9:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=552s) system work. We're going to dig into
[9:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=554s) that a little bit later on. And also, it
[9:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=557s) provides ways for you to start, stop,
[9:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=559s) and check out those services and make
[9:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=561s) sure they're okay. And you can even add
[9:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=563s) services to Init.
[9:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=574s) So, that is the basic startup procedure.
[9:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=576s) Let's dig into some of what's going on.
[9:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=579s) And we'll also have some practical
[9:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=580s) examples.
[9:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=582s) So, the next thing that we want to talk
[9:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=584s) about is the Grub Boot Loader.
[9:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=589s) Grub is a very weird cool little program
[9:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=593s) that's been around for a long, long
[9:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=595s) time. And there are two versions of
[9:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=597s) Grub. One of them is the BIOS version.
[10:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=601s) And that happens when you have a machine
[10:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=603s) that does not have UEFI on it. that is
[10:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=605s) automatically installed, the operating
[10:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=608s) system installer will detect that it
[10:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=611s) will need that version. And this is
[10:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=612s) usually the one that it throws in there.
[10:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=614s) If the machine does have UEFI, then
[10:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=616s) you'll get what's technically known as
[10:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=618s) Grub 2, although I think they're both
[10:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=620s) Grub 2 these days, but it just depends
[10:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=622s) on the implementation. The uh BIOS
[10:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=626s) bootloadader is considered to be legacy
[10:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=628s) at this point because most modern
[10:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=630s) machines use UEFI. My server does not
[10:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=633s) use UEFI,
[10:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=635s) however, and so we'll take a look at
[10:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=638s) that and how it sets that up shortly.
[10:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=642s) Um, to get into this, there are a couple
[10:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=645s) of methods. If you're running BIOS, you
[10:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=647s) need to press and hold the shift key at
[10:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=650s) boot time. So, when you get to the BIOS
[10:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=653s) screen where you have usually the name
[10:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=655s) of the, you know, the the brand of the
[10:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=657s) computer, they have a splash screen. You
[10:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=659s) need to hold down shift and this will
[11:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=661s) get you to the grub menu. And then if
[11:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=665s) you have UEFI, you need to press escape.
[11:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=668s) Just tap it right after the bias screen.
[11:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=671s) Now the trick with that is a lot of the
[11:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=675s) uh UEFI based bias systems the where you
[11:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=678s) get into the machine to um check on
[11:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=681s) things like boot order and and change
[11:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=684s) system settings. They also use escape to
[11:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=687s) get into. So timing is very critical on
[11:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=689s) this and you may have to do this several
[11:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=691s) times to get it to work. Let's go ahead
[11:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=693s) and and try that right now.
[11:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=697s) So, we are in Auntu
[11:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=700s) and this is a BIOS set m uh setup we're
[11:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=703s) going to be looking at today in this
[11:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=705s) virtual machine. And I want to restart
[11:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=710s) and we're going to restart.
[11:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=713s) And here's the boot splash for the
[11:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=716s) virtual machine system. I held down
[11:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=718s) shift and now we are in the menu for
[12:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=721s) Grub. And so we could just hit enter at
[12:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=724s) this point and we could run iuntu. If we
[12:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=727s) had another operating system installed,
[12:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=729s) either another Linux or Windows, we
[12:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=731s) could choose from this menu right now to
[12:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=734s) run that system. If we choose advanced
[12:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=738s) options for iuntu, you'll see that we
[12:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=741s) have two kernels listed. The kernel that
[12:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=744s) is first on the list is the latest
[12:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=747s) version. And then it is pretty much
[12:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=750s) standard practice in iuntu to keep the
[12:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=752s) last kernel before the current one just
[12:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=755s) in case there's some sort of problem. If
[12:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=757s) you have an update that kicks in, you
[12:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=759s) have a new kernel that's installed on
[12:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=760s) the machine and things aren't working
[12:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=762s) properly. This will give you a way to
[12:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=765s) boot the machine off the known good
[12:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=767s) kernel and then you won't have uh
[12:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=772s) too many problems getting the machine
[12:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=774s) booted because you're on the old kernel.
[12:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=776s) And then you can troubleshoot the
[12:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=778s) problem with the new kernel or you can
[13:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=781s) make it so it won't load again during an
[13:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=783s) update if you need to do that to keep
[13:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=786s) your machine running whatever. Uh we
[13:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=788s) also have recovery mode for each kernel
[13:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=790s) and that puts the machine sort sort of
[13:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=792s) into a safe mode. So let's go ahead and
[13:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=794s) take a look at that. We'll go ahead and
[13:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=796s) boot this machine into a safe mode and
[13:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=800s) we get boot messages as it comes up.
[13:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=801s) That's the kernel loading itself. And
[13:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=804s) now we have several options. We can
[13:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=807s) resume. We can clean. That will
[13:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=809s) basically try and free up some free
[13:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=811s) space if you've managed to fill up your
[13:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=813s) root directory. Um, we can do DPKG and
[13:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=817s) look for broken packages here. We can
[13:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=820s) check the file systems. We can update
[13:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=822s) the GRUB bootloadader in case you might
[13:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=825s) have changed the configuration or you
[13:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=827s) think there might be something wrong
[13:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=828s) with the way it's loading. You can try
[13:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=829s) that here. Um you can enable networking
[13:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=833s) if you need it. And you can uh drop to a
[13:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=835s) root shell program. So let's go ahead
[13:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=837s) and do that. And um so we'll do control
[14:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=842s) D to continue.
[14:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=845s) Let's see what did that tell me. That
[14:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=847s) was interesting. It said uh press enter
[14:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=849s) for maintenance or for uh control D to
[14:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=854s) continue. So that will get us out of
[14:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=855s) here. So that brings us down into some
[14:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=858s) sort of a prompt there. D will let us
[14:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=861s) continue. Okay, that's what that meant.
[14:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=864s) So there's a lot that you can do here
[14:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=866s) and feel free to explore. You can't
[14:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=868s) break anything with this. Um I found
[14:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=870s) that for instance like on my uh UEFI
[14:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=875s) version of the virtual machine, it was a
[14:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=878s) UEFI boot that this didn't seem to work.
[14:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=882s) Let's see if it works here.
[14:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=885s) See, now it says it can't work. And I
[14:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=886s) bet you it can't re. Yeah, that's kind
[14:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=888s) of a strange thing going on there. I
[14:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=890s) don't know what that is,
[14:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=892s) but anyway, uh feel free to play with it
[14:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=895s) if you want to. And u since we have
[14:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=898s) pretty much locked up our iuntu machine,
[15:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=900s) all you need to to do to get out of this
[15:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=902s) is to send alternate uh control and
[15:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=906s) delete. So, we'll do that. And that
[15:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=909s) should uh reboot the system. And it
[15:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=912s) does. So, we're going to go ahead and
[15:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=914s) let that reboot and jump back over here
[15:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=916s) and talk more about Grub.
[15:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=919s) Let me out so I can get back to where we
[15:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=921s) were. So, how does it work? It Well, it
[15:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=924s) depends on whether we're using BIOS or
[15:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=928s) UEFI. BIOS is a little bit more complex
[15:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=930s) to understand. This is the old boot
[15:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=933s) system that's falling out of favor. But
[15:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=936s) if you like to restore old machines or
[15:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=938s) uh you're on an older machine that will
[15:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=940s) let you completely turn EFI off and go
[15:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=942s) into a legacy bias mode, then you
[15:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=944s) probably need to know about this. Uh the
[15:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=947s) boot device usually uses an MS DOS style
[15:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=950s) partition with a master boot record, an
[15:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=953s) MBR. The MBR is at the very start of
[15:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=957s) storage space on a disc and it's a very
[16:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=960s) small space. The actual bootloadader
[16:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=962s) itself is only 441 bytes on an MBR.
[16:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=969s) Uh the whole Grub loader uh will
[16:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=972s) obviously not fit. So the MBR is running
[16:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=975s) uh what is really just sort of like a
[16:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=977s) link to Grub. It's more than a link
[16:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=980s) because it's executable, but really what
[16:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=981s) it does is is it tells the system,
[16:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=984s) "Okay, I'm running." And then it goes
[16:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=985s) and it loads the rest of Grub uh from
[16:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=988s) where it lives. Bias searches for the
[16:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=991s) MBR right after post. And it can read
[16:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=994s) block storage devices, the uh BIOS
[16:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=997s) itself, but it uses something called
[16:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=999s) LBA, which is block label addressing. It
[16:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1003s) is not uh very efficient, but it's
[16:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1005s) enough to get the system started. uh the
[16:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1008s) main part of grub is stored in /boot
[16:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1012s) slashgrub and bio style booting is
[16:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1015s) possible with the GPT partitioning
[16:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1018s) scheme. What it does is it creates a
[17:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1020s) small 512 byt partition at the beginning
[17:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1025s) of the boot disc that holds the MBR and
[17:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1028s) the rest of it is GPT. And I I think
[17:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1030s) that's how it did it when it set up this
[17:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1033s) Iuntu machine. So, we can run over here
[17:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1036s) and take a look at this very quickly.
[17:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1041s) See what that setup looks like.
[17:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1046s) Now, this machine uh that I'm running
[17:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1048s) Auntu on is is very slow. I've crippled
[17:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1051s) it for a reason. Um I've made it so it
[17:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1055s) runs extremely slow. And so, what we can
[17:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1058s) do is bring up a terminal here.
[17:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1061s) And what we'll run we'll do is run lsb
[17:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1063s) block which will show us the uh
[17:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1065s) partitions.
[17:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1067s) And down here you will see that we have
[17:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1070s) a very small one megabyte partition
[17:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1073s) there. That's uh can we bring that up a
[17:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1076s) little bit? Or maybe we could resize the
[17:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1078s) screen and make it so we could see the
[17:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1079s) whole thing. Thank you very much. There
[18:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1082s) we go.
[18:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1083s) So you see down here that we do have a
[18:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1086s) partition at the beginning of the drive
[18:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1088s) and then we have the root partition
[18:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1090s) where the rest of the operating system
[18:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1092s) lives. And that one megabyte partition
[18:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1094s) is uh where the legacy MBR is stored on
[18:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1097s) this system. So if we open up another
[18:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1100s) terminal here
[18:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1102s) and go ahead and make this bigger so
[18:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1104s) it's easy for everybody to see and we
[18:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1106s) will get into my file server which is up
[18:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1108s) and running. Uh this machine does not
[18:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1112s) have UEFI enabled either. This is a BIOS
[18:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1115s) machine. It's kind of a really old Dell.
[18:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1118s) Uh what we can do is is we can ls block
[18:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1121s) once again. And let's clear the screen,
[18:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1124s) make that a little easier to see, shall
[18:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1126s) we? Um you'll see that um the root
[18:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1129s) partition is in SDA1. There is no other
[18:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1134s) boot partition because it's not
[18:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1136s) necessary.
[18:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1137s) And then we have the home partition
[19:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1140s) which is in SDA2. So this is how BIOS
[19:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1143s) lays things out on a drive. And in this
[19:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1145s) case, the boot loader is crammed in the
[19:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1148s) very beginning of the drive before the
[19:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1151s) partition table in the MS DOS master
[19:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1155s) boot record because that's the way I set
[19:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1157s) this machine up. I actually used the MS
[19:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1159s) DOS partitioning uh for this machine
[19:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1162s) when I put it together.
[19:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1164s) So well, no, I won't get out of that. U
[19:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1168s) I was going to I used alternate 4 to get
[19:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1171s) out of that terminal, but we'll leave it
[19:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1172s) up there because we're going to go back
[19:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1173s) to that system to look at something else
[19:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1175s) in a little while.
[19:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1177s) So that is how it works with BIOS. Now
[19:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1179s) with the UEFI
[19:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1183s) system, it's a little bit simplified.
[19:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1185s) The boot device uses a modern GPT
[19:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1188s) partition scheme. And what you do is you
[19:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1191s) put an ESP at the front of that, which
[19:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1193s) is an EFI system partition. And that's
[19:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1197s) at the beginning of the disc. Although
[19:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1198s) you can put that in other places. Uh
[20:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1201s) that's what I'm given to understand at
[20:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1203s) least, but I always stick it right up
[20:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1205s) front because that's what most of the U
[20:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1208s) tutorials out there say to do. The ESP
[20:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1211s) holds the Grub executable file. It's the
[20:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1214s) actual bootloadader itself
[20:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1218s) and that fires off and then the firmware
[20:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1222s) starts this file right after post. The
[20:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1224s) ESP is mounted at /boot/efi
[20:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1228s) and the ESP is flagged as boot and ESP
[20:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1232s) at installation and is usually between
[20:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1234s) 512 megabytes and 1 GBTE in size. So if
[20:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1238s) we open up a terminal and we look at
[20:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1240s) this machine uh that we're recording the
[20:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1243s) video on this is my main machine big
[20:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1244s) boy. We can do ls block again and we
[20:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1248s) will see that we're running NVMe drives
[20:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1250s) here and the first partition on the
[20:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1253s) drive is
[20:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1255s) the um 512 megabyte ESP that's mounted
[21:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1260s) at boot efi. So that's how that system
[21:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1264s) works. That's the main difference. Now
[21:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1267s) the other thing that comes along with
[21:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1268s) UEFI and I will just mention it here is
[21:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1272s) the fact that you have to deal with
[21:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1274s) secure boot and if you're running
[21:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1277s) something like Iuntu or Linux Mint
[21:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1278s) usually that just takes care of itself
[21:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1281s) at install but sometimes you have to
[21:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1283s) turn that off to get things up and
[21:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1285s) running.
[21:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1287s) So that is how Grub does its thing.
[21:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1291s) The next thing we need to talk about is
[21:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1293s) the Linux kernel itself and what goes on
[21:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1296s) when that is running or rather
[21:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1299s) initializing itself and getting itself
[21:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1301s) up and running and you really can't
[21:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1305s) interact with that. This happens on its
[21:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1307s) own. The kernel is maintained by the
[21:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1310s) kernel developers and the uh
[21:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1313s) distribution that you're using. There
[21:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1315s) really is no interaction here. Uh so
[21:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1318s) what grub does is it loads the selected
[22:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1321s) Linux kernel and its corresponding init
[22:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1324s) RAM fs file into memory from its boot
[22:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1328s) directory and then it follows the
[22:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1330s) comments or the commands rather in grub.
[22:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1335s) Cfg or grub.config.
[22:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1337s) It uh may also load additional kernel
[22:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1341s) modules along with the main kernel. As a
[22:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1343s) matter of fact, that happens most of the
[22:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1344s) time by default these days. Uh the grub
[22:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1349s) config file is maintained by the
[22:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1352s) distribution, but users can modify it,
[22:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1354s) but you don't do it by editing it
[22:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1356s) directly. What you do is you edit a file
[22:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1359s) called uh grub in etc. It's under
[22:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1363s) etc/default/grub.
[22:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1365s) And then you update the grub program
[22:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1368s) with something like update grub. That's
[22:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1370s) the command. and then it will take your
[22:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1373s) changes and put it in the main file.
[22:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1376s) It's done that way because the
[23:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1380s) distribution maintains grub for you. And
[23:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1383s) if you make direct changes and the
[23:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1384s) distribution makes a change that could
[23:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1386s) cause a conflict or erase your change or
[23:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1389s) whatever, but uh this will put your
[23:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1391s) change in there and make sure that it it
[23:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1394s) stays there. And then uh once Grub fires
[23:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1399s) off the boot command for the kernel, the
[23:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1400s) kernel starts and Grub exits and the
[23:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1403s) kernel inspects the system. It sets up
[23:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1405s) hardware by loading drivers from init
[23:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1408s) RAM FS. It mounts the root file system
[23:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1410s) and then starts innit. And the moment
[23:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1413s) that it starts init user space begins.
[23:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1417s) So you can use D message to see what the
[23:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1420s) kernel is doing after you do a boot. Um,
[23:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1425s) but during the boot process, the
[23:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1428s) distributions don't show you what the
[23:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1430s) kernel is doing. You don't see it. It
[23:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1432s) never shows up on the screen. So, but
[23:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1435s) you can uh get some most of those
[23:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1437s) messages by looking at DM message. Not
[23:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1439s) all of them. Some of the boot messages
[24:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1441s) just roll right up the screen and um
[24:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1444s) they're gone. We can look at that very
[24:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1446s) quickly here. So, let's go over here to
[24:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1449s) our Iuntu virtual machine and we're
[24:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1451s) going to reboot it.
[24:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1457s) And when it comes back around, if I hit
[24:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1460s) escape,
[24:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1464s) as soon as we see the Iuntu logo here,
[24:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1469s) then it will actually show you the
[24:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1471s) messages. Now, remember, escape is also
[24:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1473s) what we would use on an EFI machine to
[24:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1476s) get to the grub boot loader. So, here's
[24:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1478s) another use for escape. But this gives
[24:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1480s) you some idea of what's going on in the
[24:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1482s) background there as Auntu loads. And
[24:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1486s) most of the distributions that have some
[24:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1489s) sort of splash screen that covers that
[24:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1492s) up will give you some sort of way to see
[24:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1494s) what's going on.
[24:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1497s) So, to kind of get an idea of what the
[24:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1499s) kernel is doing at boot time, what we
[25:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1501s) can do is um reboot our server here.
[25:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1505s) This is a very simple configuration. The
[25:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1507s) kernel doesn't actually have a whole lot
[25:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1509s) to do.
[25:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1512s) And we're going to go ahead and let this
[25:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1513s) reboot. It should only take a few
[25:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1515s) seconds. And then we will log back in.
[25:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1518s) And as soon as we get there, we'll run
[25:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1519s) dssage, which is a way to look at
[25:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1523s) messages from the kernel
[25:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1526s) in real time. If you use watch, you can
[25:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1528s) see what the kernel is doing in real
[25:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1529s) time.
[25:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1532s) As a bonus, I'll show you how that works
[25:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1534s) very quickly.
[25:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1537s) So, let us go to
[25:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1542s) log back in. The machine is up and
[25:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1544s) running. Clear the screen and we're just
[25:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1545s) going to fire off pseudo
[25:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1550s) dssage
[25:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1554s) and give it my password.
[25:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1558s) And we have to scroll up through a lot
[26:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1560s) of output here. But we'll get to the
[26:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1562s) beginning of D message and we'll see
[26:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1564s) pretty much the beginning of the boot
[26:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1566s) process.
[26:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1568s) So here we go. Here we go. Here we go.
[26:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1569s) Here we go.
[26:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1571s) I see a lot of configuration on the PCI
[26:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1574s) bus. Keep going. Keep going. Keep going.
[26:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1577s) All right. So we are at the beginning
[26:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1579s) and it's telling us that uh this is
[26:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1583s) Linux GNU GCC14 Debian 14.2.
[26:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1587s) >> [laughter]
[26:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1587s) >> to you what distribution and kernel
[26:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1591s) we're loading here.
[26:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1593s) Let's see what we can figure out. So,
[26:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1599s) there is the bias provided physical RAM
[26:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1602s) map so that the kernel can access RAM.
[26:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1607s) See if there's anything that we can
[26:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1609s) figure out as we go through here.
[26:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1616s) So you kind of get the idea. This is
[26:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1617s) what the kernel is doing as it is
[27:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1620s) booting. If we keep scrolling down here,
[27:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1625s) uh we will see soon the moment that we
[27:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1628s) see systemd appear is the moment that we
[27:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1631s) have passed things over to user space.
[27:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1634s) See now we're looking at the PCI bus.
[27:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1637s) That's what I was seeing earlier. Lots
[27:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1639s) of configuration there trying to figure
[27:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1641s) out what's going on
[27:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1643s) there. That's not It's just That's
[27:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1646s) system. It's not system D.
[27:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1649s) So when you first start seeing those
[27:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1650s) sorts of things, that's where we are. So
[27:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1652s) we'll keep going. There's the clock
[27:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1653s) right there.
[27:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1658s) Setting up the clock for the system.
[27:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1663s) And we're going to keep There's the
[27:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1665s) USBs.
[27:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1666s) So the USB bus is being brought online.
[27:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1669s) [snorts]
[27:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1672s) And here we go. I see system D which is
[27:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1675s) service one. I think that's the first
[27:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1677s) time we see that. Yes. So this is where
[28:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1681s) system D
[28:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1684s) is starting
[28:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1687s) and from this point on we are uh running
[28:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1690s) with systemd in user space.
[28:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1695s) Very cool indeed.
[28:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1700s) All right, let's get back over to the
[28:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1703s) other screen, please.
[28:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1708s) So, we've pretty much covered everything
[28:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1709s) there is to know about the kernel
[28:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1710s) starting up. You can make modifications
[28:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1713s) to how the kernel starts and what
[28:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1716s) modules it loads. And you do that by
[28:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1721s) editing the uh config files for grub.
[28:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1724s) Grub tells the kernel what to do. So if
[28:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1727s) for instance you would like to add
[28:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1730s) kernel modules and for some reason you
[28:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1731s) need to start them manually you would
[28:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1733s) add that to grub to make that happen or
[28:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1736s) if you want some module that's
[28:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1738s) automatically added not to be added to
[29:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1740s) the kernel then you would remove it and
[29:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1742s) that's how you would do that. That's
[29:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1745s) actually even just a little bit above my
[29:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1747s) head. Usually if I run into a situation
[29:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1749s) where I need to do something like that
[29:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1751s) then I look for an alternative way
[29:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1753s) around that. Um, my way of thinking with
[29:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1757s) Grub and the kernel has always been let
[29:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1759s) the distribution deal with that. That's
[29:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1764s) above my pay grade. Thank you very much.
[29:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1767s) All right, next please. Next slide. We
[29:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1771s) need to scroll up because there's a lot
[29:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1772s) on this particular page. So, now we're
[29:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1775s) going to talk about the init system. And
[29:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1777s) this is probably the part of the boot
[29:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1780s) process which you will interact with the
[29:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1782s) most.
[29:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1784s) Um the init system is what really sets
[29:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1788s) up the parts of the operating system
[29:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1790s) that you need to use all the time.
[29:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1792s) Remember the kernel is just interested
[29:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1794s) in system hardware. It provides an
[29:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1797s) interface between user space and the
[30:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1800s) hardware itself.
[30:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1802s) So a lot of those uh ways that we
[30:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1806s) interact with the hardware and the
[30:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1808s) services that run that help us uh to
[30:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1811s) make things happen on our system is set
[30:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1813s) up by init.
[30:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1816s) And in this case we're talking about
[30:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1818s) system D. System D has pretty much
[30:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1821s) become the default init system for
[30:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1824s) Linux. Uh let's just get into the
[30:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1826s) basics. The init system starts with a
[30:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1828s) process ID of one and it stays running.
[30:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1832s) It's running all the time and it even
[30:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1834s) handles shutdown. When you go to shut
[30:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1836s) down your computer, the init system
[30:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1838s) makes that happen. It loads and starts
[30:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1842s) and stops system services as needed.
[30:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1844s) These are little programs, damons if you
[30:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1846s) will, that run in the background. They
[30:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1848s) wait for input from certain places and
[30:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1850s) they do certain things and you really
[30:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1852s) don't really have to worry about them
[30:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1854s) too much. they just do their job. Uh,
[30:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1856s) systemd also has the ability to run
[30:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1859s) timers. So, if you're familiar with
[31:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1861s) running cron tasks or using anacron,
[31:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1864s) systemd will do exactly the same thing.
[31:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1867s) It has its own logging system. It has an
[31:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1870s) uh ondemand activation, so it can load a
[31:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1874s) service but not start it or hold on to
[31:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1877s) it and wait until it's needed. So, it's
[31:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1879s) not wasting system resources running
[31:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1882s) something that doesn't need to be
[31:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1883s) running. uh that is something that it
[31:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1885s) can do. It has become the default in it
[31:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1888s) for most modern Linux distributions and
[31:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1891s) it was introduced in 2010 and there was
[31:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1894s) a lot of controversy around systemd
[31:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1897s) because it has so many features that
[31:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1900s) some claim it's an operating system in
[31:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1903s) itself. And as we get through this
[31:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1905s) little part of the talk here, you're
[31:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1907s) going to probably agree with those folks
[31:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1909s) because I was rather surprised at all
[31:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1911s) the things that systemd actually can do.
[31:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1915s) uh but it has proven to be very robust
[31:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1918s) and flexible and so that is why a lot of
[32:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1921s) distributions have grabbed it up and it
[32:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1924s) also uh made it possible for
[32:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1926s) distributions to focus on other things
[32:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1928s) because now with systemd in place the
[32:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1930s) systemd developers can focus on keeping
[32:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1933s) it maintained and the distributions
[32:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1936s) don't have to deal with it quite as
[32:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1937s) much. Um so let's compare it a little
[32:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1940s) bit to the old system 5. Uh that is the
[32:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1944s) original Linux Anit system and it goes
[32:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1947s) all the way back to 1983. It was adapted
[32:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1950s) from Unix and CIS 5 uses scripts to
[32:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1954s) start services and set up things like
[32:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1958s) networking. Its execution is linear
[32:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1960s) which means that it executes commands
[32:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1964s) one after the other. It goes from one
[32:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1965s) script to another and executes them in
[32:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1968s) order. If anything hangs up or takes
[32:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1970s) unusually long, the whole system is
[32:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1972s) halted until that script is loaded or an
[32:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1976s) error message is generated saying
[32:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1977s) there's a problem.
[32:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1979s) And so that is one of the things about
[33:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1982s) uh the good old CIS 5 that was an issue
[33:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1985s) uh in that it was very rigid and it also
[33:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1990s) u took a long time to load because it
[33:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1993s) would get hung up on one process at a
[33:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1995s) time. So then multi-core CPUs came along
[33:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=1999s) and everything was multi-threaded. So
[33:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2002s) taking a look at old CIS 5 and going
[33:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2004s) wait a minute we can do this in a better
[33:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2005s) way if we run this in parallel uh maybe
[33:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2008s) we can make this a lot faster and more
[33:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2010s) efficient. Uh Iuntu developed a system
[33:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2013s) for init called upstart which was
[33:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2015s) actually a pretty good system and
[33:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2017s) according to what I read they say it was
[33:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2019s) a reactionary system which means that it
[33:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2022s) would sort of kind of adapt to
[33:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2024s) conditions as they changed on a system.
[33:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2026s) I'm not exactly sure what that means but
[33:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2028s) that's what it said. So I put it in here
[33:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2031s) and it ran in parallel when it booted
[33:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2033s) up. It would try and load as many
[33:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2035s) services simultaneously as possible.
[33:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2039s) Iuntu dropped Upstart like a hot potato
[34:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2042s) in 2015 and switched to
[34:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2046s) um systemd and has been using it ever
[34:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2050s) since and nobody ever muttered a word
[34:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2052s) afterward. It was just like upstart
[34:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2054s) goodbye. It was don't gone by. So
[34:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2058s) apparently the vuntu developers thought
[34:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2060s) systemd was a better idea. Um, systemd
[34:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2063s) is goaloriented, which means that as it
[34:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2066s) boots the system, it it looks at all of
[34:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2069s) the services and damonss that it needs
[34:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2071s) to start and manage, and it tries to
[34:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2073s) figure out the best way to do that as
[34:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2076s) quick as possible. It also looks at
[34:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2078s) things like dependencies on services,
[34:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2081s) like some services need other services
[34:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2083s) to be running before they can work. So,
[34:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2085s) it figures all that out and then it
[34:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2087s) figures out the best way to run them and
[34:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2089s) it it opens things in big blocks in
[34:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2091s) parallel when it can, which makes it uh
[34:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2094s) very quick indeed. Um, so if it has one
[34:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2097s) service that takes an unusually long
[34:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2099s) time to load, for instance, it'll work
[35:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2101s) around that and it'll it'll say, "Okay,
[35:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2103s) while you're doing that, I'm going to go
[35:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2104s) do this, this, and this over here." It
[35:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2106s) can figure that out and makes the system
[35:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2109s) load a whole lot faster.
[35:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2112s) um it uses targets instead of run
[35:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2115s) levels. Run levels is something that you
[35:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2118s) will see in a lot of old Linux
[35:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2121s) literature and it describes the state of
[35:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2123s) the system. Like run level one was like
[35:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2126s) an emergency mode and that was in single
[35:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2128s) user mode where the system was running
[35:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2130s) in real time and nobody else could log
[35:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2133s) into the system and that was for
[35:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2135s) diagnostics and setting things up. And
[35:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2137s) then there were different run levels all
[35:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2140s) the way up to run level five, which
[35:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2141s) usually meant the system was fully up
[35:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2143s) and running with graphics and all that
[35:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2145s) other stuff. And run level six, as I
[35:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2147s) recall, was an emergency run level of
[35:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2149s) some sort, too. Well, system D doesn't
[35:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2153s) care for run levels at all. It doesn't
[35:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2155s) have anything to do with it. Early on,
[35:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2157s) it was backwardly compatible and it
[35:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2159s) would report run levels to programs that
[36:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2162s) wanted to know what they were. But in
[36:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2164s) 2025, it appears that systemd doesn't
[36:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2168s) care about that. So for instance, if I
[36:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2170s) run pseudo run level in my server
[36:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2175s) running um Debian 13,
[36:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2179s) it says it's unknown. Also, there's
[36:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2181s) another way to look at the um run level.
[36:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2183s) We'll do that with pseudo as well. Who
[36:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2186s) and R. We look at that.
[36:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2190s) It doesn't return any output at all. So,
[36:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2194s) uh, system D has sort of moved on from
[36:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2196s) the concept of run levels alto together.
[36:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2198s) And I wish I would stop doing that.
[36:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2200s) [laughter]
[36:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2201s) It's a habit to do alternate F4 to close
[36:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2204s) things. And sometimes I close things
[36:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2206s) that I want to have open. And that one
[36:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2208s) of them situations right there. So
[36:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2211s) anyway, back over here and we'll keep
[36:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2213s) talking about this. So
[36:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2216s) what systemd does is it uses targets. It
[37:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2220s) sets a target. we're trying to get to
[37:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2222s) this state. What is the quickest and
[37:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2225s) most efficient way to do that? And you
[37:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2228s) can look at the default target on your
[37:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2231s) system. Super easy. So, let's look at my
[37:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2233s) main system here real quick. Uh, this
[37:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2236s) system is running Linux Mint Debian
[37:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2239s) Edition. So, we have the full graphical
[37:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2241s) interface going. We're recording video
[37:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2243s) and everything. So, what is our target?
[37:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2245s) And what we would use to figure that out
[37:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2247s) would be pseudo
[37:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2250s) system
[37:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2251s) CTL
[37:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2253s) and then we will do get
[37:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2257s) default.
[37:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2258s) I think that's right. I hope I did that
[37:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2260s) from memory correctly. Let's find out.
[37:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2267s) Yeah. So, we're running a graphical
[37:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2269s) target. Now, interestingly, when I check
[37:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2271s) this on my server, so we'll do the same
[37:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2274s) thing.
[38:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2280s) uh get default.
[38:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2284s) It's also a graphical target. [laughter]
[38:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2288s) There's no graphics running on this
[38:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2290s) machine, but it really doesn't matter
[38:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2292s) because uh seems to be working fine. It
[38:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2296s) would just seem to me that a server
[38:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2297s) would have sort of a a different sort of
[38:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2300s) um target, but systemd is targeting
[38:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2303s) everything for graphics on Debian, it
[38:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2305s) seems to be.
[38:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2308s) So, what else can we get into here?
[38:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2311s) Uh, system D calls jobs units.
[38:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2314s) Everything is a unit and it activates
[38:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2317s) those units. And if you want to see all
[38:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2320s) the units running on your machine, uh,
[38:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2322s) then you can list them. It's actually
[38:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2324s) super easy. Uh, we'll do that in just a
[38:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2327s) moment. But first, let's talk about the
[38:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2329s) different kinds of units. And this is
[38:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2331s) what blew my mind because I didn't know
[38:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2333s) this. Now I understand why people say
[38:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2335s) that systemd could be considered an
[38:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2337s) operating system in and of itself. So
[38:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2339s) the first thing that we have are service
[39:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2341s) units. We've been talking about those.
[39:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2343s) These units manage the life cycle of
[39:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2345s) system services or dammons including
[39:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2348s) their startup shutdown and restart
[39:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2350s) behavior. That makes sense. Then we have
[39:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2353s) socket units. These units define network
[39:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2356s) sockets or IPC interprocess
[39:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2359s) communication sockets that can activate
[39:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2361s) services upon receiving incoming
[39:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2364s) connections or data. So you would send
[39:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2366s) something to a certain port and it would
[39:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2369s) cause something to happen on a computer.
[39:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2371s) That's what they're talking about there.
[39:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2373s) And then we have target units. We've
[39:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2374s) talked about that. These units serve as
[39:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2376s) synchronization points during the boot
[39:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2379s) process or for grouping other units.
[39:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2381s) They are similar to run levels that uh
[39:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2384s) but offer more flexibility.
[39:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2386s) And then we have mount units. These
[39:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2389s) units manage file system mount points
[39:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2392s) defining how and where file systems are
[39:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2395s) mounted. Kind of like the FS tab file
[39:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2398s) that the kernel uses to mount things at
[40:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2400s) boot, but system D can do that as well.
[40:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2403s) Then we have the automount units. These
[40:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2406s) units define mount points that are
[40:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2408s) automatically mounted on demand when
[40:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2411s) accessed rather than at boot time. I
[40:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2413s) guess that would be like when you would
[40:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2415s) plug in a USB stick or an SD card or
[40:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2417s) something into your system, it would
[40:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2419s) automatically mount it somewhere if you
[40:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2420s) set it up to do so. Usually that's
[40:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2423s) handled by the desktop, but if you have
[40:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2425s) a server, you would have to set that up.
[40:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2428s) And I systemd offers a way for you to do
[40:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2431s) that. That's exactly what that is. I
[40:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2433s) find this to be very interesting. Swap
[40:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2435s) units. These units manage swap files or
[40:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2438s) partitions controlling their activation
[40:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2441s) and deactivation.
[40:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2443s) Then we have path units. These units
[40:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2447s) monitor specific file system paths and
[40:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2450s) can trigger the activation of other
[40:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2452s) units when changes occur in those paths
[40:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2455s) like a file is created. So if you
[40:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2458s) created a flag file somewhere, this
[41:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2461s) particular service running in the
[41:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2462s) background would cause something else to
[41:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2464s) happen on the system. That's kind of
[41:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2466s) cool if you stop and think about it.
[41:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2468s) That's a very interesting way to control
[41:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2470s) things on a computer. Just have a a file
[41:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2472s) appear in a directory somewhere.
[41:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2476s) Then there are timer units. These units
[41:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2478s) define timers for schedule activation of
[41:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2481s) other units similar to cron jobs that
[41:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2483s) are managed by systemd. One of the
[41:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2486s) reasons that I kind of like to use
[41:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2487s) Anacron for that is number one, I'm an
[41:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2489s) old Carmagian and I like older systems
[41:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2491s) and Anacron has been around forever. Uh,
[41:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2495s) when you set up a systemd timer, you
[41:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2498s) have to create the service that you want
[41:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2501s) the timer to activate and then you
[41:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2503s) create the timer file as well, which is
[41:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2506s) a kind of an extra step. And creating
[41:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2509s) files for systemd is a little bit
[41:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2511s) awkward if you're not super familiar
[41:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2513s) with the format they use. They they use
[41:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2516s) a format in these files which is very
[41:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2518s) similar to like desktop ini in Windows.
[42:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2522s) If you remember those sorts of files
[42:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2523s) where you could run initializations on
[42:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2526s) programs and whatnot with that that's
[42:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2528s) exactly the way systemd works. It's
[42:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2530s) almost exactly the same format.
[42:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2534s) Uh we have snapshot units. These units
[42:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2537s) allow you to create snapshots of system
[42:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2541s) conditions and roll back. Uh then we
[42:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2544s) have slice units. These units are used
[42:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2546s) to for resource resource management
[42:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2550s) through Linux.
[42:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2552s) I'm not quite sure what that means but
[42:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2554s) okay. And then we have control groups
[42:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2556s) allowing for the allocation and
[42:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2559s) restriction of resources to processes. I
[42:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2561s) guess that's like groups for processes.
[42:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2564s) You create a group and this process can
[42:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2566s) only access these services on the
[42:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2569s) system. And then finally we have scope
[42:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2571s) units. These units are used to manage
[42:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2574s) groups um of externally created
[42:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2578s) processes providing a way to monitor and
[43:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2580s) control
[43:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2583s) them within systemd's framework.
[43:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2586s) Like I said folks, it's almost like an
[43:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2588s) operating system in and of itself.
[43:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2592s) It wants to take over. It can do
[43:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2594s) everything. You need a All you need is a
[43:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2596s) kernel and systemd and a web browser and
[43:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2598s) a mail client and you've got an
[43:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2600s) operating system. You don't need any of
[43:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2602s) this other garbage. You don't need etc.
[43:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2604s) You don't need any files, nothing, you
[43:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2606s) know, fs tab configuration files, cron,
[43:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2610s) none of that stuff. Just throw it all
[43:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2612s) away. We'll just use We'll just use
[43:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2613s) system date.
[43:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2616s) Sure, why not, right? Okay. So, let's
[43:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2618s) take a look at all of the crap that's
[43:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2620s) running on my system here. Let's do the
[43:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2622s) server first because the server, it
[43:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2624s) doesn't have a whole lot of stuff
[43:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2626s) running, so we should be able to see it.
[43:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2628s) So, we're going to do pseudo systemctl
[43:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2633s) and we're just going to give it full.
[43:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2636s) The ctl command when it is issued with
[43:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2639s) no arguments will just list everything
[44:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2641s) running on the system. It'll let you
[44:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2643s) know what's going on. Now, let's go
[44:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2645s) ahead and make this large fill the
[44:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2648s) screen because this is going to blow
[44:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2649s) your mind. Watch this.
[44:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2651s) Wow, man. What is that? This is sort of
[44:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2653s) like the less program, but it not only
[44:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2657s) lets you go up and down line by line,
[44:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2659s) you can go side to side.
[44:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2663s) So, this is all the stuff that's
[44:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2665s) running.
[44:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2666s) So, look uh right here we have the first
[44:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2668s) mount point there. See that is the root
[44:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2672s) mount
[44:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2674s) and then we have the home mount right
[44:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2676s) here.
[44:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2678s) Home mount.
[44:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2682s) So then we have different mount systems
[44:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2684s) there or or mounting different file
[44:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2687s) systems both real and virtual. Let's see
[44:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2690s) now we get into running services like
[44:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2692s) app armor. These are um mid-level
[44:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2695s) services like cron here
[44:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2698s) console setup service. There's Getty the
[45:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2702s) Getty service because this machine does
[45:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2705s) not have a desktop. Getty is how you
[45:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2707s) would log in if you were sitting down in
[45:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2709s) front of the machine.
[45:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2712s) keyboard.
[45:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2713s) There's SSH running.
[45:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2719s) We have the Getty slice here. These are
[45:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2723s) slice services like we were just reading
[45:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2725s) about.
[45:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2729s) Debus sockets
[45:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2733s) although we don't have a desktop running
[45:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2735s) but they're there.
[45:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2738s) Let's see. We have the basic target, the
[45:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2740s) graphical target,
[45:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2742s) the Getty target. I guess that would
[45:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2745s) just provide a login and not much else.
[45:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2747s) I don't know.
[45:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2749s) They see there is so much here to get
[45:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2751s) into. You could do these videos forever.
[45:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2753s) So, that's just the um
[45:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2756s) that's just my uh server which is
[45:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2758s) running basically nothing but SSH. You
[46:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2761s) can see that we have some timers set up
[46:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2763s) here like fs trim that is for um the SSD
[46:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2768s) to make sure that it trims the file
[46:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2770s) systems once a week. We also have some
[46:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2772s) other timers running. Here's man DB goes
[46:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2776s) and make sure that the database for the
[46:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2777s) manuals is backed up
[46:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2782s) as a cleanup timer for systemd. I like
[46:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2784s) to see things cleaning up after
[46:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2785s) themselves. This is DPKG to make sure
[46:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2788s) that it's up to date right there.
[46:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2791s) Uh, this is the apt daily upgrade timer.
[46:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2795s) And I I guess that's just going out to
[46:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2798s) make sure that it's up to date because I
[46:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2800s) do not have daily upgrades in the
[46:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2802s) background set up here. So, I'm not
[46:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2804s) quite sure what that does, but somebody
[46:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2805s) out there does.
[46:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2809s) So, once you're done looking, you just
[46:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2810s) hit Q and it will let you out.
[46:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2815s) Let's see if we can see much difference
[46:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2817s) between this and um the graphical
[47:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2822s) machine here. So, let's go ahead and
[47:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2823s) open up another terminal. We'll do the
[47:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2826s) same thing. This time we're looking at
[47:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2828s) the machine I'm recording the video and
[47:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2830s) we'll run the same command. So, it's
[47:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2832s) pseudo systemctl
[47:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2837s) full.
[47:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2839s) Let's see what we can see here.
[47:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2846s) It's probably going to be very similar.
[47:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2848s) These machines both have U Debian 13 as
[47:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2852s) the base. Uh the machine that I'm
[47:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2854s) recording is Linux Mint Debian Edition.
[47:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2858s) Let's just go down here and see if we
[47:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2860s) see anything that jumps out at us.
[47:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2864s) Let's look at the mounts. There's Well,
[47:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2866s) we have an extra mount there. We have
[47:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2867s) boot efi. So we have the root mount
[47:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2871s) here. Uh we have boot efi and then we
[47:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2874s) have home there because this machine
[47:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2877s) uses efi.
[48:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2880s) This is by the way the status here of
[48:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2882s) these. It's uh says that it's loaded act
[48:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2884s) loaded active and plugged which means
[48:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2886s) it's working. We've kind of skipped past
[48:09](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2889s) that.
[48:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2891s) Let's see. Here's cups. So the printing
[48:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2893s) service is up and running on this
[48:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2894s) machine.
[48:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2898s) Let's see what else do we have here.
[48:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2899s) Let's keep going. These are mid-level
[48:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2901s) services there.
[48:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2904s) Uh we have the accounts Damon service
[48:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2906s) running on this machine. App armor. We
[48:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2908s) have also
[48:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2913s) service on this machine.
[48:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2916s) There's cron.
[48:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2919s) Let's see what else we can who said we a
[48:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2922s) lot of this we can recognize.
[48:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2924s) There's network manager running on this
[48:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2926s) machine. Network manager is not running
[48:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2928s) on the server. The network is manually
[48:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2930s) configured on the server.
[48:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2933s) So we don't have to have network manager
[48:55](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2935s) running in the background. There's our
[48:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2937s) there's RSIS log service that's logging
[49:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2940s) things. There's the SSH service up and
[49:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2942s) running.
[49:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2943s) Uh we also have UFW running on this
[49:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2946s) machine. It has a firewall up and
[49:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2947s) running. There's not one on the server.
[49:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2952s) See what else we can come up with here.
[49:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2954s) Let's see.
[49:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2958s) Lots of target files. I notice uh for
[49:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2961s) timers, pretty much the same thing.
[49:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2966s) So, yeah, that's the main system and
[49:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2967s) what's on there.
[49:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2974s) So, let's see here. Let's go ahead and
[49:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2976s) get out of there. We'll clear that.
[49:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2978s) We'll come back to it. Maybe we'll find
[49:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2980s) something else to look at here. Go back
[49:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2982s) over to the notes. Um, we can check on
[49:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2985s) the status of any service that we like.
[49:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2988s) So, let's go over here and do that on
[49:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2990s) the file server. So, we will once again
[49:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2994s) use pseudo.
[49:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=2998s) And we're going to get status
[50:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3001s) as it is pronounced in Britain. And um
[50:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3005s) let's get SSHD.
[50:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3008s) Let's see what that service is doing.
[50:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3013s) And it seems to be up and running. And
[50:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3015s) it's running fine. It's doing what it's
[50:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3017s) supposed to do. And of course, this
[50:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3019s) spills off the screen. So you can do
[50:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3021s) that so you can figure out what's going
[50:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3022s) on there. And then we can get out with
[50:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3025s) Q. And let's do that one more time. And
[50:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3028s) this time around, instead of looking at
[50:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3029s) that, we're going to look at a timer.
[50:31](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3031s) Let's look at FS
[50:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3033s) trim
[50:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3035s) and go and see what that's doing.
[50:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3040s) Oh, I spelled it wrong.
[50:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3046s) Okay, that's correct. Here we go.
[50:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3048s) It says that that timer is up and
[50:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3050s) running. Now, if we want to check and
[50:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3052s) see what a service is doing, we can use
[50:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3054s) the log to do that in u
[51:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3060s) systemd. So, we're going to look at
[51:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3062s) that. Here is the um how we uh check out
[51:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3065s) logs in systemd. This is a bit of a rub
[51:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3068s) with some people because systemd does
[51:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3070s) not save logs in plain text format. it
[51:14](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3074s) saves them in their own kind of
[51:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3076s) proprietary format and you have to use
[51:18](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3078s) the system journal here to get
[51:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3081s) information from them and that kind of
[51:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3084s) ruffles some people's feathers because
[51:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3086s) as we have discussed on this channel
[51:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3088s) Linux in and of itself is considered to
[51:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3092s) be a system of files and the more of
[51:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3094s) those that are just plain text files and
[51:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3096s) easily readable the better.
[51:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3099s) So,
[51:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3101s) not going to get into that debate, but
[51:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3103s) we will uh check this out. So, we're
[51:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3105s) going to do uh let's do journal
[51:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3110s) ctl and we will take a look at the fs
[51:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3113s) trim.
[51:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3116s) Kind of nice to know if that's working.
[51:58](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3118s) And you have to spell that correctly or
[52:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3120s) it won't work.
[52:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3122s) Okay, let's see what we get here. Yep,
[52:04](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3124s) that's correct.
[52:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3126s) I still got it wrong. Let's see. It's an
[52:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3128s) invalid argument. What did I get wrong?
[52:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3131s) Is it trim fs? No, it's fs trim.
[52:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3137s) Oh, that's why
[52:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3140s) it wasn't the spelling. It was the fact
[52:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3141s) that you forgot the u. I'm new to this,
[52:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3144s) too, in a lot of ways. I don't really
[52:26](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3146s) use this stuff all the time. So, cut me
[52:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3148s) some slack.
[52:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3150s) All right. So, we can see that this has
[52:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3152s) been working.
[52:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3154s) It's been doing exactly what it's
[52:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3156s) supposed to do.
[52:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3158s) And that is once a week it is supposed
[52:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3160s) to run the FS trim command on our SSD so
[52:43](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3163s) that we can have proper wear leveling.
[52:45](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3165s) Very nice indeed.
[52:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3168s) Let's clear that and let's look at SSHD
[52:50](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3170s) and see if we have anything from there.
[52:59](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3179s) No entries for SSHD.
[53:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3183s) Let's just see SSH and see what we get.
[53:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3186s) Oh, there we go. I'm sorry I put the D
[53:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3187s) at the end. D is old school. [laughter]
[53:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3190s) That's the name of the Damon that's
[53:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3192s) running the service itself. So now we
[53:16](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3196s) can see what SSH has been doing.
[53:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3199s) That's what's in the log.
[53:22](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3202s) SS you notice that SSHD worked before.
[53:25](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3205s) So it figured out that I had had done
[53:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3207s) that and it brought up the SSHD service,
[53:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3210s) but then when I used the log, it didn't.
[53:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3212s) So, not quite sure what the deal is with
[53:34](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3214s) that, but you get the idea. And we can
[53:38](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3218s) just scroll down and
[53:40](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3220s) kind of gives you what SSH has been
[53:42](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3222s) doing on this machine.
[53:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3224s) Very cool indeed.
[53:47](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3227s) [sighs]
[53:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3228s) So, that's about it for the Linux boot
[53:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3231s) process. I hope you got something out of
[53:53](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3233s) this video. I will mention that users
[53:56](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3236s) can add their own unit files to systemd.
[54:01](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3241s) you have a special directory for that.
[54:02](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3242s) It's etc systemd system, but I'm not
[54:06](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3246s) going to get into that in this video
[54:07](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3247s) because that's way beyond the scope of
[54:10](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3250s) what we're talking about. But someday
[54:12](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3252s) you may find yourself needing to create
[54:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3253s) a service or a timer and you'll um no
[54:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3257s) doubt want to go look that up and there
[54:19](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3259s) are some great tutorials online that
[54:21](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3261s) show you how to do that in a very simple
[54:23](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3263s) fashion. So there you go.
[54:27](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3267s) I want to acknowledge a source that I
[54:29](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3269s) used for this video that is How Linux
[54:32](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3272s) Works. It's a book by Brian Ward and it
[54:37](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3277s) has been invaluable in getting things
[54:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3279s) together for this video and I've used it
[54:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3281s) for other videos in the past. It's in
[54:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3284s) the third edition now. Mine is the first
[54:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3286s) edition. It's over 10 years old at this
[54:48](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3288s) point. This is a great book to look at
[54:51](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3291s) if you want to start learning about
[54:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3294s) Linux. I have no affiliation whatsoever
[54:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3297s) with Brian Ward or the publishers of
[55:00](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3300s) this book, but I am looking at the
[55:03](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3303s) Amazon,
[55:05](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3305s) what do you call this? It's a
[55:08](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3308s) well, it's the Amazon listing. There you
[55:11](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3311s) go. That's the word. I was going to say
[55:13](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3313s) advertisement, but it's a listing uh for
[55:15](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3315s) this book. And currently, it's available
[55:17](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3317s) on Kindle for $29.99.
[55:20](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3320s) And uh you can also get it in printed.
[55:24](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3324s) Very sturdy paperback is what mine is.
[55:28](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3328s) And it's well worth the money. You can
[55:30](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3330s) check that out. And u Brian Ward's
[55:33](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3333s) little book here has helped me
[55:35](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3335s) immeasurably over the years putting
[55:36](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3336s) these videos together.
[55:39](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3339s) Thank you for watching. Your comments
[55:41](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3341s) and suggestions are always welcome. I
[55:44](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3344s) look forward to hearing from you. This
[55:46](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3346s) video was suggested by a viewer, and I'm
[55:49](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3349s) always looking for ideas from you guys
[55:52](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3352s) so I know what to talk about next here
[55:54](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3354s) on this channel. Thank you for watching
[55:57](https://www.youtube.com/watch?v=EjrAzulPsT4&t=3357s) once again. We'll do it again soon.