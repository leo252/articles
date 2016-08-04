# Browser Monitoring for GitHub.com

[Original URL](http://githubengineering.com/browser-monitoring-for-github-com/)

> Most large-scale web applications incorporate at least some browser monitoring, collecting metrics about the user experience with JavaScript in the browser, but, as a community, we don't talk much...

Most large-scale web applications incorporate at least some browser monitoring, collecting metrics about the user experience with JavaScript in the browser, but, as a community, we don't talk much about what's working here and what's not. At GitHub, we've taken a slightly different approach than many other companies our size, so we'd like to share an overview of how our browser monitoring setup works.

Browser monitoring can present an optimization problem: if you care enough to want to monitor your users' in-browser experience, you probably also are the sort of company who cares deeply about security, privacy, and performance. Browser monitoring can be an amazing window onto your users' experience, but it also typically presents tradeoffs on all three fronts, since it often relies on loading external scripts and sending user data to a third-party application for collection and display.

For GitHub.com, we already know many common third-party solutions aren't a fit because of our security settings. GitHub.com sets fairly restrictive Content Security Policy (CSP) headers as a preventative measure against Cross-Site Scripting (XSS) attacks. These headers prevent inline JavaScript (including code inside HTML `<script>` tags) from running in modern browsers that support CSP. We also whitelist specific domains that remote scripts can load from, like our assets CDN, and try to keep that list of allowed script sources to a minimum. This automatically rules out some common real-user monitoring (RUM)/browser monitoring tools that work by writing request-specific data (like server response times) into each HTML page in a `<script>` tag, and it makes other solutions that rely on third-party scripts less appealing, since we'd need to add another script source and trust that the script poses a low enough risk to performance and security.

As application engineers, we're lucky to work alongside an Infrastructure team that is also obsessed with monitoring and metrics -- and has built a flexible graphing, monitoring and alerting toolchain on top of open source technologies like Graphite, Nagios, and D3\. While none of our tools were built with client-side monitoring in mind, they were built in a way that was flexible enough that we can add browser monitoring with only slighty more effort than it takes to add backend stats -- we already collect stats from the main GitHub.com Rails application, so collecting client-side stats just requires adding JavaScript to collect that data and post it back to the Rails app, plus Rails code to clean and filter unexpected values before sending the rest to our stats infrastructure.

## What We Monitor

Like most other sites of our size, we want to understand how fast our content is loading for end users, so we measure every browser page load. While some third-party tools keep things simple and sum up page load performance in one number (usually time to window load) or sometimes two numbers (usually one number for backend application and network time, and one number for browser rendering and subresource loading time), we prefer a bit more granularity. Because we write our own metrics collection JavaScript, it's fairly easy to move beyond collecting one or two headline numbers, and instead use the browser's [Navigation Timing API](http://www.w3.org/TR/navigation-timing/) to collect data from most points in the page load process:

![The Navigation Timing API (diagram from http://www.w3.org/TR/navigation-timing/)](https://cloud.githubusercontent.com/assets/187987/7735225/b90f499a-fef0-11e4-948e-aab9df0e7043.png)

We take these timings and graph the deltas between them in a stacked graph. We have an overall graph (below), and we also segment the stats by a few other metrics, like browser (Safari, Firefox, Internet Explorer) and page (the repositories#show page, or the blob#edit page).

![Our Navigation Timing API stats](https://cloud.githubusercontent.com/assets/187987/7738101/d9892654-ff05-11e4-8d62-340091dada79.png)

While this means collecting a lot more data than just time to window load, we benefit by being able to make a graph that helps diagnose performance problems, rather than just identify that there _is_ a performance problem. The graph shows the timings stacked from earliest to latest, and we can often narrow down the source of performance problems by looking for the first layer that starts to increase in size. For example, a slow backend application could show up in the "request" or "response" layer, but a slow image CDN would show up in the "domComplete" layer, and a performance regression in the initial execution time of a JS framework might show up in the "domInteractive" layer.

We still support some older browsers that don't implement the Navigation Timing API, so we also collect a simulated time-to-window-load number, or "cross-browser load time." This is calculated in JavaScript -- we save a "simulated navigation start" timestamp to the browser's sessionStorage on `pagehide` (when a user navigates away from or closes a page). On the next page load, we calculate the time between the start and the window `load` event. This gives us our "one big number" that we can put on dashboards and reports if we want to, and it also helps us track that the experience in browsers without Navigation Timing support isn't too different than in browsers with support.

```
# Browser page load timing stats collection
# - collects all Navigation Timing metrics
# - collects simulated time-to-load in browsers without nav timing (Safari < 8)

setNavigationStart = ->
 unless window.performance.timing
 try sessionStorage.setItem 'navigationStart', Date.now()

sendTimingResults = ->
 # Defer report until next tick so we catch the tail end of the load event timing.
 setTimeout ->
 timing =  window.performance?.timing or {}

 # Merge in simulated cross-browser load event
 timing['crossBrowserLoadEvent'] = Date.now()

 if chromeFirstPaintTime = window.chrome?.loadTimes?()?.firstPaintTime
 # firstPaintTime is in seconds; convert to milliseconds to match
 # performance.timing.
 timing['chromeFirstPaintTime'] = Math.round chromeFirstPaintTime * 1000

 # Merge in simulated navigation start, if no navigation timing
 if not window.performance.timing
 navStart = try sessionStorage.getItem 'navigationStart'
 timing['simulatedNavigationStart'] = parseInt(navStart, 10) if navStart

 GitHub.reportStats timing
 , 0

$(window).on 'pagehide', setNavigationStart
$(window).on 'load', sendTimingResults
```

While GitHub.com isn't a single-page app, we load a lot of content with [Pjax](https://github.com/defunkt/jquery-pjax), a jQuery plugin that uses AJAX and [pushState](https://developer.mozilla.org/en-US/docs/Web/Guide/API/DOM/Manipulating_the_browser_history#The_pushState()_method) to reload only a portion of the page when a user clicks a link. Pjax takes the place of a standard browser navigation, but it's usually about 1000ms faster. From a user's perspective, a pjax load _is_ a page load, but from the browser's perspective it's just another ajax request, which presents an issue. The goal of pjax is to improve user-perceived performance, but we can't collect our normal page load metrics to make sure performance is actually better.

To measure pjax timing, we use a similar strategy to our "simulated navigation start" measurement. Pjax makes this easy by firing the same events at the start and end of each navigation: `'pjax:start'` and `'pjax:end'`. When we hear the first event, we record a start timestamp. When we hear the second event, we wait a tick with a call to `setImmediate` and then record an end timestamp. Since user-facing feature code might also be listening for the `'pjax:end'` event to do DOM manipulation or data processing, waiting until the next tick lets us include that time in our measure and end up with a number that's more analogous to the time to the browser's `domContentLoaded` event.

There are some downsides to replacing browser navigations with pjax loads -- there's no default loading indicator, unlike a standard browser navigation where you'd see a loading indicator in the nav bar, and if a pjax request times out or throws an error, we fall back to a standard full-page navigation, so users who trigger that behavior get stuck with a extra-slow page load. This means that being able to compare pjax and standard page loads for the same content is important -- if we can quantify how much time we're saving users, it can help us confirm that we're really improving the experience by switching more links over to pjax behavior.

![pjax comparison graph](https://cloud.githubusercontent.com/assets/187987/7738149/4a5d5dc8-ff06-11e4-9608-555f68699a6a.png)

### JavaScript Errors

Another major focus of our browser monitoring is JavaScript errors. Our deployment process favors small, frequent deploys over big-bang releases, and for small changes or urgent fixes, the time between committing changes and having them deployed to production can be only a few minutes. Deploying small sets of changes helps reduce the risk associated with each deployment, but the higher frequency means that we're unlikely to do exhaustive cross-browser regression testing before each deploy, so it helps to have monitoring to detect when things go wrong.

GitHub maintains an internal application called Haystack, an error reporting tool that we use to view GitHub.com application-level errors. Haystack wasn't built with JavaScript errors in mind, but since it's already displaying errors from the GitHub Rails application, if we can send JS errors back to the Rails app, we can send JavaScript errors to Haystack as well.

The client-side JavaScript for collecting the errors is fairly simple. We have a single global event handler for uncaught JS errors, and most of the information we report comes from the Error object itself or the Event that triggered the error, including the error message, file name, line number, stack trace, and event target.

```
# Reports JavasScript errors to Haystack
loadTime = window.performance.now()

$(window).on 'error', (event) ->
 {message, filename, lineno, error} = event.originalEvent

 context = {
 message, filename, lineno
 url: window.location.href
 readyState: document.readyState
 referrer: document.referrer
 stack: error?.stack
 historyState: JSON.stringify window.history.state
 timeSinceLoad: window.performance.now() - loadTime
 }

 # Turn the actual target into a selector so we can see it in Haystack.
 context.eventTarget = $(context.eventTarget).inspect() if context.eventTarget?

 # Report errors to app
 errorsURL = document.querySelector('meta[name=browser-errors-url]').content
 fetch errorsURL,
 method: 'POST'
 body: JSON.stringify context

 return
```

JavaScript error reports are always a bit noisy -- browser extension code can trigger errors that look legitimate; nightly or experimental browser builds may have broken APIs, etc. An important part of keeping our JavaScript error reports actionable has been to filter out any errors we don't think are related to our code as soon as these errors get to the Rails app. Most of the time, the number of errors we filter is larger than the number we keep and send to Haystack. We filter anything with a browser extension file in the stack trace, since it's likely related to the extension behavior, and anything that matches a blacklist of specific errors we've tracked down (e.g. a particular popup blocker extension). There are always a handful of one-off errors that may not be worth digging into, but we can usually spot real problems by looking for errors happening multiple times per hour to more than one user.

At this point, some might ask, "Why not just write browser tests, so you can catch errors and regressions before you deploy them?" It's a great question -- I definitely don't see client-side monitoring as a replacement for integration or acceptance tests. But tests almost never capture the variety in real production data or the variety in real users' browser versions. Browser monitoring captures all of these by default. Plus, tests only capture errors _when you run them_, which is usually when you're committing code changes, but the environment around front end code changes all the time. We always want to know about new errors our users are running into, regardless of whether they come from us deploying new code or from the Google Chrome team rolling out a new browser version.

## How's It Going?

While the overhead of maintaining stats collection and graphing infrastructure may not make sense for every company, for us, there have been some neat benefits to our approach. For one, the fact that our browser monitoring uses the same tools that other teams use for backend and network stats means that there's a low barrier to getting other people in the company interested in front end metrics. As someone who writes front end code, I want to collect network-related Navigation Timing metrics like `domainLookupStart`, `domainLookupEnd`, `connectStart` and `connectEnd` so I can confirm that front end performance issues really are browser code issues, not network issues, but once these stats show up in Graphite, they're there for everyone in the company to look at. That means it's trivial for the Infrastructure team to start showing any of these metrics on their dashboards if they, for example, decide these metrics could be helpful for monitoring the impact of DDoS attacks on end users.

Another benefit to rolling our own browser monitoring code is that it allows us to collect new kinds of information fairly easily, if we can format it like existing stats. In the past year, engineers on the team have added a handful of new kinds of metrics as they've thought of them, usually in under 100 lines of code. We've started tracking support for new browser features (like support for the User Timing API or support for natively displaying emoji) with code similar to our performance metrics code, and we've started tracking accessibility issues by throwing special types of JavaScript errors for them and using our exist JavaScript error reporting to display them in Haystack alongside our other JavaScript errors.

### What's Next?

While our browser monitoring tools gives us fairly good visibility into our users' experience on GitHub.com, they're only effective when we look at them regularly -- there's more we could do to catch front end issues faster. Alerts are the next step I'd like to add on our path to more operable front end code. The GitHub Infrastructure team has done some amazing work to allow engineers to get alerts from different parts of our infrastructure in different channels. I'd like to be able to hook our browser metrics into the alerting tools we use for our backend metrics, so we could get a notification in chat if front end performance seems to be slightly slower or get paged if it seems like there's been a major performance regression.