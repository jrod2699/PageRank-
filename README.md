# Pagerank Project

In this project, I created a simple search engine for the website https://www.lawfareblog.com .
This website provides legal analysis on US national security issues.

## Task 1: the power method

Implement the `WebGraph.power_method` function in `pagerank.py` for computing the pagerank vector.

**Part 1:**
For my implementation, I get the following output.
```
$ python3 pagerank.py --data=./small.csv.gz --verbose
DEBUG:root:computing indices
DEBUG:root:computing values
INFO:root:rank=0 pagerank=inf url=5
INFO:root:rank=1 pagerank=inf url=4
INFO:root:rank=2 pagerank=inf url=6
INFO:root:rank=3 pagerank=2.1574e-01 url=3
INFO:root:rank=4 pagerank=2.0201e-01 url=2
INFO:root:rank=5 pagerank=1.8739e-01 url=1
```
The `--verbose` flag causes all of the lines beginning with `DEBUG` to be printed.
By default, only lines beginning with `INFO` are printed.

**Part 2:**
The `pagerank.py` file has an option `--search_query`, which takes a string as a parameter.
If this argument is used, then program returns all urls that match the query string sorted according to their pagerank.
Essentially, this gives us the most important pages on the blog related to our query.

Again, you may not get the exact same results as me,
but you should get similar results to the examples I've shown below.
Verify that you do in fact get similar results.

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='corona'
INFO:root:rank=0 pagerank=4.6472e-03 url=www.lawfareblog.com/lawfare-podcast-united-nations-and-coronavirus-crisis
INFO:root:rank=1 pagerank=4.1017e-03 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response
INFO:root:rank=2 pagerank=2.6424e-03 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=3 pagerank=2.5688e-03 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=4 pagerank=2.3822e-03 url=www.lawfareblog.com/israeli-emergency-regulations-location-tracking-coronavirus-carriers
INFO:root:rank=5 pagerank=2.3148e-03 url=www.lawfareblog.com/why-congress-conducting-business-usual-face-coronavirus
INFO:root:rank=6 pagerank=2.3027e-03 url=www.lawfareblog.com/livestream-house-oversight-committee-holds-hearing-government-coronavirus-response
INFO:root:rank=7 pagerank=2.2771e-03 url=www.lawfareblog.com/congressional-homeland-security-committees-seek-ways-support-state-federal-responses-coronavirus
INFO:root:rank=8 pagerank=2.2124e-03 url=www.lawfareblog.com/paper-hearing-experts-debate-digital-contact-tracing-and-coronavirus-privacy-concerns
INFO:root:rank=9 pagerank=2.0577e-03 url=www.lawfareblog.com/cyberlaw-podcast-how-israel-fighting-coronavirus

$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='trump'
INFO:root:rank=0 pagerank=6.9321e-02 url=www.lawfareblog.com/donald-trump-and-politically-weaponized-executive-branch
INFO:root:rank=1 pagerank=6.1483e-02 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=2 pagerank=3.5536e-02 url=www.lawfareblog.com/trump-administrations-worrying-new-policy-israeli-settlements
INFO:root:rank=3 pagerank=3.2676e-02 url=www.lawfareblog.com/document-trump-revokes-obama-executive-order-counterterrorism-strike-casualty-reporting
INFO:root:rank=4 pagerank=3.1468e-02 url=www.lawfareblog.com/dc-circuit-overrules-district-courts-due-process-ruling-qasim-v-trump
INFO:root:rank=5 pagerank=2.8920e-02 url=www.lawfareblog.com/how-trumps-approach-middle-east-ignores-past-future-and-human-condition
INFO:root:rank=6 pagerank=2.5637e-02 url=www.lawfareblog.com/why-trump-cant-buy-greenland
INFO:root:rank=7 pagerank=2.2789e-02 url=www.lawfareblog.com/oral-argument-summary-qassim-v-trump
INFO:root:rank=8 pagerank=2.1799e-02 url=www.lawfareblog.com/dc-circuit-court-denies-trump-rehearing-mazars-case
INFO:root:rank=9 pagerank=2.1428e-02 url=www.lawfareblog.com/second-circuit-rules-mazars-must-hand-over-trump-tax-returns-new-york-prosecutors

