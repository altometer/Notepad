---
published: true
title: My first Mega Server Catastrophe
layout: post
tags: [home server, server, technology]
---
# The Strife of a home-lab
The home-lab Server. Often riding the line between **testing** and **production**. Unfortunately, I ran in to a few issues that forever changed the way I handle building systems and testing them.
Let this be a cautionary tale.


## Backstory
My first server, was a Compaq dual-core 2.2GHz core2-duo Laptop with 4GB of memory. It is the first real experience I had running Ubuntu server. Version of choice? 10.04. To surmise what I had it doing:
1. File Services.
  * Act as primary backup store for my Gaming PC
  * NAS for local computer(s).
  * Owncloud Host for file sharing, syncing, and external access.
  * Torrent seedbox.
2. Minecraft Server - small server for friends and family only running occasionally.
3. Virtual Machines host - testing, R & D, virus jail, etc.
4. Minor home automation.
  * Turn on lights in office when I get home.
  * Boot Gaming PC.
5. Security camera host.
  * Manage and control multiple IP cameras.
  * Store Activity recordings.
  * Email on events.
6. Web hosting
  * Host my wordpress Blog (Previously, Ijustdothings.com).
  * Host my [side-business PC repair site](www.evrypc.com).


I installed so many hacks left and right to get that poor server running the way I wanted. After less than a year it felt growing pains. The laptop would rev its little fans 24/s furiously trying to cool all of the internals. I gutted it and actually sat the laptop motherboard on top of a few computer case fans. Around this point, I decided it was time to just start building a real server.

## Lining up the Dominoes
To roughly quote something I read from a PC building forum.

 First Post:
> Hey Guys! I just got my super-fast gaming computer up and running!

> It is a dual-CPU Xeon octo-core with two Nvidia Geforce 970's, 128 GB of memory, and a crazy fast storage system.! I set it up with 4x SSDs in RAID 0.

 Second post:
> What the heck! All my games run like garbage!

The *gamer* in question used what he thought to be the best hardware components given limited knowledge. Dual-CPU provides nearly no advantages for any Desktop OS even if that OS supports Dual-CPU. The specific processor mentioned is for servers. RAID 0 is begging to fail. etc.. etc..  
The issues with my server unfortunately, were very similar.

Don't get me wrong, I had worked with server hardware for years. I'd just not run anything close to the scale of what I was aiming to accomplish.

##### Goals
I wanted the new server to do all of the following:
1. All of the old server's services with room for 4 more cameras.
2. I wanted my new server to act as my firewall. This is before I had even heard of Pfense. My plan, unfortunately was to install iptables and a few other generic Linux server tools to manage my network.
3. The box was to act as my DNS server, managing any and all DNS queries as part of a my "Global defense mega-server"
4. Drastically increased storage. At least 2TB (up from 500GB)

##### Specs
First mistake, I bought 5 1TB USB 3.0 "High speed" HDDs from Newegg on black Friday. Somehow, I got a bug in my ear. `Sooooooftwaaaare raaaaaaiiiid`. I can hear it calling me now. The ghostly siren call of a promised free performance increase and security net. Software RAID is amazing, often on-par with hardware RAID. It is relatively hardware agnostic, and very easy to set up. Or at least, easy to set up wrong. My entire build ended up centering around the idea of using external USB HDDs as my central storage array. USB 3 with 7200rpm external Hard Drives *was supposed to be* nearly as fast as having the drives connected directly to the Motherboard via SATA.

* I bought a gaming Motherboard just because it had 4 USB3.0 ports. `pain point`
* Got the Cheapest 2U server case I could find with intention of mounting 2.5" HDDs(still in their external cases) inside.
* i5 4690k CPU. On sale for $75 (from $200) `The best part of this build.`
* 8GB in memory for expansion to 16-32 if needed.
* x5 1TB 2.5" External Hard Drives for storage array.
* 2x Gigabit Ethernet card for network access.


## Spin-up
Over the period of 3 months I ordered all the parts for the server. I had plenty of spare parts from my day job. None of which, ended up being usable.
* Special Power supply because of the form factor of the case.
* The motherboard only had 2 USB 3.0 ports, and I had to order a motherboard USB3.0 adapter.
* Then, order a second one because the first one wasn't slim form-factor and put stress on the motherboard pins when the case was closed.

