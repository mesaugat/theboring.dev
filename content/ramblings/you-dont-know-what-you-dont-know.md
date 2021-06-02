---
title: You don't know what you don't know!
description: Fixing the problem without knowing the problem.
toc: true
tags:
  - web
  - tech
  - learning
categories:
series:
date: "2021-05-30T21:38:00+05:45"
lastmod: "2021-06-02T01:30:00+05:45"
featuredImage:
draft: false
---

When you are working with computers and encounter a problem, there are situations that you don't know where the problem is. If you and your team have written a piece of software, you might figure out what the problem is and patch it up. However, we often encounter issues that we have a solution for, but we don't know the underlying problem. These problems could have come from the code itself, the operating system, the network, an external dependency or anything else. Let's call this problem a __"You Don't Know Problem"__.

There are two ways to go about the "You Don't Know Problem."

## Be happy about the solution without thinking much about the problem

It is ok not to be able to figure out why something is working or vice-versa. It's unreasonable to expect someone to know about a lot of different systems, tool and processes<sup>[[1](https://jvns.ca/blog/learn-how-things-work/#you-can-use-something-without-understanding-how-it-works-and-that-can-be-ok)]</sup>. However, if you keep skipping problems that you don't understand, you'll run into more significant issues further in your learning process. For example, you can't expect a new engineer learning React to know about the virtual DOM. They might have heard about the virtual DOM, but they don't need to understand how the virtual DOM works to write functional React code. Much later in their learning process, they will realize that virtual DOM (or reconciliation<sup>[[2](https://github.com/acdlite/react-fiber-architecture#reconciliation-versus-rendering)]</sup>) is a core React concept that helps in re-rendering apps in a performant way.

## Be curious about why you couldn't figure out the problem

Yes, it's frustrating to be able not to find the root cause of a problem. However, recognizing that you might not know something is also an essential part of the puzzle. To accept that you don't have an answer is the right first step to go ahead and learn about it. Recently, I came across this situation myself, and I'll tell you how I learned to get to the root of the problem.

First, a little bit of context, I have created a [tweet bot](https://twitter.com/bikram_sambat) that tweets a progress bar of the Nepali calendar using [unicode block elements](https://en.wikipedia.org/wiki/Block_Elements). On the first day of the new Nepali year, I noticed something strange with the progress bar. The [filled progress bar](https://twitter.com/bikram_sambat/status/1382034603372908550) had a more considerable length than the one [without progress](https://twitter.com/bikram_sambat/status/1382034607676219392). I had never seen this because it was only happening on my computer. More specifically, it was only happening in macOS. When I checked my phone, the length of both these progress bars was the same.

█████████████████ १००%

░░░░░░░░░░░░░░░ ०%

I didn't know what was causing this. My very first consideration was that it might be some weird Unicode bug. After a few minutes of reading about Unicode Block Elements, I figured out that the width of the █ [full block](https://www.compart.com/en/unicode/U+2588) Unicode character was slightly larger than that of the ░ [light shade](https://www.compart.com/en/unicode/U+2591) Unicode character. I quickly realized that I had to replace the full block character with ▓ [dark shade](https://www.compart.com/en/unicode/U+2593) instead. By this time, I had already figured out the solution without finding the root cause of the problem. I assumed that the problem was with Unicode instead of trying to analyze the situation deeply. I made changes to the [code](https://github.com/mesaugat/bikram-sambat-progress/commit/7bd7490acba4728732e0bde43e13ead9cbca145a) and never bothered looking back.

▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓ १००%

░░░░░░░░░░░░░░░ ०%

Sometimes the problem at hand can trick you into thinking that you are not smart enough. That is what happened here. I felt that I wasn't smart enough to look into the Unicode spec or figure out how Unicode works. After all, learning an entirely new thing often feels overwhelming. It meant that I didn't look into the problem again for a while. Everything was working fine now.

<blockquote class="twitter-tweet"><p lang="und" dir="ltr">▓▓░░░░░░░░░░░░░ १३%</p>&mdash; बिक्रम संवत (@bikram_sambat) <a href="https://twitter.com/bikram_sambat/status/1399229903933947905?ref_src=twsrc%5Etfw">May 31, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

After a month, I decided to learn about Unicode a little bit more so that I had at least some context as to why the Unicode progress bar had varied length despite having the same number of blocks or glyphs. After spending a few minutes reading about Unicode blocks, I came across the wiki page that I had gone through previously. I was so concerned about the width of the full block last time that I didn't even read anything in the wiki. It just seemed like an extensive list of all block characters and nothing else. However, as I scrolled through, I came across the "[Font coverage](https://en.wikipedia.org/wiki/Block_Elements#Font_coverage)" section of the document. Here is what it read:

> "Font sets like Code2000 and the DejaVu family—include coverage for each of the glyphs in the Block Elements range, Unifont also contains all the glyphs. Among the fonts in widespread use, full implementation is provided by Segoe UI Symbol."

The section further talked about supported fonts and how ASCII space may not match the width of the block elements as space character is not part of the block. To this day, it never crossed my mind that a font could have been the reason for all this. Duh! However, I had already found my clue. It was indeed a new direction that my learning was taking me. Now to test what I just learned, I opened Google Docs and put the full progress bar next to the unfilled progress bar and voilà it was a little too obvious. When I chose the system font for macOS, "Helvetica Neue," the progress bars had different lengths, but when I switched the font to "Arial," they had the same width.

__Boom!__

<!-- TODO: Add support for figcaption -->
![Font differences with unicode progress bars](/gifs/what-the-font.gif "Font differences with unicode progress bars")

Furthermore, the [Unicode Consortium](https://unicode.org), in their standard document about [Block Elements](https://unicode.org/charts/PDF/U2580.pdf) states:

> "The shapes of the reference glyphs used in these code charts are not prescriptive. Considerable variation is to be expected in actual fonts. The particular fonts used in these charts were provided to the Unicode Consortium by a number of different font designers, who own the rights to the fonts".

Isn't this statement as clear as daylight? The problem was that the width of the full block glyph in Helvetica Neue was slightly larger compared to Arial. GGs.

![Full block character font comparison](/gifs/full-block-character-comparison.gif "Full block character font comparison")

Often, the assumptions that we make while debugging a problem changes as we go ahead and learn more about it. Problems will look like a black box at first and slowly start to make sense once you connect the dots. This problem was a trivial example of something fundamental that I should’ve known but had to figure out. However, the learning doesn’t end here. I might need to learn about proportional and non-proportional fonts in the future or learn more about typography and how fonts work. All of that wasn’t required this time. As you start to work on complex problems with many integrations, the entropy increases so much that you have to break problems into smaller chunks and understand how things work.

So it goes like this, __"You don't know what you don't know until you figure it out!"__