$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='iran'
INFO:root:rank=0 pagerank=6.9207e-02 url=www.lawfareblog.com/praise-presidents-iran-tweets
INFO:root:rank=1 pagerank=2.9670e-02 url=www.lawfareblog.com/how-us-iran-tensions-could-disrupt-iraqs-fragile-peace
INFO:root:rank=2 pagerank=1.8004e-02 url=www.lawfareblog.com/cyber-command-operational-update-clarifying-june-2019-iran-operation
INFO:root:rank=3 pagerank=1.4872e-02 url=www.lawfareblog.com/aborted-iran-strike-fine-line-between-necessity-and-revenge
INFO:root:rank=4 pagerank=8.5780e-03 url=www.lawfareblog.com/iranian-hostage-crisis-and-its-effect-american-politics
INFO:root:rank=5 pagerank=8.5239e-03 url=www.lawfareblog.com/parsing-state-departments-letter-use-force-against-iran
INFO:root:rank=6 pagerank=8.3807e-03 url=www.lawfareblog.com/announcing-united-states-and-use-force-against-iran-new-lawfare-e-book
INFO:root:rank=7 pagerank=8.2223e-03 url=www.lawfareblog.com/trump-moves-cut-irans-oil-revenues-whats-his-endgame
INFO:root:rank=8 pagerank=7.2933e-03 url=www.lawfareblog.com/us-names-iranian-revolutionary-guard-terrorist-organization-and-sanctions-international-criminal
INFO:root:rank=9 pagerank=6.0261e-03 url=www.lawfareblog.com/iran-shoots-down-us-drone-domestic-and-international-legal-implications
```

**Part 3:**
The webgraph of lawfareblog.com (the P matrix) naturally contains a lot of structure.
For example, essentially all pages on the domain have links to the root page https://lawfareblog.com/  and similarly broad pages like https://www.lawfareblog.com/topics and https://www.lawfareblog.com/subscribe-lawfare.
These pages therefore have a large pagerank.
We can get a list of the pages with the largest pagerank by running

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz
INFO:root:rank=0 pagerank=8.8824e+00 url=www.lawfareblog.com/snowden-revelations
INFO:root:rank=1 pagerank=8.8824e+00 url=www.lawfareblog.com/lawfare-job-board
INFO:root:rank=2 pagerank=8.8824e+00 url=www.lawfareblog.com/masthead
INFO:root:rank=3 pagerank=8.8824e+00 url=www.lawfareblog.com/litigation-documents-resources-related-travel-ban
INFO:root:rank=4 pagerank=8.8824e+00 url=www.lawfareblog.com/subscribe-lawfare
INFO:root:rank=5 pagerank=8.8824e+00 url=www.lawfareblog.com/litigation-documents-related-appointment-matthew-whitaker-acting-attorney-general
INFO:root:rank=6 pagerank=8.8824e+00 url=www.lawfareblog.com/documents-related-mueller-investigation
INFO:root:rank=7 pagerank=8.8824e+00 url=www.lawfareblog.com/our-comments-policy
INFO:root:rank=8 pagerank=8.8824e+00 url=www.lawfareblog.com/upcoming-events
INFO:root:rank=9 pagerank=8.8824e+00 url=www.lawfareblog.com/topics
```

These pages are not very interesting, however, because they are not articles.
How can we find the most important articles? The answer is to modify the P matrix by removing all links to non-article pages.

How do we know if a link is a non-article page? This is a hard question to answer with 100% accuracy, but there are many methods that get us most of the way there.
An easy method is to remove all pages that have "too many" links. The `--filter_ratio` argument removes all pages that have more links than the specified fraction.
(If that short explanation doesn't make sense, that's okay. You can either read the source for details or just accept that the option does magic.)

