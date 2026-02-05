---
title: "open-source character management || devlog 01"
description: "laying the groundwork for an open-source solution to manage characters & worlds for creative projects"
date: "Mar 6 2025"
---

For people who like writing stories & creating worlds, a common sentiment is shared among different Reddits etc. which is: how do I actually manage and organize all this information. There's hundreds of tools out there to manage them, and I wanted to take a crack at making my own with a modern technology stack & focused on utility over social features. 

Some of the base features I'd like to include:
- create characters
- upload images 
- add descriptions & tags to characters
- filter & sort characters by tags

Once the base features are implemented, I'd like to include a few extra features that separate it from other apps on the market:
- create accounts for users & manage control over characters in a modular way (i.e: some users can add characters, some can only view them, etc.)
- version history & recovery

I did some initial research and decided on a couple tools for the stack:

### Supabase (back-end)
The actual data stored is fairly simple for a single instance, so I created a quick concept in Supabase.Depending on how complicated it gets with user management, I may roll my own Spring Boot backend, but this is plenty for now.

## Next.js (front-end)
After a little bit of debate, I decided to go with Next.js for the following:
- file based routing 
- server components & simplified fetch API
- more modern

## Storj (cloud storage)
Since we're going to be working with a large amount of media files, an object-based cloud storage is a must. While AWS S3 is the most obvious and known solution, it's prohibitively expensive with it's egress fees. I also wanted to use this project as an opportunity to try out other S3-esque providers. I shortlisted a few, like Backblaze (best free tier) and Wasabi (no egress) but settled on Storj for the following:
- e2e encryption
- backup durability
- distributed cloud is better for global performance

This is just the initial setup, I generally prefer to jump into code but I'm trying to be better about planning, so I've been laying the groundwork out a bit. In the next devlog I'll be focusing on the design & development of the basic features on the front-end.