# micro-chain [![XO code style](https://img.shields.io/badge/code_style-XO-5ed9c7.svg)](https://github.com/sindresorhus/xo) [![Build Status](https://travis-ci.org/dimapaloskin/micro-chain.svg?branch=master)](https://travis-ci.org/dimapaloskin/micro-chain)

Helps to build chains from your [micro](https://github.com/zeit/micro/) services.

### Overview

Module provides simple ability to build chains like:
```
first.api.your-domain.com
  -> second.api.your-domain.com
    -> third.api.your-domain.com
      -> final.api.your-domain.com
```


### Install
```bash
npm install --save micro-chain
```

### Example

**index.js**
```js
  const chain = require('micro-chain');
  
  const options = {
    micro: 'app.js', // or just your micro async function required before
    location: /\/some-path$/, // optional. chain will be executed only if request url matched
    chain: [{
      target: 'account.api.your-domain.com',
      mergeJson: true // response json will be merged with
                      // request json and will be passed down the chain
     }, {
      target: 'storage.api.your-domain.com/s3/save',
      mergeJson: true,
      allowedStatuses: [200, 201], // array of allowed statuses. 
                                   // request will rejected if target's response has not included status
                                   // response will equal target's response
      // will modify request data and return result if declared
      // receive Buffer
      transformRequestBody: body => {
        if (!body || !body.length) return body;
        body = JSON.parse(body);
        delete body.secret;
        return body;
      },
      // will modify response body if declared
      // receive Buffer
      transformReponseBody: body => {
        if (!body || !body.length) return body;
        body = JSON.parse(body);
        body.newProp = true;
        delete body.token;
        return body;
      }
     }, {
       // if host is not declared will extract host from original request
       target: '/notify'
     }, {
       target: 'some-stuff.api.your-domain.com',
       sendOriginalBody: true // modifed body passed down the chain.
                              // use this option if you want send original data
     }]
  };
  
  module.exports = chain(options);
```

Run:
```bash
micro index.js
```

### Author
[Dmitry Pavlovsky](http://palosk.in)
