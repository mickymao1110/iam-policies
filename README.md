# iam-policies

> 

[![NPM](https://img.shields.io/npm/v/iam-policies.svg)](https://www.npmjs.com/package/iam-policies) [![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)

## About

Define an allowed or denied set of actions against a set of resources with optional context and conditions.

Deny rules trump allow rules.

This is a fork of [@ddt/iam](https://www.npmjs.com/package/@ddt/iam) updated with new functionalities.

## Install

```bash
npm install --save iam-policies
```

Or

```bash
yarn add iam-policies
```

## Usage

```js
const {Role}=require('iam-policies')

const role = new Role([
  {
    effect: 'allow', // optional, defaults to allow
    resource: ['secrets:${user.id}:*'],
    action: ['read', 'write'],
  },
  {
    resource: ['secrets:{${user.bestfriends}}:*'],
    action: 'read',
  },
  {
    effect: 'deny',
    resource: 'secrets:admin:*',
    action: 'read'
  },
])

const context = { user: { id: 456, bestfriends: [123, 563, 1211] } }

// true
role.can('read', 'secrets:563:sshhh', context)
// false
role.can('read', 'secrets:admin:super-secret', context)
 
const friendsWithAdminContext = { user: { id: 456, bestfriends: ['admin'] } }

// false
role.can('read', 'secrets:admin:super-secret', friendsWithAdminContext)

const adminRole = new Role([
  {
    resource: '*',
    action: '*',
  },
])

// true
adminRole.can('read', 'someResource')
// true
adminRole.can('write', 'otherResource')

const conditions={
  "greatherThan":function(data,expected){
    return data>expected
  }
}

const roleWithCondition = new Role([
  {
    effect: 'allow', // optional, defaults to allow
    resource: 'secrets:*',
    action: ['read', 'write'],
    condition: {
      "greatherThan":{
          'user.age':18
      }
    }
  },
], conditions)
 
// true
roleWithCondition.can('read', 'secrets:sshhh', { user: { age: 19 } })
// false
roleWithCondition.can('read', 'secrets:admin:super-secret', { user: { age: 18 } })
```