Using this option, we can estimate the most important articles on the domain with the following command:
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2
INFO:root:rank=0 pagerank=4.5491e+00 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.9178e+00 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.8989e+00 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=2.0133e+00 url=www.lawfareblog.com/senate-examines-threats-homeland
INFO:root:rank=4 pagerank=1.8779e+00 url=www.lawfareblog.com/what-make-first-day-impeachment-hearings
INFO:root:rank=5 pagerank=1.8773e+00 url=www.lawfareblog.com/livestream-house-armed-services-committee-hearing-f-35-program
INFO:root:rank=6 pagerank=1.8707e+00 url=www.lawfareblog.com/whats-house-resolution-impeachment
INFO:root:rank=7 pagerank=1.7679e+00 url=www.lawfareblog.com/congress-us-policy-toward-syria-and-turkey-overview-recent-hearings
INFO:root:rank=8 pagerank=1.6539e+00 url=www.lawfareblog.com/summary-david-holmess-deposition-testimony
INFO:root:rank=9 pagerank=9.7088e-01 url=www.lawfareblog.com/events
```

When Google calculates their P matrix for the web,
they use a similar process to modify the P matrix in order to reduce spam results.
The exact formula they use, however, is a jealously guarded secret.

In the case above, notice that we have removed the blog's most popular article (www.lawfareblog.com/snowden-revelations).
The blog editors believe that Snowden's revelations about NSA spying are so important that they link to the article on every single webpage in the domain,
and out "anti-spam" `--filter-ratio` argument removed this article from the list.
In general, it is a challenging open problem to remove spam from pagerank results,
and all current solutions rely on careful human tuning and still have lots of false positives and false negatives.

**Part 4:**
The runtime of pagerank depends heavily on the eigengap of the $\bar\bar P$ matrix,
and that this eigengap is bounded by the alpha parameter.

I ran the following four commands:
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose 
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --alpha=0.99999
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2
$ python3 pagerank.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2 --alpha=0.99999
```
The last command took considerably more iterations to compute the pagerank vector. This begs the question does the second command (with the `--alpha` option but without the `--filter_ratio`) option not take a long time to run? The answer is that the P graph for www.lawfareblog.com naturally has a large eigengap and so is fast to compute for all alpha values,but the modified graph does not have a large eigengap and so requires a small alpha for fast convergence.

Changing the value of alpha also gives us very different pagerank rankings.
For example, 
```
$ python3 pagerank_solution.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2
INFO:root:rank=0 pagerank=4.5491e+00 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.9178e+00 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.8989e+00 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=2.0133e+00 url=www.lawfareblog.com/senate-examines-threats-homeland
INFO:root:rank=4 pagerank=1.8779e+00 url=www.lawfareblog.com/what-make-first-day-impeachment-hearings
INFO:root:rank=5 pagerank=1.8773e+00 url=www.lawfareblog.com/livestream-house-armed-services-committee-hearing-f-35-program
INFO:root:rank=6 pagerank=1.8707e+00 url=www.lawfareblog.com/whats-house-resolution-impeachment
INFO:root:rank=7 pagerank=1.7679e+00 url=www.lawfareblog.com/congress-us-policy-toward-syria-and-turkey-overview-recent-hearings
INFO:root:rank=8 pagerank=1.6539e+00 url=www.lawfareblog.com/summary-david-holmess-deposition-testimony
INFO:root:rank=9 pagerank=9.7088e-01 url=www.lawfareblog.com/events

$ python3 pagerank_solution.py --data=./lawfareblog.csv.gz --verbose --filter_ratio=0.2 --alpha=0.99999
INFO:root:rank=0 pagerank=7.0149e-01 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=7.0149e-01 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=1.0552e-01 url=www.lawfareblog.com/cost-using-zero-days
INFO:root:rank=3 pagerank=3.1755e-02 url=www.lawfareblog.com/lawfare-podcast-former-congressman-brian-baird-and-daniel-schuman-how-congress-can-continue-function
INFO:root:rank=4 pagerank=2.2040e-02 url=www.lawfareblog.com/events
INFO:root:rank=5 pagerank=1.6027e-02 url=www.lawfareblog.com/water-wars-increased-us-focus-indo-pacific
INFO:root:rank=6 pagerank=1.6026e-02 url=www.lawfareblog.com/water-wars-drill-maybe-drill
INFO:root:rank=7 pagerank=1.6023e-02 url=www.lawfareblog.com/water-wars-disjointed-operations-south-china-sea
INFO:root:rank=8 pagerank=1.6020e-02 url=www.lawfareblog.com/water-wars-song-oil-and-fire
INFO:root:rank=9 pagerank=1.6020e-02 url=www.lawfareblog.com/water-wars-sinking-feeling-philippine-china-relations
```

