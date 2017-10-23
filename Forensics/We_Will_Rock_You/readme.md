# We Will Rock You (Forensics - 100 Points)

> Isn't Queen the best?

Solution
--------

You are given a [zip](wewillrockyou.zip) containing an encrypted flag. The flag can be recovered by bruteforcing the key on the zip archive. The title "We Will *Rock You*" is a hint that the zip can be bruteforced with the rockyou dictionary.

![](./zipcrack.png)

After learning the password is *_purple_* we can then use it to extract the flag.

![](./extraction.png)

The first element is the image of the Wonka bar while the second element is the hidden flag.

Flag: 'GoldenTICKET{Qu33n_roolz_y0#1}'

