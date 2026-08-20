---
title: "Unpacking the Linux WiFi Stack: Writing and Integrating Wireless Drivers - Alexis Lothoré, Bootlin"
url: "https://www.youtube.com/watch?v=kvyLE4esjPE"
videoId: "kvyLE4esjPE"
channel: "The Linux Foundation"
channelId: "UCfX55Sx5hEFjoC3cNs6mCUQ"
duration: "2886"
views: "1114"
isLive: false
isPrivate: false
---

#the-linux-foundation

![Unpacking the Linux WiFi Stack: Writing and Integrating Wireless Drivers - Alexis Lothoré, Bootlin](https://www.youtube.com/watch?v=kvyLE4esjPE)

[0:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=0s) Hi everyone. Uh thanks for attending uh
[0:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2s) my very first ELC talk by the way. Um
[0:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=5s) I'm here today to talk to you about uh
[0:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=8s) wireless technology and especially about
[0:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=10s) the implementation in the Linux canel.
[0:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=13s) Uh so I am Alex. I work at a company
[0:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=16s) named Bootling. Uh we are specialized in
[0:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=19s) embedded Linux development and many
[0:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=21s) software around the Linux canel. And
[0:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=24s) recently I've been able to work on some
[0:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=26s) wireless device uh related topics
[0:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=28s) especially around this small chip here
[0:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=30s) will wil 1000 and a bit about uh around
[0:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=34s) Wilk 3000 which has been upstream quite
[0:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=36s) recently from Marak who is who may be
[0:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=39s) around and um this talk is about giving
[0:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=42s) you some very introductory elements
[0:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=44s) about how to start um writing some
[0:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=47s) device drivers maybe tuning some
[0:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=49s) existing device drivers and where to
[0:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=51s) look at uh in the Linux kernel.
[0:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=54s) Um for this talk I plan to give you uh
[0:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=57s) this talk in five parts. I will try to
[1:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=60s) uh keep it as small as possible but I
[1:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=62s) need to talk a bit about the standards
[1:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=63s) obviously. Then we will see how it is
[1:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=66s) implemented in the link scanel. What is
[1:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=68s) the official wireless stack and how our
[1:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=71s) wireless drivers uh integrate in there
[1:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=74s) and uh we will actually uh see some code
[1:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=77s) uh see what is the basic skeleton to
[1:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=79s) write a device driver supporting a
[1:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=81s) wireless device. Finally we will see uh
[1:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=85s) of course some tools to start playing
[1:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=86s) with our new driver and possibly how to
[1:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=89s) start debugging and analyzing um any
[1:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=92s) possible failure uh with our driver.
[1:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=95s) So talking about the standard and the
[1:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=97s) specifications. So Wi-Fi by itself is
[1:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=100s) only a marketing name. There is a well-
[1:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=102s) definfined standard behind it and this
[1:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=104s) is it 802.11.
[1:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=107s) Um this is not a fixed standard. It
[1:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=110s) keeps being improved year after year.
[1:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=112s) That's why we have different versions
[1:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=114s) with different features with the general
[1:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=116s) goal of improving the performance of our
[1:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=119s) wireless devices like the range uh
[2:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=122s) robustness and things like that. We are
[2:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=125s) currently on Wi-Fi 7 which has been
[2:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=127s) released last year if I'm not wrong and
[2:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=130s) there are new standards uh under
[2:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=131s) progress uh which should be released
[2:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=133s) later.
[2:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=136s) About this standard it occupies the two
[2:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=138s) lowest layer in our network stack. um it
[2:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=142s) defines at the physical layer how we can
[2:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=144s) use radio to emit Wi-Fi frames. So what
[2:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=147s) is the way to access the medium? What
[2:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=150s) are the bands I am supposed to use with
[2:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=152s) my device? What is the modulation and
[2:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=154s) things like that? And on the MAC layer,
[2:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=157s) it defines the type of messages
[2:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=159s) exchanged by my wireless devices. In
[2:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=162s) this case, we have three types of
[2:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=164s) messages that could be exchanged between
[2:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=166s) devices and all the procedures,
[2:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=168s) algorithms um to uh exchange messages on
[2:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=171s) the network to join the network and
[2:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=173s) maintain uh the network.
[2:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=177s) About those networks, um there are
[2:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=179s) different networks specified by 802.11.
[3:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=182s) For this talk, I will focus on the very
[3:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=184s) basic uh setup uh meaning the one we
[3:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=186s) have at home. For example, when you have
[3:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=188s) a router delivered by your ISP, you are
[3:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=191s) using an infrastructure network. In this
[3:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=193s) case, you have multiple um station
[3:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=196s) devices joining a network through a
[3:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=198s) specific station which is called an
[3:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=200s) access point. This is not the only way
[3:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=202s) of building a Wi-Fi network. You can get
[3:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=205s) rid of some access points depending on
[3:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=207s) the network you are using like a do mesh
[3:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=210s) peer-to-peer and so on. But for this
[3:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=212s) talk we will focus on the very basic one
[3:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=214s) the one we are used to use dayto-day.
[3:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=218s) About the hardware it comes in a wide
[3:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=220s) variety of shapes and features um
[3:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=223s) features because um um any piece of
[3:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=225s) hardware does not support the same
[3:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=227s) revision of the 802.11 uh standard.
[3:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=230s) That's why when you buy some wireless
[3:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=232s) device it may support 802.11b
[3:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=237s) AC AX and so on. But also because the
[4:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=240s) interfaces to work with this device may
[4:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=242s) be different. We can get uh PCI cards,
[4:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=245s) USB dongles, bare modules to solder on
[4:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=248s) our products and so on. One thing that
[4:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=251s) does not generally change between all
[4:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=253s) those devices is that they depend on
[4:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=255s) some firmware to run. But more on that
[4:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=257s) later
[4:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=260s) about the implementation and the overall
[4:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=263s) organization in the canel. uh we have to
[4:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=265s) distinguish between two categories of
[4:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=267s) devices when we talk about those
[4:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=269s) wireless devices. Uh we have the full
[4:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=272s) Mac and the soft Mac devices. The full
[4:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=274s) Mac devices are in charge of handling
[4:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=276s) both the physical and Mac layer. There
[4:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=279s) are pros and cons to this. Uh the pros
[4:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=281s) is that it is a kind of offload in the
[4:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=283s) end. So we may expect some better
[4:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=285s) performance from full Mac devices.
[4:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=287s) However, of course, they may be more
[4:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=289s) complex both at hardware level and
[4:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=292s) possibly at firmware level. And also if
[4:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=294s) we have an issue like a firmware bug for
[4:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=297s) example, it may be harder to patch it
[4:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=298s) because we may depend on a closed source
[5:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=300s) firmware provided by a vendor and so we
[5:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=303s) have to wait for this vendor to release
[5:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=304s) the fix. On the other side we have soft
[5:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=308s) Mac devices in this case our device and
[5:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=310s) so the driver only the physical layer.
[5:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=313s) The Mac layer is running in software in
[5:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=315s) our kernel. This is a generate layer
[5:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=317s) implemented in the kernel. In this case,
[5:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=320s) we expect on the contrary a simpler
[5:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=322s) hardware and possibly cheaper in this
[5:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=323s) case. Uh and if we have an issue in the
[5:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=326s) MAC layer, we can fix it. It's canel. We
[5:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=329s) fix the bug. We deploy our canel again
[5:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=332s) and all the soft disase benefit from
[5:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=334s) this fix. However, there is one
[5:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=337s) consequence and it is um especially
[5:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=339s) important for low power platforms. Our
[5:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=342s) Mac layer is running in software. So, it
[5:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=344s) um our overall wireless performance may
[5:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=347s) start to be CPU bound. This is
[5:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=349s) especially uh important to know if you
[5:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=351s) are running an access point with plenty
[5:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=353s) of stations connected. Your Mac layer is
[5:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=355s) running in software and depends on your
[5:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=357s) CPU.
[5:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=359s) In the end, uh we have plenty of soft
[6:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=361s) Mac and full Mac drivers supported in
[6:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=363s) the connect currently, but we have way
[6:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=365s) more soft Mac devices when possible.
[6:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=367s) This is generally the go-to path to
[6:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=369s) implement our device driver. If you want
[6:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=372s) to take a look at all those drivers,
[6:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=374s) they are in drivers net wireless uh in
[6:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=376s) the kernel tree.
[6:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=379s) Now that it is said, what about the
[6:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=381s) overall stack? Uh this is how the
[6:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=383s) wireless stack look in Linux. If we
[6:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=385s) start from the bottom, we have obviously
[6:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=387s) our hardware. On top of that, I have
[6:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=390s) either a soft Mac or a full Mac um
[6:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=393s) driver. If I have a soft Mac, on top of
[6:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=396s) this driver, I have the generic Mac 8011
[6:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=399s) layer running in the kernel. So this one
[6:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=402s) will be in charge of the MAC layer
[6:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=404s) crafting and passing frames for 802.11
[6:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=408s) um a bit of encryption decryption Q
[6:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=410s) management and everything specified by
[6:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=412s) the MAC layer for 802.11.
[6:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=415s) On top of this we have the CFG 802.11
[6:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=419s) layer. This is the main framework to
[7:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=420s) control our wireless devices to control
[7:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=423s) and configure. This is the interface uh
[7:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=426s) with which we want to interact from user
[7:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=428s) space. And to do so we have a thin layer
[7:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=430s) which is netling sockets and a specific
[7:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=433s) family of net sockets. The 80211 net
[7:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=436s) family. Through this socket our user
[7:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=440s) space applications can interact through
[7:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=442s) comments and receive events from the cfg
[7:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=445s) 80211 framework. On user space side, we
[7:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=448s) can have either our own application
[7:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=451s) written with Libanel for example or some
[7:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=453s) small generic open source demons you
[7:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=456s) likely have if you have a um Linux
[7:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=458s) desktop or laptop some WPA supply
[7:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=460s) training possibly some OS APD if you
[7:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=462s) have an access point and at a higher
[7:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=465s) level a network manager con man systemd
[7:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=467s) network D and so on.
[7:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=471s) Before taking a look at some actual
[7:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=474s) code, we have some more details to learn
[7:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=476s) about. Um, in the Linux model, we have
[7:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=478s) to distinguish between the wireless
[8:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=480s) device, which is the actual piece of
[8:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=482s) hardware, and the interfaces we see. For
[8:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=484s) example, when I type IP link on my
[8:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=486s) workstation, the wireless device is
[8:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=489s) represented by this uh Wi-Fi here,
[8:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=492s) wireless fi. This is the representation
[8:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=494s) of my hardware. If I have two pieces of
[8:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=497s) network chip, I will have two wireless
[8:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=499s) file. On top of one wireless fi, I may
[8:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=502s) have zero, one or multiple virtual
[8:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=505s) interfaces. And those virtual interfaces
[8:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=507s) are what I see when I type IP link. This
[8:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=511s) zero, do one and so on. So we will have
[8:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=515s) to deal with those different entities.
[8:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=517s) By default, as a user, I expect to have
[8:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=519s) at least one virtual interface created
[8:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=522s) whenever my hardware is initialized. And
[8:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=524s) as a user, I can add other interfaces on
[8:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=527s) top of this same hardware.
[8:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=532s) Now let's take a look at some actual
[8:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=534s) implementation detail. Uh we will see
[8:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=536s) how to write some small driver skeleton.
[8:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=539s) The first thing we have to know is about
[9:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=541s) the main data structure that we are
[9:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=543s) going to manipulate. This is the strict
[9:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=545s) wireless file. As the name suggests,
[9:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=548s) this is really about describing the
[9:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=549s) hardware and more specifically about my
[9:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=551s) hardware capabilities. So whenever I
[9:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=554s) will write a driver either directly or
[9:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=557s) indirectly depending on soft Mac or full
[9:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=559s) Mac driver I will have to allocate the
[9:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=561s) structure and to fill all the details
[9:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=563s) about what my hardware is able to do. Is
[9:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=566s) it able to act as a station an access
[9:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=568s) point to peer and so on. Is it able to
[9:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=571s) have concurrent combinations of those
[9:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=573s) interfaces? What bands it is expected to
[9:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=576s) operate on etc. On the right for example
[9:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=579s) we have an enum that I will have to use
[9:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=581s) to say okay my device is able to act
[9:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=583s) only as a station.
[9:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=588s) So to get actually this uh structure we
[9:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=591s) will start with softmark driver. So the
[9:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=593s) most common solution that you will see
[9:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=595s) in the canal you won't allocate directly
[9:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=597s) this wireless file. it will be wrapped
[9:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=599s) in some other structures and so you will
[10:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=601s) rather start by manipulating an IE E
[10:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=605s) 80211 structure IE 80211 hardware
[10:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=609s) structure to get this one you call this
[10:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=611s) allocate hardware API it will consume a
[10:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=614s) set of operations your driver operations
[10:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=616s) more on that later and it will give to
[10:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=619s) you uh in your driver this hardware
[10:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=621s) structure it is then up to you to
[10:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=623s) complete the structure with your
[10:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=624s) hardware capabilities in there inside
[10:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=627s) the structure you will have your
[10:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=628s) wireless file. So that's where you will
[10:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=630s) set your low-level capabilities and some
[10:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=634s) additional flags. For example, to let
[10:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=636s) the MAC 8211 layer know about what it
[10:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=639s) should handle or not depending on your
[10:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=641s) off-road capabilities.
[10:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=643s) Once you have filled your structure, you
[10:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=645s) are ready to register it in the canel
[10:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=647s) and from there you pass back this
[10:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=649s) hardware structure to the MAC 80211
[10:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=651s) layer. It will register the wireless
[10:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=653s) device and perform automatically some
[10:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=655s) steps for you like registering your
[10:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=657s) first virtual interface. That's why when
[11:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=660s) you insert your driver, you may have
[11:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=662s) automatically your WLAN zero interface
[11:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=665s) created.
[11:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=668s) All right, I have mentioned that I have
[11:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=669s) some ops to define. Um you can take a
[11:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=672s) look at the relevant structure um 80211
[11:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=674s) ops in the Mac 80211.h.
[11:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=678s) You will see that there is a ton of
[11:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=680s) operations. You don't need to implement
[11:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=682s) all of those. It really depends on the
[11:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=684s) features you want to implement and on
[11:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=686s) the capabilities of your hardware. But
[11:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=688s) you have a very minimal set of
[11:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=690s) operations to implement otherwise you
[11:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=692s) won't even be able to register your
[11:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=694s) hardware in the canel. And those are
[11:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=697s) those eight I guess operations.
[11:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=701s) So you can start uh your wireless driver
[11:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=703s) by implementing those eights. First you
[11:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=706s) have this add interface remove interface
[11:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=708s) pair. The Mac 80211 core will call those
[11:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=711s) operations in your driver to let you
[11:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=713s) know that the user or the canel wants to
[11:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=716s) add a new wireless interface on top of
[11:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=718s) your hardware. It is up to you here to
[12:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=721s) register this virtual interface in a
[12:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=722s) list possibly notify the device firmware
[12:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=725s) about this virtual interface if it needs
[12:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=727s) it. But this one is really about the
[12:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=730s) virtual interfaces being added or
[12:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=732s) removed. On top of that, you also need
[12:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=734s) at some point to know when to initialize
[12:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=737s) your hardware. And to get this
[12:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=739s) information, we have some start stop
[12:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=741s) methods to implement. Those start stop
[12:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=744s) methods will be called right before
[12:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=746s) enabling the first virtual interface and
[12:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=749s) right after disabling the last virtual
[12:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=751s) interface. So that's a small window in
[12:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=753s) which you are supposed to make your chip
[12:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=755s) ready to operate, start listening to
[12:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=758s) some frames that could be um targeted to
[12:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=760s) your device and so on.
[12:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=762s) And so we have this timeline in here
[12:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=765s) with all the add and remove interface
[12:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=767s) and the start stop letting us uh
[12:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=769s) initialize deinitialize our hardware. So
[12:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=771s) here we really distinguish between
[12:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=773s) adding adding sorry an interface and
[12:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=776s) enabling it.
[12:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=779s) All right. So that's half of those
[13:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=782s) operation. Of course the whole goal of
[13:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=784s) this thing is to transmit frame. So I
[13:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=786s) have a t operation to implement here.
[13:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=789s) This is a very basic version. The core
[13:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=792s) whenever it needs to send a frame on
[13:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=793s) behalf of user space will call this
[13:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=796s) operation and provide an escape buff the
[13:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=798s) standard representation of a packet in
[13:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=800s) the kernel. This is a push model. Uh so
[13:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=804s) really the core is pushing a frame to my
[13:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=805s) driver. It is up to me to send the frame
[13:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=808s) through the device and then notify
[13:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=811s) asynchronously my upper layers about
[13:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=813s) either the success or failure uh about
[13:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=815s) this packet sending. In fact, this is
[13:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=818s) not the only TX related operation I have
[13:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=821s) to implement. I also have this wake TXQ.
[13:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=824s) In this case, this is rather a pool
[13:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=826s) model. I still have to implement it. Uh
[13:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=828s) and in this case the kernel and so the
[13:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=831s) MAC 80211 core is queuing some packets
[13:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=834s) in a queue and is letting me know each
[13:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=836s) time it cues a packet and then it is up
[13:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=839s) to me in the corresponding ops to either
[14:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=841s) start dqing or manipulating the
[14:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=843s) different MAC 80211 cues to get the next
[14:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=846s) packets um through some specific APIs
[14:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=849s) like txdq uh next tq and so on. I do not
[14:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=853s) need to provide my own uh implementation
[14:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=856s) of this callback. Uh if I want a very
[14:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=858s) generic way of sending packets and dqing
[14:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=861s) packets, I can directly use some default
[14:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=863s) implementation in place of this wake tq.
[14:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=866s) And in this case, I will have this whole
[14:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=868s) timeline of packet sending on the on the
[14:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=872s) right.
[14:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=874s) Um yeah. And so basically default
[14:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=876s) implementation will automatically call
[14:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=878s) my TX operation.
[14:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=882s) Okay, so that's it for sending. I'm
[14:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=884s) still missing two operations. I have a
[14:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=886s) small config um operation to implement.
[14:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=889s) This one will be called by the core each
[14:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=891s) time it needs to reconfigure some
[14:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=893s) low-level hardware detail on my device
[14:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=896s) should it enable power save should it
[14:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=898s) change some um channel oper um operating
[15:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=902s) channels and things like that. So it
[15:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=905s) will provide a bit field a bit mask
[15:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=907s) sorry of the elements to change and to
[15:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=910s) update on my device and I have this
[15:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=913s) configure filter operation to implement.
[15:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=916s) By default I will move many receipt
[15:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=919s) frame to the upper layers but the canel
[15:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=921s) may not be interested in all the frames.
[15:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=923s) So to save some processing time it um it
[15:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=926s) is able to set a filter to letting me
[15:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=928s) know about the frame I should drop
[15:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=930s) rather than pushing to the canel.
[15:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=934s) Okay, so we have the basic operation. We
[15:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=937s) are still missing one important detail.
[15:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=938s) This is about receiving frames and
[15:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=940s) pushing toes to the canel. We have
[15:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=943s) different APIs provided by the MAC
[15:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=945s) 802.11 layer um all um in the same
[15:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=949s) family. So the basic one is the
[15:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=951s) underscore RX and very basically I will
[15:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=954s) take the received frame from my chip,
[15:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=956s) put it in an ASKB and push it through
[15:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=958s) this API. Of course, I will likely use
[16:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=961s) this kind of API in an interrupt chain
[16:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=964s) because this is how my device will
[16:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=966s) notify me about a new packet being
[16:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=968s) present and I have different version
[16:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=970s) depending on whether I want to handle
[16:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=972s) some queuing in my driver, if I want to
[16:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=975s) do it directly in the interrupt and so
[16:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=977s) on. In any case, there is one constraint
[16:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=980s) about this family of API. I need to have
[16:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=983s) an 802.11 header uh at the beginning of
[16:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=986s) this SKB. This is what uh those APIs
[16:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=989s) expect.
[16:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=992s) Okay. So we have all the very basic
[16:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=995s) building blocks of our driver. If I want
[16:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=997s) to write a short skeleton, I will create
[16:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1000s) this MAC 80211 structure containing all
[16:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1002s) my basic operation. Once again this is
[16:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1005s) very a tiny subset of all available
[16:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1007s) operations.
[16:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1009s) And then in my driver initialization uh
[16:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1012s) sequence. So in the prop function I will
[16:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1015s) start for example by preparing the
[16:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1016s) receive path with some interrupt uh
[16:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1019s) registering and workq um declaration. I
[17:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1022s) will allocate my hardware structure and
[17:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1025s) then I will set some fields in there. I
[17:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1027s) may have some MAC address to configure
[17:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1029s) to attach some strict device. I will
[17:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1032s) have some flags to set. So this is what
[17:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1034s) this hardware set APIs are doing here.
[17:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1037s) So setting some rate control
[17:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1038s) capabilities, some power safe
[17:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1040s) capabilities and I will do it both on my
[17:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1043s) hardware structure and the wireless fi I
[17:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1046s) have to configure my wireless fi. So for
[17:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1048s) this basic example I am saying I support
[17:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1050s) both station and access point modes and
[17:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1053s) once I'm done I will register my
[17:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1055s) hardware structure. From this point, the
[17:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1058s) kernel will know about my wireless
[17:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1060s) device. And since it is a soft Mac
[17:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1062s) driver, the MAC layer will automatically
[17:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1064s) register uh and expose to user space my
[17:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1067s) WLAN zero.
[17:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1071s) Just a short part about the RX path uh
[17:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1074s) assuming that our packets are received
[17:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1076s) through an interrupt. Um so my I don't
[17:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1079s) want to deal with um the packet
[18:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1081s) management directly into the interrupt.
[18:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1082s) I want to defer it in some bottom half.
[18:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1085s) So this is the second function and in
[18:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1087s) the second function I will retrieve my
[18:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1089s) packet prepare it into an SK buff. So
[18:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1092s) this struct
[18:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1094s) and push it to the U canel. So that's
[18:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1097s) how I will um transmit the received
[18:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1099s) packets to the user space and or the
[18:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1102s) kernel the MAC 802 layer uh if it needs
[18:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1104s) it.
[18:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1107s) So that's pretty much it for the soft
[18:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1109s) Mac drivers um which covers a large part
[18:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1112s) of available devices um on the market.
[18:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1115s) But sometimes I have some full Mac
[18:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1117s) drivers. That's in fact the case for the
[18:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1120s) drivers I've been working on the wil
[18:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1121s) 1000. And in this case I don't have Mac
[18:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1124s) 80211 anymore. So I have plenty of
[18:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1127s) things to handle on my own that were
[18:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1128s) done by this layer before. So for
[18:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1131s) example instead of declaring some
[18:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1132s) itropoly 80211 ops uh structure I will
[18:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1136s) declare directly some cfg 80211 ops uh
[19:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1140s) structure I won't have my wrapped
[19:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1143s) hardware structure I will directly
[19:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1145s) manipulate and allocate this time my
[19:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1148s) wifi structure
[19:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1151s) on top of that I will have some new
[19:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1153s) structures that I will have to
[19:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1155s) manipulate that was not really mentioned
[19:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1156s) in the previous slide but whenever the
[19:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1158s) mac 80211 layer calls my driver it
[19:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1161s) provides some specific VF structure I
[19:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1164s) won't have this anymore here I will have
[19:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1166s) a lower level structure which is a
[19:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1168s) wireless dev this represent my wireless
[19:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1171s) de um w um virtual interfaces at the
[19:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1175s) lower level
[19:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1177s) another important thing I don't have mac
[19:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1179s) 80211 anymore so I have to register on
[19:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1182s) my own the struct device so not only I
[19:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1185s) need to perform the wireless fi
[19:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1187s) registration I also need to register a
[19:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1189s) strict net device. If I want to have a
[19:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1192s) default interface and each time a user
[19:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1194s) wants to add a new virtual interface, I
[19:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1197s) will add to do it uh as well. This is
[20:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1200s) important because I need to um to
[20:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1204s) enforce the link between those net
[20:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1206s) devices and the wireless um the virtual
[20:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1208s) interfaces and that's done through a
[20:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1211s) specific pointer inside my net device
[20:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1213s) structure. So I will have to remember to
[20:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1215s) properly set this pointer in my driver
[20:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1217s) each time I want to add a virtual
[20:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1219s) interface.
[20:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1222s) Once again I have some operations to
[20:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1224s) implement. There is once again a lot of
[20:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1226s) them many more than uh in the MAC layer
[20:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1230s) and once again I don't need to implement
[20:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1232s) all of those. Some are very specific to
[20:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1234s) some um non-standard features. I don't
[20:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1237s) have a strict set of mandatory features
[20:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1240s) to implement. um the CFG layer won't um
[20:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1243s) refuse registration because you are
[20:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1245s) missing an operation. You will just have
[20:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1247s) some uh specific canal errors at runtime
[20:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1250s) when the user will try to call some
[20:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1252s) specific ops. But as a starter, if you
[20:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1255s) want to implement a very basic station,
[20:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1258s) you can for example count on those
[21:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1260s) operation and implement those. Some will
[21:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1262s) be similar to what we have seen for the
[21:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1264s) Mac layer. So I will have I will have
[21:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1267s) some add virtual interface delete
[21:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1269s) virtual interface. That's where I will
[21:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1271s) have to register the virtual interface
[21:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1273s) but also register the net device that
[21:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1275s) matches it. I will have to implement
[21:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1279s) some scan features. Uh so that's how the
[21:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1281s) user space will ask to learn about all
[21:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1283s) the surrounding uh access points. some
[21:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1286s) connect disconnect procedure uh with
[21:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1289s) which I will receive information about
[21:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1291s) what access point I want to connect and
[21:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1293s) some more specific key management
[21:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1295s) operations that will be used for example
[21:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1297s) by a user space supplicant more on that
[21:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1300s) uh in a few slides
[21:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1304s) as discussed earlier I need to register
[21:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1306s) a net device and so I need to declare
[21:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1309s) some net device operations once again I
[21:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1311s) don't need a very wide set it really
[21:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1313s) depends on the features I want to
[21:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1314s) implement But here you need at least the
[21:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1317s) features that were implemented by the
[21:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1319s) MAC 802 layer when you implemented the
[22:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1321s) MAC 80211 ops. So for example, I need
[22:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1324s) some open and stop um ops that will be
[22:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1328s) called whenever I bring up or down my
[22:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1332s) virtual interface and I need an
[22:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1335s) operation to be able to send packets. So
[22:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1337s) I need the NDO start
[22:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1340s) on the receive side. I don't have some
[22:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1342s) CFG 80211 specific APIs. I will directly
[22:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1346s) use the standard uh netstack uh receive
[22:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1348s) APIs. So some native RX or npro receive
[22:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1353s) if I um if my driver implements a nappy
[22:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1355s) loop. Of course I have some exceptions
[22:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1359s) uh whenever the upper layers subscribe
[22:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1360s) to some specific uh 802.11 frames. I
[22:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1364s) will have some specific callbacks to um
[22:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1367s) push those frames up uh to the upper
[22:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1369s) layers.
[22:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1371s) So I don't have any skeleton for full
[22:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1375s) max full Mac drivers. Here's a main
[22:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1377s) point is about remembering about this
[23:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1380s) struct device and specifically to
[23:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1382s) remember about this small pointer to
[23:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1384s) link to a wireless dev uh inside the
[23:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1387s) structure. However, we can discuss a bit
[23:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1390s) a few additional outstanding features
[23:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1392s) let's say that we should consider when
[23:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1394s) writing wireless drivers. First of all,
[23:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1397s) I have mentioned in the first slides
[23:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1399s) that we need a firmware running on our
[23:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1401s) device to operate it. This firmware is
[23:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1404s) of course not part of your driver. It is
[23:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1406s) out of your driver. It is out of the
[23:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1408s) kernel. In fact, most of them are hosted
[23:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1411s) in a standard Linux repository on
[23:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1413s) canel.org, the Linux firmware
[23:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1415s) repository. So, it is up to you as a
[23:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1417s) user, as a developer to make sure that
[23:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1419s) the firmware you need is present in this
[23:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1421s) repository and as a user to install it
[23:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1424s) uh on your system. You generally expect
[23:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1427s) those firmwares to be present in lib
[23:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1429s) firmware and your driver will request
[23:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1432s) when relevant this firmware with the
[23:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1434s) request firmware API. So it is up to
[23:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1437s) your driver to find this firmware,
[23:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1439s) request it and load it into your device
[24:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1441s) at the relevant point of time during
[24:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1444s) your device lifetime. Um I will get back
[24:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1446s) to it later but you are not supposed to
[24:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1448s) load this firmware right at prop time
[24:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1449s) but maybe in the start operation in your
[24:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1452s) driver.
[24:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1455s) A word about uh key management and
[24:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1457s) connection in general. By default, your
[24:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1460s) driver and the wireless subsystem in the
[24:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1462s) canal overall is not really able to
[24:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1464s) handle by itself the standard ways of
[24:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1467s) connecting to an access point today. It
[24:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1469s) supports some open networks and some web
[24:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1472s) network but please don't use it at home.
[24:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1474s) You rather should use some WPA2, WPA3,
[24:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1478s) some more modern standards to connect uh
[24:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1481s) to your access point. This is not
[24:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1484s) supported by the kernel. This is
[24:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1485s) deferred to user space. So you need a
[24:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1487s) supplicant on user space side to handle
[24:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1489s) all those operations. There are some um
[24:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1491s) hand checks to perform which will
[24:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1493s) negotiate some cryptographic keys to uh
[24:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1496s) encrypt the traffic and so on. And so
[24:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1498s) this is deferred to user space. So there
[25:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1500s) will be multiple uh net link 80211
[25:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1503s) messages exchanged between this user
[25:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1505s) space components and the kernel and or
[25:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1507s) your driver.
[25:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1509s) This is important to know because at
[25:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1510s) driver level depending on your device
[25:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1512s) depending on its firmware it may be able
[25:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1515s) to handle more than the basic operation.
[25:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1518s) So for example being able to handle the
[25:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1521s) whole WPA on check in this case you want
[25:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1524s) to let the um wireless car know about
[25:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1527s) this because you don't want it to make
[25:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1529s) the all the back and forth with the user
[25:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1532s) space. So how is it done? You have your
[25:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1535s) wireless file structure at
[25:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1537s) initialization and registration. You
[25:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1538s) will make sure to set the relevant flag
[25:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1540s) to let the core know about this
[25:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1542s) capability. And then once the user space
[25:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1545s) initiates the connection, it will it
[25:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1547s) will just wait for your device to um to
[25:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1549s) finalize and return the status of this
[25:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1552s) connection.
[25:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1555s) A word about regulatory. uh whenever you
[25:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1558s) are using a radio device and I think
[26:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1560s) that's not only true for Wi-Fi you are
[26:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1563s) expected to uh respect uh some
[26:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1565s) regulations you cannot use any transmit
[26:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1567s) power you cannot use any uh w radio
[26:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1570s) bands as you want so you have some
[26:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1572s) regulations to respect and the overall
[26:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1575s) goal of this framework is to make sure
[26:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1577s) that the user cannot accidentally fail
[26:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1580s) to comply with those rules so we have
[26:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1582s) some mechanisms to try to enforce
[26:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1584s) correctly the relevant rules
[26:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1587s) All those rules are not registered in
[26:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1589s) the kernel. Once again, this is hosted
[26:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1591s) in a dedicated repository in the
[26:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1594s) wireless DB and we have this half
[26:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1596s) textual format uh listing all the known
[26:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1599s) rules for any supported country. Here we
[26:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1602s) have an example of the regulation in
[26:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1603s) Netherland. I can use my wireless device
[26:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1606s) in the 2.4 GHz with a transmit power up
[26:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1610s) to 100 m.
[26:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1613s) So this is some textual data. it can be
[26:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1615s) turned into a binary. So I will have to
[26:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1618s) install this regulatory DB file into my
[27:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1621s) uh system and at any time when the cfg
[27:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1625s) 80211 core um initialize itself it will
[27:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1629s) search for this file to load all the
[27:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1631s) known rules it won't be able to apply
[27:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1634s) yet a specific set of rules because it
[27:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1636s) does not know yet what regulatory it
[27:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1638s) should apply. So it will apply this
[27:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1640s) world regulatory which is a generic one
[27:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1643s) while we don't know yet how to apply the
[27:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1645s) regulatory from user space I can enforce
[27:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1648s) a specific regulatory so here we are
[27:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1650s) using IW to set the Netherland
[27:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1652s) regulatory from there my core will be
[27:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1655s) able to configure any wireless file
[27:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1658s) registered in the canel with those rules
[27:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1660s) thanks for example to the config ops we
[27:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1663s) have implemented earlier
[27:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1666s) once again at driver level you may be
[27:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1668s) interested in the customization that you
[27:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1670s) can do at regulatory level. First of
[27:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1674s) all, you don't have to do anything for
[27:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1676s) this. As I have mentioned, the config
[27:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1678s) call back could be called to set your
[28:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1680s) takes power automatically by the core.
[28:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1682s) But in some cases, you want to know when
[28:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1684s) the regulatory is changed. So you can
[28:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1686s) subscribe with this reg notifier call
[28:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1688s) back in your wireless file to know when
[28:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1691s) the regulatory change and so you could
[28:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1693s) perform some checks for example compared
[28:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1695s) to some data you may have in a
[28:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1697s) nonvolatile memory.
[28:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1699s) You can go further. Maybe your driver
[28:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1701s) somehow is able to guess about what
[28:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1703s) should be the current regulatory because
[28:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1705s) it has received the info from the
[28:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1707s) firmware which is processing beacons
[28:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1709s) from access point for example because
[28:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1711s) you have an uh EROM containing the
[28:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1714s) information from some previous
[28:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1715s) connections or things like that. So you
[28:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1717s) have this regulatory hint which allows
[28:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1719s) you to notify the core about what you
[28:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1722s) think should be the current regulatory
[28:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1724s) and more than that you can also tune
[28:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1727s) whether the core should be enforcing
[28:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1729s) some regulatory to you um because you
[28:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1732s) cannot comply with any regulatory or
[28:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1735s) because you know that you have stricter
[28:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1737s) regulatory and you don't want those to
[28:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1739s) be overridden by the core wireless
[29:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1742s) system. So you have once again some
[29:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1744s) flags that you can set at registration
[29:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1746s) time. But in any case once again the
[29:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1749s) overall goal of this is to make sure not
[29:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1752s) um to make sure for the user not to fail
[29:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1754s) to comply without knowing it uh with
[29:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1757s) those regulation. So if you start tuning
[29:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1759s) those regulatory make sure that you
[29:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1761s) cannot make the user emit with too much
[29:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1763s) power for example.
[29:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1767s) Okay. Uh one last consideration uh if we
[29:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1770s) discuss a bit of power save the 802.11
[29:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1772s) standard specifies um that our station
[29:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1775s) devices can enter some inactivity period
[29:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1778s) let's say sleep period in which it um
[29:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1781s) during which it will not be able to
[29:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1783s) receive and transmit frames. There is a
[29:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1786s) well- definfined mechanism for this
[29:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1788s) during which the station will notify the
[29:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1791s) access point about this period sending a
[29:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1794s) new frame and from there the access
[29:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1797s) point will remember about the sleeping
[29:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1799s) station and if there is a frame targeted
[30:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1802s) to my sleeping station it will be
[30:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1804s) buffered on the access point side and
[30:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1806s) the access point will start emitting
[30:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1808s) well will modify the beacon it
[30:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1810s) periodically emits to notify about those
[30:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1813s) spending frames from time to time My
[30:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1816s) station will wake up, listen for the
[30:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1818s) beacons and check if there is a pending
[30:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1820s) message for it. If so, there is another
[30:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1822s) well- definfined message to ask the
[30:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1824s) access point to uh DQ those pending
[30:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1827s) messages and to send it. So, this is
[30:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1829s) this uh PS4 frame. Once again, this is a
[30:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1833s) feature which is pretty well supported
[30:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1835s) with the softml layer. I still have to
[30:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1837s) let uh the core know about the fact that
[30:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1840s) I can support this feature. So we have
[30:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1842s) yet another flag to set this time in the
[30:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1844s) hardware structure and uh after that um
[30:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1848s) so the core will consider that I am
[30:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1850s) compatible with this slip mode. I will
[30:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1852s) either still have to deal with some
[30:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1855s) specific frames like sending the new
[30:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1857s) frames whenever I enter or leave those
[31:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1860s) sleep periods or I can defer some of
[31:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1863s) this work to the MAC 80211 layer.
[31:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1867s) So once again plenty of flags to set at
[31:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1870s) registration time for the full Mac
[31:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1873s) driver. Uh you we consider that you will
[31:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1876s) handle it on your own. So the only thing
[31:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1877s) you can implement here is the set power
[31:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1880s) MGMT ops. Uh which will be called
[31:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1882s) whenever the user enable or disable
[31:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1885s) power save and then it's up to you to
[31:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1887s) handle everything.
[31:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1889s) Talking about the user about this power
[31:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1891s) save he's able to toggle it with IW
[31:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1894s) again. So here we enable power save on a
[31:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1897s) specific virtual interface and it is
[31:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1900s) worth mentioning especially for early
[31:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1902s) development because even if you do not
[31:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1904s) call this command line power save may be
[31:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1906s) enabled on your station for multiple
[31:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1908s) reasons maybe because uh your device uh
[31:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1911s) your drivers has registered the device
[31:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1913s) telling don't worry you can enable power
[31:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1916s) save by default or because the kernel is
[31:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1918s) built uh with this specific a config and
[32:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1921s) it is pretty important to know about it
[32:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1923s) because it can be quite painful
[32:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1925s) to um start debugging an issue for
[32:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1927s) multiple hours and realize that in fact
[32:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1930s) your chip has been sleeping and possibly
[32:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1932s) having a bug in the firmware during the
[32:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1934s) sleep and things like that. So that's
[32:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1936s) likely something that you want to make
[32:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1937s) sure to disable uh during early
[32:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1939s) development and deal with it later u um
[32:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1942s) in a second step.
[32:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1946s) All right. Um just a few design tips. Um
[32:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1950s) those tips are not really related to
[32:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1952s) wireless drivers. They could be extended
[32:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1954s) to any device driver, but that's what I
[32:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1957s) feel uh is relevant for this topic.
[32:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1959s) First, your hardware does not to be um
[32:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1962s) initialized right at prop time. And
[32:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1965s) that's generally what the wireless
[32:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1966s) maintainers will ask you whenever you
[32:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1968s) submit a new driver. You should keep it
[32:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1971s) off as long as possible. And what is as
[32:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1973s) long as possible as long as the user
[32:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1975s) does not need it. That's why you have
[32:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1977s) all those start stops implemented.
[33:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1980s) That's a good place where to start
[33:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1982s) actually loading a firmware for example,
[33:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1984s) that's where you will start listening
[33:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1986s) for the interrupt about receive
[33:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1988s) messages. But as long as the interface
[33:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1990s) is done as long possibly as you don't
[33:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1992s) have any virtual interface on top of
[33:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1994s) your wireless file, you don't need to um
[33:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=1997s) make the firmware run and initialize all
[33:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2000s) the hardware.
[33:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2002s) about the registration order. Uh once
[33:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2004s) again make sure to have properly
[33:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2006s) initialized all the fields in your data
[33:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2008s) structure before passing to the canel
[33:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2011s) because as soon as you register your
[33:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2013s) hardware structure or your wireless file
[33:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2015s) the canel can immediately start calling
[33:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2018s) your um driver ops. With MAC 802.11
[33:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2022s) there's not much room for mistakes let's
[33:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2024s) say uh because there is only one set of
[33:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2027s) registration API but for full Mac you
[33:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2029s) have both the wireless file to register
[33:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2031s) and the net device. So you can start
[33:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2033s) making mistakes here if you are not
[33:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2034s) careful about proper initialization
[33:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2037s) before registration.
[34:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2040s) If uh you have a wireless chip there's a
[34:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2042s) high chance that you have different
[34:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2044s) revisions of the chip uh different buses
[34:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2046s) that you can use to address the chip. Of
[34:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2049s) course, you are expected not to
[34:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2051s) duplicate the code between those chips.
[34:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2053s) You should keep the matters separated.
[34:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2056s) For example, if you have a card um which
[34:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2059s) support I don't know SDIO and a UART to
[34:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2062s) work, you should likely create some SDIO
[34:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2065s) and UAT dedicated files and keep your
[34:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2067s) wireless operation in a single set of
[34:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2069s) file shared whoop sorry shared between
[34:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2073s) um those two buses. But once again, this
[34:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2075s) is not really a wireless specific.
[34:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2079s) And finally, um, don't be scared by the
[34:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2081s) amount of things to implement. If you
[34:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2083s) take a look at the MAC 8021 header and
[34:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2086s) CFG 80211 header, you will see lines and
[34:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2089s) lines of operations to implement. You
[34:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2092s) don't need to start implementing all of
[34:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2093s) those. I've given to you here the very
[34:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2096s) minimal sets you may start with and you
[34:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2099s) do not even need to implement those.
[35:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2100s) just by stubbing stubbing toes. Uh
[35:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2103s) generate some logs from toes, return
[35:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2105s) some standard uh success code just to
[35:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2107s) learn how they will be called by the
[35:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2109s) core depending on what user space action
[35:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2111s) is triggered.
[35:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2115s) All right. Um let's finish this uh tool
[35:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2118s) by checking how to actually test our new
[35:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2121s) fancy driver. About the user space
[35:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2124s) tools, the first one I could use to
[35:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2126s) start playing with my driver is IW. This
[35:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2128s) is a standard user space tool which
[35:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2130s) communicates through the net link socket
[35:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2132s) we have discussed earlier. This is a
[35:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2134s) command line interface coming with many
[35:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2135s) subcomands. So for example I can learn
[35:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2138s) about all the wireless file registered
[35:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2140s) in the kernel and I will have all the
[35:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2142s) list of capabilities. So for example I
[35:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2145s) can check whether I have forgotten about
[35:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2147s) a specific flag at reg at registration.
[35:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2151s) I can start manipulating my virtual
[35:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2153s) interfaces. So here I add a WLAN one
[35:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2156s) which will act as a station. That's what
[35:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2159s) the managed flag means here. And I can
[36:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2162s) possibly start playing with some
[36:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2164s) connection and scan. So here triggering
[36:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2166s) a scan connecting to a simple network.
[36:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2169s) Once again this is not WPA23 here. This
[36:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2172s) is web likely. I can take a look at the
[36:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2176s) connection status and I can also start
[36:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2179s) performing some very early debug. So for
[36:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2181s) example, I can ask EW to let me know
[36:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2184s) about any Netlink 802.11 events notified
[36:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2188s) by the kernel. So to see the um the
[36:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2191s) overall timeline of messages exchanged
[36:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2193s) between user space and canel.
[36:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2197s) If I want to go further, I will likely
[36:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2199s) want to check that I can connect to
[36:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2200s) standard networks and in this case I
[36:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2202s) will need a supplicant. I have different
[36:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2205s) solutions. Uh the standard one in the
[36:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2207s) Linux ecosystem is WPA supplicant. It
[36:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2210s) comes both as a demon and a command line
[36:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2214s) tool and in fact a library to write your
[36:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2216s) own tools and you have some other
[36:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2219s) alternatives. There is uh IWD which is a
[37:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2222s) more recent from Intel I guess and to
[37:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2225s) start using it is really
[37:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2227s) straightforward. I have a very minimal
[37:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2229s) configuration file to write only those
[37:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2231s) two lines and I think the second one is
[37:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2232s) not even needed. I will have to start my
[37:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2235s) WPA supplicant demon. I provide the
[37:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2238s) kernel interface. It should use the
[37:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2240s) network interface it should operate on
[37:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2242s) and the configuration and then I can
[37:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2245s) start using WPA CLI to initiate the scan
[37:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2249s) spot the network on which I want to
[37:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2250s) connect configure the credentials and
[37:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2253s) initiate the connection and with only
[37:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2255s) those operation I can start uh checking
[37:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2258s) that the written driver behaves
[37:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2259s) correctly and interact correctly with my
[37:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2261s) hardware.
[37:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2265s) Uh last word about debugging. Uh first
[37:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2268s) of all there is many debug logs in both
[37:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2271s) CFG 80211 core and Mac 802.11 core. So I
[37:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2275s) have to take care um about building
[37:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2278s) those at build time. So making sure that
[38:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2280s) that they are embedded in my can image
[38:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2282s) and module um files. But then at runtime
[38:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2285s) I can use the dynamic debug entries to
[38:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2287s) enable those messages. On top of that I
[38:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2290s) also have plenty of trace points. So if
[38:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2293s) you are familiar with F trace and tracmd
[38:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2296s) you can enable those trace points and
[38:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2298s) those are very useful because you will
[38:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2300s) see all those DRV something those are
[38:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2303s) the entry point and exit points where
[38:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2306s) the kernel is actually calling your
[38:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2308s) driver operations. So you can make sure
[38:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2310s) what exact execution path trigger your
[38:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2313s) driver.
[38:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2315s) Finally, at some point you may be
[38:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2317s) interested in packet contents and making
[38:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2319s) sure that your driver emits the correct
[38:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2321s) packets. Um, you can use another device
[38:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2324s) like your development laptop. Raise a
[38:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2328s) monitor interface onto it uh with the
[38:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2330s) add command we have seen earlier. you
[38:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2333s) will bring it up possibly configure it
[38:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2334s) to listen on a specific channel on which
[38:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2336s) you are performing your tests and then
[38:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2338s) use some TCP dump and from there you
[39:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2341s) will be able to see all the 802.11
[39:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2344s) frames exchanged between your device
[39:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2346s) test and an access point for example so
[39:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2349s) here I am tracing a whole connection
[39:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2351s) sequence between a device and an access
[39:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2354s) point
[39:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2355s) of course if I can do this in TCP amp I
[39:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2358s) can do it in wireshark and that's even
[39:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2360s) nicer because there I will be ble to
[39:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2362s) analyze the detail of every packet um
[39:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2365s) generated by my device or received by my
[39:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2368s) device.
[39:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2371s) All right. Uh that's pretty much it
[39:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2374s) about what I wanted to show you. Just a
[39:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2375s) few additional resources. This was
[39:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2377s) really a high level quick start of how
[39:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2380s) to write the driver. If you want to go
[39:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2382s) further, there is a nice official Linux
[39:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2384s) wireless documentation. This is a
[39:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2386s) dedicated website where the maintainers
[39:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2388s) and the main developers of the wireless
[39:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2390s) subsystem have put some notes um some
[39:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2393s) discussions and reasons about
[39:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2394s) implementation. So a very good source of
[39:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2397s) information. You have access to the
[39:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2399s) 802.11 standards. The most recent
[40:02](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2402s) version are paying documents but you
[40:05](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2405s) don't need the most recent versions
[40:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2407s) unless you are implementing some very uh
[40:09](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2409s) bleeding edge features. So the older
[40:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2411s) versions are available for free. And
[40:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2414s) finally, of course, you have access to
[40:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2416s) all the community code and
[40:17](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2417s) documentation. You have access to the
[40:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2418s) mailing list and the drivers uh in
[40:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2421s) drivers net wireless.
[40:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2424s) So that's all for me. I hope uh you will
[40:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2427s) have uh learned a few tips about the
[40:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2429s) device driver and if we have a bit of
[40:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2431s) time uh I would be glad to try to answer
[40:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2433s) your questions. Thank you.
[40:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2447s) Yes.
[40:49](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2449s) >> Very nice.
[40:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2451s) >> Thank you.
[40:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2451s) >> And I've done some work with some wire
[40:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2456s) drivers. So obviously DNA transfer is a
[41:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2461s) part of
[41:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2463s) when doing TXR. Is it similar?
[41:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2467s) >> So the question is um compared to
[41:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2471s) Ethernet development where we may have
[41:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2473s) some DFMA management at driver level is
[41:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2476s) there the same concerns on the wireless
[41:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2478s) part. So yes of course uh it is up to
[41:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2480s) you to to configure. You will have some
[41:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2482s) DMA for example on the bus you are
[41:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2484s) using. You may have some DMA on the
[41:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2486s) controller um interfacing your device.
[41:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2489s) And of course that's a matter you have
[41:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2491s) to address in your driver. Once again,
[41:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2493s) if you are um writing the basic skeleton
[41:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2496s) of your driver, you maybe don't need to
[41:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2497s) start writing um DMA management at the
[41:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2500s) beginning. You can start with a basic uh
[41:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2502s) operation. But yeah, that's the same
[41:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2504s) concern.
[41:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2514s) >> Oh, uh is there any other difference
[41:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2517s) between wired and wireless drivers? Uh I
[42:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2521s) would say yes. I have no clear picture
[42:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2524s) in my mind. That's a very wide question.
[42:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2527s) Uh from my point of view, the testing
[42:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2530s) seems a bit harder because you will have
[42:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2532s) uh to deal uh with some unentities let's
[42:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2535s) say when you are testing wireless some
[42:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2538s) loss packets, some radio performance and
[42:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2540s) you may suffer a bit less from it uh
[42:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2543s) with Ethernet. uh but then you have a
[42:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2546s) whole variety of details that are of
[42:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2549s) course um specific to Wi-Fi. The nice
[42:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2552s) thing however is that uh you clearly see
[42:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2555s) that there has been a lot of efforts in
[42:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2557s) the core subsystem in CFG 80211 in MAC
[42:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2561s) 80211 to make sure that not all drivers
[42:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2563s) replicate custom behavior in there. So
[42:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2566s) you have some generic frameworks even
[42:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2568s) for mo the most specific features that
[42:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2571s) your device may support.
[42:56](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2576s) Uh yes,
[42:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2578s) >> maybe just
[43:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2584s) instead of TCP.
[43:21](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2601s) >> Okay. And the solution was aerodyump.
[43:24](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2604s) Is it part of air crack?
[43:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2606s) >> Yeah. Uh so indeed that's a nice suit to
[43:29](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2609s) perform testing and especially because
[43:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2611s) this one will automatically perform the
[43:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2613s) virtual interface bring up I guess the
[43:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2615s) channel configuration.
[43:37](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2617s) Yeah. So so indeed it's very nice if you
[43:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2620s) want to go uh straightforward to the
[43:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2622s) final testing direction. Here the goal
[43:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2624s) was really to give you the lowest level
[43:46](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2626s) commands um to perform your testing to
[43:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2628s) bring manually your interface. But of
[43:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2630s) course, yes, you have some ways to
[43:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2632s) automatically bring this setup as a
[43:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2634s) whole.
[44:00](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2640s) >> Uh, yes.
[44:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2646s) >> Uh, sorry, could you try to speak
[44:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2648s) higher? any tool to analyze network.
[44:13](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2653s) >> Okay, so the question is there is any uh
[44:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2656s) any tool to analyze the network packets
[44:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2658s) exchange between user space and canel.
[44:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2660s) So there is one basic ways to do it
[44:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2663s) similarly to what we have done with the
[44:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2665s) monitor interface. You can bring up a
[44:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2667s) virtual net link monitor interface which
[44:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2670s) is not related to the wireless subsystem
[44:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2672s) by the way. So you will do some IP link
[44:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2675s) um add something well you will use the
[44:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2678s) IP routt util to bring up a net link
[44:41](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2681s) monitor interface and then you will be
[44:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2683s) able to start either TCP dump or
[44:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2685s) wireshack on this net link interface and
[44:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2688s) if you start wireshack wireshack
[44:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2690s) contains all the deectors needed to pass
[44:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2693s) the content of those net link messages.
[44:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2695s) So you have some debug tools in it.
[44:59](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2699s) Uh, yes.
[45:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2701s) >> Do you have a good recommendation for
[45:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2703s) modern Wi-Fi chipsets with good
[45:07](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2707s) especially open one?
[45:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2710s) >> Wow, that's a big Christmas list.
[45:14](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2714s) Uh, to be honest, no, I don't have such
[45:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2716s) a recommendation. Uh, my work was quite
[45:18](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2718s) focused on specific chips. Uh here my
[45:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2722s) best suggestion would be to take a look
[45:25](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2725s) at either the mailing list and or uh the
[45:27](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2727s) gy story in drivers net wireless. If
[45:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2730s) there is activity in here and possibly
[45:32](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2732s) in the Linux firmware directory as well
[45:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2734s) which would prove that the vendor is
[45:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2736s) active as well that would be a good hint
[45:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2739s) that uh the device and the corresponding
[45:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2742s) driver is in good shape.
[45:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2751s) Uh yes, maybe one last question.
[45:54](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2754s) >> So any
[45:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2758s) direction on how to debug issues? I've
[46:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2761s) been working on developing some drivers
[46:04](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2764s) for a Wi-Fi device
[46:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2768s) after every six hours. I can't figure
[46:11](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2771s) out what that is about.
[46:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2776s) So the question is uh is there any tip
[46:19](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2779s) about uh debugging devices that may
[46:22](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2782s) reboot sporadically right? Um
[46:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2786s) if you are lucky you may have some
[46:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2788s) additional debug interfaces on your
[46:30](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2790s) chip. So you have the main bus to start
[46:33](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2793s) interacting with your chip and possibly
[46:35](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2795s) depending on the interface brought by
[46:38](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2798s) this chip depending on the firmware
[46:40](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2800s) running inside the chip. You may have
[46:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2802s) for example if you're lucky two small
[46:44](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2804s) UAT pins available to uh on which the
[46:47](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2807s) firmware inside the wireless chip is
[46:50](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2810s) outputting some logs. That could be one
[46:53](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2813s) solution to try to um identify those
[46:55](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2815s) reboots. But honestly that will always
[46:58](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2818s) be a difficult topic because here we are
[47:01](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2821s) talking about a reboot inside the chip
[47:03](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2823s) which depend on some binary blob uh
[47:06](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2826s) provided by the vendor. So you are a bit
[47:08](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2828s) clueless if the bug is on the driver
[47:10](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2830s) side in the canel. You have all the
[47:12](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2832s) tools to investigate it but that will be
[47:15](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2835s) always more difficult on the device
[47:16](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2836s) side. However, so there is a topic about
[47:20](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2840s) debugging it, but the topic about unling
[47:23](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2843s) those reboots is a known concern in the
[47:26](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2846s) Mac 802.11 layer. So you have some ops
[47:28](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2848s) that you can implement which acts like
[47:31](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2851s) um last resort. You know that you your
[47:34](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2854s) driver has um your firmware has crashed
[47:36](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2856s) for some reason. The core may be able to
[47:39](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2859s) be notified about this. Your driver is
[47:42](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2862s) telling to the core I don't know in
[47:43](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2863s) which state I am. And so you can
[47:45](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2865s) implement OBS so the core can restart
[47:48](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2868s) and reinitialize properly your device.
[47:51](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2871s) So you have some fallbacks that you can
[47:52](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2872s) implement in this case.
[47:57](https://www.youtube.com/watch?v=kvyLE4esjPE&t=2877s) All right. So thank you all.