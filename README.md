# Intermail

A 21st century SMTP replacement.

## Why?

* SMTP must die
* millions of emails containing private information are sent daily
  * all sorts of access tokens including actual passwords
  * transactional emails that enable social engineering hacks
  * confidential business and private information
  	* *but, my business email signature clearly says the information is confidential*
* implementing PGP properly is a pain with current system

## Goals

### Easy to use

* for webmasters
  * ORM-like layer to translate regular email data into intermail objects
  * easy to install and manage, works well along SMTP
  * choose which features to install
* for devs
  * fucking sweet API
* for users
  * intermail doesn't attempt to deliver user email at the moment, it's only a transport
  mechanism between email providers

### Secure

* TLS
  * prevents MiTM data interception
  * prevents DNS spoofind attacks
  * problem: TLS keys stolen
  	* option: using PGP, only the metadata is leaked (as is with SMTP)
	* option: OTR-style session keys (bonus: forward secrecy)
* PGP
  * public keys for <user@domain.com> can be obtained by simply asking mx(domain.com) for them
  * there is only one authorative source for public keys: the domain that actually handles email for a particular user
  	  * example: <user@domain.com> will be handled by domain.com
	  * example: <user@user.me> will be handled by domain.com, because the MX of user.me points to domain.com
  * domains have a primary key store for their own users (and hosted users, see above) and a secondary store that acts like a cache for themselves, and that can be made public
* spam
  * whitelist/blacklist for domains
  * difficult, since now spammers can send totally opaque emails
  	* http://blog.cloudmark.com/2014/11/13/spam-filtering-for-dime-encrypted-email/
* DDoS
  * huge messages, a lot of requests
* improving email providers security
  * out of the scope of intermail, but it's an easy target for attackers that renders intermail's security benefits moot
  * publish best practices for storing email
  * publish pgp and zero knowledge libs
  * publish automated solutions for setting up a server securely
  * ultimately, publish better oss alternatives to dovecot, iredmail, etc.
 
### Fast

SMTP is a chatty, primitive protocol.

* fast protocols: HTTP/2, SPDY, ws, etc.
* efficient encodings: protobuf, msgpack, capnproto, thrift, etc*
* optimising transport
  * request batching (or persistent connection - wild idea)
  * files and email body sent separately
  * payload compression (unless built in at a lower level)

\* - fun! [http://leopard.in.ua/2013/10/13/binary-serialization-formats/]()

### PGP-enabled

As mentioned previously, if PGP is enabled on the receiving side, the public keys
can be obtained seamlessly from the primary key store.

The approach is *way* simpler than `google/end-to-end` or DIME, because it's based on
a simple observation:
> if you don't trust your email provider to hold your public keys reliably, why do you
trust it with your private information?

## Secondary goals

* minimize user error
* maximize adoption
* leverage existing architecture
  * don't reinvent the wheel, especially if it's a *crypto* wheel
  * relies on TLS, PGP, MX and PTR records, etc. to do the heavy lifting

## Misc

* assume TLS is broken, server is hacked, users/devs/webmaster are complete morons
* marketing wins protocol wars (pond, anyone?)

### Attention needed

* compatibility tables
* fucking good object design for emails
* reference implementation (go/node/python + major languages - *yes, PHP included*)
* tooling: nginx/apache plugins, docker/lxc/apt/yum/pacman/brew/npm/cargo/gem/etc packages

### Marketing

* start with PGP startups, show it works in the real world
* approach celebrities in the field (moxie, igrigorik, tcpatek, cperciva, etc)
* research better algorithms to use
* optionally, integrate with `end-to-end` or `dime`
* focus on the community (bullshit vague idea)

