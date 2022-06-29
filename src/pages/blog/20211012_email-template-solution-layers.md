---
layout: $/layouts/post.astro
title: Email Templates and Solution Layers
description: So you made a change to an email template in Dev and rolled that through to Test but the template is still the old one; what happened?! Read on for this wild ride.
tags:
  - email-templates
  - solutions
author: Reece Campbell
authorTwitter: RamblingReece
date: 2021-10-12
category: powerplatform
---

So you made a change to an email template in Dev and rolled that through to Test but the template is still the old one; what happened?! Read on for this wild ride.

OK, so maybe not that wild but very interesting and frustrating. If you have any interest at all in sending templated emails and you have some form of deployment process, where you develop/customise in an unmanaged environment and deploy to a managed production environment (you do have that right?!), then this could be interesting to you.

Right, first things first, let me show you the issue we are facing here.

In Dev the email template looks like this:
![Email Template in Dev environment](/assets/images/2021/email-templates/dev-template.png)

In Production the email template looks like this after I have deployed my changes from Dev:
![Email Template in Production environment, looking very different](/assets/images/2021/email-templates/prod-template-before.png)

Obviously something has gone wrong here. As impressive as this ASCII art is, it is not what I want deployed in Production!

Investigating further, I can see that the version of the solution in both Dev and Production are the same, so the changes should be there so we need to investigate the layers. In the Production environment, within the maker portal, I can check the solution layers for the specific component as below and see that there is an Unmanaged layer.

![Screenshot showing how to get to the solution layers screen](/assets/images/2021/email-templates/prod-solution-layers-menu.png)

![Showing the unmanaged layer on top of managed in Production](/assets/images/2021/email-templates/prod-template-layers-before.png)

What is happening? I am not going in depth, you can read more about it in the Docs here [Power Platform Docs: Solution Layers](https://docs.microsoft.com/en-us/power-platform/alm/solution-layers-alm) but suffice to say someone has made a change to the template in Production which has introduced an unmanaged layer on top of the managed solution layer (the Base solution in the screenshot) which is where my Dev changes are. Unmanaged layers will always override other layers as it always sits on top.

In Production, you should not be making changes, these should be brought through a Dev/Test/Production governance process but this does happen so how do I fix it? Microsoft introduced the option to remove an unmanaged layer in the recent past and that is my go to here as if I remove the layer then whatever is deployed in the solution should be what shines through. So let's see what happens...

![Screenshot showing how to delete the unmanaged layer](/assets/images/2021/email-templates/prod-solution-layers-remove.png)

![Screenshot showing the prompt you get when attempting to delete the unmanaged layer](/assets/images/2021/email-templates/prod-solution-layers-remove-prompt.png)

![Screenshot of the error given to say you can't delete the unmanaged layer for an email template even though the system gives you the option to do this! Madness](/assets/images/2021/email-templates/prod-solution-layers-error.png)

After all that I get this lovely error. Why?! WHY?! Why not just tell me up front I can't remove the layer for an email template? That aside, what now?

The issue to resolve here is that there are unmanaged customisations that are impeding our managed cusomisations so really we just need to remove them from Prod. The first step has failed, can't remove these for email templates, the second step would be to delete the template and dependent workflow (which is usually what sends the email) then re-import the managed solution to bring them back but this is getting harder and harder as really Microsoft doesn't want you to delete managed components.

There are only 2 solutions I have found so far, and neither are really nice, but they do work. The best solution obviously is not to be in the position in the first instance!

## Solution 1: Embrace Unmanaged

Unfortunately there is not an easy way to remove the unmanaged layer at this time so this solution embraces it instead!

In Dev, create a new solution that will only contain the email templates, add the email templates then export as unmanaged and import that into Prod. That will replace the unmanaged layer in Production with the unmanaged customisations in the imported solution; our Dev changes.

This means you need to make sure to include this new solution as part of any deployment process. At least in future if there is a change and you are able to remove the unmanaged layer then this workaround can be removed.

## Solution 2: Use the upgrade Luke

For this we will use the managed solution upgrade option to actually delete the email template and workflows and other dependencies for us. Obviously this is a little more risky in a way, and more time consuming, as we are actually changing our source solution and re-deploying.

For this to work you need to have a solution that has the email templates and dependent components, usually workflows, included in a dev environment.

You remove the email template(s) and dependent component(s) from that solution, **NOT THE ENVIRONMENT**. This will allow us to package a managed solution that will remove the offensive components on the next deployment, provided you make sure to use the Upgrade option and not the Update one (upgrade is the default these days for manual solution imports).

Import that new solution into Prod, note the email template(s) and dependent component(s) are gone.

Return to the Dev environment and add the template(s) and dependent component(s) back to your solution, export as managed and import into Production to cleanse the environment of unwanted customisations; atleast of the email template variety.

## Conclusion

I hope this has been an interesting, useful and maybe even a tiny bit entertaining read. There is definitely more to cover in email templates as even in going through the process to write this article I have noticed a few other "quirks" I'd like to cover.
