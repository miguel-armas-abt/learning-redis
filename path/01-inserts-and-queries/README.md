# INSERCIÓN Y CONSULTA DE DATOS

[← Regresar a notas](../../README.md) <br>

---
[1. Modos de almacenamiento](#1-modos-de-almacenamiento) <br>
[2. Strings](#2-strings) <br>
[3. Hashes](#3-hashes) <br>
[4. Lists](#4-lists) <br>
[5. Sets](#5-sets) <br>
[6. Sorted sets](#6-sorted-sets) <br>
[7. Keys](#7-keys) <br>

---

## 1. Modos de almacenamiento

Redis admite guardar datos en caché utilizando distintos tipos de estructuras:

| Tipo de Dato    | Descripción                                                                           |
|-----------------|---------------------------------------------------------------------------------------|
| `String`        | Valor simple, ideal para almacenar datos básicos o respuestas de servicios.           |
| `Hash`          | Mapa de claves y valores, perfecto para representar objetos o perfiles de usuario.    |
| `List`          | Estructura de datos ordenada; utilizada en colas, logs y procesamiento por lotes.     |
| `Set`           | Colección de elementos únicos; útil para almacenar datos sin duplicados.              |
| `Sorted Set`    | Colección de elementos únicos con una puntuación; ideal para rankings o leaderboards. |

---

## 2. Strings

- Son los tipos de datos básicos en Redis.
- Se utilizan para almacenar <U>valores simples</U> como sesiones, tokens o respuestas de una API.

> ### ▶️ Insertar  
> ```shell script
> SET tokens:APP "eyJhbGciOiJSUzI1NiI...sInR5cCI6IkpXVCIsImt"
> ```
> 
> ### 🔎 Consultar
> ```shell script
> GET tokens:APP
> ```
> 
> Respuesta esperada:
> ```
> "eyJhbGciOiJSUzI1NiI...sInR5cCI6IkpXVCIsImt"
> ```

---

## 3. Hashes

- Permiten almacenar múltiples campos y valores asociados a una misma clave.
- Es ideal para representar entidades estructuradas.
- Redis no soporta consultas con filtros sobre los hash.
- Redis devuelve los datos de un hash como una secuencia de pares campo-valor.

> ### ▶️ Insertar
> Disponible para `Redis 4.0+`
> ```shell script
> HSET users:1001 code "76543210" name "Linus Torvalds" age 30
> ```
> 
> ### 🔎 Consultar
> ```shell script
> HGETALL users:1001
> ```
> 
> Respuesta esperada:
> ```
> 1) "code"
> 2) "76543210"
> 3) "name"
> 4) "Linus Torvalds"
> 5) "age"
> 6) "30"
> ```
>
> ### ❌ Eliminar un campo específico
> ```shell script
> HDEL users:1001 age
> ```

---

## 4. Lists

- Son colecciones ordenadas que permiten insertar y recuperar datos en un orden determinado.
- Son útiles en escenarios de colas y procesamiento de tareas.

> ### ▶️ Insertar
> ```shell script
> LPUSH poc-queue:payments "payment_01"
> LPUSH poc-queue:payments "payment_02"
> ```
> 
> ### 🔎 Consultar
> ```shell script
> LRANGE poc-queue:payments 0 -1
> ```
>
> Respuesta esperada:
> ```
> 1) "payment_01"
> 2) "payment_02"
> ```

---

## 5. Sets

- Son colecciones de elementos únicos.
- Ideales para eliminar duplicados y almacenar agrupaciones de ítems <u>sin un orden definido</u>.

> ### ▶️ Insertar
> ```shell script
> SADD set:colors "red"
> SADD set:colors "blue"
> SADD set:colors "green"
> ```
> ### 🔎 Consultar
> ```shell script
> SMEMBERS set:colors
> ```
>
> Respuesta esperada:
> ```
> 1) "blue"
> 2) "green"
> 3) "red"
> ```

---

## 6. Sorted Sets

- Similares a los Sets pero con la ventaja de asignar una puntuación a cada elemento, lo que permite mantenerlos ordenados.
- Esto es ideal para implementar rankings o leaderboards.

> ### ▶️ Insertar
> ```shell script
> ZADD players-ranking 1500 "player_A"
> ZADD players-ranking 2000 "player_B"
> ZADD players-ranking 1800 "player_C"
> ```
> ### 🔎 Consultar
> ```shell script
> ZRANGE players-ranking 0 -1 WITHSCORES
> ```
>
> Respuesta esperada:
> ```
> 1) "player_A"
> 2) "1500"
> 3) "player_C"
> 4) "1800"
> 5) "player_B"
> 6) "2000"
> ```

---

## 7. Keys
- Una key es el identificador asignado a un dato almacenado. Podría decirse que es el nombre que hemos definido para nuestra caché.
- Nomenclatura recomendada: `namespace:subkey:id`
- El comando `KEYS` permite hacer una búsqueda lineal de las keys mediante patrones (por ejemplo, `keys users:*`).
- Ejecutar `KEYS *` en entornos productivos puede afectar el rendimiento si son demasiadas keys.
- En producción, es preferible utilizar el comando `SCAN` para iterar las keys sin bloquear el servidor.


> ### 🔎 Consultar keys con un patrón específico
> ```shell script
> KEYS *
> KEYS tokens:*
> ```

> ### 🔎 SCAN
> ```shell script
> SCAN 0 MATCH tokens:* COUNT 100 # Iterar todas las keys que comiencen con `tokens:` a partir de un cursor inicial `0`
> ```

> ### 🔎 Verificar existencia de una key
> Retorna `1` si la key existe y `0` si no.
> ```shell script
> EXISTS tokens:APP
> ```

> ### 🔎 Determinar el tipo de dato almacenado en una key
> Esto te indicará si la key almacena un `string`, `hash`, `list`, `set`, `zset`, entre otros.
> ```shell script
> TYPE tokens:APP
> ```

> ### ❌ Eliminar una key
> Este comando es compatible con todos los tipos de datos (Strings, Hashes, Lists, Sets y Sorted Sets).
> ```shell script
> DEL tokens:APP
> FLUSHDB   # Eliminar todas las keys de la BD actual
> FLUSHALL  # Eliminar todas las keys de todas las BD
> ```

> ### ▶️ Renombrar una key
> ⚠️ **Advertencia:** Si `new_key` ya existe, será sobrescrita.
> ```shell script
> RENAME old_key new_key
> ```

> ### 🕓 Establecer un tiempo de expiración para una key
> Evitar que las keys permanezcan indefinidamente en memoria.
> ```shell script
> EXPIRE tokens:APP 3600 # 3600 segundos (1 hora)
> ```

> ### 🔎 Consultar el tiempo de expiración de una key
> ```shell script
> TTL tokens:APP
> ```