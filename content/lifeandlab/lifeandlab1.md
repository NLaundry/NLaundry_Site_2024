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




## Lab

---



The documentation has definitely been written by someone way more knowledgeable than myself which means it has a number of spots that a noobie like myself, can get tripped up on. So, after my many attempts and bug fixes, I wrote an install script which you can check out on my github #TODO insert github. 

Since

