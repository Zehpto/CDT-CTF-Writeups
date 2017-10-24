# How can you still be hungry? (Crypto - 300 Points)

> I29hATEfEToqz2E0v3wownEuzTUpC29fAE95ACMuBmE0C3wlBCIuBDQewCIux2Eqy25pC2Iox3MoxmI9
> Format: GoldenTicket{xxxxxxxx}

Solution
--------

This problem uses the baconian cipher. Since the cipher uses only 'A' or 'B' we know either the captial letters are 'A's or 'B's. In the challenge, the capitals were 'A's. We can then convert the ciphertext to it's A/B binary form.

```
AABBAA BB BAABABB AAA BB AABAAAB BABBA ABBABAAAA AABA ABA BAAAB AABAAB BA AAABAAA AAAAA BAABBB AABBAB AA AAAAB BAB AAA BBAAAB AAABBBA BB BA AAABAAB BB AA BABBAAA AA B AABBAABAA AAAAABAB BAA B AAAAB BA BABBA BA BAAAABB AB AAB BAAAAB AABB BA ABBAAAA AAB ABA AAABB AB AAAA ABAABBAB AA AAB BBAABB ABAABA BABBBA BAA ABB AABABA BAAB AA ABAABAA
```

From here, an online decoder such as [Rumkin](http://rumkin.com/tools/cipher/baconian.php) can be used.

![](./baconian_decode.png)

We then get the following:
> GOL D ENTIC KETBACON AN DCHO COLA TE AWINNIN GCO MB IN ATIONFO RSURE

For the last step, the flag needs to be formatted.

Flag: 'GoldenTicket{weve_had_one_yes_but_what_about_second_dessert}'

