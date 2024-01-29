# apollo-mock-http

An easy and maintainable way of injecting mock data into Apollo GraphQL Client for fast feature development decoupled from API/Backend.

[![CircleCI](https://circleci.com/gh/intuit/apollo-mock-http/tree/main.svg?style=shield)](https://circleci.com/gh/intuit/apollo-mock-http/tree/main)
[![Coverage Status](https://coveralls.io/repos/github/intuit/apollo-mock-http/badge.svg?branch=main)](https://coveralls.io/github/intuit/apollo-mock-http?branch=master)
![NPM Version](https://img.shields.io/npm/v/apollo-mock-http)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)
![Contributions welcome](https://img.shields.io/badge/contributions-welcome-orange)

<!-- [![All Contributors](https://img.shields.io/github/all-contributors/intuit/apollo-mock-http?color=ee8449&style=flat-square)](#contributors) -->

Checkout the [Downloads trend](https://npm-stat.com/charts.html?package=apollo-mock-http)!

## Usage

```bash
yarn add apollo-mock-http -D
```

If you want a quick hands-on with the project, you can checkout this **[example usage in CodeSandbox](https://codesandbox.io/s/apollo-mock-http-example-7nwheq)**

```javascript
import { injectMock } from 'apollo-mock-http';

// Pass in the links that are making up the Apollo client, and other mock data configurations
injectMock({
  links,

  // Switch between mock and real with a flick :)
  enableMock: true,

  // Operations which need to be mocked (queries / mutations)
  targetOperations: ['getCompanyName', 'getCompanyEmployees'],

  // Mock data configurations for various operations
  mockData: {
    'getCompanyName': {data: {name: 'Test', __typename: 'String'}, errors: null},
    'getCompanyEmployees': {data: {employees: ['TestUser'], __typename: 'Employee'}, errors: null},
    'getCompanyId': {data: {id: '123', __typename: 'CompanyID'}, errors: null},
    ...
  },

  /* [Optional]
  By default, the mock link gets injected directly into the chain of links which is provided.

  In case, your links follow some structure like
    {linkName: 'L1', linkObj: new ApolloLink()}
  you can use this function to generate object containing mock link
  */
  createCustomLinkObj: (generatedMockLink) => {
    return {
      linkName: 'mockHttp',
      linkObj: generatedMockLink
    };
  }
})

```

For simulating error scenarios, use `errors` attribute in the mock response as below

```javascript
mockData: {
  'getCompanyName': {

    data: null,

    // Attribute (error or errors) usually depends on how your Error Links consume response. But this is a standard response Apollo Client can handle.
    errors: [{
      message: 'sample error'
    }]
  },
},
```

> [!TIP]
> You can also utilize data from localstorage as mockdata. The library looks for the localstorage key **'AMH_mockdata'** and uses that data in case mockdata isn't provided when instantiated. Ensure the string is a parseable JSON. NOTE: values in localstorage are strings!

## Architecture

In order to be able mock responses, we will inject a mock link into the link chain that the Apollo Client uses., and based on the configuration the relevant mocked response is returned.

[![Watch the video](./docs/architecture.gif)](./docs/architecture.gif)

## Contributing

Please check [Contributing](./CONTRIBUTING.md) guide for any collaboration. Thanks.

## Inspiration

Apollo GraphQL client is the default client for many enterprises for GraphQL data fetching on the browser side. During feature developments, many teams face the issue of APIs not ready. We tend to mock the data in the components, and while merging to master, we stress to remove the mocks to keep things clean. We run a huge risk here, and a lot of secondary checks get into picture to ensure master is always Production ready. Things could become easily unmanageable and could eventually blow up which could cause feature rollback, and customer getting blocked.

We have come up with a strategy of mocking the data near network layer. That means components interact just like how they interact in Production. There is a single place where mock data injection happens, and it's very easy to enable / disable the mocks, making it very easy for developers working together, and could reliably develop, test different scenarios of UI like success, failure, different responses etc. This brings a lot of confidence in rolling out. The biggest benefit is all teams can get themselves decoupled from APIs under construction, and move faster - Huge productivity. Also, this puts less load on Backend Data Servers, and huge bandwidth for others.