Eventually, I booted and installed the OS to a temporary HD to start configuring. This was my first time configuring software RAID, and I needed a "stable" environment to start with for setting up the software RAID. The internal network card didn't work. `pain point` Ordered a PCI slim two-port Ethernet card after much Troubleshooting. Finally, booted in to Ubuntu server. It took me nearly a week of tinkering to set up the RAID. `pain point` I was testing and learning at the same time. When I had what I though was a stable RAID, I attempted to clone the server OS with DD to the RAID from a booted USB drive. Somehow, I managed to write directly to the first disk in the RAID, breaking it and preventing reassembly. Oops. Eventually I settled on configuring RAID 10 with 4 drives and using the fifth as a hot-spare. `pain point` This would give me double the read and double the write speed of any of the drives individually. It also would allow for up to three drive failures before there would be irreparable damage.

I Picked Ubuntu 12.04 (one major version behind) to install. The OS installed.. mostly okay. There were some strange issues with the packages downloading and I managed to break the RAID again. Eventually, I got Ubuntu installed on a freshly formatted storage array. LVM with ext4. Three partitions, OS, Data, Swap all on the RAID. Woohoo! `pain point`

By the time this was all installed, I decided that I wanted to upgrade to Ubuntu 13.04. This, ended up breaking a few things. Old packages not being removed, new packages conflicting, and some parts of the server seemingly not working at all.

## Spin-up take 2
Let's start from scratch. I took out all of the USB drives, booted from a USB Install disk. Rebuilt the RAID starting with two disks. Why not practice expanding the RAID rather than build it as RAID 10 off the bat? This would give me insight to the commands needed for if/when I would expand the RAID for more capacity later. After fully expanding the RAID, I rebooted and installed Ubuntu 13.04, LVM, same partitions as before. The only difference, is I had started to use what was the hot-spare as an external HDD. `pain point`

Things were starting to look up, until I found that the second Ethernet port on my NIC refused to work. I ended up configuring the motherboard's onboard NIC and only the first port of my expansion card in order to have my firewall setup. Before, I couldn't use the onboard NIC due to being unable to download drivers without an Internet connection.

I installed IPtables and a few other networking tools: DHCP, DNS, and squid. The Firewall was slow. Over the course of another week I dealt with excessive delays on any web traffic. Every protocol, everything coming in or out to the Internet had at least a 50ms delay. It was torturous. To add insult to injury, my 10 year old Netgear router was faster than the server. Even when I had the server's filtering rules turned off and squid uninstalled, no dice. It was as slow as dialup. Eventually I caved and bought an off-the-shelf firewall. The Zyxel USG 20 is basically a cisco asa 5505 clone and met all my firewall needs marvelously. Scratch giving up on Firewalling, DNS, DHCP, or any network control aspects of the new server entirely.

For my Seedbox, I wanted to use uTorrent server. I had some experience with the software on my Laptop-server, but wanted to scale things up and properly configure it. uTorrent server maps to an internal port for web access. This proved to be cumbersome for a budding Linux Web Admin. I had to remap internal port 2777 to a virtual hosted 80 and the subdomain download.Ijustdothings.com. I'd never done or tried URL rewrites before and couldn't get it working. The uTorrent server by default hosts itself locally as 127.0.0.1/utorrent:2777 . Eventually, I gave up trying to virtual host it to a subdomain and just set up port forwarding. So, externally I had to access uTorrent's portal via Ijustdothings.com/utorrent:2777. Not ideal, but workable.

## Rule #1: Have a backup. Rule #2: Have a backup.
I set up an external 2TB Hard Drive to act as the backup for the entire server. This was my first time setting up a Linux backup and I took a few days to do it right. To summarize, I used rsync to create differential hard-link backups. A brief overview here...

For the first day:
```
$ TODAY=`date +%Y%m%d`
$ rsync -av -e / /media/externalHDD/backups/$TODAY
```
For the second day and there-after:
```
$ TODAY=`date +%Y%m%d`
$ YESTERDAY= `date +%Y:%m:%d -d "yesterday"`
$ cp -al /media/externalHDD/backups/$YESTERDAY /media/externalHDD/backups/$TODAY
$ rsync -av -e --delete / /media/externalHDD/backups/$TODAY
```
Theoretically, this backup job allows me to have extremely fast, file level differential backups. If I zip the historical backups (which I did). Older than yesterday's, it effectively "de-duplicates" the backup.  6 lines of code to start, add another 5-6 for compression. Who needs a $10,000 backup software?  I was so confident, I tested multiple times doing full-OS failure tests. Doing stupid things like running `$ rm -r /` to nuke the entire file system and successfully restoring it from the Backup Drive.

I almost wanted to script automatic recovery from the Backup. Any time the server fails drastically enough to need a full disk backup, it's probably best to be manually involved some way.

## Carrying on.
At last, a stable framework. Nearly 3 months of tinkering to get to a point where I  felt comfortable moving everything from the Laptop-server to my new fancy home-lab server. Here's only a sample of unreplaceable data that was migrated:

* Family photos
* Video projects
* Bitcoin wallet
* And many moooore.

With my new server, I started to do expand its responsibilities. On average, the new server was handling everything the old server was doing with only 5% CPU and 25% memory usage. Introducing [Tiny Tiny RSS reader](https://tt-rss.org/gitlab/fox/tt-rss/wikis/home). Aggregating all of my news, comics, and web life. Fully configuring this software was no small feat. At the time, I was regularly reading 45+ webcomics, 20+ tech blogs, checking 10+ subReddits, and just beginning to use it for catching other data. I had never read so much, so fast. RSS is a wonderful experience and for those who've never used one, you don't know what you are missing. Imagine spark notes of any website you want. All the fluff, formatting, and ads removed.


## Enter, the storm
Quite literally, unfortunately.

6 months. I got to enjoy the wonder, splendor, and benefits of my server for 6 months. Then it rained, thundered, and eventually, Lightning. I was at work and out of site when it happened. I got a text message because my [Uptime Robot](https://uptimerobot.com/) Noticed the server go down. This wasn't anything unusual. Even though it was on a UPS, the server did occasionally go down. Either the internet at my apartment would go out, or the power was out long enough to drain the battery and initiate a safe shutdown. No biggy. I get home late that night, didn't bother with the server. Work the next morning, I realize I forgot to bring the server online. My motion alarm email didn't go off for the  security cameras. UPS dropped off a package triggering a "Package Delivered" Email, but no camera alert showing a brown truck. t it arrived. I made a special note to bring the server online when I got home.

That evening, to my horror, turning on the server yielded a black "No boot device found" screen. Upon closer inspection, several of the USB drives were not spinning up. Even worse, my backup drive was giving the click of death.  It seemed that some of my equipment was toast. Thanks a lot APC. I immediately got frustrated, and scared. So, I shutdown everything leaving it for another day.

## Recovery (attempted)
Friday evening that week, I cleared my weekend plans with intention of spending however long it took to get everything back up and running.

I booted the server to a USB boot Linux disk and tried to assemble the RAID.
`$ mdadm --assemble --scan`
`$ RAID not started, only 2 out of 3 drives ready.`
Oh...crap. No backup, doing it live, I have to make this work. I can't find any documentation on my setup, my RAIDblock size, RAID type, anything. I spent the next 8 hours Googling my way in circles. With 4 drives plugged in to the server, I started troubleshooting a RAID 5 failure. Doing what I could to try to find something, anything to restore my data.

What I eventually realized, is that I had somehow installed the wrong USB drive instead of one of the RAID drives back when I wiped the server to start from scratch. I had been using the hot-spare drive as a external Disk for school. This means, the server was running with -1 drive even **before** the failure. I proceeded for almost all of that Saturday assuming I was troubleshooting RAID 5. I did things to try to force recreation of the RAID headers. Things you don't do unless you are completely sure about your setup. Well...I had RAID 10 on the drives. Not, RAID 5. Re-writing the RAID sync blocks themselves doesn't destroy any data, but doing so on a LVM does. Eventually, I realized that I had originally used RAID 10, not 5. That I had likely destroyed my data.

On top of everything else, I discovered that if I had known what I was doing, I would have been able to recover the server. Out of my drives A1, A2, B1, B2 : A1 and B2 had failed. This meant that the RAID could technically have recovered. At the very least, all of the data copied off to a new backup drive. I was missing the proper header information to auto re-create the RAID. Only because in my USB environment I didn't install the needed prerequisites for LVM management. In my recovery attempts I was unable to see the disks at all due to the missing LVM software.
> My USB Linux install was Ubuntu 13.04 64x Desktop. The server, was originally loaded with 13.04 64x, server.

## Retribution (kinda)
I was unplugging everything angrily. When I found it, I was getting ready to throw my server out of the second story window and had pulled it out of the server rack. **A 2.5" Hard Drive**  Taped to the back of my server cabinet, a small hard drive. On it, a sticky note *"For when the server blows up."* Cradling the drive like a newborn, I popped it in to my external drive reader. A backup. A glorious backup. It was 6 months out of date. I lost all of my work, my projects, my files, but only for the last 6 months. No longer was I without everything I had ever worked on.

Immediately I used multiple copying softwares to duplicate this drive to no less than four drives. I was debating burning it to DVDs just to be *sure*. I could never be sure. I thought I was doing everything right. I had a backup, it was running, and I even had RAID set up! But, because of my oversights, lack of documentation, and experimenting on "production" environment, I nearly lost everything.

It's been almost two years since all this happened. Today, I turned on my new server.

**Time to get to work.**
