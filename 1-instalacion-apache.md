1. Instalar Apache

   ```sh
   sudo apt update
   sudo apt install apache2
   ```

2. Instalar Php modulos necesarios compatibles con Laravel

   ```sh
   sudo apt install php libapache2-mod-php php-mysql php-cli php-xml php-mbstring php-zip
   ```

3. Clonar el repositorio

   ```sh
    https://github.com/MiguelAngelRamos/book-api
   ```

4. Instalar Composer

   ```sh
   sudo apt install composer
   ```
5. Install

  ```sh
  composer install
  ```
6. Generar el Secret Api

 ```sh
 php artisan jwt:secret
 ```

7. Migraciones
  ```sh
  php artisan migrate
  ```