---
title: "How to create a Debian respin using Live-Build and the custom script written by @eznix"
url: "https://www.youtube.com/watch?v=XZ_QGjMiBpU"
videoId: "XZ_QGjMiBpU"
channel: "OldTechBloke"
channelId: "UCCIHOP7e271SIumQgyl6XBQ"
duration: "2004"
views: "17362"
isLive: false
isPrivate: false
---

#oldtechbloke

![How to create a Debian respin using Live-Build and the custom script written by @eznix](https://www.youtube.com/watch?v=XZ_QGjMiBpU)

[0:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=0s) hello welcome back to the OTB channel
[0:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=3s) have you ever fancied creating your own
[0:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=6s) custom Linux distribution well today's
[0:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=9s) video is going to look at how you can
[0:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=12s) create a Debian stable reece pin using
[0:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=16s) the live build application that's
[0:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=18s) available for Debian and a script that's
[0:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=21s) been developed by a fellow youtuber
[0:23](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=23s) called ethnics it provided me with hours
[0:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=27s) of endless fun over the last couple of
[0:29](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=29s) weeks and I thought you might be
[0:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=30s) interested in it okay
[0:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=48s) so creating a customized Debian Riesman
[0:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=52s) why would I want to do this well I
[0:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=56s) certainly don't want to create my own
[0:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=59s) distro and put it out there for everyone
[1:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=62s) to download or at least not at this
[1:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=64s) stage basically because I'm not a
[1:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=69s) developer and I don't feel that I'd be
[1:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=72s) able to maintain a distro that was out
[1:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=75s) there for the public but secondly where
[1:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=79s) I've got to so far it's not at that
[1:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=81s) stage to be honest I wanted to create a
[1:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=85s) reskin for two reasons one I like to
[1:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=90s) tinker and I wanted to see if I could
[1:33](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=93s) and secondly I thought it would be great
[1:38](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=98s) to actually have an ISO file of my Marte
[1:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=105s) desktop pre-configured so that if I have
[1:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=108s) to reinstall it or install it on another
[1:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=111s) machine and it's all up and running
[1:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=113s) really easily and so it's it's hobby
[1:58](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=118s) it's been my own interest really more
[2:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=120s) than anything else and the thing that
[2:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=124s) sort of sparked this interest was when I
[2:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=127s) watched a video by a fellow youtuber
[2:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=131s) called ethnics and let me just
[2:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=133s) move over to the split-screen here the
[2:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=137s) video was called building as Knicks how
[2:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=140s) to use Debian live build soup-to-nuts
[2:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=144s) and what ethnics has done here is he's
[2:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=148s) put together a number of different
[2:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=152s) videos that he's made and combine them
[2:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=155s) into a single video that goes through
[2:38](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=158s) the whole process now it was released
[2:41](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=161s) something like 10 months ago so it's not
[2:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=165s) completely up to date as far as the
[2:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=167s) script and the releases he's currently
[2:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=170s) got out there is concerned but it's a
[2:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=173s) great starting point I will include a
[2:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=176s) link to this video and also a link to as
[3:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=180s) Nyx's YouTube channel I would highly
[3:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=185s) recommend you subscribe to him and you
[3:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=187s) have a read through what you've done so
[3:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=190s) far and watch some of his videos now
[3:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=193s) once you find a read through as Nyx's
[3:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=195s) videos
[3:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=196s) it's also worth going to his SourceForge
[3:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=199s) page where he actually hosts his own
[3:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=204s) custom distro as nick's OS and there are
[3:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=210s) two projects really listed up at this
[3:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=215s) page the first is the ethnics OS distro
[3:41](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=221s) which includes not only an ISO file but
[3:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=225s) also at our package with all the build
[3:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=229s) scripts and directories that you would
[3:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=232s) need to build as Nix OS or your own
[3:55](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=235s) custom distro from scratch and he also
[3:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=239s) includes a script which will allow you
[4:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=242s) to convert a standard Debian into as
[4:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=247s) Nick so s now one thing I should say at
[4:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=250s) this stage is the ethnics OS is built on
[4:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=254s) xfce so all of the scripts relate to
[4:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=259s) xfce and if you collect the click on the
[4:23](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=263s) file section on SourceForge and
[4:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=267s) - as Nix OS you will see that as well as
[4:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=270s) the ISO file he includes a tar file and
[4:33](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=273s) if you download this this will get you
[4:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=277s) started building your own distro so I
[4:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=282s) downloaded the tar file I extracted it
[4:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=285s) and I ended up with a directory called s
[4:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=288s) annex 102 in my downloads folder and you
[4:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=292s) can see in front of you it basically
[4:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=296s) contains a whole range of different
[4:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=297s) folders the two most important folders
[5:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=302s) to take note of are the build script
[5:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=305s) build ethnics 102 that's included here
[5:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=309s) and the documents folder I went to the
[5:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=314s) documents folder first of all and I read
[5:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=318s) about well I open the prepare how to
[5:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=321s) text which describes the various stages
[5:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=325s) you should do when customizing the build
[5:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=331s) it all starts by installing the debian
[5:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=334s) package of live build so that's just the
[5:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=337s) simple apt-get install live build but
[5:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=340s) they then goes into more detail about
[5:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=343s) collecting all the wallpapers that you
[5:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=346s) want included on your custom distro
[5:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=350s) copying the xfce
[5:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=352s) for folder in your doc config folder
[5:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=356s) across to the ethnics 102 folder and
[6:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=360s) also collecting any extra scripts or deb
[6:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=365s) packages that you want included there is
[6:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=369s) then a second document in this folder
[6:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=371s) called build as Nick's how-to and
[6:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=377s) essentially this talks through what his
[6:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=380s) script does and I'm not going to talk
[6:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=385s) through it because I'll leave you to do
[6:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=387s) that yourself but I studied this for
[6:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=390s) quite a long time and I also went to the
[6:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=394s) live build folder where
[6:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=396s) he's included an HTML version of the
[6:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=400s) live build manual so let me just see if
[6:44](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=404s) I can drag that across no I can't
[6:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=409s) there we go um the basics and I spent
[6:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=416s) quite a number of hours just going
[6:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=419s) through this bottom line though I'm not
[7:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=425s) going to explain each and every stage of
[7:08](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=428s) this but this was my starting point
[7:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=431s) downloading as Nyx's tar file reading
[7:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=435s) his documentation and then deciding to
[7:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=438s) try and customize this to my own liking
[7:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=442s) so I'm gonna go through what I did so
[7:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=447s) okay I did a lot of reading and trying
[7:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=451s) to wrap my head around what as Nix has
[7:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=454s) done I'm a great believer in when
[7:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=460s) somebody's done the donkey work the hard
[7:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=463s) graft which ethnics has ultimately done
[7:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=467s) rather than trying to reinvent the wheel
[7:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=470s) we should use something that works and
[7:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=474s) you can actually make no modifications
[7:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=477s) whatsoever to his script or any of the
[8:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=483s) folders the includes in his download you
[8:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=486s) can just run the script and you'll get
[8:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=490s) as Nix OS I wanted to do something a
[8:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=494s) little bit different some of you might
[8:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=496s) have seen that I created a video several
[8:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=502s) weeks ago called mate Linux your own and
[8:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=505s) I customized my grub splash screen I
[8:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=511s) customized my like DM screen and I
[8:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=516s) wanted to make sure that rather than
[8:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=519s) having the plymouth boot splash that
[8:41](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=521s) that was disabled so I decided that I
[8:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=525s) would try and customize as Nyx's script
[8:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=529s) so it allowed me to essentially get a
[8:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=532s) lightly customized version of Debian to
[8:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=536s) my liking with my images so let me
[9:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=542s) explain what I did so the first thing I
[9:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=545s) did was I copied the ethnics 102 folder
[9:08](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=548s) that downloaded and put it in my home
[9:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=551s) directory
[9:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=551s) I then gathered up all the backgrounds
[9:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=555s) and wallpaper that I wanted and I copied
[9:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=558s) them into the backgrounds folder I've
[9:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=562s) left des Nyx's wallpaper there because
[9:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=564s) some of it's quite nice but I've also
[9:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=567s) copied in my wallpaper and there are two
[9:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=570s) versions of it one is just my wallpaper
[9:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=574s) the other is called background - dot jpg
[9:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=577s) which is essentially the same but that's
[9:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=580s) going to be used for the light DM
[9:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=583s) background okay that's number one
[9:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=588s) I also copied over my XFC for directory
[9:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=594s) from my dot config file along with the
[9:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=597s) decomp directory and the auto start
[10:01](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=601s) directory I wanted auto start because I
[10:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=605s) used the Planck doc and on my current
[10:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=610s) configuration
[10:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=611s) I had planck auto starting when i logged
[10:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=614s) in so it made sense to copy that across
[10:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=617s) and then went into the boot loaders
[10:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=620s) directory and I created a 640 by 480
[10:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=628s) splash screen exactly the same as my
[10:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=631s) wallpaper
[10:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=632s) again it's 640 by 480 and a copied that
[10:38](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=638s) into each of the directories slightly
[10:41](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=641s) different format for the one in for the
[10:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=645s) legacy bootloader it has to be splashed
[10:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=648s) xpm GZ but basically the same thing okay
[10:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=654s) and the same in each of those other
[10:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=656s) folders Ivan created a separate
[11:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=660s) directory called light DM
[11:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=663s) I copied across my light DM
[11:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=666s) configuration file and my slick greeter
[11:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=669s) configuration file and then last but not
[11:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=673s) least I created another folder called
[11:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=676s) default grub and in there I copied
[11:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=680s) across my etc' grub file in which I
[11:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=684s) disabled the Plymouth splash screen and
[11:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=687s) I also copied across my grub splash
[11:33](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=693s) screen grub background TGA okay
[11:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=699s) so I've got all the images that I wanted
[11:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=703s) my own grub background my own wallpaper
[11:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=708s) my own light DM background and the light
[11:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=713s) DM configuration files and the grub
[11:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=717s) configuration files right and then went
[12:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=722s) to the script now there are a couple of
[12:08](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=728s) stages with this script this is after
[12:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=731s) you've installed live build the script
[12:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=734s) will automatically create a build folder
[12:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=738s) for you called as Nix OS 102 once it's
[12:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=745s) done that it will then create
[12:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=748s) directories within that build directory
[12:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=752s) under a folder called includes dot CH
[12:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=757s) root now if you want to make any
[12:44](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=764s) customizations to your system you will
[12:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=768s) need to set them up here so I knew that
[12:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=773s) I wanted to include my xfce for file my
[12:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=779s) Deakin file and my auto start file on
[13:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=783s) the new system the best way to do this
[13:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=787s) is to create an e TC scale directory
[13:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=792s) plus your dot config directory under
[13:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=795s) that
[13:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=796s) and to put it in to include stock route
[13:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=799s) I also wanted to make some
[13:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=802s) customizations to grub itself and to
[13:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=806s) drop in my background therefore in the
[13:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=810s) includes Schrute directory
[13:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=812s) I created a boot grub directory I also
[13:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=817s) created another directory etc' like DM
[13:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=820s) and another directory etc' defaults now
[13:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=826s) the way to think about this is any
[13:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=829s) configuration files that you have on
[13:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=831s) your system if you want those
[13:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=836s) configuration files to be reflected on
[13:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=839s) your custom ISO you have to first create
[14:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=843s) the skeleton directory for those custom
[14:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=847s) or configuration files to go right ok
[14:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=852s) all good so far I then moved on to
[14:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=857s) copying across my wallpapers my
[14:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=862s) configuration files and everything else
[14:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=866s) that I wanted in that customized so into
[14:29](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=869s) the schrute environments into those
[14:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=871s) folders that I've just created so you'll
[14:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=875s) see here if we go down I am copying
[14:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=880s) across my light DM everything in that
[14:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=883s) light DM directory into the e.t.c light
[14:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=887s) DM directory in the schroot environments
[14:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=889s) I'm copying across my etc' default grub
[14:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=896s) file into etc' default in the fruit
[15:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=900s) directory I'm copying across my grub
[15:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=905s) background into the boot forward slash
[15:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=910s) grub directory including included in the
[15:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=914s) fruit environment so I create the
[15:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=919s) directories in the fruit environments I
[15:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=921s) then copy across all the images and
[15:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=926s) files that are once
[15:29](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=929s) in the customized so into those
[15:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=931s) directories now I won't pretend for one
[15:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=936s) minute that I figured out how to do this
[15:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=939s) immediately
[15:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=942s) my first thought as you know I'm a user
[15:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=946s) of Marte was to create a Marte ISO and
[15:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=953s) it did build an ISO file but I got
[15:58](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=958s) kernel panic when trying to boot it so I
[16:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=960s) decided to stick with xfce and to
[16:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=966s) install xfce on my desktop and to go
[16:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=970s) with that and to kind of develop it from
[16:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=972s) there and I had a couple of attempts
[16:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=981s) where it was almost there but not quite
[16:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=985s) my grub splash was working fine
[16:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=990s) initially on the live build but it
[16:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=994s) didn't seem to be passed over when I
[16:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=996s) installed the system the system still
[16:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1000s) just used the default grub my wallpaper
[16:44](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1004s) wasn't showing up on the install system
[16:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1007s) or on the live live CD and I couldn't
[16:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1012s) figure out why that was and initially I
[16:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1017s) was struggling to get like DM working
[17:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1020s) which is what resulted in me creating a
[17:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1023s) separate default grub a separate light
[17:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1026s) DM directory and playing about with the
[17:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1030s) script until it worked for me
[17:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1033s) bottom line is all eventually worked
[17:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1037s) with the exception that I could not get
[17:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1041s) the default wallpaper to show up either
[17:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1045s) on the live system or the install system
[17:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1048s) and I couldn't figure out how to rectify
[17:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1052s) this until I eventually came across a
[17:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1057s) post on a particular forum and the
[17:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1062s) answer was
[17:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1063s) let me go back to the screen the answer
[17:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1067s) was to go into that XFC 4 folder that
[17:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1073s) had copied across to open up within that
[17:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1077s) folder xf conf
[18:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1080s) and Xfce per channel XML and in there
[18:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1086s) you'll find an XML file called XFC for
[18:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1090s) desktop dot XML I open that up and I
[18:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1098s) found that if I changed every single
[18:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1101s) entry that referred to a wallpaper in
[18:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1105s) this XML file and I'd put my wallpaper
[18:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1108s) by the way in us our share backgrounds
[18:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1111s) if I changed it all to my OTB jpg and
[18:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1115s) then rebuilds suddenly the wallpaper was
[18:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1120s) showing up so it was a little bit of
[18:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1126s) trial and error to be honest and I know
[18:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1128s) this is a complex script but essentially
[18:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1131s) it consists of a number of simple stages
[18:55](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1135s) install live build create a directory
[19:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1142s) for your live build the ink that
[19:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1144s) contains the includes root directory
[19:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1150s) mirror in your includes docks root
[19:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1153s) directory anything that you want to end
[19:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1156s) up in your final build
[19:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1158s) including the directory structure copy
[19:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1162s) across your customized files and
[19:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1165s) configurations into those directories
[19:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1168s) that you've just created and then to the
[19:33](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1173s) build so that was my basic changes made
[19:38](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1178s) to the script I then copied the script
[19:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1182s) over into my home directory I opened it
[19:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1189s) up again and I know I'd already sorted
[19:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1193s) out making the folders in the chroot
[19:55](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1195s) environment
[19:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1196s) and copying the relevant files over to
[20:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1200s) that environment but there's two other
[20:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1203s) important parts right at the beginning
[20:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1206s) the script is going to set up a build
[20:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1209s) environment with the elbe config command
[20:12](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1212s) I have essentially stuck with what was
[20:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1217s) in the ethnics script right from the
[20:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1220s) word go
[20:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1220s) I've made one amendment only and that is
[20:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1224s) to this that's this one which is
[20:27](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1227s) interactive shell what that does are
[20:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1232s) towards the end of the build process it
[20:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1235s) allows me to interact with the build and
[20:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1239s) to double check that all my directories
[20:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1242s) have been created and my configuration
[20:44](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1244s) files have been copied across I then
[20:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1249s) moved on to what ethnics calls phase
[20:52](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1252s) four which is where he talks about or
[20:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1257s) where he lists all the different
[20:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1259s) software that he wants installed I left
[21:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1263s) this pretty much default except I also
[21:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1266s) installed chromium the materia gtk theme
[21:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1270s) and the slick greeter so I just added
[21:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1274s) that to the list okay everything else is
[21:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1278s) as default from there I opened a
[21:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1285s) terminal and let's just make this
[21:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1288s) fullscreen I went to route I moved to my
[21:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1299s) home directory and what I want to run is
[21:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1306s) this script build ethnics 102 i kept me
[21:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1311s) fingers crossed I'll just use bash to
[21:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1316s) run the script and I'll hit enter and
[22:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1320s) I'll speed this section up until we get
[22:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1324s) to the interactive element
[22:08](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1328s) okay well rather than speeding it up I
[22:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1331s) decided to just pause it as it takes
[22:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1334s) quite a while it took about half an hour
[22:16](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1336s) to do the build before it gets to the
[22:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1339s) interactive elements so we'll move on
[22:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1342s) now it's got to that stage so it's now
[22:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1346s) got to the stage of allowing us to
[22:29](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1349s) interact with the build and you'll know
[22:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1352s) it's got to this stage because it'll
[22:35](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1355s) stop at a prompt that says live route at
[22:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1359s) whatever your PC's name is or whatever
[22:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1362s) your ps1 prompt is if I issue LS here
[22:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1367s) you will see the directory structure of
[22:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1370s) the build environment I have included
[22:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1374s) this interactive element just so that I
[22:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1377s) can check that so far the modified
[23:01](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1381s) script has done what it needs to do so
[23:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1385s) for instance from here I can CD into the
[23:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1390s) e.t.c light DM directory and I just
[23:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1398s) wanted to check that it's included my
[23:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1401s) light DM kampf and my slick greeted
[23:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1404s) Kampf
[23:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1405s) I could also CD in to us our share
[23:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1410s) background so just to check that all of
[23:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1426s) those backgrounds I copied across are
[23:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1428s) there which indeed they are asked you
[23:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1431s) see background to jpg is there an OTB
[23:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1434s) JPEG is there as well as Oliver ethnics
[23:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1439s) is there wallpaper once you're happy
[24:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1444s) that the script has moved everything
[24:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1447s) across as it should at this stage all
[24:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1449s) you have to do is type exit and it will
[24:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1453s) carry on and it will create your ISO
[24:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1458s) we'll come back in a second once it's
[24:21](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1461s) completed that process
[24:23](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1463s) right so the script is finished and
[24:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1468s) you'll see here in my home directory as
[24:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1470s) well as the ethnics 102 directory
[24:33](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1473s) there's also another directory called as
[24:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1476s) nix OS 102 this is actually the build
[24:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1480s) folder and within that bill folder it's
[24:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1483s) dropped an image called live image amd64
[24:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1487s) hybrid ISO okay so first thing to do
[24:55](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1495s) let's test that in VirtualBox our
[25:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1502s) VirtualBox is absolutely wonderful for
[25:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1504s) this sort of testing I've set it up as
[25:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1506s) normal with 8 gig of ram and a 32 gig
[25:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1511s) virtual hard drive and I've attached
[25:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1514s) that ISO the live image AMD 64 hybrid
[25:19](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1519s) ISO so let's start it and see what
[25:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1522s) happens
[25:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1526s) well that's a good sign so far the grub
[25:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1530s) splash is there and we have the options
[25:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1532s) here to just run a live session or to
[25:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1537s) actually do a graphical install it
[25:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1540s) doesn't have calamari such a huge uses
[25:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1543s) the Debian installer so let's try the
[25:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1545s) live system to start off with and to see
[25:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1550s) what happens here it seems to take a
[25:55](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1555s) little while to boot but I know having
[25:58](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1558s) done this on previous bills that it does
[26:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1563s) actually boot fine so fingers crossed
[26:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1569s) here while it's booting it's
[26:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1578s) established a connection and there we go
[26:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1586s) it's picked up the wallpaper so this is
[26:32](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1592s) the live environments if we go to
[26:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1597s) settings on the live environment you can
[26:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1600s) see that if I go to appearance for
[26:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1605s) instance it's applied the materia dark
[26:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1608s) theme it's also picked up hopefully the
[26:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1614s) icons the papyrus icons they tend to use
[27:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1620s) in the home directory pretty much what
[27:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1625s) you'd expect but if I go to view show
[27:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1629s) hidden folders and I go into config
[27:14](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1634s) config the decomp the auto start and the
[27:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1638s) xfce for folders are the ones I copied
[27:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1642s) across so if I go into X sorry Auto star
[27:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1646s) you should see that the planck
[27:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1648s) desktop file is already there so all
[27:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1651s) good is planck running i don't know
[27:37](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1657s) let's have a look yes it is it's also
[27:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1660s) started planck so we have the bare bones
[27:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1663s) here of what i have on my desktop the
[27:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1669s) key though is will it install okay it's
[27:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1673s) now rebooting and we get the same grub
[27:58](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1678s) screen up but this time i want to do an
[28:01](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1681s) install so let me hit the graphical
[28:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1683s) installation and it should just go
[28:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1685s) straight into the Debian installer which
[28:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1689s) it has you've all seen this before so
[28:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1693s) I'm not going to record this section
[28:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1695s) I'll do the installation and we'll come
[28:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1697s) back and we'll see if the install system
[28:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1700s) actually works right so this is the
[28:22](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1702s) moment of truth I call the OTB spin
[28:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1706s) let's hit start and see if it boots
[28:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1711s) okay well first and foremost my grub
[28:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1716s) splash screen is there which is great I
[28:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1720s) don't want to see the plymouth splash i
[28:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1723s) just want to see a tech startup so
[28:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1728s) hopefully that's all configured and
[28:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1730s) working properly we will see in a minute
[28:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1734s) I'm keeping my fingers crossed here one
[28:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1736s) of the things that I have found about
[28:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1739s) this is it does seem to take a while to
[29:01](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1741s) boot into the desktop not quite sure why
[29:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1745s) but it does eventually start to work and
[29:13](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1753s) there we go and that's exactly what I
[29:17](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1757s) want to see I don't want to see Plymouth
[29:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1760s) right we've got to like DM it's got my
[29:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1765s) light DM file their background I set it
[29:30](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1770s) up as OTB as the user so let me login
[29:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1779s) now this is the stage that seems to take
[29:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1782s) I was gonna say it takes a little while
[29:44](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1784s) but this one came up quite quite quickly
[29:48](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1788s) and there you have it I wonder if I can
[29:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1791s) make this fullscreen it's not got the
[29:58](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1798s) the guest additions install so I may
[30:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1802s) need to just make a few tweaks here so
[30:06](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1806s) let me have a look display what could I
[30:10](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1810s) get it to let me think let's just go for
[30:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1818s) 1680 by 1050 it doesn't want to do that
[30:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1826s) for some reason
[30:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1836s) 1600 by 900 there we go
[30:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1839s) that'll do to start off with okay so
[30:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1843s) you're not seeing it fullscreen I'd have
[30:45](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1845s) to install the guest additions for that
[30:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1847s) but nevertheless what I have is a custom
[30:51](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1851s) distro that's installed I could save
[30:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1856s) that iso image put it on a USB stick and
[30:59](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1859s) i can install this on this system or a
[31:03](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1863s) laptop or anything else for that matter
[31:07](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1867s) so that's how to create a custom distro
[31:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1871s) with the debian live build application
[31:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1875s) and the script by ethnics with some
[31:18](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1878s) modifications that i've applied this is
[31:23](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1883s) work in progress people so I'm not going
[31:25](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1885s) to be putting this ISO up anywhere what
[31:28](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1888s) I will do though is I will stick my
[31:31](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1891s) script or my version of ethnics is
[31:34](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1894s) script into Google Drive and make it
[31:40](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1900s) public if you want to download it and
[31:42](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1902s) have a look and I'll include a link in
[31:46](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1906s) the well below this video in the
[31:49](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1909s) information section so work-in-progress
[31:54](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1914s) it's not where I want it to be yet I
[31:57](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1917s) want it to be a Marte desktop but I've
[32:01](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1921s) had a great deal of fun getting to this
[32:04](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1924s) stage and I thought you might just be
[32:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1925s) interested in seeing where I'm up to if
[32:09](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1929s) you want to follow along and do
[32:11](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1931s) something similar please read all of the
[32:15](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1935s) documentation start off with looking at
[32:20](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1940s) as Nyx's videos download is tar file
[32:24](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1944s) read the documents that he's kindly
[32:26](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1946s) supplied get your head round the process
[32:29](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1949s) and play away we all like to tinker and
[32:36](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1956s) if you're at a loose end one Saturday I
[32:39](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1959s) can only say this provides hours of
[32:43](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1963s) endless fun so I probably won't do
[32:47](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1967s) something like this again for a while
[32:50](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1970s) I will do my normal videos and I will
[32:53](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1973s) probably produce around the end of the
[32:56](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1976s) year one of my rambles if you want to
[33:00](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1980s) make sure that you get that please
[33:02](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1982s) subscribe and hit the notification bell
[33:05](https://www.youtube.com/watch?v=XZ_QGjMiBpU&t=1985s) and I'll see you next time everyone