Which of these rankings is better is entirely subjective,
and the only way to know if you have the "best" alpha for your application is to try several variations and see what is best.
If large alphas are good for your application, you can see that there is a tradeoff between quality answers and algorithmic runtime.
We'll be exploring this tradeoff more formally in class when we incorporate statistics into our discussion.

## Task 2: the personalization vector

The most interesting applications of pagerank involve the personalization vector.

**Part 1:**
Implement the `WebGraph.make_personalization_vector` function.
This function enables the `--personalization_vector_query` command line argument,
which provides an alternative method for searching by doing the filtering on the personalization vector.

Some examples of output provided by the personalization vector command line argument are as follows: 
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='corona'
INFO:root:rank=0 pagerank=1.1550e+00 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=1.1550e+00 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=9.6007e-01 url=www.lawfareblog.com/brexit-not-immune-coronavirus
INFO:root:rank=3 pagerank=9.6007e-01 url=www.lawfareblog.com/rational-security-my-corona-edition
INFO:root:rank=4 pagerank=2.3670e-01 url=www.lawfareblog.com/chinatalk-how-party-takes-its-propaganda-global
INFO:root:rank=5 pagerank=1.4743e-01 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=6 pagerank=1.4743e-01 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=7 pagerank=1.4284e-01 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response
INFO:root:rank=8 pagerank=1.4276e-01 url=www.lawfareblog.com/trump-cant-reopen-country-over-state-objections
INFO:root:rank=9 pagerank=1.3752e-01 url=www.lawfareblog.com/livestream-house-oversight-committee-holds-hearing-government-coronavirus-response
```

Notice that these results are significantly different than when using the `--search_query` option:
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --search_query='corona'
INFO:root:rank=0 pagerank=1.2818e-01 url=www.lawfareblog.com/congress-needs-coronavirus-failsafe-its-too-late
INFO:root:rank=1 pagerank=5.9760e-02 url=www.lawfareblog.com/house-oversight-committee-holds-day-two-hearing-government-coronavirus-response
INFO:root:rank=2 pagerank=5.6009e-02 url=www.lawfareblog.com/britains-coronavirus-response
INFO:root:rank=3 pagerank=5.5654e-02 url=www.lawfareblog.com/prosecuting-purposeful-coronavirus-exposure-terrorism
INFO:root:rank=4 pagerank=5.2564e-02 url=www.lawfareblog.com/paper-hearing-experts-debate-digital-contact-tracing-and-coronavirus-privacy-concerns
INFO:root:rank=5 pagerank=5.1218e-02 url=www.lawfareblog.com/livestream-house-oversight-committee-holds-hearing-government-coronavirus-response
INFO:root:rank=6 pagerank=4.8113e-02 url=www.lawfareblog.com/why-congress-conducting-business-usual-face-coronavirus
INFO:root:rank=7 pagerank=2.8136e-02 url=www.lawfareblog.com/israeli-emergency-regulations-location-tracking-coronavirus-carriers
INFO:root:rank=8 pagerank=2.6084e-02 url=www.lawfareblog.com/lawfare-podcast-united-nations-and-coronavirus-crisis
INFO:root:rank=9 pagerank=2.0644e-02 url=www.lawfareblog.com/congressional-homeland-security-committees-seek-ways-support-state-federal-responses-coronavirus
```

Which results are better?
That depends on what you mean by "better."
With the `--personalization_vector_query` option,
a webpage is important only if other coronavirus webpages also think it's important;
with the `--search_query` option,
a webpage is important if any other webpage thinks it's important.
You'll notice that in the later example, many of the webpages are about Congressional proceedings related to the coronavirus.
From a strictly coronavirus perspective, these are not very important webpages.
But in the broader context of national security, these are very important webpages.

