I just had my first open source contribution merged. I added a feature to EcoTrace, an open source tool that measures how much carbon a piece of software produces, so its numbers can flow straight into Prometheus, a monitoring system many engineering teams already use.

The most useful moment came before anyone else saw my work. My first version passed all of my own tests, but a review tool I built this term flagged one claim in my plan as something I had assumed, not tested. I checked it against the real service and found the bug: every new measurement was quietly erasing the one before it. I redesigned how the data is grouped, added tests that catch that mistake, and the maintainer approved and merged the pull request two days after I opened it.

What I took away:
- Passing tests only prove what they actually test.
- The most valuable checks are often the strict ones that slow you down.
- AI can speed up the work a lot, but I still own every claim I make about it.

Thanks to the EcoTrace maintainer for the quick, careful review, and to CodePath's AI301 course for the push to contribute in the open.

https://github.com/Zwony/ecotrace/pull/250

#OpenSource #Python #SoftwareEngineering #CodePath
