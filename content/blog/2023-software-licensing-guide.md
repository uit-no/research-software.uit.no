+++
title = "Draft: Research software licensing guide"
description = ""
date = "2023-06-21"

[extra]
authors = "Radovan Bast and Kathleen A. Smart"
+++

<div class="uk-alert-danger" uk-alert>

**This is a preliminary draft of a proposed research software licensing guide
to be used by researchers at UiT**. We are sharing this draft with colleagues
and various stakeholders for review and comments.  Please send comments,
suggestions, and corrections to
[radovan.bast@uit.no](mailto:radovan.bast@uit.no).

</div>

<div class="uk-alert-primary" uk-alert>

This guide is shared under the [Creative Commons CC0
license](https://creativecommons.org/share-your-work/public-domain/cc0).

</div>

<!-- toc -->


## What is "research software"?

We consider any code, script, notebook, or file, regardless of size, as
"research software" if it is needed to generate, visualize, or reproduce
data/results that are part of, or will form part of a publication. You don't
need to be a "proper software engineer" to produce software. Most research
software is not written by "proper software engineers" (whatever that means).

Examples of "research software" for the purpose of this guide:
- Script to convert data from one format to another
- Script to read data and visualize it
- Program that generates data
- Analysis script
- Set of scripts that form an analysis pipeline
- Code that is compiled
- Code that is dynamically interpreted and not compiled
- Web app


## Why software licenses matter

Imagine the following frequent scenario: You find some great code or data that
you want to reuse for your own publication. This is good for the original
author - you will cite them. Maybe other people who cite you will cite them.
You need to modify the code a little bit, or you remix the data a bit. But
then, when it comes time to publish, you realize there is no license to the
original work. Thus, you can't release the new stuff you made!

Now you have a problem:
- You manage to publish the paper without the software/data but others cannot
  build on your software and data and
  you don't get as many citations as you could.
- Or, you cannot publish it at all if the journal requires that papers should
  come with data and software so that they are reproducible.

Next time you are smarter and check the license before building on someone
else's work.  And others may approach your work the same way.

Open science is built upon sharing of research data and software in a FAIR
(findable, accessible, interoperable, and reusable) manner. For research data,
reuse entails applying an open data license (generally using [Creative Commons
licenses](https://creativecommons.org/)), but sharing of software requires licenses with more specific
terms.

In open science we strive towards reproducibility (results can be verified by
other researchers) and reusability (results can be reused in follow-up research
by you and/or others without starting over from scratch).

Without a license that allows reuse and redistribution, and additionally clarifies terms
under which code can be reused and redistributed:
- others may not be able to publish derivative work based on your code
- you may not be able to publish derivative work based on somebody else's code
  or even your own code (e.g. after changing jobs)

This means that **clarifying license terms is essential for derivative work
based on your research to be even publishable**. Choosing an open source
license can also be good insurance for you against being locked out of your
own code after changing affiliation/group or job.


## When should I add a license?

Choose a license early in the project, even before you publish it. Later in the
project it may become complicated to change it.  Agreeing on a software license
does not mean that you have to make it open immediately.  You can also follow
the "open core" approach: You don't have to open source all your work. Core can
be open and on a public branch. Unpublished code can be on a private
repository.

However, we recommend to work as if the code is public even though it still may
be private: This is to avoid surprises about code in the history with
incompatible license years later when you decide to open the project.


## How do I add a license to my work?

### If your work is derivative work

(here we will add a decision tree/ flow-chart)

Your code is derivative work if you have started from an existing code and
made changes to it or if you incorporated an existing code into your code.

If your code is derivative work, then **you need to check the license of the
original code**. Depending on the license, your choices might be limited. In
this case we recommend to use these two resources:
- [Joinup Licensing Assistant - Find and compare software licenses](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-find-and-compare-software-licenses)
- [Joinup Licensing Assistant - Compatibility Checker](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-compatibility-checker)

If the original code does not have a license, you may not distribute your
derivative code. You can try to contact the authors and ask them to clarify
the license of their code.

Practical steps for **incorporating something small into your own project** with a license
that allows you to do so (as
an example incorporating a function or two from another project):
- Create a `LICENSES/` folder in your project and "put the unmodified license text
  (i.e., the license text template without any copyright notices) in your
  `LICENSES/` folder" (<https://reuse.software/faq/#license-templates>). This
  way if you reuse code from multiple projects, you can keep there multiple
  license files.
- Put the code that you incorporate into a separate file or separate files. This makes
  it later easier to see what was incorporated, and what was written from scratch.
  On top of the file(s) which you have incorporated into your project add (and
  adapt) the following header ([more examples](https://reuse.software/faq/)):
  ```python
  # SPDX-FileCopyrightText: 2023 Jane Doe <jane@example.com>
  #
  # SPDX-License-Identifier: MIT
  ```
  The [REUSE](https://reuse.software/) initiative was started by the [Free
  Software Foundation Europe](https://fsfe.org/) to make licensing of software
  projects easier.  It is OK if you prefer to not follow this strict format but
  the advantage of following it is that the
  [reuse-tool](https://github.com/fsfe/reuse-tool) makes it then easy to verify
  and update license headers if you have many files from different sources.
- If it does not make sense to have several files in your project (e.g. when incorporating
  something into a notebook), then add a note/comment
  about the license and where the code came from on top of the function.
- Although it is not dictated by the license but it can still be nice to
  acknowledge the incorporated functions/code in your README/documentation and to cite
  their work if you publish a paper about your code.
- Some licenses are more permissive (you can keep your changes private) but some licenses
  require you to publish the changes (share-alike).

Practical steps for making **changes to an existing project** with a license
that allows you to do so:
- If the project is on GitHub or GitLab or similar, first fork the project
  (copy it into your user space where you can make changes).
- For the BSD and MIT licenses you are not obliged to state your changes but it can
  still be helpful for others if you do. You can state your changes in the
  header of the files you have modified. It can be helpful to state
  bigger-picture changes in the README file of the project.
- Some licenses are more permissive (you can keep your changes private) but some licenses
  require you to publish the changes (share-alike).


### If your work is not derivative work

If you have started "from scratch", and not used any existing code, or
incorporated existing code into your code, then you may consider your code to
be not derivative work. Note that if you only use (but not incorporate or
change) libraries, plug-ins, and packages, which are distributed via platforms
like PyPI, Conda, CRAN, Crates, ... this is typically not considered derivative work.

Before you may choose a license, clarify the following points with, for
example, your supervisor, collaborators, or principal investigator:
- Does your work contract, grant, or collaboration agreement dictate a
  specific license?
- Is there an intent to commercialize the code?
- When there is unknown or mixed ownership: If there are multiple persons or
  organizations as owners of the code, all must agree to the license.

Do not invent your own license. Choose one of the standard licenses, otherwise
compatibility is not clear:
  - [Joinup Licensing Assistant - Find and compare software licenses](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-find-and-compare-software-licenses)
  - [Joinup Licensing Assistant - Compatibility Checker](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-compatibility-checker)

If there are no restrictions from the above points, and no overriding guidelines exist,
we recommend one of the following two licenses:
- **European Union Public Licence, Version 1.2 or later (EUPL)**
  (<https://data.europa.eu/doi/10.2799/77160>): this license is an official
  license of the European Union (EC Decision, part of European law). It is
  interoperable, reciprocal (derivative work must publish and provide back
  the modified source code), and compatible with many standard licenses. It
  is a good choice if you want to make sure that you have access to changes
  and improvements applied to your code so that you can integrate them back
  and reuse them in your own work or you want some competitive advantage
  (right to relicense) over other users of your work.
- **MIT License**: Choose this license to prioritize ease of reuse
  over requiring derivative work to provide modifications back to you and the
  community: Others will be able to do whatever they want with your code as
  long as they include the original copyright and licence notice in any copy
  of the software/source. For small and short-lived projects this can be a
  good choice.

Note that with the exception of CC0 (i.e. public domain dedication)
[CC](https://creativecommons.org/) licenses are not appropriate for software
(although they can be used for software documentation).

Practical steps:
- Create a `LICENSES/` folder ([example](https://github.com/bast/runtest/tree/main/LICENSES)).
- Put the unmodified license text
  (i.e., the license text template without any copyright notices) in plain
  text format into the folder  ([example](https://github.com/bast/runtest/tree/main/LICENSES)).  Here are
  the two above licenses in plain text:
  [EUPL](https://joinup.ec.europa.eu/sites/default/files/custom-page/attachment/2020-03/EUPL-1.2%20EN.txt)
  and [MIT](https://en.wikipedia.org/wiki/MIT_License#License_terms) (but the
  latter contains a copyright notice which we rather want to have on top of
  files).
- Add copyright and license information to each file following
  <https://reuse.software/tutorial/> which uses a standard format with
  so-called [SPDX identifiers](https://spdx.org/licenses/). Example below
  ([example](https://github.com/bast/runtest/blob/3b210d2e9bdbdc1903a1dab9da32e161d390092d/runtest/tuple_comparison.py#L1-L3)):
  ```python
  # SPDX-FileCopyrightText: 2023 Jane Doe <jane@example.com>
  #
  # SPDX-License-Identifier: EUPL-1.2
  ```
  The [REUSE](https://reuse.software/) initiative was started by the [Free
  Software Foundation Europe](https://fsfe.org/) to make licensing of software
  projects easier.  It is OK if you prefer to not follow this strict format but
  the advantage of following it is that the
  [reuse-tool](https://github.com/fsfe/reuse-tool) makes it then easy to verify
  and update license headers if you have many files from different sources.
- Create a [CITATION.cff file](https://citation-file-format.github.io/)
  ([example](https://github.com/bast/runtest/blob/main/CITATION.cff); more about it below).
- For really small projects with one or two files the above may seem excessive
  and some projects choose to not have copyright information on top of their
  files and they only have one `LICENSE` file and a `CITATION.cff` file and that is
  OK for really small projects.


## Make it persistent and citable

Choosing a license, adding a license file, and putting your code on GitHub or
GitLab is good start, but to ensure FAIR (findable, accessible, interoperable,
and reusable) research software for long term access, we recommend to go two
steps further:
- Add a file called [CITATION.cff](https://citation-file-format.github.io/) ([example](https://github.com/bast/runtest/blob/main/CITATION.cff)).
- Get a [digital object identifier
  (DOI)](https://en.wikipedia.org/wiki/Digital_object_identifier) for your code
  on [Zenodo](https://zenodo.org/) ([example](https://zenodo.org/record/8003695)).

The reason why we currently recommend [Zenodo](https://zenodo.org/) over
[DataverseNO](https://dataverse.no/) **for software products** is that it is
easier to update versions.  DataverseNO currently does not allow to assign
different licenses on a file-level which may result in license conflicts
(possible case: publishing software under CC0 on DataverseNO and having it
under a more restrictive license on GitHub at the same time). We believe that
linking from the DataverseNO entry to the software as "related dataset" is a
better option.

[Software Heritage](https://www.softwareheritage.org/) and
[CodeMeta](https://codemeta.github.io/) exist as an alternative ecosystem that
is currently receiving some attention on a European level. Comparison and links
to converters can be found in <https://zenodo.org/record/8086413>.

Additionally, UiT and other major national ([RCN Open Science](https://www.forskningsradet.no/en/research-policy-strategy/open-science/)) and European
funders ([EU Open Science](https://research-and-innovation.ec.europa.eu/strategy/strategy-2020-2024/our-digital-future/open-science_en)) require open access to publications and research data
(including research software) as early as possible, so self-archiving your
research software satisfies these requirements. For more information on these
policies, please see: [UiT Research Data
Portal](https://en.uit.no/research/research-dataportal), [UiT Regulations on
Research Data](https://uit.no/regelverk/sentraleregler#kapittel_734648), or
contact the University Library at
[researchdata@uit.no](mailto:researchdata@uit.no). 


## How about data?

Citing from the [Principles and guidelines for management of research data at
UiT](https://uit.no/regelverk/sentraleregler#v-pills-742423):
- "The researcher shall make research data openly available for further use to
  all relevant users, providing there are no legal, ethical, security or
  commercial reasons for not doing so."
- "Research data shall be equipped with licenses for access, reuse, and
  redistribution."
- "The licenses should be internationally recognised and place as few
  restrictions as possible on the access, reuse and redistribution of the
  data."

By default, [UiT's open research data
archive](https://dataverse.no/dataverse/uit) recommends the [Creative Commons
CC0 license](https://creativecommons.org/share-your-work/public-domain/cc0).
The [UiT Research Data Portal](https://en.uit.no/research/research-dataportal/art?p_document_id=726373)
contains information and guides on why and how to add such a license to your
data.

The [Horizon Europe
guideline](https://ec.europa.eu/info/funding-tenders/opportunities/docs/2021-2027/horizon/guidance/programme-guide_horizon_en.pdf)
specifies that data should be CC0 or CC-BY.  Metadata must be CC0. The
Norwegian Research council does currently not specify licenses. The omnipresent
rule is "as open as possible, as closed as necessary".

We wish to emphasize that using a restrictive license with the intention to
protect privacy of the data is not a good substitute for having the data
sufficiently anonymized.


## Questions and contact

Unsure which license to choose?  Unsure how to proceed with sharing your code
or reusing somebody else's code?  The [research software engineering (RSE)
group](https://research-software.uit.no/) at [UiT](https://uit.no/) is here to
help you. You can contact us at [rse@uit.no](mailto:rse@uit.no) or come to our
office hours. For questions regarding sharing of publications and research
data, contact the library at [researchdata@uit.no](mailto:researchdata@uit.no).


## Great resources

- [Research institution policies to support research software (compiled by the Research Software Alliance)](https://www.researchsoft.org/software-policies/)
- Guide from the Aalto University in Finland: ["Opening your Software at Aalto University"](https://www.aalto.fi/en/open-science-and-research/opening-your-software-at-aalto-university)
- [Joinup Licensing Assistant - Find and compare software licenses](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-find-and-compare-software-licenses)
- [Joinup Licensing Assistant - Compatibility Checker](https://joinup.ec.europa.eu/collection/eupl/solution/joinup-licensing-assistant/jla-compatibility-checker)
- [Social coding lesson material](https://coderefinery.github.io/social-coding/) by [CodeRefinery](https://coderefinery.org/)
- [Citation File Format (CFF)](https://citation-file-format.github.io/)
- [License Selector](https://ufal.github.io/public-license-selector/)
- [GitHub licensing guide](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository)


## Acknowledgements

We are very grateful to Korbinian Michael Bösl, Richard Darst, Luca Ferranti,
Noortje Haugstvedt, and Jenny Ostrop, for their comments and suggestions which
significantly improved this document.