Google engineers spend TONs of time fine-tuning their pagerank personalization vectors to remove spam webpages.
Exactly how they do this is another one of their secrets that they don't publicly talk about.

**Part 2:**
Another use of the `--personalization_vector_query` option is that we can find out what webpages are related to the coronavirus but don't directly mention the coronavirus.
This can be used to map out what types of topics are similar to the coronavirus.

For example, the following query ranks all webpages by their `corona` importance,
but removes webpages mentioning `corona` from the results.
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='corona' --search_query='-corona'
INFO:root:rank=0 pagerank=1.1550e+00 url=www.lawfareblog.com/covid-19-speech-and-surveillance-response
INFO:root:rank=1 pagerank=1.1550e+00 url=www.lawfareblog.com/lawfare-live-covid-19-speech-and-surveillance
INFO:root:rank=2 pagerank=2.3670e-01 url=www.lawfareblog.com/chinatalk-how-party-takes-its-propaganda-global
INFO:root:rank=3 pagerank=1.4276e-01 url=www.lawfareblog.com/trump-cant-reopen-country-over-state-objections
INFO:root:rank=4 pagerank=1.2874e-01 url=www.lawfareblog.com/lawfare-podcast-mom-and-dad-talk-clinical-trials-pandemic
INFO:root:rank=5 pagerank=1.0639e-01 url=www.lawfareblog.com/fault-lines-foreign-policy-quarantined
INFO:root:rank=6 pagerank=1.0238e-01 url=www.lawfareblog.com/us-moves-dismiss-case-against-company-linked-ira-troll-farm
INFO:root:rank=7 pagerank=1.0238e-01 url=www.lawfareblog.com/livestream-house-foreign-affairs-committee-holds-hearing-crisis-idlib
INFO:root:rank=8 pagerank=1.0238e-01 url=www.lawfareblog.com/livestream-house-armed-services-committee-holds-hearing-priorities-missile-defense
INFO:root:rank=9 pagerank=1.0238e-01 url=www.lawfareblog.com/livestream-house-armed-services-holds-hearing-national-security-challenges-north-and-south-america
```
You can see that there are many urls about concepts that are obviously related like "covid", "clinical trials", and "quarantine",
but this algorithm also finds articles about Chinese propaganda and Trump's policy decisions.
Both of these articles are highly relevant to coronavirus discussions,
but a simple keyword search for corona or related terms would not find these articles.

**Part 3:**
Exploring other topics related to national security. Example, immigration queries yield the following:

```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='immigration'
INFO:root:rank=0 pagerank=2.7301e-02 url=www.lawfareblog.com/class-actions-and-due-process-relief-immigration-detainees-after-jennings-v-rodriguez
INFO:root:rank=1 pagerank=1.3047e-02 url=www.lawfareblog.com/document-forced-labor-constitutes-material-support-board-immigration-appeals-finds
INFO:root:rank=2 pagerank=7.4183e-03 url=www.lawfareblog.com/trumps-executive-order-immigration-summary
INFO:root:rank=3 pagerank=5.3663e-03 url=www.lawfareblog.com/trump-signs-executive-order-limiting-immigration
INFO:root:rank=4 pagerank=5.2973e-03 url=www.lawfareblog.com/what-was-most-important-todays-supreme-court-immigration-decision
INFO:root:rank=5 pagerank=2.1038e-03 url=www.lawfareblog.com/refugee-executive-order-immigration-act-and-governments-bottom-line
INFO:root:rank=6 pagerank=1.9865e-03 url=www.lawfareblog.com/executive-power-and-immigration-reform
INFO:root:rank=7 pagerank=1.8471e-03 url=www.lawfareblog.com/ninth-circuit-argument-turns-whether-ban-clashes-immigration-act
INFO:root:rank=8 pagerank=1.7918e-03 url=www.lawfareblog.com/new-travel-ban-undermining-immigration-and-nationality-act
INFO:root:rank=9 pagerank=1.6746e-03 url=www.lawfareblog.com/trump-repeats-his-lies-about-terrorism-immigration-and-justice-department-data
```
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --filter_ratio=0.2 --personalization_vector_query='immigration' --search_query='-immigration'
INFO:root:rank=0 pagerank=3.2969e-01 url=www.lawfareblog.com/trump-asks-supreme-court-stay-congressional-subpeona-tax-returns
INFO:root:rank=1 pagerank=2.0818e-01 url=www.lawfareblog.com/livestream-nov-21-impeachment-hearings-0
INFO:root:rank=2 pagerank=2.0818e-01 url=www.lawfareblog.com/opening-statement-david-holmes
INFO:root:rank=3 pagerank=1.2913e-01 url=www.lawfareblog.com/senate-examines-threats-homeland
INFO:root:rank=4 pagerank=1.2817e-01 url=www.lawfareblog.com/what-make-first-day-impeachment-hearings
INFO:root:rank=5 pagerank=1.2817e-01 url=www.lawfareblog.com/livestream-house-armed-services-committee-hearing-f-35-program
INFO:root:rank=6 pagerank=1.2779e-01 url=www.lawfareblog.com/whats-house-resolution-impeachment
INFO:root:rank=7 pagerank=1.1905e-01 url=www.lawfareblog.com/congress-us-policy-toward-syria-and-turkey-overview-recent-hearings
INFO:root:rank=8 pagerank=1.1408e-01 url=www.lawfareblog.com/summary-david-holmess-deposition-testimony
INFO:root:rank=9 pagerank=8.5106e-02 url=www.lawfareblog.com/district-court-ruling-computer-searches-border
```

