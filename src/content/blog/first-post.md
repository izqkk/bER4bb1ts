---
title: "CTF Writeup | John Doe Strikes Again"
pubDate: 2023-06-25
description: "This is a writeup for the John Doe Strikes Again CTF challenge from n00bzCTF."
tags: ["n00bzCTF", "writeup", "OSINT"]
---

This is a writeup for the **John Doe Strikes Again** CTF challenge from [n00bzCTF](https://ctf.n00bzunit3d.xyz/). I participated in this competition with the **bER4bb1t$** CTF team which was a lot of fun.

---

This OSINT challenge started off a little bit unusual with the XOR decryption.
From the challenge description we got three important pieces of information:

- The encrypted message from our dear John
- The **secret key** we can decrypt the message with
- A username of John

My first thought was decrypting the message using [CyberChef](https://gchq.github.io/CyberChef/) but that just resulted in gibberish.
Luckily I remembered that the `b''` wrapping around the message comes from python and denotes a **byte string**.
So I thought: "Let's make a decode script in python!".
With the power of the internet and my _python skills_ I had a beautiful working script after a few minutes:

```python
def xor_with_key(data, key):
    key_bytes = key.encode()
    xored_bytes = bytes(x ^ key_bytes[i % len(key_bytes)] for i, x in enumerate(data))
    xored_data = xored_bytes.decode()
    return xored_data

data = eval(input(">>"))
key = "YouCanNeverCatchJohnDoe!"
xored_data = xor_with_key(data, key)
print(xored_data)
```

The variable **data** stores the encrypted message from a user input.
After that we call the **xor_with_key** function with the **data** and **key** variable and print the result.

---

**Note:** It's important to use the **eval()** function here because otherwise the user input will be represented as a string and not as the bytes we supply. We also have to supply the whole message including **b''** for it to work properly.

---

When decrypting the message with our little script we get the following:

```
John Doe's Assistant: Hey, John Doe! What you listening to?

John Doe: You know how much I love music so don't ask me that question every again!

```

Let's keep that in the back of our minds and continue with some basic OSINT on the username we were given.

---

The first thought that comes to mind when given a username is using a tool like [Sherlock](https://sherlock-project.github.io/)
to find out if there are any social network accounts present with that username.

```r
$ sherlock 31zdugxvkayexc4hzqhixxcfxb4y
[*] Checking username 31zdugxvkayexc4hzqhixxcfxb4y on:
[+] Enjin: https://www.enjin.com/profile/31zdugxvkayexc4hzqhixxcfxb4y
[+] Quizlet: https://quizlet.com/31zdugxvkayexc4hzqhixxcfxb4y
[+] Spotify: https://open.spotify.com/user/31zdugxvkayexc4hzqhixxcfxb4y
[+] Telegram: https://t.me/31zdugxvkayexc4hzqhixxcfxb4y
[+] authorSTREAM: http://www.authorstream.com/31zdugxvkayexc4hzqhixxcfxb4y/
[+] ebio.gg: https://ebio.gg/31zdugxvkayexc4hzqhixxcfxb4y
[*] Search completed with 6 results

```

John told us he **loves** music so let's check out his spotify account. On it we find a playlist in which description it refers to the playlist image. Through that we know we have to look for John Doe probably on the n00bz CTF discord server.
If we search for the user John Doe on there we can find something in his profile description.
So let's check out his profile on the n00bz CTF website.
The team he's in is another encrypted message that decrypts to:

```
Think Way BackThink Way
```

Kinda weird wording but it's most likely a reference to the [Wayback Machine](https://archive.org/web) so lets check out the n00bz CTF website on there.
After searching for a while we see that there is a is a team website linked from the team John Doe is in.
It links to [https://ctf.n00bzunit3d.xyz/t0ps3cr3t](https://ctf.n00bzunit3d.xyz/t0ps3cr3t) which at first seems empty but on closer inspection hides the encrypted flag.
Decrypting it with the program we made before we get the flag.<br>
Flag:

```
n00bz{n0_0n3_c4n_3sc4p3_MR.051N7,_n0t_3v3n_J0HN_D03!}
```

Thank you for reading.
