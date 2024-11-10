+++
title = 'Life and Lab 1: De-Optimizing Goals and ZFS Boot Menu'
date = 2024-11-08T11:55:51-05:00
draft = true
+++

Hey friends,

Welcome to the first Life and Lab post! If you're coming from *A Little Better* on Substack, thank you so much for sticking around for this new direction. I hope you enjoy it as much as I am right now.

---

Over the past few weeks I've been working on two things. In "life", I've been coming to terms with a slower pace of progress in my projects. I have a tendency to get excited about new projects, overload myself with tasks, then burnout FAST. So, in the *life* section we'll talk about how I've been trying to de-optimize and de-projectify aspects of my life to lower these tendencies and reduce burnout. This is something I've been working on for a few weeks now and it's been working really well for me so far.

In the *Lab* space, I've been working on migrating my 3 primary machines to a Debian with root on ZFS setup - specifically to take advantage of ZFS boot menu. While the benefits are fantastic (and outlined in my recent youtube video #TODO: youtube video link here) the install process for zfs boot menu is arduous and error prone. Since I've already covered most of this in my Youtube video, I'll instead talk about some interesting tidbits I ran into while debugging and fixing things, namely: Ventoy for creating bootable USB drives, and here-documents in shell scripts.

## Life

---

As I wrote about in a past *A Little Better* post *Headspace Management*, I think you can only hold 2 to 3 major projects in your head at any given time. These are my "focal" projects. Right now, most of these are work related. At the moment, they're: creating this content, doing my PhD research, and learning more about systems administration/homelabbing. 

For these big goals, I manage them as projects. I set some outcome-oriented goals like "write up a study and publish a paper on this by the end of the year" or "finish the Linux Foundation Certified Systems Administrator certification by the end of the year." I also track my habits and monitor my inputs so I'm consistently making progress. It's tiresome and occupies a lot of headspace, but it's important to make consistent progress, so I do it.

### Progress isn't always the goal

The problem is, this kind of goal-setting is a deeply engrained habit for me; so much so, that I catch myself creating projects out of every day things - things I don't have headspace to "project-ify". Even in gaming, which I do to relax and socialize, I think about how I can improve. I think "wouldn't it be great if I play a ranked game once per day and review it. Then I could really hit diamond or masters!" But, this takes my leisure and makes it goal-pursuit time. Now, instead of achieving the goal of socializing and relaxing, I'm failing at this secret goal that snuck into my head - improving my gameplay. 

Nowhere is this more poignant form me than in my personal health. Social media is full of advice on how to make progress in strength, flexibility, cardio, whatever health goals you may aspire to. And it was useful to me for a while (shout out Jeff Nippard) - but these days I just don't have the headspace to make my health a project - it's more of a maintenance task right now.

Still, my mindset was about outcome and progress so, as I ran, I'd find myself increasingly disappointed in myself. My heart rate monitor would go off telling me to walk for a bit and I'd think "God, that's pathetic. I think my cardio is even worse than last week!" ... I was berating myself for not hitting goals that I hadn't even intended to set!

That is until a few weeks ago when serendipity struck. One day my apple watch was dead and it was time to run. I almost chose not to run at all, but thought that would be a pretty stupid reason not to get outside on such a beautiful day. I ran without it and it was bliss. 

No beeping, no failure feedback, just sunshine and a little cardio - whether it was running or walking I was happy to be exercising. It felt good to get out of my head and out into the fresh air. I had no progress to track because there was no data, and so I had no idea if I'd ran "better" or last week. I just knew that I ran and that it was good for me that I had. I was proud and happy instead of feeling like I'd failed to make progress.

My apple watch died and I stumbled into a process-oriented mindset.

I had so much fun running like this that I decided to stop tracking progress in other aspects of my life. I stopped counting how many reps I do during my morning calisthenics - I just go until my muscles say "yeah, we're done bro." It's not like by counting I could've done any more any way.

### The Benefits of Not Tracking your Progress

It's been a few weeks of de-projectifying my health and I've found myself exercising more and in a greater variety of ways. For example, I used to avoid taking the stairs because I'd get out of breath and then think "God, my cardio sucks. I'm not making any progress!" Now, because I'm not worried about making progress, and my goals are actually just to sneak in exercise wherever I can, I happily take the stairs. When I'm out of breath, I think "great! I just got a few minutes of blood pumping in the middle of my work day!" 

I've also completely gotten out of the gym, which I've never liked, and committed to morning calisthenics. They're more fun, easier for me to fit into my schedule, and I don't feel like I'm competing with social media's army of gym bros. I'm more consistent than ever, happier, and less concerned about building muscle. I'm just staying healthy.

I think by not tracking progress, I've opened myself up to two things. First, I can just be proud of myself for doing exercise regardless of how it goes which makes it more fun and helps me stay consistent. Second, I listen to my body more. There's no external data like the number of reps or length/speed of a run, so I pay attention to how I'm feeling and push myself to the extent that I can. It makes my exercise a sort of mindfulness that I've really come to enjoy!

### Guiding Questions and Actionable Steps

1. Are you turning anything into a project that you don't actually want to? If so, Is it causing you pointless stress?
2. Is there something you'd like to try de-optimizing and switching to a process-orientation?

I think a lot of people, like me, might benefit from expanding what they consider exercise and removing the pressure of "making progress"



## Lab

---

