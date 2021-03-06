# Outline

**Preface**

Everyone who used Anki for a prolonged period of time knows that its algorithm that determines review periods for cards is far from perfect. It does not work well with default settings, nor is there any good reasons for choosing a different set of settings, other than intuition. There is a bunch of addons that implement various fixes, and some of these are apparently better than others. Nevertheless, there are only a few attempts at implementing the algorithm starting from the theoretical framework cited by Anki's inspiration Supermemo, that is, Ebbinghaus forgetting curve. And even if such an implementation is consistently made, it'd be as good as the Ebbinghaus model itself, which may sound good as a theoretical tool but is rather questionable when it comes to how people actually remember things: anyone can use their experience to understand that the real process is way more complicated. An algorithm that starts with an oversimplification will inevitably be far from the most efficient.

Understanding how the memory really works lies outside of the scope of the current project. Luckily, there's a single thing Anki does right, and that's saving the full statistics of answers for statistic purposes. That data, properly analyzed, can give us insights into how to optimize the core algorithm so that Anki is utilised in the most efficient way (or at least in the most efficient way we can think of)

**Theoretical framework**

A general model for a SRS is such: for a given card there's a number of times when it was exposed to a user, and for every exposure we know the answer the user gave (for now let's suppose the possible answers are only "remember" and "don't remember", lumping all of the Easy, Normal and Hard into the first category). Using this information about exposures and answers we should construct a function that, for a given card, will give a probability of an incorrect answer ("forget") on a given day in the future. This function (we'll call it the forgetting function) is influenced by future reviews, whether passed or failed, and it's the task of the algorithm to optimize the dates of the future reviews for all the cards.

What does optimization mean in this context? We should assume that, as it usually happens with Anki, a user has a big enough deck of cards from which he pulls a number of new cards every day (typically 5 to 20), and that the user started the deck some time ago so that the number of daily reviews has stabilized: provided that the reviews go as expected by our forgetting function, the user will have roughly the same number of daily reviews no matter how long he continues this routine. In fact, this condition would be used as a restriction for possible forgetting functions in our model. Then at any given day the user has the same daily number of cards to review, and he also has all the cards in his deck that he has already seen: some of them he forgot already, some of them he'll forget in the near future and some of them he'll review before forgetting (apparently some of them he'll never forget, too). For each of these cards we know the value of forgetting function for this day, therefore we can calculate the mathematical expectation of the number of cards the user will remember if he were to review the whole collection today (we are not generally interested in cards the user has seen recently so we can restrict this to the cards that were reviewed earlier than a couple of weeks ago, or cards for which the time step between reviews is bigger than a given value, which is known as "mature cards", but that's not important in principle)

Therefore, on a given day the user has a number of daily reviews and an expected number of cards he remembers from the whole collection (let's call it number of known cards), where the first one stays the same and the second increases day to day. To make the process stable we also want the average increase in the number of known cards to stay the same on average, which will restrict the forgetting function further.

The condition of optimality is therefore this: daily increase in the number of known cards, divided by the number of daily reviews, should be maximal.

This condition is linear: it does not matter how much new cards per day you have, since every card is independent of others, so the very same set of settings will suffice whether you have 5 new cards per day or 20. In reality it's not true: people grow tired during the review process and their forgetting function will increase the more daily reviews they have. We'll see along the way if this fact is possible and/or necessary to incorporate into our model.

**Actual plan**

Given the above, the plan of this project consists of three simple steps:

1) Think up a forgetting function that would depend on a few (1 to 3 looks realistic) variables that are in turn calculated from the card's history, preferrably in a simple manner that allows for analytic expressions for various averages for the card, so that for every card in a deck this forgetting function can be readily calculated. Some variables may be deck-wise while others may belong to a card.

2) Using the statistical data of someone's prolonged usage of Anki, check whether the forgetting function we thought up on a previous step is retarded or a reasonable approximation of the actual forgetting process. This should be done using statistical techniques I have but the vaguest idea about, but hopefully Wikipedia will help us.

3) Using analytical (optimistic) or numerical (realistic) calculations, obtain the next review date for a given forgetting function, such that it will statistically optimise the effectiveness of the learning process as defined above.

This is, of course, an idealisation. What's realistically needs to be done first is some sort of a tool to extract historical information about cards from Anki's DB and present it in a form that allows preliminary processing and visualising whatever characteristics of cards' actual behaviour to have a glimpse into it. Then it'll hopefully be more clear what sort of forgetting function do we need.

Another thing that should be done in the beginning is to take Ebbinghaus forgetting curve as a forgetting function, in two variants: one for the forgetting coefficient being calculated for the whole model based on full statistics, and second with the forgetting coefficient being calculated for each card based on its individual history. For both variants statistical characteristics (reviews per day and new known cards per day) should probably be easily calculable, and both of them can be checked against actual statistics to see if this model is decent or not. It's reasonable to expect that a more complicated model will be necessary.


# Addons

* **[Experimental Card Ease Factor](https://github.com/eshapard/experimentalCardEaseFactor)**

   Modifies the algorithm to work according to the Ebbinghaus forgetting curve model, while targeting a 85% success rate. Also removes the redundant "easy"/"hard" choices. For a more detailed explanation, read https://eshapard.github.io/anki/thoughts-on-a-new-algorithm-for-anki.html
* **[Avg Ease](https://github.com/eshapard/Anki-avgEase)**

   With the addon above, your cards will eventually stabilize on an ease factor that gives you a success rate of 85%, which may take a lot of reviews. This speeds the process up by assigning new cards a starting ease equivalent to the average ease of your mature cards
* **[Another Retreat](https://ankiweb.net/shared/info/1481634779)**

   Because 15% of your reviews will be lapses, we need a better way of handling them instead of just resetting their interval back to one day. This addon's approach is to set it to the last successful interval, which is a sensible default
* **[Load Balancer Scheduler](https://ankiweb.net/shared/info/208879074)**

   A portion of the new interval is randomized by Anki to prevent cards always coming up in the same order they were introduced. This selects the interval with the least number of cards due, resulting in a consistent number of reviews across the week
* **[Speed Focus Mode](https://ankiweb.net/shared/info/1046608507)**

   Forces you to concentrate on reviewing by automatically selecting 'again' after a given number of seconds. Helps cut down review time. You shouldn't be spending more than 10s per card anyway, as the goal is to be able to recall words quickly
