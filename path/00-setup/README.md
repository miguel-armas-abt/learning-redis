# CONFIGURACIÓN

[← Regresar a notas](../../README.md) <br>

----

## ⚙️ Instalar Redis

[Descargar](https://github.com/microsoftarchive/redis/releases) `Redis-x64` en `.zip` y guardar los binarios en la ruta sugerida `C:\dev-environment\redis\Redis-x64-3.0.504`.

> ### ▶️ Encender servidor de redis
> ```shell script
> redis-server
> redis-server ./redis.windows.conf # configure el archivo con un password personalizado
> ```
---

> ### ▶️ Encender cliente de redis
> ```shell script
> redis-cli
> redis-cli AUTH <password>
> ```
---