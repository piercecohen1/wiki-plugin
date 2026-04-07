---
title: "Nate Herk — Obsidian + Karpathy Just 10x'd Claude Code Projects"
source_type: transcript
author: "Nate Herk"
date_published: 2026-04-07
url: "https://youtube.com/@nateherk"
tags: [raw, transcript]
---

# Nate Herk — Obsidian + Karpathy Just 10x'd Claude Code Projects

Raw transcript. Do not modify.

---

What you're looking at right here is 36 of my most recent YouTube videos organized into an actual knowledge system that makes sense. And in today's video, I'm going to show you how you can set this up in 5 minutes. It's super super easy. You can see here how we have these different nodes and different patterns emerging. And as we zoom in, we can see what each of these little dots represents. So, for example, this is one of my videos, $10,000 aentic workflows. We can see it's got some tags. It's got the video link. It's got the raw file. And it gives an explanation of what this video is about and what the takeaways are. And the coolest part is I can follow the back links to get where I want. There's a backlink for the WAT framework. There's a backlink for Claude Code. There's a backlink for all these different tools I mentioned like Perplexity, Visual Studio Code, Nano Banana, Naden N. It also has techniques like the WT framework or bypass permissions mode or human review checkpoint. So, as this continues to fill up, we can start to see patterns and relationships between every tool or every skill or every MCP server that I might have talked about in a YouTube video. And I can just query it in a really efficient way now that we have this actual system set up. And the crazy part is I said, "Hey, Cloud Code, go grab the transcripts from my recent videos and organize everything. I literally didn't have to do any manual relationship building here. It just figured it all out on its own."

And then right here, I have a much smaller one, but this is more of my personal brain. So this is stuff going on in my personal life. This is stuff going on with, you know, UpAI or my YouTube channel or my different businesses and my employees and our quarter 2 initiatives and things like that. This is more of my own second brain. So I've got one second brain here and then I've got one basically YouTube knowledge system and I could combine these or I could keep them separate and I can just keep building more knowledge systems and plug them all into other AI agents that I need to have this context. It's just super cool.

So Andre Carpathy just released this little post about LLM knowledge bases and explaining what he's been doing with them. And in just a matter of few days, it got a ton of traction on X. So let's do a quick breakdown and then I'm going to show you guys how you can get this set up in basically 5 minutes. Something I've been finding very useful recently is using LLM to build personal knowledge bases for various topics of research interest. So there's different stages. The first part is data ingest. He puts in basically source documents. So he basically takes a PDF and puts it into Cloud Code and then Cloud Code does the rest. He uses Obsidian as the IDE. So this is nothing really too game-changing. Obsidian just lets you visually see your markdown files.

He said here, "I thought that I had to reach for fancy rag, but the LLM has been pretty good about automaintaining index files and brief summaries of all documents and it reads all the important related data fairly easily at this small scale." So right now he's doing about 100 articles and about half a million words. So there's a few other things that we'll cover later, but the TLDDR is you give raw data to cloud code. It compares it, it organizes it, and then it puts it into the right spots with relationships, and then you can query it about anything.

Why is this a big deal? Because normal AI chats are ephemeral, meaning the knowledge disappears after the conversation. But this method, using Karpathy's LLM wiki, makes knowledge compound like interest in a bank. People on X are calling it a game changer because it finally makes AI feel like a tireless colleague who actually remembers everything and it stays organized. It's also super simple. You don't need a fancy vector database embeddings or complex infrastructure. It's literally just a folder with markdown files.

One X user turned 383 scattered files and over 100 meeting transcripts into a compact wiki and dropped token usage by 95% when querying with Claude.

The thing about the hot cache — if I go to the herk brain in the wiki, you can see there's a hot.md right here. And this is basically just a cache of like 500 words or 500 characters that it saves, which is like what is the most recent thing that Nate just gave me or that we talked about. In the context of my executive assistant, this is really helpful. You know, it might save me from having to crawl different wiki pages.

So now the final question about this that I wanted to cover is like does this kill semantic search rag? And the answer is no, but kind of yes. And it all depends on the goal of the project and the goal of the context, how much context you have. If you have hundreds of pages with good indexes, you're fine with wiki graph. But if you were getting up to the millions of documents, then you're going to want to actually do more of a traditional rag pipeline.

[End of transcript]
