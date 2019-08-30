# AEX 1

```
AEX: 1
Title: AEX process
Author: Sascha Hanse <shanse@posteo.de>
License: CC0-1.0
Discussions-To: https://forum.aeternity.com/t/aeternity-best-current-practices-aexpansions/2461
Status: Review
Type: Meta
Created: 2019-02-01
```


## Simple Summary

This document describes the process a proposal has to go through in order to
be accepted into the AEX repository.


## Motivation

The purpose of AEXs is to provide high quality and accessible specifications
to be used when developing applications on top of Aeternity.
We intend AEXs to be the primary mechanisms for proposing new standards, for
collecting community technical input on an issue, and for documenting the
design decisions. Because the AEXs are maintained as text files in a versioned
repository, their revision history is the historical record of the feature
proposal.


## Specification

### AEX Types

There are three types of AEXs:

- A **Standards Track** AEX describes any change or addition that affects the
  interoperability of applications using Aeternity. Standards Track AEXs consist
  of two parts: a design document and a reference implementation.
- An **Informational** AEX describes a design issue, or provides general
  guidelines or information to the Aeternity community, but does not propose a
  new feature. Informational AEXs do not necessarily represent an Aeternity
  community consensus or recommendation, so users and implementors are free to
  ignore Informational AEXs or follow their advice.
- A **Meta** AEX describes a process surrounding AEXs or proposes a change to
  (or an event in) a process. They often require community consensus; unlike
  Informational AEXs, they are more than recommendations, and users are
  typically not free to ignore them. Examples include procedures, guidelines,
  changes to the decision-making process, and changes to the tools or
  environment used in Aeternity development.


### Workflow

Parties involved in the process are you, the champion or AEX author and the
AEX editors.

⚠️ Before you begin, vet your idea, this will save you time. Ask the Aeternity
community first if an idea is original to avoid wasting time on something that
will be be rejected based on prior research (searching the Internet does not
always do the trick). It also helps to make sure the idea is applicable to the
entire community and not just the author. Just because an idea sounds good to
the author does not mean it will work for most people in most areas where
Aeternity is used. Examples of appropriate public forums to gauge interest
around your AEX include the [Aeternity forums](https://forum.aeternity.com)
and the issues section of this repository. In particular, the issues section
of this repository is an excellent place to discuss your proposal with the
community and start creating more formalised language around your AEX.

Your role as the champion is to write the AEX using the style and format
described below, shepherd the discussions in the appropriate forums, and build
community consensus around the idea.


#### Stages

| Stage | Purpose | Entrance Criteria | Changes Expected |
|:---|:---|:--|:---|
| Draft | rapid iteration in small/focused working group | none | major |
| Review | review and feedback from editor and other interested parties | initial specification, editor assigned, template filled, authors and editor consider document to be in a state where the general public can give constructive feedback | no major TODOs left |
| Last call (yyyy-mm-dd to yyyy-mm-dd) | indicate that authors and editors consider document to be complete; solicit last round of feedback | at least one working implementation (if applicable), authors and editors deem the proposal complete | minor |
| Final | proposal is considered to be completed | no unaddressed substantiated objections are left | crucial or cosmetic updates only |
| Active | proposal can be in constant flux | no unaddressed substantiated objections are left | crucial or cosmetic updates only |
| Updated | signalling that an updating standard might have to be considered | an updating proposal entered the `Final` state | crucial or cosmetic updates only |
| Superseded | standard should no longer be used | community consensus around a superseding proposal | none |
| Rejected | editors deemed this proposal to be unworkable | proposal was rejected before, authors were unwilling to respond to feedback, too unfocused, too broad, duplication of effort, being technically unsound, not providing proper motivation or addressing backwards compatibility | none |
| Withdrawn | signalling that the proposal is no longer relevant | withdrawn by original authors | none |


```
+---------------+             +---------------+             +---------------+
|     Draft     |------------>|     Review    |<----------->|   Last call   |
+---------------+             +---------------+             +---------------+
       ^      |                 |     ^                       |   |   |   |
       |      v                 |     |                       |   |   |   |
       |  +---------------+     |     |                       |   |   |   |
       |  |   Withdrawn   |<----+-----|-----------------------+   |   |   |
       |  +---------------+           |                           |   |   |
       v                              |                           |   |   |
+---------------+                     |                           |   |   |
|    Rejected   |<--------------------+---------------------------+   |   |
+---------------+                                    -----------------+   |
                                                     |                    |
                                                     v                    v
          +---------------+             +---------------+    +---------------+
          |    Updated    |<----------->|     Final     |    |     Active    |
          +---------------+             +---------------+    +---------------+
                  |                             |
                  +----------+       +----------+
                             |       |
                             v       v
                         +---------------+
                         |   Superseded  |
                         +---------------+
```

Each status change is requested by the AEX author and reviewed by the AEX
editors. Use a pull request to update the status.

AEXs should be changed from `Draft` or `Review` status, to `Rejected` status,
upon request by any person, if they have not made progress in three years. Such
a AEX may be changed to `Draft` status if the champion provides revisions that
meaningfully address public criticism of the proposal, or to `Review` status if
it meets the criteria required as described in the previous paragraph.

The transition from `Last Call` to either `Final` or `Active` happens
automatically if during the last call period, typically 14 days, no
substantiated objections are voiced or left unaddressed.

A `Last Call` which results in material changes or substantial unaddressed
technical complaints will cause the AEX to revert to `Review`.


### Transferring AEX Ownership

It occasionally becomes necessary to transfer ownership of AEXs to a new
champion. In general, we'd like to retain the original author as a co-author
of the transferred AEX, but that's really up to the original author. A good
reason to transfer ownership is because the original author no longer has the
time or interest in updating it or following through with the AEX process, or
has fallen off the face of the 'net (i.e. is unreachable or isn't responding
to email). A bad reason to transfer ownership is because you don't agree with
the direction of the AEX. We try to build consensus around an AEX, but if
that's not possible, you can always submit a competing AEX.

