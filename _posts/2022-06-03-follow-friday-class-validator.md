---
title: FFP - Class validator
date: 2022-06-03 08:00:00 +02:00
code: true
category: follow friday project
keywords: typescript, javascript, oop, follow friday project
---

Cada viernes comparto algún recurso interesante que ehe encuentro por la red. Sobretodo enfocado al desarrollo.

Este viernes quiere compartir **[class-validator](https://github.com/typestack/class-validator)**.

Este proyecto "permite el uso de la validación a partir de decoradores (o no). Utiliza internamente Validator.js para realizar la validación. Class-Validator funciona tanto en las plataformas de navegador como en Node.js".

<!--more-->

## Instalación

```
npm install class-validator --save
```

> Note: Please use at least npm@6 when using class-validator. From npm@6 the dependency tree is flattened, which is required by `class-validator` to function properly.

## Uso

Cree su clase y coloque algunos decoradores de validación en las propiedades que desea validar:

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

Para más info, visita el proyecto original en [github repo](https://github.com/typestack/class-validator).