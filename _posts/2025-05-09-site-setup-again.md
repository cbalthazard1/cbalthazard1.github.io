---
layout: post
category: Website
title: "Site Setup (Again)"
---

I've been thinking about static sites a lot recently. Obviously [this site is static](https://balthazard.dev/website/2024/06/07/site-setup/), but at work we've been moving a dynamic site over to be static instead, not (in any real way) backed by a database. More on that in a future post, because it's been a fascinating project.

It's got me thinking about the limitations to this site: its UI isn't as clean/customizable as I would like, it doesn't rely on any current/in-vogue systems, and some of the markdown workarounds (particularly the one for spacing around bullet-pointed lists) just suck. Time to do a bit of a revamp.

###### Vercel

Most of my experience is with Heroku, so I want to try out something new-ish that is ideal for static sites. That's Vercel. I have had some exposure to the platform - just today I was figuring out how to seed a postgres Supabase integration through Vercel. But I'd love to get more familiar with its deployment cycle and its preview apps. We'll see how much I can get going on a Hobby plan.

Also (and please don't break this deployment, GA), I'll be happy to move on from Github Actions to a more robust deployment method. It does work, but excited to try out another option for this site.

###### 11ty (Eleventy)

[Why Eleventy?](https://www.11ty.dev/#why-should-you-use-eleventy)

Of these reasons, the ones that stand out to me are the control and the community. I based some decisions for the old site on not being as familiar with some frontend pieces, and wanting to hand-wave those away to get a site out there. And those ended up as points of frustration. This time around, I'd prefer to keep full control of what I'm putting out there, even if I need to learn some Next.js along the way.

And knowing the generator is not only stable but has a community behind it is reassuring - not to mention, a former manager who I greatly admire used Eleventy for [her site](https://angeliqueweger.com/now/), which looks fantastic.

###### Timeline

A couple weeks? It's just me here.

The main pieces are figuring out what I want to keep, and how a user should be introduced to the site. It's probably more effective to have some 'about me' stuff upfront on the homepage instead of whatever post I happened to publish most recently.

And speaking of posts - is that the primary content? Not sure! I'll need to think through the site's purpose, and align the frontend vibe and structure to that aim.

Beyond that, I hope/expect Vercel will make deployment straightforward. DNS is one of those things you only need to do a couple times a year, so you have to re-learn it each time, but I expect NameCheap plus Vercel won't be too complex.

Time to start writing tickets.