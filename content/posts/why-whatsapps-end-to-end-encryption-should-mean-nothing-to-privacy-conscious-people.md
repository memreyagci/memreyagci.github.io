---
title: "Why Whatsapps End to End Encryption Should Mean Nothing to Privacy Conscious People"
date: 2021-06-26T05:47:41+08:00
draft: false
---

Everyone who advocates free & open-source software must have heard "..but WhatsApp is end-to-end encrypted, which means no one, not even Facebook can read them" as a response at least once. While it is true that end-to-end encrypted data can only be decrypted by the two parties holding the keys (assuming there are no flaws in its implementation), I would like to indicate my reasons on why and how your privacy might still be compromised.

<!--more-->

I will start out by explaining a few concepts about software being open or closed source in server-side and client-side. If you think you are tech-savvy enough, you can just skip the part between the dividers and just read the given reasons.

> [Open source](https://en.wikipedia.org/wiki/Open_source) is source code that is made freely available for possible modification and redistribution.

> [Proprietary software](https://en.wikipedia.org/wiki/Proprietary_software), also known as non-free software or closed-source software, is computer software for which the software's publisher or another person reserves some rights from licensees to use, modify, share modifications, or share the software.

To sum it up, unless we see the source code of a software, we can't fully verify whether there is any kind of tracking and data collection.

As in most of the cases, online services such as social platforms, cloud services, etc. have 2 sides whose code we should be concerned about: Client and server.

To put them simply:

* Client is the application on our phone, laptop, or any other device we have for using a service.
* Server is where these services provides connection among users, hosts the content, and so on.

We can categorize these services into 3 groups in terms of being open-source or not:

* Services with open-source client and server.
* Services whose clients are open-source, but their servers are not.
* Services with proprietary (closed-source) client and server.

(Technically there can be a forth option where server code is published but client's is not, but I don't know anyone who does that.)

---

In terms of platforms like Signal, which has its clients' and server's source codes are public, the whole system can be audited, and whether tracking and data collection occurs can be detected. We can say that this is (almost) invasion-proof, though while we can somehow know whether the compiled clients are different than in the source code, we can never really verify if the code they published for the server-side is really what they are running. Thus, decentralization is the best, though it is out of the scope of this post. Regardless, these type of audited platforms are the most preferable among the centralized ones.

As for platforms like Telegram, although we don't know what they do on the server-side, if what is done on client provides us enough trust in terms of seeing what goes out from our device, and in what form (encrypted or not), this might not be that much of a big deal for some.

However, as for completely closed-source platforms like WhatsApp, no matter what kind of security implementations they claim to use, even if they really do so, has no value at all. There are different scenarios that can be thought of:
WhatsApp might be collecting no information at all, all messages are encrypted as they claim.

Although end-to-end encryption is implemented, it is not impossible that an unencrypted version of the data is also sent to their servers. After all, neither the client or the server can be audited, and since the messages are decrypted on our devices so we can read them, sending and unencrypted version off of our devices without no one knowing is quite doable. 

Even if the messages are encrypted, we also cannot know whether metadata is, for the reasons stated above. How valuable metadata is a topic that deserves its own post.

---

I wanted to state my reasons on why WhatsApp cannot be trusted no matter what. If you think there is any mistake in what is written, feel free to [contact me](mailto:meygc@tutanota.com).

[Read more](https://scontent.whatsapp.net/v/t39.8562-34/122249142_469857720642275_2152527586907531259_n.pdf/WA_Security_WhitePaper.pdf?ccb=1-3&_nc_sid=2fbf2a&_nc_ohc=aHysNjhG4xUAX-9J5cp&_nc_ht=scontent.whatsapp.net&oh=5dfeb9fa959d2a6052d64ede3ec33c51&oe=60E4C219) about WhatsApp's end-to-end encryption protocol (which is The Signal Protocol designed by Open Whisper Systems).
