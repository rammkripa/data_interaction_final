# Data Interaction Final Project: Ram M. Kripa

## Papaya Privacy Presents: Your Data Story

Link to deployed site: TBC

## Contributions: Your Data Story

1. Created Bar.svelte. (Reused from HW3)
2. Created HorizontalBar.svelte (Reused from HW3)
3. Added text to explain each plot and what interactive components exist in them.
4. Made bar charts interoperable to filter and affect the website visits over time viz.
5. Implemented scrollyteller with sticky chart on the right
6. Generated synthetic data for a whole month in the same format as data collected from CookieMonster and used it for the visualizations.

## Contributions: Papaya CookieMonster

Repository Link: [https://github.com/papayaverse/cookie_monster/](https://github.com/papayaverse/cookie_monster/)

I created a Data Collection mechanism for my existing CookieMonster Extension.
New functions:

`content.js`

1. collectData

`background.js`

1. saveBackendCookiePreferences
2. saveBackendData
3. Listener for `collectData`
4. collectCookiePreferences
5. Listener to call `collectCookiePreferences` when extension is updated

## Contributions: CookieMonster Backend API

Repository Link: [https://github.com/papayaverse/preferences_api](https://github.com/papayaverse/preferences_api)

API Link: [Heroku Deployment](https://cookie-monster-preferences-api-499c0307911c.herokuapp.com/docs)

Store the browsing data as well as cookie preferences collected from cookieMonster in an AWS S3 bucket

New Endpoints:

1. POST `/cookiePreferences`
2. POST `/dataPreferences`
3. POST `/dataBrowsing`


## How does it all fit together?

A web-user downloads the Papaya CookieMonster extension, sets their `cookie preferences` (whether that is Accept all, Reject all, Only Marketing cookies, or Only Performance cookies), and starts browsing. The extension executes their preferences on thousands of websites automatically.

However, to truly empower web-users, it is pivotal that they wield full control over who gets to access which parts of their data and for what purposes.

To do this requires a data collection and storage mechanism, which I have developed through the functions I updated and created in the `Papaya CookieMonster` and `CookieMonster API`.

It is also important that users understand what data of theirs is being collected, and what can be learned from it.

From this, `Papaya Privacy Presents: Your Data Story` was born. It is a data story generated from the web-user's own browsing data. While the collection mechanism does work, I was unable to collect data for an entire month without blowing up my AWS costs. Hence, I used the same schema and generated some sample data for `Your Data Story.`



