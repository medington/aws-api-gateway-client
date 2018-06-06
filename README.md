[![dependencies Status](https://david-dm.org/kndt84/aws-api-gateway-client/status.svg)](https://david-dm.org/kndt84/aws-api-gateway-client)
[![Build Status](https://travis-ci.org/kndt84/aws-api-gateway-client.svg?branch=master)](https://travis-ci.org/kndt84/aws-api-gateway-client)
[![npm](https://img.shields.io/npm/dm/aws-api-gateway-client.svg)](https://www.npmjs.com/package/aws-api-gateway-client)


# Overview
A module for AWS API gateway client based on auto-generated JavaScript SDK. This module can be used not only for Node.js but for front-end. In addition, it generalizes original SDK's endpoint specific methods.

Reference:  
https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-generate-sdk.html

# Prerequisites
For the JavaScript SDK to work your APIs need to support CORS. The Amazon API Gateway developer guide explains how to [setup CORS for an endpoint](https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html).

# Install
```
npm install aws-api-gateway-client
```

# Use the SDK in your project

Require module
```
var apigClientFactory = require('aws-api-gateway-client').default;
```

Set invokeUrl to config and create a client. For autholization, additional information is required as explained below.
```
config = {invokeUrl:'https://xxxxx.execute-api.us-east-1.amazonaws.com'}
var apigClient = apigClientFactory.newClient(config);
```

Calls to an API take the form outlined below. Each API call returns a promise, that invokes either a success and failure callback

```
var params = {
    //This is where any header, path, or querystring request params go. The key is the parameter named as defined in the API
    userId: '1234',
};
// Template syntax follows url-template https://www.npmjs.com/package/url-template
var pathTemplate = '/users/{userID}/profile'
var method = 'GET';
var additionalParams = {
    //If there are any unmodeled query parameters or headers that need to be sent with the request you can add them here
    headers: {
        param0: '',
        param1: ''
    },
    queryParams: {
        param0: '',
        param1: ''
    }
};
var body = {
    //This is where you define the body of the request
};

apigClient.invokeApi(params, pathTemplate, method, additionalParams, body)
    .then(function(result){
        //This is where you would put a success callback
    }).catch( function(result){
        //This is where you would put an error callback
    });
```

# Using AWS IAM for authorization
To initialize the SDK with AWS Credentials use the code below. Note, if you use credentials all requests to the API will be signed. This means you will have to set the appropiate CORS accept-* headers for each request.

```
var apigClient = apigClientFactory.newClient({
    invokeUrl:'https://xxxxx.execute-api.us-east-1.amazonaws.com',
    accessKey: 'ACCESS_KEY',
    secretKey: 'SECRET_KEY',
    sessionToken: 'SESSION_TOKEN', //OPTIONAL: If you are using temporary credentials you must include the session token
    region: 'eu-west-1', // OPTIONAL: The region where the API is deployed, by default this parameter is set to us-east-1
    systemClockOffset: 0, // OPTIONAL: An offset value in milliseconds to apply to signing time
    retries: 4, // OPTIONAL: Number of times to retry before failing. Uses axon-retry plugin.
    retryCondition: (err) => { // OPTIONAL: Callback to further control if request should be retried.  Uses axon-retry plugin.
      return err.response && err.response.status === 500;
    }
});
```

# Using API Keys
To use an API Key with the client SDK you can pass the key as a parameter to the Factory object. Note, if you use an apiKey it will be attached as the header 'x-api-key' to all requests to the API will be signed. This means you will have to set the appropiate CORS accept-* headers for each request.

```
var apigClient = apigClientFactory.newClient({
    apiKey: 'API_KEY'
});
```

# Troubleshooting
The standard Javascript SDK as generated by AWS API Gateway hard codes assertions for your query parameters and headers, which is not possible in this module. Therefore, the params object passed to your `apigClient.invokeApi()` must contain keys that conform exactly to your API definitions.

```
http://example.com/prod?userId=123&info=hello&state=NY

var params = {
    userId: '123',
    info: 'hello',
    state: 'NY'
}
```