If you are interested in assuming ownership of an AEX, send a message asking
to take over, addressed to both the original author and the AEX editor. If
the original author doesn't respond to email in a timely manner, the AEX editor
will make a unilateral decision (it's not like such decisions can't be reversed :).


### Editors

For each new AEX that comes in, an editor does the following:

- Read the AEX to check if it is ready: sound and complete. The ideas must
  make technical sense, even if they don't seem likely to get to final status.
- The title should accurately describe the content.
- Check the AEX for language (spelling, grammar, sentence structure, etc.),
  markup (Github flavoured Markdown), code style

If the AEX isn't ready, the editor will send it back to the author for
revision, with specific instructions.

Once the AEX is ready for the repository, the editor will:

- Assign an AEX number (generally the PR number or, if preferred by the
  author, the issue # if there was discussion in the issues section of this
  repository about this AEX)
- Merge the corresponding pull request
- Send a message back to the AEX author with the next step.

In general, the editors:

- Don't pass judgment on AEXs.
- Are intended to fulfil administrative and editorial responsibilities.
- Monitor AEX changes, and update AEX headers as appropriate.


#### List of editors

- Sascha Hanse (@knarz)
- Philipp Piwowarsky (@thepiwo)


### Format

Successful AEXs should contain most of the following sections:

- **Preamble**: RFC 822 style headers containing metadata about the AEX
- **Simple Summary**: a simplified and layman-accessible explanation of the
  AEX
- **Abstract**: a short (~200 word) description of the (technical) issue being
  addressed
- **Motivation**: clearly explaining why the existing standards are inadequate
  to address the problem that the AEX solves
- **Specification**: should describe the syntax and semantics of any new
  feature and should be detailed enough to allow competing, interoperable
  implementations
- **Rationale**: fleshes out the specification by describing what motivated
  the design and why particular design decisions were made. It should describe
  alternate designs that were considered and related work, e.g. how the
  feature is supported in other languages. The rationale may also provide
  evidence of consensus within the community, and should discuss important
  objections or concerns raised during discussion.
- **Backwards Compatibility**: if a proposal is supposed to supersede another
  proposal without providing backwards compatibility then it should contain a
  section describing these incompatibilities and their severity. Further, it
  should explain how the author proposes to deal with these incompatibilities.
