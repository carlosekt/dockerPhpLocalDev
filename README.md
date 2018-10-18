# dockerPhpLocalDev
Docker PHP development environment.

##Software Stack
- Nginx<br>
- Php 7.2 (optional: 5.6, 7.0, 7.1)<br>
- MariaDb 10<br>
- Redis<br>
- Php extensions: Phalcon 3, Memcached, Redis, MongoDB, Imagick, Opcache, Xdebug
- Composer, Artisan, Asset-Plugin

Directory structure:<br>
-- Your_projects_folder<br>
    |<br>
    -- sites<br>
    |  |<br>
    |  -- site_1<br>
    |  |<br>
    |  -- site_2<br>
    |<br>
    |<br>
    -- docker
    
##Installation
1. Copy example.env to .env
2. Change settings in .env
3. Add your_site.conf to /nginx/site-enabled (see /nginx/site-disabled/example.conf)
4. Add site host to your hosts file (example: 127.0.0.1     example.local)