# Elite Boutique

Tienda de comercio electrónico sobre Bagisto 2.5, Laravel y Vue. Este repositorio contiene el código de la tienda y de su panel de administración.

<p align="center">
  <img src="public/elite-boutique-logo.png" alt="Elite Boutique" width="190">
</p>

[Instalación local](#instalación-local) · [Configuración de la tienda](#configuración-de-la-tienda) · [Solución de problemas](#solución-de-problemas)

## Instalación local

### 1. Descarga el proyecto

Necesitas Git, Composer 2, PHP 8.4 o 8.5 con las extensiones requeridas por [composer.json](composer.json) y un servidor MySQL 8.0, MariaDB 10.11 o PostgreSQL 16. Los ejemplos usan MySQL. Para PostgreSQL, habilita también `pdo_pgsql`.

```bash
git clone https://github.com/rdi0r/ecommerce.git bagisto
cd bagisto
composer install
```

### 2. Prepara la base de datos y el entorno

Crea una base de datos **vacía** y un usuario con permisos para crear y modificar tablas en ella. Copia el archivo de entorno de ejemplo.

```powershell
Copy-Item .env.example .env
```

En Bash, usa `cp .env.example .env`. Ajusta los valores de ejemplo en `.env` con los de tu entorno.

```dotenv
APP_NAME="Elite Boutique"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000
APP_ADMIN_URL=admin
APP_TIMEZONE=America/Mazatlan
APP_LOCALE=es
APP_CURRENCY=MXN

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=elite_boutique
DB_USERNAME=usuario_local
DB_PASSWORD=
DB_PREFIX=
```

Para MariaDB, usa `DB_CONNECTION=mariadb`. Para PostgreSQL, usa `DB_CONNECTION=pgsql` y el puerto de tu servidor, normalmente `5432`. Guarda las credenciales reales en tu `.env` local y mantén ese archivo fuera de Git.

### 3. Ejecuta el instalador

```bash
php artisan bagisto:install
php artisan migrate:status
php artisan serve --host=localhost --port=8000
```

En el instalador confirma el idioma, la moneda y la conexión de base de datos. Crea el usuario administrador y decide si quieres cargar productos de demostración. El instalador genera la clave de la aplicación, ejecuta las migraciones y crea el enlace de almacenamiento público.

**Usa `bagisto:install` únicamente para una instalación nueva.** El instalador elimina las tablas de la base seleccionada. Para actualizar una instalación existente, sigue el procedimiento de actualización en [UPGRADE.md](UPGRADE.md).

Con el servidor iniciado, abre la [tienda local](http://localhost:8000/) o el [panel de administración](http://localhost:8000/admin/login).

### Variables del entorno

Los valores del ejemplo preparan la tienda para español y pesos mexicanos. [.env.example](.env.example) contiene la plantilla completa.

| Variable | Valor del ejemplo | Uso |
| --- | --- | --- |
| `APP_URL` | `http://localhost:8000` | URL pública que usa Bagisto para generar enlaces y cargar recursos. |
| `APP_ADMIN_URL` | `admin` | Prefijo de la ruta del panel. |
| `APP_LOCALE`, `APP_CURRENCY`, `APP_TIMEZONE` | `es`, `MXN`, `America/Mazatlan` | Valores iniciales de idioma, moneda y zona horaria. |
| `DB_CONNECTION`, `DB_HOST`, `DB_PORT` | `mysql`, `127.0.0.1`, `3306` | Motor y dirección del servidor de base de datos. |
| `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD` | Credenciales locales | Base de datos y cuenta con acceso a ella. |
| `DB_PREFIX` | Vacío | Prefijo opcional de las tablas. Si conectas una base existente, usa el prefijo con el que se creó. |

## Configuración de la tienda

1. En **Configuración → Canales**, edita el canal de la tienda. Selecciona **Español** y **MXN**, verifica su categoría raíz y revisa el logo, el favicon y el **Meta título** de la página de inicio.
2. En **Configurar → Ventas**, revisa los métodos de envío y pago activos. Para PayPal, usa una cuenta Sandbox y prueba el checkout antes de activar cobros reales. Smart Button requiere **Client ID** y **Client Secret**. Standard usa el correo de la cuenta PayPal Business y notificaciones IPN.
3. Crea o importa tus categorías, productos e inventario. Haz una compra de prueba para comprobar el carrito, el envío y la confirmación del pedido.

Con Apache o Nginx, apunta la raíz pública del sitio a `public/` y usa la URL del sitio como `APP_URL`. Por ejemplo, si Apache sirve la tienda en `http://localhost/tienda`, usa esa URL en `.env` y entra al panel en `/tienda/admin/login`.

## Solución de problemas

| Síntoma | Qué revisar |
| --- | --- |
| Composer informa que la versión de PHP no cumple los requisitos. | Ejecuta `php -v` en la misma terminal desde la que llamas a Composer. Si hay varias versiones instaladas, ajusta el `PATH` para usar PHP 8.4 o 8.5. |
| MySQL devuelve `SQLSTATE[HY000] [1045]`. | Verifica el usuario y la contraseña de `.env` y confirma que ese usuario tiene acceso a la base desde el host configurado. |
| El panel carga sin estilos o muestra `Vite manifest not found`. | Comprueba que el servidor apunta a `public/` y que el checkout contiene los archivos compilados de `public/themes/`. |
| La tienda genera enlaces a otro dominio o puerto. | Corrige `APP_URL` y ejecuta `php artisan optimize:clear`. |

## Desarrollo y licencia

Consulta [AGENTS.md](AGENTS.md) para los comandos de desarrollo y pruebas, [CONTRIBUTING.md](CONTRIBUTING.md) para contribuir y [LICENSE](LICENSE) para la licencia MIT. La [documentación de Bagisto](https://devdocs.bagisto.com/) y el [foro](https://forums.bagisto.com/) cubren la plataforma base.

## 🐳 Run with Docker

A single command gives you a fully installed store — migrations, seed data and search indexes are all baked into the image, so there is no first-run setup.

```bash
docker run -d --name bagisto -p 80:80 webkul/bagisto:latest
```

Images are published for each web server and each database, so you can match whatever you already run:

| | MySQL 8.0 | MariaDB 10.11 | PostgreSQL 16 |
|---|---|---|---|
| **Nginx** | `:latest` · `:latest-nginx` · `:latest-nginx-mysql` | `:latest-nginx-mariadb` | `:latest-nginx-postgres` |
| **Apache** | `:latest-apache` · `:latest-apache-mysql` | `:latest-apache-mariadb` | `:latest-apache-postgres` |
| **OpenLiteSpeed** | `:latest-litespeed` · `:latest-litespeed-mysql` | `:latest-litespeed-mariadb` | `:latest-litespeed-postgres` |

Pin a release by swapping `latest` for the version, e.g. `webkul/bagisto:2.5.0-nginx-postgres`. Setting `DB_HOST` to an external server skips the bundled database entirely. Full details in [`docker/production/README.md`](docker/production/README.md).


## ☁️ Cloud Installation via Amazon AMI

You can also deploy Bagisto quickly using our pre-configured Amazon Machine Image (AMI) available on the AWS Marketplace:

👉 [**Launch Bagisto on AWS**](https://aws.amazon.com/marketplace/pp/prodview-r3xv62axcqkpa)

This AMI allows you to get started with Bagisto on a cloud environment without manual setup. Ideal for scalable production or testing environments.