- **Test Cases**: links to test cases if applicable
- **Implementations**: implementation or link to implementations, if
  applicable

A AEX template can be found under `AEX-X`.


#### Preamble

Each AEX must begin with an RFC 822 style header preamble. The headers must
appear in the following order. Headers marked with "*" are optional and are
described below. All other headers are required.

```
AEX: <to be assigned by editors>
Title: <AEX title>
Author: <a list of the author's or authors' name(s) and/or username(s), or name(s) and email(s), e.g. (use with the parentheses or triangular brackets): FirstName LastName (@GitHubUsername), FirstName LastName <foo@bar.com>, FirstName (@GitHubUsername) and GitHubUsername (@GitHubUsername)>
License: <license names, abbreviated>
License-Code (*optional): <license names, abbreviated>
Discussions-To: <URL>
Status: <Draft | Active Review | Last Call (yyyy-mm-dd to yyyy-mm-dd) | Final | Updated | Superseded | Rejected | Withdrawn>
Type: <Standards Track | Informational | Meta>
Created: <date created on, in ISO 8601 (yyyy-mm-dd) format>
Requires (*optional): <AEX number(s)>
Replaces (*optional): <AEX number(s)>
Superseded-By (*optional): <AEX number(s)>
Updates (*optional): <AEX number(s)>
Updated-By (*optional): <AEX number(s)>
```

Header fields permitting lists must separate elements with commas.

The `Discussions-To` field should point to a discussion of the proposed
standard. Most of the initial work should happen in small, focused working
groups and only posted once it is in a reasonably stable state.
Please try to refrain from having WIP documents or very early drafts in this
repository as they tend orphan.

Each proposal must start in the `Draft` state and will move through the
phases in accordance to the criteria defined in this document.

If a proposal depends on another AEX, the `Requires` field should indicate
so.

`Superseded-By`, `Replaces`, `Updates` and `Updated-By` fields are required
since standards are in constant flux. Editors and authors should make sure
that old AEXs are update where appropriate.


#### Copyright

The following recommended licenses should be used both for code and the
specification:

- [BSD-2-Clause](https://opensource.org/licenses/BSD-2-Clause)
- [BSD-3-Clause](https://opensource.org/licenses/BSD-3-Clause)
- [CC0-1.0](https://creativecommons.org/publicdomain/zero/1.0/)
- [GNU-All-Permissive](http://www.gnu.org/prep/maintain/html_node/License-Notices-for-Other-Files.html)

Each submitted proposal needs to list at least one license. If code is
included, a separate license for it can be specified. The licenses should
be included in the header via the `License` and `License-Code` fields. If
those fields do not cover the requirements, include a copyright section in
your proposal.

```
License: CC0-1.0
License-Code: Apache-2.0
```

Or with multiple licenses:

```
License: CC0-1.0
         BSD-3-Clause
License-Code: Apache-2.0
```

Other acceptable licenses:

- ISC: [Internet Systems Consortium License](https://www.isc.org/downloads/software-support-policy/isc-license/)
- Apache-2.0: [Apache License, version 2.0](http://www.apache.org/licenses/LICENSE-2.0)
- MIT: [Expat/MIT/X11 license](https://opensource.org/licenses/MIT)
- AGPL-3.0+: [GNU Affero General Public License (AGPL), version 3 or newer](http://www.gnu.org/licenses/agpl-3.0.en.html)
- FDL-1.3: [GNU Free Documentation License, version 1.3](http://www.gnu.org/licenses/fdl-1.3.en.html)
- GPL-2.0+: [GNU General Public License (GPL), version 2 or newer](http://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html)
- LGPL-2.1+: [GNU Lesser General Public License (LGPL), version 2.1 or newer](http://www.gnu.org/licenses/old-licenses/lgpl-2.1.en.html)


## References

Many parts of this document are inspired by or adapted with only minor
modifications from many of the already existing standardisation processes.

- [EIP-1](https://eips.ethereum.org/EIPS/eip-1)
- [BIP-2](https://github.com/bitcoin/bips/blob/master/bip-0002.mediawiki)
- [TC39](https://tc39.github.io/process-document/)
- [The Internet Standards Process](https://www.rfc-editor.org/rfc/rfc2026.txt)
