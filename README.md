# MageUnconference NL 2026 notes

**Make a Pull Request to merge your notes here and share it with all others.**

## Random links

- []()

## Session "Must-Install Magento Modules"

**Notes by Simon Sprankel**

### Live

* https://github.com/aregowe/magento2-module-polyshell-protection
* https://github.com/idhlagency/magento2-disable-stock-reservation
* https://github.com/avstudnitz/AvS_ScopeHint2
* https://www.geissweb.de/eu-vat-enhanced-for-magento-2.html
* https://mageme.com/magento-2-form-builder.html
* https://github.com/creatuity/magento2-interceptors
* https://github.com/mage-os/module-admin-activity-log
* https://github.com/elgentos/magento2-static-deploy
* https://github.com/Ethan3600/magento2-CronjobManager
* https://github.com/ho-nl/magento2-ReachDigital_BetterIndexer
* https://github.com/Vendic/module-optimize-cache-size
* https://github.com/fisheyehq/module-url-rewrite-optimiser


### Dev

* https://github.com/yireo/Yireo_Whoops
* https://github.com/yireo/Yireo_EmailTester2
* https://github.com/yireo/Yireo_CheckoutTester2
* https://github.com/yireo/Yireo_ExtensionChecker / https://github.com/yireo/Yireo_ExtensionCheckerCli
* https://github.com/idhlagency/ampersand-magento2-upgrade-patch-helper
* https://github.com/mage-os/magento-cache-clean


## Session "Should Mage-OS be modular?" by Jelle Besseling

**Notes by Lau Bannenberg**

