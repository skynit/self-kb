---
title: "Creating Your Own Linux Distribution"
url: "https://www.youtube.com/watch?v=pZcbHdBs_TE"
videoId: "pZcbHdBs_TE"
channel: "Chris Titus Technical"
channelId: "UCtYg149E_wUGVmjGz-TgyNA"
duration: "1241"
views: "156503"
isLive: false
isPrivate: false
---

#chris-titus-technical

![Creating Your Own Linux Distribution](https://www.youtube.com/watch?v=pZcbHdBs_TE)

[0:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1s) all right how's everybody doing this
[0:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=3s) morning
[0:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=4s) uh we have a special special
[0:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=7s) a live stream today where we're
[0:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=9s) basically building kind of our own
[0:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=11s) environment for linux uh i saw some
[0:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=14s) really good stuff
[0:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=15s) on chat that was uh you know pre-game
[0:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=18s) chat i guess
[0:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=19s) where some people were talking about gen
[0:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=21s) 2 and lfs which is great
[0:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=23s) a lot of people don't realize this but
[0:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=25s) gen 2 is the
[0:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=27s) distribution that chrome os uses it's
[0:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=29s) just a very customized
[0:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=31s) uh os now most people know i hate
[0:34](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=34s) to say hello here's all these different
[0:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=36s) distributions try them out because i
[0:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=37s) think
[0:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=38s) honestly just pick a mainline branch and
[0:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=40s) then go for it so
[0:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=41s) in my eyes there's really only three
[0:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=44s) distributions
[0:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=46s) that you really that really exist and
[0:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=48s) that's debian
[0:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=50s) arch and and a lot of the the red hat
[0:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=54s) based stuff which would be fedora
[0:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=56s) for for most people but there's so many
[0:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=58s) spin-offs of that and really when it
[1:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=60s) comes to the spin-offs of those
[1:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=61s) distributions
[1:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=62s) it comes to customization so in today's
[1:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=65s) live stream really what we're going over
[1:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=67s) is just kind of taking one of those
[1:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=69s) mainline branches
[1:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=70s) and i think we're gonna probably do this
[1:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=72s) maybe once every month or two
[1:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=75s) and the next one we'll pick a different
[1:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=77s) branch so
[1:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=78s) in today's branch of course we're gonna
[1:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=80s) choose
[1:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=81s) arch linux uh which when it comes to
[1:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=84s) arch uh
[1:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=86s) arch is fantastic for learning linux
[1:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=89s) so when it comes down to that that's
[1:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=93s) that's really the the big thing about uh
[1:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=96s) arch linux that i want people to take
[1:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=98s) away from it it's a great
[1:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=99s) learning experience if i just want
[1:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=101s) something to work forever and ever and
[1:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=103s) ever and just kind of leave it in the
[1:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=104s) background much like my streaming pc
[1:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=106s) that i use to
[1:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=107s) display all the overlays with my stream
[1:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=109s) deck and everything typically that's
[1:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=110s) debian based
[1:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=111s) i have a custom project we've already
[1:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=113s) kind of started it's called archmatic
[1:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=115s) it's been on my repost for a long time
[1:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=117s) but i kind of retooled it this past
[1:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=119s) week and fixing it up to basically do a
[2:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=123s) lot of the automation for us
[2:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=125s) eventually it'll get to the point where
[2:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=127s) it just is a one
[2:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=129s) script and you just click that and just
[2:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=131s) does everything for you
[2:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=133s) but for today's video it's obviously
[2:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=136s) broken up into about five parts
[2:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=138s) the reason why breaking it up when
[2:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=140s) before you combine it into a script it
[2:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=141s) just makes it easier to troubleshoot
[2:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=143s) stuff are you gonna go package this in
[2:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=145s) an iso
[2:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=146s) i am not i am not crypto thank you for
[2:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=149s) the super chat by the way
[2:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=151s) um i will make obviously it's on github
[2:34](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=154s) i eventually want to make it just so you
[2:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=156s) can kind of learn the steps and
[2:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=158s) customize this script to your needs what
[2:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=160s) i like to do we're just going to
[2:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=162s) first grab some utilities uh from
[2:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=164s) pac-man
[2:45](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=165s) uh we're going to have to do a syyy just
[2:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=167s) to kind of update the
[2:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=169s) what's there and then once it grabs our
[2:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=172s) database we'll be able to
[2:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=174s) grab some of the utilities we need first
[2:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=177s) off we're going to need git
[2:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=178s) curl w get
[3:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=181s) just some basic things just to grab
[3:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=184s) those scripts that we're talking about
[3:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=186s) yeah and some people are asking in chat
[3:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=189s) about
[3:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=189s) desktop environments of today um we're
[3:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=192s) not going to use any desktop
[3:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=193s) environments this is going to be
[3:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=195s) from not from scratch because links from
[3:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=197s) scratch are building the kernel you're
[3:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=198s) building out the package manager you're
[3:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=200s) building out everything
[3:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=201s) where arch just kind of makes it easy
[3:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=204s) and
[3:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=205s) i mean i say easy but relatively
[3:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=208s) to build your own and get every single
[3:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=211s) little piece what you want
[3:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=213s) uh so that's the point of this this
[3:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=215s) stream is really to get
[3:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=216s) everything that you use and make it
[3:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=219s) streamlined so you're not gonna have a
[3:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=222s) whole bunch of bloat you're not gonna
[3:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=223s) have any bloat really
[3:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=224s) every single package we're purposely
[3:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=227s) installing
[3:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=228s) and saying these are exactly what i will
[3:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=230s) use
[3:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=231s) and then there won't be any excess
[3:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=233s) packages that we never use
[3:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=234s) in this uh thing so we're gonna just get
[3:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=236s) clone and we're gonna get
[3:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=238s) that github project we're going to clone
[4:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=240s) it
[4:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=241s) and then we can start the actual setup
[4:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=244s) now this i've never actually tried this
[4:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=245s) script prior to the live stream
[4:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=247s) which well probably should have but you
[4:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=250s) know i always like to show
[4:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=252s) failure on my live streams
[4:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=255s) uh it's just it's just fun that way
[4:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=258s) so you guys can know that hey nobody's
[4:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=260s) perfect
[4:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=262s) and uh when you look at like a youtube
[4:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=264s) video and someone does it in like five
[4:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=265s) minutes
[4:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=266s) just know that that's probably chopped
[4:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=268s) up a bit
[4:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=270s) so let's go ahead run the pre-install
[4:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=273s) pre-installs first and then you go once
[4:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=275s) it boots into the system
[4:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=276s) zero through nine i couldn't remember
[4:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=278s) the order i put it in
[4:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=280s) it is still early for me all right so
[4:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=282s) this is the pre-install
[4:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=283s) once you get in here clone it and then
[4:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=286s) from the clone
[4:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=287s) reinstall this will reformat our drives
[4:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=289s) but i don't care let's let's see what
[4:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=291s) happens
[4:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=292s) yeah so this will automatically format
[4:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=294s) your drive so don't don't run it on
[4:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=296s) like your main box um obviously
[4:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=299s) that would not be good so this what it
[5:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=301s) does is
[5:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=302s) it says hey um this
[5:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=305s) is gonna format your drive it doesn't
[5:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=307s) lsb okay so you can pick out what one
[5:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=310s) is the actual thing so we're gonna go
[5:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=311s) dev sda because that's our
[5:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=313s) our actual main disk for this machine it
[5:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=316s) should go through format it
[5:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=318s) let's see what happens all right so
[5:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=321s) proceed anyways
[5:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=323s) yes so like i hate making
[5:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=326s) distributions or i hate making uh new
[5:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=329s) distributions so
[5:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=330s) really this is just gonna be a
[5:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=331s) customized arch you can call it whatever
[5:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=333s) you want
[5:34](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=334s) i'll i'll leave it to you guys as this
[5:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=336s) is something i want you guys to just run
[5:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=338s) and at the end of the day you're just
[5:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=339s) going to have
[5:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=341s) the base arch and then obviously here in
[5:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=343s) a month or two
[5:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=344s) we can switch over and do the same thing
[5:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=346s) for debian
[5:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=347s) to where it would automatically do a lot
[5:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=349s) of this stuff as well
[5:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=350s) but overall i think the script's working
[5:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=352s) really well i really like how the
[5:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=353s) formatting worked out really
[5:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=355s) good there we're going to see if the
[5:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=356s) actual partition tables are proper this
[5:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=358s) time
[5:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=359s) the last time we got to this step uh the
[6:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=361s) partition tables weren't
[6:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=363s) uh so i'm curious to see if this cg disc
[6:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=365s) or sg disc is what we're using for
[6:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=368s) in the script so this time around it
[6:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=369s) uses sg disk
[6:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=371s) we'll see how well it does with the
[6:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=372s) partition tables and and how well it
[6:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=374s) partitioned that
[6:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=375s) efi partition how many times can i say
[6:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=378s) partition
[6:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=378s) all right now let's do a boot ctl
[6:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=381s) install i believe
[6:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=385s) this is ch yeah it is ch root i believe
[6:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=388s) yeah yeah we're ch root in
[6:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=390s) so we should be able to just do b ctl
[6:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=392s) install
[6:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=397s) that looked like it worked but i don't
[6:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=400s) think we have a loader
[6:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=401s) if we go into so this is using systemd
[6:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=404s) boots so
[6:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=404s) folks that never seen systemd boot it's
[6:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=406s) actually easier than grub
[6:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=408s) if everything's set up exactly right um
[6:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=411s) but a lot of times it's not so it can
[6:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=414s) definitely throw you through a loop if
[6:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=416s) we go into our boot
[6:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=417s) let's see here we have the linux image
[7:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=420s) in efi we should have an actual loader
[7:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=423s) file
[7:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=424s) which i don't see any loader you know
[7:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=427s) what i don't think in the options i
[7:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=429s) don't i don't think this is right
[7:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=430s) anyways
[7:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=431s) because i think i need to use uuid when
[7:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=433s) i'm making this loader
[7:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=435s) so under boot loader we need to actually
[7:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=438s) add those uh the directory
[7:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=441s) and entries i think we should be fine
[7:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=443s) after that though
[7:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=444s) so let's flip back over here and see
[7:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=447s) what we got
[7:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=451s) let's come back into the boot directory
[7:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=453s) do an ls
[7:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=455s) loader ls and
[7:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=459s) i think in entries we should see
[7:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=461s) something which we don't have anything
[7:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=463s) so let's make an
[7:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=464s) arch.com entry
[7:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=467s) and then here we'll make the title
[7:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=470s) title um arch
[7:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=474s) linux and from here we're going to do a
[7:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=477s) full
[7:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=478s) retrieve of a blk id
[8:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=482s) and we're going to grab sda
[8:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=486s) 1 or sda2 so we're going to actually
[8:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=490s) wrap actually let's grab ext4 i believe
[8:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=494s) would be oh i'm trying to think of what
[8:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=497s) i named this
[8:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=499s) i don't think i named yeah let's just
[8:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=501s) grab the whole blk id and then we'll
[8:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=502s) just clean it up
[8:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=504s) all right so we don't need sda1
[8:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=509s) we'll delete that sda2
[8:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=513s) we'll just get rid of and then let's
[8:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=516s) just
[8:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=516s) punch into insert mode and then we're
[8:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=519s) just going to pull this
[8:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=520s) back grab just the uuid
[8:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=524s) and that should be it
[8:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=528s) let's escape that and then we're just
[8:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=532s) going to delete the rest of the line
[8:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=535s) so i'm just grabbing the uuid and we're
[8:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=537s) just building this one
[8:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=538s) systemd entry out it's weird that i
[9:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=541s) think there's some program for systemd
[9:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=543s) i think i can't remember what it is but
[9:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=545s) it'll actually build the loader files
[9:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=547s) for you i need to look more into that so
[9:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=549s) i'm not custom building it like this
[9:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=550s) so this one let's just go ahead
[9:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=554s) delete that we'll put root in
[9:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=558s) equals and quote it
[9:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=561s) and then end up with rw at the end i
[9:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=564s) always keep hitting escape darn vms
[9:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=569s) but anyways this is a good test
[9:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=572s) and then we'll save and quit so for
[9:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=575s) clear
[9:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=576s) list we can cap that arch and this is
[9:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=580s) what
[9:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=580s) our boot should be sometimes you can do
[9:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=582s) a u code in here i don't think i need
[9:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=584s) the u code
[9:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=586s) on this maybe i do let's see i don't
[9:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=589s) have it over
[9:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=590s) here but i did notice on this one
[9:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=594s) having the intel u code
[9:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=598s) which this is an intel on this guy i
[10:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=600s) don't know if i actually need that for
[10:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=602s) the virtual box or not let's take a look
[10:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=606s) let's take a look and and when i say
[10:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=608s) linux is linux a lot of this
[10:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=610s) is universal so right now i don't see a
[10:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=614s) u code anywhere in here
[10:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=615s) as far as the image also matching what
[10:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=618s) we just installed on that so if we
[10:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=620s) cat into loader
[10:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=624s) oh cat loader entries
[10:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=628s) arch i always like anytime i make a
[10:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=632s) custom boot entry just checking to make
[10:34](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=634s) sure
[10:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=635s) that like this right here vm
[10:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=638s) linux also matches this file then we
[10:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=641s) also have init ram
[10:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=642s) fs linux in it ram fs dash linux
[10:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=646s) right here so those files are good but
[10:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=648s) don't put
[10:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=649s) entries in here for files you don't have
[10:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=651s) obviously that would
[10:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=653s) not work you might see other things in
[10:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=655s) just the boot directory
[10:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=656s) when you do a listing you might see uh
[10:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=659s) amd
[11:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=660s) dash u code or intel dash u code you'd
[11:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=662s) want to add that option
[11:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=664s) into the loader file uh per per the
[11:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=667s) entry in here so you can actually see
[11:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=669s) that right here but
[11:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=671s) we don't need to do it in this instance
[11:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=676s) all right so now
[11:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=681s) yay we have ip
[11:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=684s) we'll first update our databases
[11:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=688s) and then let's do a listing
[11:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=691s) [Music]
[11:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=692s) all listing all right yeah we're this is
[11:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=695s) bare bones so we're not
[11:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=697s) we're not really using anything uh let's
[11:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=699s) let's see where we start with
[11:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=701s) so this is as bare bones linux distro as
[11:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=704s) you can get
[11:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=707s) just i'm already starting to blow it up
[11:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=709s) with neofetch i hear you guys i hear you
[11:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=711s) guys i don't even need to look at chat
[11:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=712s) to know that's what you guys are saying
[11:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=713s) all right so bare bones linux distro
[11:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=716s) about 67 megs of memory
[11:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=719s) being used um 163 packages
[12:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=724s) so overall i mean that's pretty pretty
[12:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=726s) [Music]
[12:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=728s) light
[12:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=731s) so we will be using awesome window
[12:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=732s) manager we will not be using
[12:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=734s) any desktop environments however there's
[12:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=736s) going to be pieces of desktop
[12:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=737s) environments we might grab
[12:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=739s) like lx appearance i think is one um
[12:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=742s) a lot of x utilities i like to use i
[12:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=745s) switched off x screensaver and i'm now
[12:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=747s) using xfc for
[12:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=748s) power manager i love the xfce power
[12:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=751s) manager
[12:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=752s) i think it's one of the best so i'm just
[12:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=755s) kind of piecemealing a little bit of
[12:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=756s) this
[12:36](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=756s) but what i don't want is to hit a large
[12:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=759s) desktop package and install an entire
[12:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=761s) desktop environment and load up this
[12:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=763s) whole system
[12:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=764s) as if this ever goes over like a
[12:45](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=765s) thousand packages
[12:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=767s) i did something wrong um so i think from
[12:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=770s) here let's go ahead
[12:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=772s) and now we have to install some of our
[12:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=774s) stuff which
[12:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=775s) get curl that should be able to get us
[12:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=778s) most of the way there
[12:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=779s) [Music]
[13:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=782s) and we're going to get clone
[13:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=785s) notice that the installation media is
[13:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=787s) different from the actual install so
[13:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=788s) that's why we're cloning it twice
[13:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=791s) and we're going to just go crystal's
[13:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=793s) tech archmatic
[13:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=796s) and then we can actually do a listing
[13:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=800s) i need to grab my aliases it's driving
[13:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=803s) me batty
[13:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=804s) all right anyhoo all right uh
[13:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=807s) ls al and we should have all executing
[13:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=811s) scripts
[13:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=811s) and we can run our setup and this
[13:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=815s) should create our user get everything
[13:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=817s) exactly how it should be
[13:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=820s) oh somebody says arch
[13:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=823s) somebody done like awesome window
[13:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=824s) manager
[13:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=827s) hater all right we'll call this one
[13:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=830s) titus arch
[13:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=831s) for the host name username we're going
[13:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=833s) to call this titus
[13:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=834s) password super secure
[13:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=838s) and here we go this should
[14:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=842s) start customizing the distro itself uh
[14:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=845s) using just the zero setup
[14:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=846s) and then we'll walk through each step
[14:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=849s) and see what we get
[14:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=851s) and now now we can do this
[14:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=855s) now this should install this is doing
[14:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=857s) xorg and i think i put little
[14:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=859s) little brackets here to see what all is
[14:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=861s) going on but i'll try and say what's
[14:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=862s) happening as it's installing
[14:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=865s) so right now it's just doing all the
[14:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=866s) xorg stuff which is this display
[14:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=868s) renderer
[14:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=870s) alright so this one is finished for the
[14:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=872s) base and now we can move on to
[14:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=875s) the software the software portion is
[14:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=877s) just like your basic utilities that are
[14:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=879s) needed
[14:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=880s) a file manager that type of thing so
[14:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=882s) let's go ahead and run it
[14:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=884s) so we're going to go to software pac-man
[14:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=888s) so this is just going to be some little
[14:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=889s) things i am adding the lts kernel
[14:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=892s) if you're ever running arch you always
[14:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=894s) want the lts kernel because there's
[14:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=895s) going to be times things break
[14:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=897s) so that's why
[15:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=900s) and also like this basic setup should
[15:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=903s) get you to
[15:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=904s) basically how my desktop looks as well
[15:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=907s) so
[15:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=908s) it should be a very very minimal install
[15:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=910s) with
[15:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=911s) pretty much everything that you need or
[15:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=913s) everything that i use
[15:14](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=914s) but if there is something missing fork
[15:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=916s) it add add whatever packages you want to
[15:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=919s) it
[15:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=919s) or remove packages there's things that i
[15:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=922s) know a lot of the linux community hates
[15:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=923s) that i use
[15:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=924s) like vs code um and some
[15:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=928s) some other probably software packages in
[15:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=930s) there as well
[15:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=933s) uh the system specs on this guy is four
[15:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=935s) megs of memory
[15:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=937s) uh two cores um
[15:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=942s) 32 gigs of memory or 32 gigs on the hard
[15:45](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=945s) drive
[15:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=947s) so not not much uh if we look at
[15:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=950s) let's see what we're at on the neofetch
[15:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=952s) already
[15:54](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=954s) yeah we're already up to 800 packages
[15:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=957s) yeah we're getting close to that
[15:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=959s) thousand thousand package marker
[16:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=961s) but pretty much everything's installed
[16:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=962s) except for the aur
[16:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=964s) stuff which i don't really use much in
[16:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=966s) the aur so this should go pretty quick
[16:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=968s) all right so we got all that in um now
[16:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=971s) we can do the post install
[16:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=973s) and we should be able to boot up here
[16:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=978s) and there we are
[16:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=982s) all right got got my display in just
[16:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=984s) fine
[16:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=985s) that worked in the script this part did
[16:29](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=989s) not
[16:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=992s) ah oh you know what it is i bet you
[16:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=997s) um
[16:40](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1000s) let's go into the config file
[16:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1006s) is there not there's not even an awesome
[16:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1007s) config all right so i know what happened
[16:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1009s) git clone um i
[16:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1012s) forgot to clone my configuration uh into
[16:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1016s) here so github.com
[16:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1018s) and we're gonna just clone this directly
[17:01](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1021s) into here and we should have pretty much
[17:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1023s) an identical setup
[17:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1024s) to what i'm running on most of my
[17:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1025s) systems
[17:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1027s) so material
[17:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1031s) awesome and then we're gonna run that to
[17:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1035s) dot config awesome
[17:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1041s) and then we should be able to reload
[17:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1042s) this and we should have pretty much my
[17:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1044s) desktop that you see on all my videos
[17:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1046s) all right let's let's try reload awesome
[17:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1048s) here we're gonna just go awesome
[17:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1051s) restart oh there we go
[17:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1057s) so we've got that
[17:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1061s) my little synology drive so now we have
[17:43](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1063s) the desktop
[17:44](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1064s) so this isn't using and if i do
[17:47](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1067s) [Music]
[17:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1069s) terminator i would use my hotkeys but
[17:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1071s) it's just going to pull up my stuff
[17:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1073s) so i can actually pull this in and if i
[17:56](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1076s) did it right
[17:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1077s) we should be able to do some translucent
[17:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1079s) effects and some other stuff
[18:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1080s) i did switch the project out um i don't
[18:03](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1083s) know if a car is in chat
[18:04](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1084s) but when it comes to material awesome i
[18:06](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1086s) did do pycom
[18:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1088s) instead of compton compton's been
[18:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1090s) deprecated for a while now
[18:12](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1092s) um so we can actually um
[18:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1095s) ditch that so we're gonna actually just
[18:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1097s) change this around
[18:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1099s) you know i'll probably use monospace for
[18:21](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1101s) now i do have a video coming out next
[18:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1103s) week
[18:24](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1104s) going over terminal customization so
[18:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1107s) to make terminal look decent but we're
[18:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1110s) going to just
[18:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1110s) clean this up just a hair
[18:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1115s) mono space yeah let's go
[18:38](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1118s) regular and blow that up a little
[18:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1122s) bit background let's add a little
[18:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1126s) transparency shall we 69
[18:49](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1129s) transparency and we'll use some themes
[18:53](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1133s) go green on black i like that
[18:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1137s) and we should be solid here so let's
[19:00](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1140s) close that out
[19:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1142s) pull that back up and
[19:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1145s) yeah that didn't work yeah still don't
[19:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1147s) have that transparency i'll work on the
[19:08](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1148s) transparency uh for now let's do a neo
[19:11](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1151s) fetch
[19:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1153s) not bad i mean it's not bad so memory
[19:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1156s) usage
[19:17](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1157s) uh this whole operating system's rocking
[19:20](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1160s) about 200 megs of memory
[19:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1162s) um it's using about 800 packages which
[19:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1165s) is not bad
[19:26](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1166s) not like oh my god look at me i've done
[19:28](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1168s) it in minimal packages because we
[19:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1170s) installed a crap ton of stuff here
[19:32](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1172s) we have full functionality
[19:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1175s) pretty darn awesome
[19:39](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1179s) uh but that's awesome window manager in
[19:41](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1181s) a nutshell
[19:42](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1182s) um that's what i use on my main box the
[19:45](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1185s) whole idea of this script is to kind of
[19:46](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1186s) build this out in arch
[19:48](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1188s) like i said we're going to revisit this
[19:50](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1190s) once i get this arch script kind of
[19:51](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1191s) cleaned up a bit
[19:52](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1192s) then we're gonna go over to debian um
[19:55](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1195s) because pretty much every distro falls
[19:57](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1197s) into most of these things and also like
[19:58](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1198s) fedora
[19:59](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1199s) uh we'll do a fedora one too um
[20:02](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1202s) but you know just to show that when it
[20:05](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1205s) comes to distributions it's really
[20:07](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1207s) about the syntax and just getting things
[20:09](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1209s) cleaned up
[20:10](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1210s) um this went pretty smooth for the most
[20:13](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1213s) part for it being the very first
[20:15](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1215s) run-through of a
[20:16](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1216s) fresh script i thought it was pretty
[20:18](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1218s) good um
[20:19](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1219s) obviously the start and post definitely
[20:22](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1222s) need some work
[20:23](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1223s) uh i'm terrible at bash scripting but
[20:25](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1225s) i'm getting better
[20:27](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1227s) um and yeah that's that's about it when
[20:30](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1230s) it comes to
[20:31](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1231s) building your own um we'll do this one
[20:33](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1233s) was archbase again next month
[20:35](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1235s) let's do let's plan on doing a debian
[20:37](https://www.youtube.com/watch?v=pZcbHdBs_TE&t=1237s) based run through of this and build out