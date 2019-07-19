---
layout: post
title: Going Cloud Native.
---

I have become grizzly. The hair, the beard and the world view. I have been in this industry for 2 decades. In that time I have immersed myself and quite a number of technologies - end result - grey hair.

## Going Native
In anthropology, going native is defined as: ```To adopt the lifestyle or outlook of local inhabitants```
Gen-X's like myself are often told that we are not event digital inhabitants, but rather digital immigrants, so going cloud native is going to be a challenge.

## Outlook of the Locals
What then defines the lifestyle and outlook of a cloud native?

Here are some ideas:
* Favour composition over creation.
Simply put, discover and use what is available before writing it yourself. There are very clever people out there. And they have done it better than you can in many lifetimes. Use it. It is the opposite of what we knew in the old days as the 'Not developed here' skepticism or libraries. The trick is to find the working parts and compose the complete solution. In lego, you just press the bricks together, you don't make the brick. Don't make bricks. 
* Automate the pain away. 
I recently created a two machine test lab to test some on-prem AD(Active Directory) and [Exchange](https://products.office.com/en-za/exchange/microsoft-exchange-server) behavior, swearing under my breath the whole time. The Exchange install process has come a long way since I last did it in 2014, but I still felt some pain. This is not cloud native thinking. In cloud native you provision - you don't download, install, install dependencies, install again and then maintain. The cloud native drives the pain levels down with automation. Whether it is provisioning in the IaaS or SaaS space, or deploying with a git push, or CI/DI workloads, the natives have zero tolerance for pain.
* Change is ubiquitous
I configured the CI/DI chain of builds and release pipelines for one of my customer's team in Azure. It was great, and after a few iterations is was running smoothly. Everything was defined in the UI. I knew where to go and had links saved to the most important pages. The next time I went to change some of the configuration, the yaml defined CI/DI builds and pipelines started to appear, and a year later all the builds and pipelines were defined in yaml. A lot of the UI disappeared (for the better). Cloud natives take change as a given, the only variable is the velocity of change. This leeds logically to the next observation. 
* Do not believe the Marketing
This should be a blog on it's own with lots of references to Kool-Aid, but cloud natives does not believe the marketing that the big suppliers (Microsoft, Google, Amazon) write about their products. It is not that it is all lies, but it is not always the truth. Marketing is only true at specific epoch. Cloud natives understand that the velocity of change in cloud renders marketing immediately inaccurate. The cloud natives uses marketing to peak their interest, but then verifies the suitability of a product or service.                                                                 



I am on a journey of discovery like a old style anthropologist, to live and learn among the cloud natives, and will hope to report back here on my finding.

