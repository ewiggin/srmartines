---
title: Cómo usar correctamente defensivas en tu código
date: 2022-04-06 16:54:00 +01:00
code: true
category: off-topic
keywords: off-topic, code, best practices, typescript
---

No importa el código, lo importante es que funcione. Error. Es el principio de un mal diseño, un mal código y un paraíso para los bugs. Aunque pudieras presentar una demo con este razonamiento, no podrás escalar de forma adecuada y hacer de tu producto una estrella.

Unos pequeños consejos para mejorar tu código de forma sencilla pero efectiva. 

<!--more-->

## ¿Qué tipo de orador eres?

En realidad este código no es que este "mal" ya que cumple una función. Pero al igual que pasa con las personas, a veces podemos explicar las cosas de varias maneras. 

Unos al no saber como sintetizar lo que quieren decir hablan y hablan durante un rato, dando rodeos y dando detalles posiblemente superfluos sobre un asunto en particular. Cuando llevan 5 minutos explicando, el que los escucha se ha perdido y les pide que repita algo del principio.

```typescript
// Complejidad a la hora de leer el código
if (user) {
    if (user.id) {
        let isAdmin
        if (user.role) {
            isAdmin = user.role === 'Admin'
        } else {
            isAdmin = false
        }

        return isAdmin

    } else {
        throw new Error('user not found')    
    }
} else {
    throw new Error('user not found')
}
```

Otros en cambio, tienen la capacidad de sintetizar todo lo que dicen y lo resumen en un par de palabras. Sobre estos, probablemente el que les escuche les pedirán que vuelvan a repetir lo que han dicho y que aporten más detalles. 

```typescript
// Exceso en el uso de Operador condicional (ternary operator) 
return user ? user.id ? user.role ? user.role === 'Admin' : false  : throw new Error() : throw new Error()
```

Y finalmente, están los que tienen en cuenta el contexto, el publico objetivo y a ellos mismos. Estos últimos, usan el numero adecuado de frases evitando detalles superfluos sobre el tema y el que lo escucha lo entiende a la primera. 

```typescript
// Uso adecuado de las defensivas
if (!user || !user.id) {
    throw new Error('user not found')
}

return user.role === 'Admin'
```

En la programación al igual que en la comunicación entre personas es importante el cómo se dicen las cosas y en este caso, en cómo se escriben. 

## ¡Defendamos el código!

### Comprobación de valores nulos

Un consejo que doy a todo el que hace pair-programming conmigo es desconfiar del código, más aún dependiendo del lenguaje que se está usando. Dar por sentadas las cosas nos puede traer dolores de cabeza, básicamente porqué no podemos imaginarnos todos los escenario posibles.

```typescript
// Código erróneo
function getUserRole(user): string {
    return user.role; // X error valor nulo
}

// Posible solucion
function getUserRole(user): string {
    if (!user) {
        return 'no-role';
    }

    return user.role;
}
```

Cuando defendemos el código de posibles valores nulos, hace que este sea más robusto y seguro. Como has visto, no he usado ningún `else` (cuando sería correcto hacerlo) sino que he jugado con el `return`. Hablo de esto en la siguiente sección.

### Uso del return en vez de else

Como bien sabrás el `return` devuelve el valor de la función y detiene la ejecución del código del ámbito actual, por lo que todo lo que este debajo del `return` no se va a evaluar.

```typescript
function hello() {
    return 'hello';
    return 'martines'; // Esto nunca se va evaluar, ya hemos devuelto el resultado en el anterior return
}
```

Esto nos da juego de evitar innecesariamente el uso de `else` y facilita la lectura del código. En el siguiente ejemplo podemos comprovar como se van repitiendo innecesariamente le código, sobretodo en la parte del `else`. Este código _funciona_ pero no es suficiente. Imagina que esto formara parte de una librería de más de 400 lineas de código, si no procuramos simplificar con el objetivo de facilitar la lectura, una semana después de escribir este código nos costaría encontrarlo y entenderlo.

```typescript
function isAdmin(user): boolean {
    let isAdmin

    if (user) {
        if (user.id) {
            if (user.role) {
                isAdmin = user.role === 'Admin'
            } else {
                isAdmin = false
            }
        } else {
            isAdmin = false
        }
    } else {
        isAdmin = false
    }

    return isAdmin
}
```

Usando correctamente el `return` el código podría quedar simplificado y fácil de leer:

```typescript
function isAdmin(user): boolean {
    if (!user || !user.id) {
        return false;
    }

    return user.role === 'Admin'
}
```


### Short circuit evaluation

Una ultima manera con la que podemos defender y agilizar nuestro código es usando el `short circuit`. 
Se trata de asignar un valor haciendo un circuito conectado por operadores lógicos AND y OR, esto nos permite
usar booleanos para defender el código, usar valores por defecto en caso de ser `undefined` o `null`.

A continuación varios ejemplos:

#### Usar valores por defecto

```typescript
const userRoles = undefined;
const role = userRole || 'no-role'; 
// Resultado: `role` es 'no-role'
```
En este simple ejemplo usando el operador OR cuando asignamos una variable, podemos conseguir que en 
caso de que el valor que queramos asignar es `falsy` (0, undefined, null) se asignará el siguiente valor 
no `falsy`, en este caso `'no-role'`.

#### Comprobaciones en cadena

Además de asignar valores a las variables, podemos usar booleans para poder hacer comprobaciones y evitarnos
algunos `ifs`.

A continuación dos ejemplos:

```typescript
// AND circuit (devuelve el primer Falsy que encuentra o el ultimo valor)
const isAdmin = user && user.id && user.role === 'Admin'

// OR circuit (asigna el primer valor que sea truthy)
const notAdmin = !user || !user.id || user.role !== 'Admin' 
// en este caso como lo estamos negando se convierten en bolean
```

Referencias:

- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND)
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)

