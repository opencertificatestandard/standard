# Open Certificate Standard

A format for property compliance certificates that anyone can check, not just
the software that made it.

**Status: draft, published for comment.** No standards body has adopted it.
Nobody else has implemented it yet.

- [The specification, version 1](site/v1/index.html)
- [Test vectors](site/v1/test-vectors.json)

## The problem

Gas safety records, electrical condition reports and boiler service
certificates all get relied on later, by letting agents, councils, insurers and
courts. Each one is a PDF built by one company's software, in that company's
own shape.

You cannot ask a question of it. You cannot check whether the landlord's
address is really there, or whether someone typed "as above". You cannot tell
whether the file in your hand is the file that was issued. If a landlord
changes software, the data does not go with them.

## What it defines

**A record.** A small JSON object holding the details the law asks for: the
property, the landlord, the engineer, the date, and what was found. No company
name appears anywhere in it.

**A way to hash it.** RFC 8785 canonical JSON, then SHA-256. The rules are
tight enough that two separate implementations produce the same hash from the
same record.

**Checks you can run.** Conformance at three levels, each check written so
software can decide it. A check that needs a human opinion does not belong
here.

## The document is not the record

If a certificate's identity is the bytes of one PDF, that PDF becomes the legal
record. The layout can never be corrected. The software is stuck on the same
PDF library forever. A document with a real mistake in it cannot be fixed,
because fixing it makes it look tampered with.

If the identity is the data, none of that applies. A PDF, a web page and a
printout of the same record are all equally valid. None of them is the record.

## Test vectors

Nine vectors, generated from a running implementation rather than written by
hand, so a vector cannot drift from what the code does. Each carries the
record, its canonical string and its hash. They cover the cases where
implementations disagree.

| Vector | What it catches |
|---|---|
| `minimal` | The baseline. |
| `key-order-does-not-matter` | Same hash as `minimal`. A locale-aware sort fails this. |
| `absent-null-empty-and-blank-are-the-same` | Same hash as `minimal`. One system writing `""` where another leaves the key out. |
| `a-blank-item-is-not-no-items` | Dropping a blank entry renumbers every item after it and attaches a defect to the wrong appliance. |
| `no-and-zero-are-answers` | Treating `false` as absent erases "no" from a safety record. |
| `unicode-is-normalised-to-nfc` and `unicode-composed-twin` | A pair that must hash identically. Skip normalisation and each one passes alone while the pair fails. |
| `let-premises-with-a-duty-holder` | A complete Level A record. |
| `a-reissue-supersedes-its-predecessor` | A correction is a new record naming its predecessor's hash, never an edit. |

**How to use them.** Canonicalise the record and compare with `canonical` byte
for byte. Then SHA-256 the UTF-8 bytes of that string and compare the lowercase
hex with `hash`. Compare the canonical string first. A mismatch there points at
one serialisation rule. A mismatch on the hash alone only tells you something
differs somewhere.

## Status and governance

Draft, version 1, published for comment. No standards body has adopted it.

Written by [CertBox](https://certbox.app), which makes certificate software for
tradespeople. The working implementation came before the specification.

Authorship is not ownership. The licence permits anyone to implement, fork or
extend it, no company name appears in the format, and long-term governance is
one of the open questions in the specification.

## Conformance is measured

Anyone can claim to follow a standard. This one is built so the claim can be
checked by whoever receives the certificate. A conformance claim states the
version, the level, the certificate types it covers, the date, and who is
making it, and each of those can be tested against real documents.

Published figures are expected to be partial at first. The implementation
behind this draft covers 273 certificate types, of which **121 currently
produce a Level A record**. The remainder are held back by fields their forms
do not yet collect, mostly an outcome or a defect recorded against each
individual item.

Suppliers adopting the standard are encouraged to publish their own figures the
same way, including the gaps. A conformance level that only ever goes up tells
a landlord nothing.

## Comment

Wanted from other software companies, letting agents, and anyone working on the
private rented sector database. The open questions are listed at the end of the
specification. They are real questions, not rhetorical ones.

[Open an issue](https://github.com/opencertificatestandard/standard/issues), so
other implementers can read the answer and argue with it. Or write to
<support@certbox.app>.

## Licence

Specification text: [CC BY 4.0](LICENSE). Test vectors, reference checker and
tooling: [MIT](LICENSE-CODE). Copying the vectors into your own test suite
should not saddle your test files with attribution notices.