Whenever I'm setting up new machines two thoughts always cross my mind: "where did I put that usb with the live installer?" and "I wonder if I could write a bash script to do this for me." This time around I decided to try to solve both problems ... it was an ambitious weekend. 

### Ventoy - One Drive to Rule Them All

Usually, in the search for "the right" USB drive, I stumble across a dozen other USBs all with different distros I might want to keep around. Ventoy solves this problem. Instead of having to format each drive with a single ISO, Ventoy turns a single drive into a boot loader that can have any number of ISOs or EFI executables. Drag and drop a few favourites, even windows (more on that when I release my microwin setup tutorial), and next time you boot from it, you'll have your pick of the litter.

If you're not distro hopping on bare metal very often, this may sound less exciting. But, it's always a great idea to keep a few utility distros laying around for recovery. I'll be keeping an LTS Ubuntu ISO, ZFSBootMenu EFI executable, SystemRescue, Windows Live ISO, and Debian handy at the very least. 

Save yourself some time flashing drives. Give Ventoy a try! 

#### Links:

[Ventoy Website](https://www.ventoy.net/en/index.html)
[System Rescue bootable linux environment](https://www.system-rescue.org/manual/Overview/)
[ZFS Boot Menu with Recover Image](https://docs.zfsbootmenu.org/en/v2.3.x/index.html)


### Here Documents in Bash Scripts

The ZFS Boot Menu install process requires a chroot into a newly bootstrapped linux environment. This means that at some point, you're shifting into a new shell environment which poses the question "how do I keep executing things in the chroot from this script?". Luckily, there's a trick for this. We can use here-documents ... and in a few cases, as ugly as it is, nested here-documents.

**Here-documents** in bash (`<<EOF`) allow you to create multi-line strings within a script, often used for injecting configuration or command sequences in a single, readable block. They’re especially helpful when scripting setups or creating configuration files dynamically. The syntax looks like this:

```bash
command <<EOF
# Lines of text or commands
EOF
```

Anything between `<<EOF` and `EOF` will be interpreted as input to the specified command. The delimiter `EOF` can be any word, but it’s conventionally `EOF` or `EOL`.

#### ZFS Boot Menu Install Script Example

In my ZFSBootMenu setup, I needed to use a here-document to pass and execute an entire shell script within a `chroot` environment. This let me run multiple commands and set up configurations in a clean, organized way. However, some parts required **nesting here-documents** to dynamically generate configuration files within the chroot, which introduced some syntactic quirks.

```sh
enter_chroot() {
	echo "Entering chroot environment to configure system..."
	chroot $MOUNT_POINT /bin/bash <<-EOF
	# Set hostname
	echo "$HOSTNAME" > /etc/hostname
	echo "127.0.1.1    $HOSTNAME" >> /etc/hosts
	
	# Configure apt sources
		cat > /etc/apt/sources.list <<-EOF_APT
		deb http://deb.debian.org/debian bookworm main contrib non-free-firmware
		deb-src http://deb.debian.org/debian bookworm main contrib non-free-firmware
		
		deb http://deb.debian.org/debian-security bookworm-security main contrib non-free-firmware
		deb-src http://deb.debian.org/debian-security/ bookworm-security main contrib non-free-firmware
		
		deb http://deb.debian.org/debian bookworm-updates main contrib non-free-firmware
		deb-src http://deb.debian.org/debian bookworm-updates main contrib non-free-firmware
		
		deb http://deb.debian.org/debian bookworm-backports main contrib non-free-firmware
		deb-src http://deb.debian.org/debian bookworm-backports main contrib non-free-firmware
		EOF_APT
    # ...
    EOF

```


Here’s a closer look at what’s happening:

1. **Main Here-Document**: The outer `<<-EOF` here-document is used to feed multiple commands into `chroot`, allowing the script to execute these commands as though they’re running directly in the chroot environment. The dash `-` after `<<` (`<<-EOF`) is important here—it lets us indent commands using tabs without affecting execution.

2. **Nested Here-Document**: Inside the outer here-document, I used another here-document (`<<-EOF_APT`) to create the `sources.list` file dynamically. By nesting this here-document, I could inject all the required apt sources at once, without needing multiple `echo` commands, which would be messier and harder to read.

3. **Indentation with Tabs**: For this setup to work correctly, **tabs must be used for indentation**. When using dash-prefixed here-documents (`<<-`), leading tabs are ignored, making it possible to align code blocks nicely. Using spaces, however, breaks this behavior, causing bash to interpret the indentation as part of the content, which can lead to syntax errors or formatting issues.


In a complex setup like this, where we’re passing nested content to a chroot environment, using tabs for indentation and `<<-` for dash-prefixed here-documents helps keep the structure readable and ensures bash interprets the content correctly. This approach reduces clutter and makes it easier to manage the overall setup by keeping related commands and configurations together. 

So, when working with nested here-documents — especially when passing commands into chroot environments — **using tabs for indentation** and `<<-` to enable this flexibility is essential to avoid tricky syntax issues.


#### Links:

[ZFSBootMenu Install script Git Repo](https://github.com/NLaundry/zfsbootmenu-autoinstaller)
[ZFS Boot Menu Docs](
[OpenZFS Docs](
[Bash Here Docs documentation](https://www.gnu.org/software/bash/manual/html_node/Redirections.html#Here-Documents)


## Wrap up

Any way, that's all I've got this week. I think the next issue will be shorter than this. I got on a bit of a ramble there. Have a great week!

Cheers,
Nathan Laundry