The latter results provide very interesting article titles! 

## Modifications using Word2Vec

This implementation of pagerank uses the `gensim` library to create word vectors modify the output of queries. Whenever a query -- or personalization vector query -- is made, the query focuses on the 5 most similar words to it, meaning that if we use the minus - sign in a query, we'd get the top urls the exclude the query and its 5 most similar words. For the `gensim` model, `glove-twitter-25` was used limiting the modifications to twitter data and only 25 dimensions. However, larger models trained on more data yield better results. 

Here are the results after making the modifications: 
```
$ python3 pagerank.py --data=./lawfareblog.csv.gz --search_query='weapons'
INFO:gensim.models.keyedvectors:precomputing L2-norms of word weight vectors
INFO:root:rank=0 pagerank=0.004571518860757351 url=www.lawfareblog.com/why-did-you-wait-moral-emptiness-and-drone-strikes
INFO:root:rank=1 pagerank=0.0031107424292713404 url=www.lawfareblog.com/dc-district-court-dismisses-journalists-drone-lawsuit
INFO:root:rank=2 pagerank=0.0020231129601597786 url=www.lawfareblog.com/revived-cia-drone-strike-program-comments-new-policy
INFO:root:rank=3 pagerank=0.0019667143933475018 url=www.lawfareblog.com/us-court-appeals-dc-circuit-dismisses-suit-over-us-drone-strike
INFO:root:rank=4 pagerank=0.001178761012852192 url=www.lawfareblog.com/iran-shoots-down-us-drone-domestic-and-international-legal-implications
INFO:root:rank=5 pagerank=0.0011619674041867256 url=www.lawfareblog.com/slaughterbots-and-other-anticipated-autonomous-weapons-problems
INFO:root:rank=6 pagerank=0.0011276121949777007 url=www.lawfareblog.com/german-courts-weigh-legal-responsibility-us-drone-strikes
INFO:root:rank=7 pagerank=0.0008373793680220842 url=www.lawfareblog.com/shift-jsoc-drone-strikes-does-not-mean-cia-has-been-sidelined
INFO:root:rank=8 pagerank=0.0007870369008742273 url=www.lawfareblog.com/atomwaffen-division-member-pleads-guilty-firearms-charge
INFO:root:rank=9 pagerank=0.0007856971933506429 url=www.lawfareblog.com/waiving-imminent-threat-test-cia-drone-strikes-pakistan
```
