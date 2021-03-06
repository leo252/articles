# Uploading Videos to YouTube Using NodeJS

[Original URL](https://www.codementor.io/nodejs/tutorial/uploading-videos-to-youtube-with-nodejs-google-api)

> Using different service APIs may be difficult, especially for beginners, but not only. In this post, I will try to make clearer how to upload a video on YouTube, using NodeJS. Back in 2013 I...

Using different service APIs may be difficult, especially for beginners, but not only. In this post, I will try to make clearer how to upload a video on YouTube, using NodeJS.

Back in 2013 I published my first NPM module: [`youtube-api`](https://github.com/IonicaBizau/youtube-api)", an object-oriented wrapper for the YouTube v3 API. People liked this module and the feedback came. Contributions, bug reports, stars etc. That means people actually used my module which is incredible.

So, let's suppose you have a video file (codename: `video.mp4`) and you want to upload it on your YouTube account. While there are a couple of authenticating methods (JWT, OAuth2, Server Key etc), to upload videos on YouTube, you need OAuth2 authentication.

The [documentation](https://developers.google.com/youtube/v3/guides/moving_to_oauth#service_accounts) cleary states:

> Service accounts do not work for YouTube Data API calls because service accounts require an associated YouTube channel, and you cannot associate new or existing channels with service accounts. If you use a service account to call the YouTube Data API, the API server [returns an error](https://developers.google.com/youtube/v3/docs/errors#youtube.api.RequestContextError-unauthorized-youtubeSignupRequired) with the error type set to `unauthorized` and the reason set to `youtubeSignupRequired`.

So, we need OAuth2 authentication. If you are _not_ familiar with OAuth2, you may need to [read the docs](https://developers.google.com/identity/protocols/OAuth2).

## Creating a Google application

Like for other API services, you need to create an application which will be the bridge between _you_ and the _API service_. To access the YouTube resources, you need to create a Google application.

![](http://i.imgur.com/fq1MKr1.png)

1. Set the project name in the popup that was opened.

![](http://i.imgur.com/AU3QU2j.png)

1. After the application is created, go to _APIs & auth_ and click the _APIs_ menu item.

![](http://i.imgur.com/EyviJbV.png)

1. Here you have to enable the _YouTube Data API_. For that, click the _YouTube Data API_ link and then click the Enable API button.

![](http://i.imgur.com/jNBz7qr.png)

1. Before getting the credentials, you have to set the _Consent screen_ information (the product name is mandatory):

![](http://i.imgur.com/4iG7TJT.png)

1. Now it's the time to get the application credentials. But there are no credentials yet. You have to create the OAuth2 Client ID. To do that, go to _Credentials_ page and click Create new Client ID. A popup will be opened.

![](http://i.imgur.com/1BYE168.png)

1. Set the _Authorized JavaScript origins_, just put `http://localhost:5000` because there will run our local server (you may add other origins like development and production urls). The callback url is `http://localhost:5000/oauth2callback` after allowing the application to access your account (YouTube resources in this case), you will be redirected there.

![](http://i.imgur.com/Wc9bPtw.png)

1. After clicking the Create Client ID button, you will get your credentials.

![](http://i.imgur.com/3clxASP.png)

1. Now click the Download JSON button to download the credentials in JSON format.

Since you have the credentials on your computer, we can now start coding our script.

## Using `youtube-api` to upload the video

We will set up a [`lien`](https://github.com/LienJS/Lien) server which will handle the OAuth2 callback url.

See the inline comments. It's easier to understand the things.

```
// YouTube will handle the YouTube API requests
var Youtube = require("youtube-api")

 // We will use `fs` to read the video file
 , Fs = require("fs")

 // `r-json` will read the JSON file (credentials)
 , ReadJson = require("r-json")

 // `lien` handles the server requests (localhost:5000)
 , Lien = require("lien")

 // Logging things
 , Logger = require("bug-killer")

 // Open in the default browser
 , Opn = require("opn")
 ;

// Copy the downloaded JSON file in `credentials.json`
const CREDENTIALS = ReadJson("./credentials.json")
 // Set the video path (it can be any video file)
 , VIDEO_PATH = "video.mp4"
 ;

// Init the lien server
var server = new Lien({
 host: "localhost"
 , port: 5000
});

// Create the stdin interface (prompts)
var stdIn = ReadLine.createInterface({
 input: process.stdin
 , output: process.stdout
});

// Authenticate using the credentials
var oauth = Youtube.authenticate({
 type: "oauth"
 , client_id: CREDENTIALS.web.client_id
 , client_secret: CREDENTIALS.web.client_secret
 , redirect_url: CREDENTIALS.web.redirect_uris[0]
});

// Open the authentication url in the default browser
Opn(oauth.generateAuthUrl({
 access_type: "offline"
 , scope: ["https://www.googleapis.com/auth/youtube.upload"]
}));

// Here we're waiting for the OAuth2 redirect containing the auth code
server.page.add("/oauth2callback", function (lien) {
 Logger.log("Trying to get the token using the following code: " + lien.search.code);

 // Get the access token
 oauth.getToken(lien.search.code, function(err, tokens) {
 if (err) { lien.end(err, 400); return Logger.log(err); }

 // Set the credentials
 oauth.setCredentials(tokens);

 // And finally upload the video! Yay!
 Youtube.videos.insert({
 resource: {
 // Video title and description
 snippet: {
 title: "Testing YoutTube API NodeJS module"
 , description: "Test video upload via YouTube API"
 }
 // I don't want to spam my subscribers
 , status: {
 privacyStatus: "private"
 }
 }
 // This is for the callback function
 , part: "snippet,status"

 // Create the readable stream to upload the video
 , media: {
 body: Fs.createReadStream(VIDEO_PATH)
 }
 }, function (err, data) {
 if (err) { return lien.end(err, 400); }
 lien.end(data);
 });
 });
});
```

Before running the script, we have to install some dependencies:

```
$ npm i r-json lien opn bug-killer youtube-api
```

Then just run the script `node your-script.js`. You will have to allow your Google application to access your YouTube account and be patient until your video is uploaded.

## Results

If everything runs smoothly, you will see your video in your YouTube account. If you want to play with other YouTube resources check out [this playground application](https://github.com/IonicaBizau/test-youtube-api).

If you have any questions, ideas or anything, [check out the `youtube-api` package on GitHub](https://github.com/IonicaBizau/youtube-api) and [open an issue](https://github.com/IonicaBizau/youtube-api) if the things are still unclear to you.

Happy uploading!
