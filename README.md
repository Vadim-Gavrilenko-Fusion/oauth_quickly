# oauth_quickly

## OAuth - open authorisation

|client                     | Server                                       | OAuth prowider (Google)                                |
|---------------------------|----------------------------------------------|--------------------------------------------------------|
|/auth/provider -->         |  /auth/google handler -->                    | Grant permission                                       |
|                           |  receive user details from provider (Google) | <-- redirect to custom route e.g. auth/provider/cb     |
|                           | loockup/ create user in own databadse        |                                                        |
|  browser stores cookise   | <-- create unique cookie                     |                                                        |
|             -->           | decode cookie & retrieve user information    |                                                        |

## Passport install
```
npm i passport
npm i passport-google-oauth
npm i passport-google-oauth20
```
## Create a routes middleware
Google
```
router.get('/google', passport.authenticate('google', {
  scope: [
    'https://www.googleapis.com/auth/userinfo.profile',
    'https://www.googleapis.com/auth/userinfo.email'
  ]
}));
```

## Create a file with options: 
```
const passport = require('passport');
const GoogleStrategy = require('passport-google-oauth20').Strategy;

const config = require('../config');

passport.serializeUser((user, done) => {
  done(null, user);
});

passport.deserializeUser((user, done) => {
  done(null, user);
});

module.exports = passport.use(new GoogleStrategy({
  // options for google strategy
  clientID: config.googleAuth.GOOGLE_CONSUMER_KEY,
  clientSecret: config.googleAuth.GOOGLE_CONSUMER_SECRET,
  callbackURL: config.googleAuth.callbackURL,          - redirect URI in Google
  proxy: true
},
  // passport callback function
  // accessToken, refreshToken, profile, done  
(async (token, tokenSecret, profile, done) => {

  new User({
    userName: profile.displayName,
    ...
  })
  try {
    done(null, profile);
  } catch (error) {
    done(error, null);
  }
})));
```

## Auth in Google page

redirect URI in Google - адрес, на который перенаправляет ответ Google 
```
key: AIzaSyBqDy3KMC9gx4dbsm2hSsB_kOcERcmPkBQ

102014241030320709789
 
ClientID = 617155753533-efeoq7r4knv8vv5rbk51v4i6cu7rtrsd.apps.googleusercontent.com
```

## Create middleware in route/index.js
```
const passport = require('./utils/passport')
```
## Create a riderect URL in routes 
callbackURL: config.googleAuth.callbackURL
```
// Google
router.get('/google', passport.authenticate('google', {
  scope: [
    'https://www.googleapis.com/auth/userinfo.profile',
    'https://www.googleapis.com/auth/userinfo.email'
  ]
}));

// Callback route for google to redirect ot
route.get('/google/redirect', passport.authenticate('google'), (req, res) => {
  res.send('URL);
})
```
