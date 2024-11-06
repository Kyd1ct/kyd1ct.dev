---
title: BSides STPete24
date: 2024-10-24
draft: false
author: Kyd1ct
description: How I created two steganography and memory forensics challenges for the BSides StPete CTF
---

## Introduction
Back in September 2024, BSides St. Petersburg took place. A friend of mine hosted the CTF and asked me if I wanted to participate by creating a few challenges. I ended up creating two forensics-based CTF challenges.

1. Cache Me If You Can
    - This challenge highlights the complexity of Windows RAM data and teaches participants how much information can be extracted using memory forensics. By working through this task, players will dive into tools like Volatility to uncover hidden data within volatile memory.
2. Greg's Cryptic Thumbs-Up
    - A multi-layered steganography challenge where players must find a hidden link within a GIF, which leads to a PDF file containing another concealed message. This exercise pushes participants to think outside the box and explore different techniques for uncovering hidden information within files.

## Challenges
### Cache Me If You Can
As mentioned, this challenge focused on RAM forensics and aimed to teach the participants about how valuable volatile data can be for an investigation. I decided to focus on the following:
- Split the flag in two halves and hide them in separate places.
- Encourage creativity - I used a variety of file names and tools to insert the data in memory.
- Environmental variables.
- Web browser history.
- Encryption of the flag.

I started by creating a long flag. I first encoded it in Base64, then encrypted it using AES. The result was split in two halves.  
After the flag halves were done, I booted my VM and created multiple files - Notepad, Notepad++, vim. I opened them together with some other files in an attempt to trick the users. One of the files was named with the first half of the flag, while the other two contained either hints, or added as a deception.

After this part was done, I grabbed the key used during the encryption, split it in half again, and added two separate environmental variables.  

The second half of the flag was located within a PasteBin link and the participants had to extract it from a Google Chrome process dump. I opened multiple links to make it trickier for the participants if they did not consider looking for PasteBin.

Once the AES key and both halves of the flag were identified, the participants could use [CyberChef](https://gchq.github.io/CyberChef/) to decrypt it and win the challenge.

The challenge can be accessed in my [GitHub](https://github.com/Kyd1ct/BSides-StPete-2024/tree/main/Cache%20Me%20If%20You%20Can) page, together with a full walkthrough.

### Greg's Cryptic Thumbs-Up
This challenge featured multi-layer steganography, attempting to challenge and trick the users. I decided to focus on the following:
- Hide data within a GIF.
- GIF leads to a PDF with a hidden message - maybe it was "printed"?
- Decode the hidden message by hand or by using a toolkit.

The idea of this challenge was to be simple, yet pose a challenge to users who have not previously dealt with printer dots.

As mentioned, the first part of the challenge was hidden data within a GIF. I split the GIF into frames and edited parts of a link (split into 4 parts) into those frames. The colour of the text was purposefully made to blend into the background. This to examine it frame by frame to obtain the link. 

The link lead to a PDF file with a hidden message. Once the participants zoomed in, they could see the tiny dots that printers usually include. The dots were placed on the PDF using the [Deda Toolkit](https://github.com/dfd-tud/deda). Participants could use the same toolkit to decode the data, or they could do it by hand if they had enough experience.
The message hiddin in the dots contained the flag which consisted of date, time, and a serial number - information which is included by printers on each sheet of paper. 

Once again, the challenge can be accessed in my [GitHub](https://github.com/Kyd1ct/BSides-StPete-2024/tree/main/Greg's%20Cryptic%20Thumbs-Up) page together with a full walkthrough.