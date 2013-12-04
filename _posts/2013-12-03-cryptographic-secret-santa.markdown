---
layout: post
title:  "Cryptographic Secret Santa"
date:   2013-12-03 18:51:47
categories: cryptography
---

## Abstract

One of the most popular holiday games, Secret Santa (also known as Kris Kringle
in some circles) is a fun and easy way for a group of friends, family members
or co-workers to exchange gifts.

The basic concept of the Secret Santa game is simple. All of the participants'
names are placed into a hat, box, etc. and mixed up. Each person then chooses
one name from the box, but doesn't tell anyone which name was picked. He/she is
now responsible for buying a gift for the person selected.

From this point, exists variations on the game. One being a random person
starting by revealing who his/her gift is for, and then subsequently. Another
variation is for each gift being labeled and each person in turn tries to guess
his/her Secret Santa.

This article focus on the first part of the game: the attribution of the Secret
Santas. I present a way to make this happen remotely without having to trust a
central choosing authority, nor the network, by using asymmetric key encryption
and signing.

In this text, we will refer as *Secret Santa* the person who is responsible for
buying a gift for his/her *secret friend* (the person whom shall receive the
gift for his/her Secret Santa).

## Goals

### Attribution of Secret Santa without a trusted central authority

It would be trivial to solve the remote attribution of Secret Santa if each
participant would send his/her credential to a central authority, and in turn,
this authority will randomly assign the Secret Santas, revealing only to the
Secret Santas who they should buy a gift for (the secret friend).

The problem with this approach is that the participant should trust that the
central authority will not reveal his/her secret friends to others - willfully
or accidentally, since this information exists at some point in the random
selection.

This is specially critical if the central authority is managed by one of the
participants.

### Attribution of Secret Santa sequence with a single loop

This is very desirable for a more pleasant game. When the first participant
start revealing his/her secret friend, we want the subsequent revelations to
cover all participants in a single chain until reaching the first again.
Without closing the loop before everyone was revealed.

In the later case, it would cause an interruption in the game and the group
will have to randomly pick someone again to "start over" the game.

This problem is hard to solve if using paper names in a hat, but is easily
solved algorithmically: the sequence of attributions can be a random
permutation of the participant's names, where the last is the Secret Santa of
the first (forming a closed ring).

### Don't trust the network or the participants

Don't trust that the network is safe, and also, don't trust that the
participants won't try to peek who's his/her Secret Santa or someone else's.
Each participant must be allowed to only know who is his/her secret friend, and
nothing more.

## Mechanism

### Initial agreements

Each participant chooses a name for himself/herself. This is a legible,
identifiable, and unique name for everyone else in the group. Most likely is
your first name.

Also, each participant chooses two cryptographic key pairs:

  * "identity key pair" (used for signing)
  * "anonymous key pair" (used for encryption)

### Publishing

Being chosen the keys, the participants publish their "identity public keys"
associated with their "identity names" - so everyone knows how to verify any
message signed by you.

Also, the participants publish their "anonymous public keys" anonymously. This
is important. The others should not be able to associate you with the
anonymously published public key.

To make sure this process is correct where each participant have one and only
one anonymous public key published. The number of keys should be equal to the
number of participants, and each one should confirm that he/she has published
the key and it's being listed (never pointing out which one is it though).

At the end of the publishing we should have an associated list with the
identity public keys and the participant names, and a separate list of
anonymous public keys that only each participant know which one is his/her
public key, but not the others.

Finally, the participants must agree on a unique permutation of the anonymous
published public keys. This can be done in several ways (_e.g._ by agreeing on a
random seed and shuffling algorithm, or let someone to do the shuffling).

The important thing is that this permutation will be the Secret Santa sequence,
i.e. each participant that owns the public key is the Secret Santa of the
immediately followed public key - the last one is the Secret Santa of the first
(forming a closed ring). A permutation guarantees the nice property that the
revelation game will end in a single loop.

### Secret message

Each participant, then, knows where he/she is on the list, but doesn't have a
clue of who is his/her Secret Santa or secret friend.

At this step, each participant will create an encrypted message that can only
be decrypted by his Secret Santa (thus revealing to he/she who's is his/her
secret friend).

The message should be signed by the participant, so the Secret Santa will know
it's trusted, and not someone trying to cheat by going as another participant.
The computation of the message must have this format:

    ENCRYPT(X, NAME : SIGNED(Y, NAME) : SALT)

Where

  * `X` is the participant's preceding anonymous public key in the list (i.e.
    the participant's Secret Santa's anonymous public key).
  * `NAME` is the agreed participant's identity name.
  * `Y` is the participant's identity private key.
  * `SALT` is a random salt, using to make the message cryptographically
    stronger.

The secret message is published (it doesn't matter if it's anonymous or not).

### Reading the message

