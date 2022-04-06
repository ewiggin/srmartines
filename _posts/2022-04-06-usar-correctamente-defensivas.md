---
title: Cómo usar correctamente defensivas en tu codigo
date: 2022-04-06 16:54:00 +01:00
code: true
category: off-topic
keywords: off-topic, code, best-practices
---

## ¿Qué tipo de orador eres?

En realidad este codigo no es que este "mal" ya que cumple una función. Pero al igual que pasa con las personas, a veces podemos explicar las cosas de varias maneras. 

Unos al no saber como sintentizar lo que quieren decir hablan y hablan durante un rato, dando rodeos y dando detalles posiblemente superfluos sobre un asunto en particular. Cuando llevan 5 minutos explicando, el que los escucha se ha perdido y les pide que repita algo del principio.

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

Otros en cambio, tienen la capacidad de sintetizar todo lo que dicen y lo resumen en un par de palabras. Sobre estos, probablmente el que les escuche les pediran que vuelvan a repetir lo que han dicho y que aporten más detalles. 

```typescript
// Exceso en el uso de Operador condicional (ternary operator) 
return user ? user.id ? user.role ? user.role === 'Admin' : false  : throw new Error() : throw new Error()
```

Y finalmente, están los que tienen en cuenta el contexto, el publico objetivo y a ellos mismos. Estos ultimos, usan el numero adecuado de frases evitando detalles superfluos sobre el tema y el que lo escucha lo entiende a la primera. 

```typescript
// Uso adecuado de las defensivas
if (!user || !user.id) {
    throw new Error('user not found')
}

return user.role === 'Admin'
```

En la programación al igual que en la comunicación entre personas es importante el cómo se dicen las cosas y en este caso, en cómo se escriben. 

## ¡Defendamos el código!

### Comprovación de valores nulos

Un consejo que doy a todo el que hace pair-programming conmigo es desconfiar del código, más aún dependiendo del lenguaje que se está usando. Dar por sentadas las cosas nos puede traer dolores de cabeza, basicamente porqué no podemos imaginarnos todos los escenario posibles.

```typescript
// Codigo erroneo
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

Cuando defendemos el codigo de posibles valores nulos, hace que este sea más robusto y seguro. Como has visto, no he usado ningún `else` (cuando sería correcto hacerlo) sino que he jugado con el `return`. Hablo de esto en la siguiente sección.

### Uso del return en vez de else

Como bien sabrás el `return` devuelve el valor de la función y detiene la ejecución del codigo del ámbito actual, por lo que todo lo que este debajo del `return` no se va a evaluar.

```typescript
function hello() {
    return 'hello';
    return 'martines'; // Esto nunca se va evaluar, ya hemos devuelto el resultado en el anterior return
}
```

Esto nos da juego de evitar innecesariamente el uso de `else` y facilita la lectura del código. En el siguiente ejemplo podemos comprovar como se van repitiendo innecesariamente le codigo, sobretodo en la parte del `else`. Este codigo _funciona_ pero no es suficiente. Imagina que esto formara parte de una librería de más de 400 lineas de codigo, si no procuramos simplificar con el objetivo de facilitar la lectura, una semana después de escribir este código nos costaría encontrarlo y entenderlo.

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

Usando correctamente el `return` el codigo podría quedar simplificado y fácil de leer:

```typescript
function isAdmin(user): boolean {
    if (!user || !user.id) {
        return false;
    }

    return user.role === 'Admin'
}
```


### Short circuit evaluation

- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND)
- [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_OR)

```typescript
// AND circuit (asigna el ultimo valor que sea TRUE, o el ultimo valor)
const isAdmin = user && user.id && user.role === 'Admin'

// OR circuit (asigna el primer valor que sea TRUE, o el ultimo)
const notAdmin = !user || !user.id || user.role !== 'Admin' 
```
