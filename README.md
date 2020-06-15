# Seia-Soto/twitchArc

The archiving system for Twitch broadcasts.

## Table of Contents

- [Todo](#todo)
- [Development](#development)
- [Usage](#usage)
- [API](#api)
- [License](#license)

----

# Todo

- [x] Twitch private client ID: get Twitch private API token(id) via web driver.
- [x] Twitch channel status: get Twitch channel status via API.
- [ ] Twitch stream m3u8: get the metadata of the channel streams.

# Development

If you want to dive into the function definitions, go [API](#api) section to see deeply.

## Environment

I have developed this application in following environment:

- Node.JS v10.20.1 (via NVM)
- Windows 10 x64 Pro 19H1
- Atom-Editor
- Standard.JS

## Code styles

There is some rules that I personally keep in mind.

1. ESlint
  - Use [Standard.JS](https://standardjs.com/).
  - We may disable some rules as we need but keep if it is not essential.

2. Personal rules
  - Use 2 spaces as tab.
  - Don't use semicolon unless it is **really** required.
  - Don't use NPM instead of Yarnpkg. (Don't create the lock file of NPM)
  - Use [debug](https://www.npmjs.com/package/debug) package as logging package. (It isn't native module)
  - Don't use `import` or `require` keyword together and make a collection of `import` or `require` as the type of module(local(`./`) or dependency(or global module)).

## Installation

If you want to start the development of this application, just install dependencies!
Developing Node.JS application is not that hard to start.

```bash
yarn
```

- I prefer and use [Yarnpkg](https://classic.yarnpkg.com/lang/en/) personally. Also, NPM will work well, but if you want to maximize the compatibility via lock file, install it.

## Testing

First of all, I don't use any testing utility as you can see.
Instead of, I included `.test.js` files to see how the specific function works.

- Also, I don't prefer TypeScript. Personally, use [deepscan.io](https://deepscan.io) to inspect the bugs at the development time.

# Usage

There is no available executable command to this repository. Actually, scripts won't work yet because this application is not completed and won't work.

- If you want to use specific function of this application, you can install this repository into your project instead. See [API](#api) section.

# API

If you want to use this application as module, you can hack it via installing from GitHub!

## Installation

You can install this package via Yarnpkg.

```bash
yarn add https://github.com/Seia-Soto/twitchArc
```

If you want to use NPM.

```bash
npm i Seia-Soto/twitchArc
```

### Accessing the specific function

I personally locate the core functions in [`/structures`](/structures) folder and create the `index.js` file to use easily from anywhere.
So, you can import specific function you want via following way.

```javascript
// NOTE: Require `/structures` directly.
const {
  twitch: {
    getPrivateToken
  }
} = require('Seia-Soto/twitchArc/structures')
```

## Documentation

### structures/twitch/getPrivateToken

Get the private token(client id) from Twitch web app via playwright(web driver, see [microsoft/playwright](https://github.com/microsoft/playwright)).

- arguments
  - opts \<Object\> (optional)
    - domain \<String\>: `twitch.tv`
    - timeout \<Number\>: 15 * 1000
- returns
  - <Promise: Object>
    - clientID: <String> (optional)
    - error: <Error> (optional)

```js
const {
  twitch: {
    getPrivateToken
  }
} = require('Seia-Soto/twitchArc/structures')

getPrivateToken()
  .then(({ clientID = 'No client ID detected!' }) => console.log(clientID))
```

#### Notes

- Getting client ID from Twitch web app.

Twitch uses client ID to determine the API requester and changed the private API requesting method to use client ID forcibly.
As a result, we need to crawl it if we don't want to update the static client ID every time it changes.
In `network` tab of web browser development tools, we can see the request which ends with `/access_token` and that request should include the client id in its `Client-ID` header.
How simple it is?
Also, it is not that difficult because there is a lot of web driver library.
In this time, I selected the `playwright` project from Microsoft as web driver library to control web browser.

- Determining when we should stop crawling.

To determine when is the end of page load, we cannot use events such as `load` or `documentloaded` because Twitch web app is React.JS app.
React.JS as front-end library which builds the website via JavaScript, we cannot stop the checking requests with `documentloaded`.
Also, about `load` event, since Twitch is streaming website, the request will be created continuously unless we close the tab.
So, instead of using event listeners, I have decided to use timer.

### structures/twitch/getChannelStatus

Get the status of the channel including stream information via Twitch GQL api.

- arguments
  - opts \<Object\>
    - gqlURL \<String\>: `https://gql.twitch.tv/gql` (optional)
    - username \<String\>
    - clientID \<String\>
- returns
  - \<Promise: Object\>
    - data \<Object\>
      - user \<Object\> (optional)
        - stream \<Object\> (optional)

If you want to see the what actually you can get, please refer the Graph QL query in the source code.

```js
const {
  twitch: {
    getChannelStatus
  }
} = require('Seia-Soto/twitchArc/structures')

getChannelStatus({ clientID: 'kimne78kx3ncx6brgo4mv6wki5h1ko', username: 'fluentAroma' })
  .then(data => console.log(data))
```

#### Notes

- About Twitch unofficial GQL API.

You may find the `/gql` XHR requests in the network tab of the web browser developer tools.
Yes, it is Graph QL API endpoint of Twitch.
The official GQL API of Twitch was not introduced in public, so I used some awesome tools to build the query. (The current query content is from the following repository because I googled about it after the discovery: [mauricew/twitch-graphql-api](https://github.com/mauricew/twitch-graphql-api))
To dig additional information of the private GQL API of Twitch, I used [Insomnia REST API Client](https://insomnia.rest/) which is really powerful and easy to use.
The Insomnia recognized the GQL API endpoint and automatically crawled the documentation for me.
Isn't it really comfortable even this is included in official GQL specification?

- Check the status of user.

If you want to check if the user is exists and is streaming, just `null` check the returning payload.

# License

If you want, please refer this repository in your project :>

```
MIT License Copyright 2020 Seia-Soto

Permission is hereby granted, free of
charge, to any person obtaining a copy of this software and associated
documentation files (the "Software"), to deal in the Software without
restriction, including without limitation the rights to use, copy, modify, merge,
publish, distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to the
following conditions:

The above copyright notice and this permission notice
(including the next paragraph) shall be included in all copies or substantial
portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF
ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO
EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```