After everyone publishes their encrypted secret message, each participant,
using his anonymous private key, tries to decrypt all the other participant's
messages. The message publishing should be directed to the entire group, as to
not rise any suspicions.

If one succeeds (_i.e._ there's a readable name in it) then, one validates the
signed part by applying the corresponding name's public identity key and
verifying that the enclosing signed message is the correct name.

That way, the participant will know who is his/her secret friend, and will be
also confident that no one else is able to know this information.

## Cheating

If a cheating participant decides to encrypt his/her message with an anonymous
public key that is not preceding himself/herself in the list (_i.e._ not his/her
Secret Santa), then the result of this is that the correct Secret Santa will
not be able to decrypt any message, and, someone else will be getting two
secret friends.

In this case, the cheating participant will be known once everybody proves
which is each one's public keys, and the game - since compromised - should be
started over.

Due to cryptographic signing, there's no way for a participant to go as someone
else. If one attempts to, the signing verification will fail.

Also, it's cryptographically hard for someone else to read other messages
despite the one written by his/her secret friend.

## Example

Let's say we have "Alice", "Bob", "John" and "Mary" as participants. And we're
going to look at "Alice" perspective for this example.

Alice's compute it's key pairs:

Anonymous key pair:

  * Private: `a97be0dddedae7ee2c3b0606ac5d847c`
  * Public: `323821846e4bb6c77879883d308f559a`

Identity key pair:

  * Name: "Alice"
  * Private: `d673da77a11b01fecfcd0249a3590eb2`
  * Public: `222af26396887e2b0b498545e2e4ae06`

They all publish the following public anonymous keys (of course we don't know
the others, only which one it's Alice's):

  * `c49f0091291832e94d0f8594bbc170b5`
  * `323821846e4bb6c77879883d308f559a` (Alice's)
  * `9c17f423629c2ef27f84621acbde7d38`
  * `e86e0c29f3750f654dcc9f5ec266fb07`

They also publish their identity public keys associated with their names:

  * `222af26396887e2b0b498545e2e4ae06` Alice
  * `00f1a42f5d610acb29c1f26761f52b34` Bob
  * `666d25b7170206ef3ce8ad1ea10332e6` John
  * `514c3e02da64796e96db9a3cc5ee3594` Mary

In this game, they decide to let Mary do a shuffling (using her preferred
shuffling program) and she comes up with the following list, now official:

  * `e86e0c29f3750f654dcc9f5ec266fb07`
  * `9c17f423629c2ef27f84621acbde7d38`
  * `323821846e4bb6c77879883d308f559a` (Alice's)
  * `c49f0091291832e94d0f8594bbc170b5`

Now Alice creates it's secret message to the predecessor (her Secret Santa):

    ENCRYPTED(<Alice's preceding anonymous public key>,
              "Alice" : SIGNED(<Alice's private identity key>, "Alice") : <random salt>)
        =>
    ENCRYPTED("9c17f423629c2ef27f84621acbde7d38",
              "Alice" : SIGNED("d673da77a11b01fecfcd0249a3590eb2", "Alice") : "afe62bad180")

Only the participant that owns `9c17f423629c2ef27f84621acbde7d38` corresponding
private key will be able to read Alice's secret message, and then, he/she will
see the starting "Alice", so he/she knows Alice is the secret friend. But just
to be sure this message is really from Alice, he/she takes Alice's identity
public key and uses to read the signed "Alice" name - confirming her identity.

![Composing of Alice's Message](/images/alices-message.png?raw=true)

See the figure for a overview on how Alice may compose her secret message and
publish:

  1. First, she takes her public identifier "Alice" (her name), and sign
     using her identity private key.
  2. Then, she takes the immediately preceding anonymous public key from her
     own key in the agreed permutation (her Secret Santa) and uses it to
     encrypt the signed message. Note that since the key was published
     anonymously, she don't know the identity of her Secret Santa.
  3. It turns out that Bob is Alice's Secret Santa. So he is the only one
     able to decrypt the secret message (using his anonymous private key).
  4. He then see that the message was signed by Alice, and he then takes
     Alice's public identity key and uses it to verify the signature (reading
     her name) and confirming tha she is his secret friend.

## Implementation

Although it's feasible for this mechanism, being used by a group of hacker
friends, to be implemented with command line tools (`pgp`, `ssh-keygen`) and
email lists for publishing - it's very desirable to have some sort of
application (web application) to make it easier for non hacker friends to join
the game.

Nevertheless, by using a web application, participants will have to have a
certain level of trust for some convenience.

The description presented in this article is in a high level language. When
implementing, two details may seem important to take into consideration:

  * Each message may have a starting code to identity that the decryption was
    successful - so we can trust an algorithm to find the secret friend.
  * The signing and encryption algorithms may produce a message that also
    tells which algorithm was used.

