---
layout: post
section-type: post
title: Bit-flipping attack on CBC block cipher mode 🗡
category: tech
tags: [ 'crypto', 'redteam' ]
---

Continuing in the theme of attacking crypto primitives, today we'll see how
easily we can pull off a bit-flipping attack on CBC block cipher mode. I'll assume
that you understand how CBC works (if not, you can read the post on [Padding Oracle attack]({% post_url 2017-05-29-padding-oracle-attack %})).

Let's have a look at the decryption of CBC:

![CBC](https://upload.wikimedia.org/wikipedia/commons/6/66/Cbc_decryption.png)

If you notice, each ciphertext block is used to XOR the output of the
next block. This is done in order to recover the final plaintext of that block, since
the Encryption algorithm is chaining the blocks in order to scramble the end ciphertext.
If that process didn't take place, then you would end up with a 1-1 correlation between
the plaintext byte and the ciphertext byte, leaking a lot of information about the plaintext.

Here is an example of an original image and its encryption without chaining the blocks:

![plaintext](https://upload.wikimedia.org/wikipedia/commons/5/56/Tux.jpg)

![ciphertext](https://upload.wikimedia.org/wikipedia/commons/f/f0/Tux_ecb.jpg)

Hopefully now you can visualize what *"leaking a lot of information"* means.

Back to the decryption diagram. By flipping a bit in a ciphertext block, we can flip
the output plaintext bit of the next block.
Why?
Because XOR is an associative bit operation, and XOR-ing with '1' changes a '0' to '1' and vice versa, by the definition of XOR.

So far so good, but what could go wrong by flipping a bit in the end plaintext?
A lot!
Imagine a server returning to the client the following cookie (i.e.) encrypted:

"admin=0"

First mistake, has already been made, by giving control of authorization to the client.
The client can manipulate all the information it controls, ciphertexts included.
Now by flipping the 7th byte of the IV (since the output ciphertext in this case is only two blocks long) we
will manage to flip the 7th bit of the first block of the plaintext that the server will compute, in
this case the result will be:

"admin=1"

Aha! In case the server doesn't authenticate the ciphertext that we submit (by using a MAC) and doesn't perform
any additional authorization control on its side then we will escalate vertically ourselves to an admin!

Let's see how simple this attack is:

<script src="https://gist.github.com/le4ker/2eceadbd3f64bf62d252f720bbb226d3.js"></script>

You might think that flipping just a bit is easy, but flipping more bits, in order for example
to transform a "false" to "true" is equally easy, since all you need to do is to simply flip more bits.

A mitigation in this case would be to use the MAC-then-encrypt scheme, or simply
use a block cipher mode that provides authentication out of the box.

The most important learning from this attack is that user input *should never be processed without
being authenticated first*, which is part of Moxie Marlinspike's [Cryptographic Doom Principle](https://moxie.org/blog/the-cryptographic-doom-principle/).