The question is a bit leading; everyone who came to this talk seemed to be in favor. But there was a lot to say about how it can be done. Jelle Besseling demonstrated [mageos-maker](https://mageos-maker.cresset.tools/), his take on this. At the same time, other people (Ryan Hoerr?) were also working on this, and there's now a [Mage-OS minimal distribution](https://mage-os.org/product/minimal/) available. A bit earlier, Jakub Winkler also raised the topic of [Great Magento Decoupling](https://www.linkedin.com/events/7454445588059701249/). And [Yireo Composer Replacement Tools](https://github.com/yireo/magento2-replace-tools) have been around for quite a while now; but that's trimming things down afterward, instead of starting small and adding as needed.

Saying you want Magento/Mage-OS to be more modular is one thing; actually getting it done is another. It certainly looks possible for Mage-OS to become more modular, since they already did a minimal distribution. But can you get these changes back into Magento Community Edition or Adobe Commerce is another question. It's possible that smaller changes might make it into Community Edition, especially if they're not going to cause breaking changes/clashes with what Adobe is doing with Adobe Commerce. For example, removing dependencies on the newsletter or reviews modules from the catalog modules is maybe possible. But gnarlier changes like removing dependencies on bundled products from the core catalog modules seem like a non-starter.

Jelle demonstrated that it *is* possible for Mage-OS though. Mageos-maker helps you assemble your own minimal distribution, but it also comes with replacement packages to help fix some of the dependencies. You can choose to have a Magento setup with only simple, configurable and virtual products, without bundle or grouped products in it. Right now that requires some Composer module replacement to switch out badly coupled modules, and it hasn't been battle-tested yet, but it's a working demo.

We talked about where things could go from here. As usual, smaller easier to test and review steps are probably more helpful than giant change-everything PRs. Every modest PR to Mage-OS that removes an instance of bad coupling is a good step forward. 

There was some concern about third party vendors that rely on transitive dependencies. They use functionality from modules A and B, and only list A as a dependency. If we decouple A from B, their previously functional dependency chain would break.

We had a tangent about what frontend theme we should assume: Luma, Hyvä, or some hypothetical Mage-OS minimalist base theme, because theme templates can also have implicit module dependencies in them.

I believe it would be helpful if we had automated tooling that provided a holistic map of module interdependencies. [Yireo Extension Checker](https://github.com/yireo/Yireo_ExtensionChecker) does this to some degree, at least checking for any class usage. But it might not catch magic strings like 'bundled', or dependencies in big frontend templates. Also, that's mostly aimed at checking your own homework in module development. But we could try to take this idea further to actually showing the dependency graph, and annotating whether dependencies are wanted or not. That would allow you to mark an unwanted dependency as a target for fixing. It could also lead to architecture tests where you can flag any PR that introduces new dependencies as "this either needs to be a deliberate choice, or it's an error". It would also be really helpful/necessary for third party module builders so they can specify what their modules need from you minimalist/choose your own project setup.

## Session "Magento Upgrades: should you wait or upgrade immediately?"
**Notes by Lau Bannenberg**

The poster was put up on Thursday, and selected on Friday after the original author had left. We found this out when gathered to see what spicy take he might have on the question :P

Hiding from the midday heat we discussed anyway. Turns out everyone is in agreement that the security patches are not up for debate: you do them as soon as possible. The last year or so, Adobe has been fairly good about not putting too much extra new functionality in them, so they're really mostly about security issues. (Probably because they're not really adding much new stuff to Magento Community Edition...) But the upside of this is that they're straightforward patches.

There's the question of how you sell that to merchants. Quite a lot of agencies (still) work with a ticketing system where tasks get estimated and agreed by merchants first. But for security patches, rapid action and decisions are needed. Most agencies have some variant of a standing order to just pick up the patch immediately when it lands without having to get approval to work on it. If it can be applied quickly (it usually can), it goes for testing and merchant approval. If the merchant doesn't raise any blockers, it gets released quickly. (And most agencies present this to merchants as "this is not up for negotiation".)

Third party packages with security advisories are another thing. Recent Composer versions are focusing more attention on these, and there's tools like Dependabot that can also flag issues. For agencies using Bitbucket, there's also [Violinist](https://violinist.io/dependabot-for-bitbucket) which works like Dependabot for PHP, but (also) works for Bitbucket. Private Packagist is working on [Conductor](https://packagist.com/features/conductor) but that still hasn't reached general availability yet. Other strategies include: have a weekly chore to just update affected packages manually, or update them whenever you happen to be working on a project for this client.

Supply chain security is a hot topic recently. Composer is hard at work adding security features.  Since 2.9, Composer will prevent you from upgrading to any package version with known security issues. Since 2.10 it prevents changing version tags to a different commit (so if you did something wrong during a release, you have to make a *new* tag). They paired with Aikido to add security scanning. In 2.11 (not released yet) we should get a key feature: setting a minimum age for package version, for example two days. That means Aikido has two days to scan the new version for malware before you can `composer update` to it. That should be a really significant improvement in security. But we'll need to learn how to bypass it occasionally (to install a security patch same-day).

This sort of led into the question of how to do main Magento version upgrades. To this, the answer to the opening question was a bit more varied. From hearsay, Elgentos just triggers an automatic pipeline to begin staging an upgrade and run it through test benches as soon as possible. Others (like me) found that third-party vendors can take their sweet time adding support for the new version. If your shipping integration doesn't support for example the new Symfony Mailer (2.4.8) you might have to wait a while. A lot of attendees felt waiting for the P1 patch to drop is the sweet spot. 

One of the big hairy issues with Magento upgrades is previous theme customization. How do you detect that the vendor template has changed, and what should be done to your customized version in the theme to keep up? Also with Hyvä this is an issue, when they make breaking changes, or add functionality to a template that you won't get unless you upgrade your theme as well.

The most popular solution for this is the [Ampersand Upgrade Patch Helper](https://github.com/idhlagency/ampersand-magento2-upgrade-patch-helper) which allows three-way comparisons between your customized theme version, the old vendor version, and the new vendor version. There is also a [GUI](https://github.com/elgentos/magento2-upgrade-gui) for it.

Another (not open source available) tactic that was discussed is a Composer plugin that watches PHTML files in your themes and inserts a hash of the vendor version in a comment. If the vendor version changes, your theme file will get its hash comment updated which makes the change visible (git status).

A problem with upgrading customized theme files is that it's often not obvious what the original reason for customization was. Of course, disciplined git commit messages are a good solution to this, but discipline can be lacking. A lightweight solution to get at least *some* context is to use a git hook that prepends the branch name (and/or ticket ID) to each commit message, so you can trace a change back to a ticket. In my company I've written a small Composer plugin that we install into our projects. On every "composer install", it guarantees this git hook is installed.

For the more backend side of upgrades, [Rector](https://getrector.com/documentation) is great. It builds on top of PHPStan to do a great deal of automatic refactoring. For example, go through all your legacy app/code modules and hunt for [dynamic class properties](https://getrector.com/rule-detail/complete-dynamic-properties-rector) (deprecated PHP 8.2) and [implicitly nullable parameters](https://getrector.com/rule-detail/explicit-nullable-param-type-rector) (deprecated PHP 8.4) and fix them in a few seconds. Those rules are (probably) the minimum you need to get compatible, but you can also do more extensive modernizations, like adding type hints, converting to constructor property promotion, removing redundant docblocks, and many more. Rector is deterministic and highly configurable. You can pick which rules you want to apply, --dry-run them and so forth. If you're modernizing a codebase, it's fast and cheap to first do a lot of Rector preprocessing before you do any kind of AI-based refactoring.


## Session "Shared Fates" by Francis Gallagher
**Notes by Lau Bannenberg**

Francis began by introducing the concept of a shared fate: some system or thing that if it breaks, you also get into trouble. If your car breaks, you are going to have a hard time getting to places on time. If your payment service provider breaks, how are you going to place new orders?

The key idea here is not to prevent all shared fates, you usually can't. But to be aware of them *before* it's a 3AM emergency situation. At that point you can say that you thought about this and you know what to do next.

### Detection
Recognizing shared fates starts with asking yourself "how does this fail?" and some follow-up questions to identify danger spots:
* Does it use an external system?
* Does it use an (other) internal system?
* Does it do something "novel"?
* Does it go against the grain of Magento?

The more "yes" you get here, the more risky this thing probably is. But it might still be something that from a business perspective, you can't do without.

### Remediation
When you identify that you're dealing with a shared fate, there are things you can do.
* **Call out the risk**, so that it doesn't come as a 3AM surprise. Even if you can't do anything else to remediate the risk, you should make sure that it's known.
* **Push back against the feature** If the feature is risky and low-value, maybe it shouldn't exist.
* **Won't fix** Maybe you (and the merchant) decide that fixing it is not feasible. A smaller merchant might not be able to have a contract with a backup payment services provider in case the primary PSP has an outage.
* **Graceful degradation** Fall back to less-ideal but still functional behavior. If your address autocomplete API is down, you can make sure the checkout always falls back to allowing manual input. If you can't look up shipping options/pickup points, just offer "standard shipping" for a fixed price.
* **Get it out of the critical path** If this is not absolutely key to making a sale, can you make sure it can't block that? For example, opt-in to an external newsletter service. If that API is having a big slowdown, it shouldn't grind your checkout to a halt. Put the signup into a queue to handle asynchronously.
* **Send alerts** Make sure someone gets a message that the system is in trouble. Make sure they don't get a hundred messages per minute.
* **Know who to call** Have a list of emergency contacts on the other side of the shared fate you're working with.
* **Make it observable** Whoever gets the alert should be able to look into why and where things are breaking. Now that you're aware of this shared fate, instrument it with logging etc. so debugging it is easy. Relevant tool: [PHP Spy](https://github.com/adsr/phpspy) which you can attach to running PHP processes to get traces. So if you're wondering "I wonder what that long-running process is supposed to be doing?", this might help.
* **Fall back to customer service** Activate an error message or page that tells customers that you're working on the problem, but in the meantime they can call this number and get help.
* **Handle things manually** You might not be able to program an automatic remediation, but having a list of manual things you can do can still help. "If external reviews are broken, change this config so we don't try to show them. That gives us time to fix them without showing customers broken pages."
* **Turn off that one feature** The rest of your store could still continue working, even if you have to turn off some of the functionality.
* **Recovery Plan** Once you've fixed the outage, how much of the lost sales can you recover? If your PSP is down, you could allow customers to pay by bank transfer instead. You'll have to send them a payment link for that and process more orders by hand, but a pending-payment order is better than nothing. (But accept that they might cancel, because they bought elsewhere in the meantime.)
* **Coupon!** You can put up an error page saying you're down for the moment, but here's a coupon code for when we're back up.
* **Abandoned cart** Once you're back up, you might win back some lost sales through abandoned cart messaging.

### Conclusion
The most important thing is recognizing the shared fate exists, before a 3AM incident. After that you can discuss what solutions are feasible to shield against the fate happening, and how to recover if it does.