This repo should provide comprehensive documentation about our DFP implementation, including examples and an FAQ. Unless otherwise noted this documentation was compiled by Rob Denton, just after implementing DFP in December 2016. I will include all information that I think someone would need to have to walk in from the street to fully understand how DFP works with our multiple sites. That said, things change, especially when it comes to Google. Always double check with DFP's most recent documentation.

For more, see this [Tracker issue](https://github.com/registerguard/tracker/issues/618) I used while implementing. 

### OS4 > YB > DFP

In order to have a DFP premium account you have to have 2 million pageviews a day, which we do not have. In order to access the benefits of DFP premium, we have employed a company called [OS4](http://www.os4.media/) to reach that threshold with a co-op of other mid-sized newspapers. OS4 provides a single DFP account for all of their clients and then divvy up seats to publications and limit their permissions to only their ad units. This causes some issues, which I will discuss later with taxonomy and KVP.

It's my understanding that OS4 contracts with a company called [YourBow](http://www.yourbow.com/aboutus.php) which provides technical assistance in setting up the DFP system and some custom code support. I'll discuss this later on when I talk about custom creative templates.

So, we're basically going through OS4 who contracts YB to get to DFP. These layers of abstraction provide support but also constricts some things that would be easy to change if we had an independent account. It's my understanding that being part of the co-op also provides us opportunities to leverage a common marketplace with other mid-sized publications.

### Contacts

* Dick van Halsema [OS4 rep] - dvh@os4.media
* Danijela (Daca) Nenadovic [YourBow implementation consultant] - daca@yourbow.com
* Marko Petkovic [YourBow code consultant] - marko@yourbow.com

Prior to launch, all communication went through a Basecamp project, which got very confusing at times. There are several threads, some overlapping and some topics jump back and forth between different threads. There is a lot of good info in here, but would be very hard to follow for anyone without knowledge of the process. Everything post-launch uses the OS4 support portal.

### Targeting ads

#### Taxonomy

There are two ways to target ads in DFP. The primary way is via taxonomy, which roughly aligns with our site's nav. If we were to upgrade the CMS, the DFP taxo should also be changed. In fact, we implemented DFP with a very minimal taxo in favor of relying more heavily on subcategories/key-value pairs. This is not ideal. Given the opportunity, the KVPs should be left behind in favor of a proper taxo. 

Our taxo is always prefixed by RGM, OS4/YB require a three-letter code for each publication in the network. Ours is RGM. So, you can target run-of-site ads by simply targeting RGM, or you can target something like Healthy Families by targeting RGM > life > healthyfamilies. These parent-child relationships cascade, so anything targeting RGM > sports will also apply to all of the children of sports (football, trackandfield, basketball, outdoors).

You can explore the full taxonomy by logging into DFP and going to [Inventory > Ad Units > Ad Units](https://www.google.com/dfp/30582678#inventory).

There are some non-nav taxo items to make targeting easier for Melissa and Michelle. These include the four Marketplace sub-sites, the three newsletters, etc.

#### Key-value pairs

KVPs are the second way to target in DFP. As I said earlier, we're using KVPs in a way other than recommended. I'm confident that the KVPs will work as expected but it's certainly working against the system, which I am not a fan of.

There are several OS4 keys that are consistant across different publishers. The one we use most heavily is `pos` for position. There are `atf & btf` (above & below the fold) as well as some custom rgm ones. We use rgm_one, rgm_two, rgm_three to target different medium rectangles on the homepage. Above and below are used for leaderboards.

There are two custom keys made for the RG, both are free-from, instead of predefined. For more on this, you can read more [here](https://support.google.com/dfp_premium/answer/188092?hl=en).

`rgm_subcat` is a way to target multiple subcategories from DT. This is helpful when all football subcats need to be targeted.

`rgm_article_id` is a way to target specific CMS IDs from DT. These are used with sponsored content posts or to exclude ads from certain stories.

You can explore all the KVPs by logging into DFP and going to [Inventory > Key-values](https://www.google.com/dfp/30582678#inventory/customTargeting).

### Tagging

There was more than 40 template files that had to be touched to roll out DFP. I defined them into these piles:

* Primary - DT (main news website)
* Secondary - Low traffic RG sites like go, projects and marketplace (In the tracker issues, I may have described these as tertiary)
* Third parties

John handled a lot of the secondary and third parties while I nailed down expanding billboards and site skins with YB. Bishop took care of NIE and Marketplace. I did the rest.

Thankfully, DFP tagging allowed for a lot of simplification in the main news site. There's still a lot of logic but it's about half as bad as it was. See example files for more.




More to come...

