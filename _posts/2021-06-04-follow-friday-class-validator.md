---
title: FFP :: Class validator
date: 2021-06-03 08:00:00 +02:00
code: true
category: follow friday project
keywords: typescript, javascript, oop, follow friday project
---

Every friday I will share an awesome project or service.

This friday I will share **[class-validator](https://github.com/typestack/class-validator)**.

This project "allows use of decorator and non-decorator based validation. Internally uses validator.js to perform validation. Class-validator works on both browser and node.js platforms."

<!--more-->

## Installation

```
npm install class-validator --save
```

> Note: Please use at least npm@6 when using class-validator. From npm@6 the dependency tree is flattened, which is required by `class-validator` to function properly.

## Usage

Create your class and put some validation decorators on the properties you want to validate:

```typescript
import {
  validate,
  validateOrReject,
  Contains,
  IsInt,
  Length,
  IsEmail,
  IsFQDN,
  IsDate,
  Min,
  Max,
} from 'class-validator';

export class Post {
  @Length(10, 20)
  title: string;

  @Contains('hello')
  text: string;

  @IsInt()
  @Min(0)
  @Max(10)
  rating: number;

  @IsEmail()
  email: string;

  @IsFQDN()
  site: string;

  @IsDate()
  createDate: Date;
}

let post = new Post();
post.title = 'Hello'; // should not pass
post.text = 'this is a great post about hell world'; // should not pass
post.rating = 11; // should not pass
post.email = 'google.com'; // should not pass
post.site = 'googlecom'; // should not pass

validate(post).then(errors => {
  // errors is an array of validation errors
  if (errors.length > 0) {
    console.log('validation failed. errors: ', errors);
  } else {
    console.log('validation succeed');
  }
});

validateOrReject(post).catch(errors => {
  console.log('Promise rejected (validation failed). Errors: ', errors);
});
// or
async function validateOrRejectExample(input) {
  try {
    await validateOrReject(input);
  } catch (errors) {
    console.log('Caught promise rejection (validation failed). Errors: ', errors);
  }
}
```

For more documentation please visit the original project on [github repo](https://github.com/typestack/class-validator).