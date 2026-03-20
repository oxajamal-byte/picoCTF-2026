# Undo — 100 pts

**Category:** General Skills  
**Author:** Yahaya Meddy

## Overview

The challenge drops you into a server and asks you to reverse some Linux text transformations. The only hint is to look into the `tr` command.

## Approach

Connected to the server with netcat and saved the output to a file so I could mess with it without reconnecting every time:

```bash
nc foggy-cliff.picoctf.net 62188 | tee output.txt
```

The output was garbled text. Clearly some kind of character substitution happened to it. Since the hint pointed directly at `tr`, I started with the most common reversible transformation in CTFs which is ROT13. ROT13 shifts every letter 13 positions in the alphabet and the nice thing about it is that applying it twice gives you back the original:

```bash
cat output.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

That was it. Flag came right out.

## Takeaway

The `tr` command looks basic but it shows up all the time in CTFs and in real sysadmin work. ROT13 is always worth trying first when you see character substitution because its its own inverse. Also got into the habit of saving server output to a file before working on it which saved me a lot of time on later challenges.
