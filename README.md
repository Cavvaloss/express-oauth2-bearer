[![Build Status](https://travis-ci.org/auth0/express-openid-jwt.svg?branch=master)](https://travis-ci.org/auth0/express-openid-jwt)

This library provides an authentication middleware for express.js that validate access tokens.

The purpose of this library is to protect oauth2 resources.

## Installation

```
npm i express-oauth2-bearer --save
```

## Basic Usage

If the access token received by your API are JWT tokens signed in the same way than OpenID Connect id_tokens, you can use the openid strategy as follows:

```javascript
const { auth, strategies, requiresScopes } = require('express-oauth2-bearer');

app.use(auth(strategies.openid({
  issuerBaseURL: 'https://foobar.auth0.com',
  allowedAudiences: 'https://api.mysite.com'
})));

app.get('/products',
  requiresScopes('read:products'),
  (req, res) => {
    console.dir(req.auth.claims);
    res.sendStatus(200);
  });
```

The OpenID strategy is the default strategy and ISSUER_BASE_URL and ALLOWED_AUDIENCES can be configured with environment variables, so the above code is equal to this:

```javascript
const { auth, requiresScopes } = require('express-oauth2-bearer');

app.use(auth());

app.get('/products',
  requiresScopes('read:products'),
  (req, res) => {
    console.dir(req.auth.claims);
    res.sendStatus(200);
  });
```

You can provide your own strategy as follows:

```javascript
const { auth, strategies, requiresScopes } = require('express-oauth2-bearer');

const validateAccesToken = async (token) => {
  const token = await db.tokens.find(token);
  if (token.expired) { return; }
  return token;
};

app.use(auth(validateAcessToken)));

app.get('/products',
  requiresScopes('read:products'),
  (req, res) => {
    console.dir(req.auth.claims);
    res.sendStatus(200);
  });
```

## Parameters

`jwt.auth` accepts an asynchronous function receiving a token and returning a set of claims.

`jwt.strategies.openid` accepts the following parameters:


| Name                | Default                         | Description                                                                    |
|---------------------|---------------------------------|--------------------------------------------------------------------------------|
| issuerBaseURL       | `env.ISSUER_BASE_URL`           | The url address for the token issuer.                                          |
| allowedAudiences    | `env.ALLOWED_AUDIENCES.split(',')`       | The allowed audiences for the token.                                           |
| clockTolerance      | `5`                             | The clock's tolerance in seconds for token verification.                       |
| clientSecret        | `env.CLIENT_SECRET`             | The client secret, only required if you need to validate tokens signed with symmetric algorithms. |

`jwt.requiresScopes` accepts either an string or an array of strings.

## License

MIT 2018 - Auth0 Inc.
