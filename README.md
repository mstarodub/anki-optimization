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
