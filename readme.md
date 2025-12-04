# Todos los servicio que hemos levantado hasta ahora y como levantarlos 
- [Todos los servicio que hemos levantado hasta ahora y como levantarlos](#todos-los-servicio-que-hemos-levantado-hasta-ahora-y-como-levantarlos)
  - [Como levantar un servicio:](#como-levantar-un-servicio)
  - [Servicios levantados hasta ahora:](#servicios-levantados-hasta-ahora)
    - [Portaner:](#portaner)
    - [Excalidraw:](#excalidraw)
    - [Apache:](#apache)
    - [VSCode:](#vscode)
    - [Duplicati:](#duplicati)
    - [Snapdrop:](#snapdrop)
    - [Kali linux:](#kali-linux)
    - [Wordpress y db:](#wordpress-y-db)
    - [File-browser:](#file-browser)
    - [Firefox:](#firefox)
    - [Obsidian:](#obsidian)
    - [Libreoffice:](#libreoffice)
    - [Jelllyfin:](#jelllyfin)

## Como levantar un servicio:
Entrar a el codigo yml del documento y pegar el codigo del servicio que quieras levantar como se ve en esta foto:

![alt text](image.png)

Despues tienes que abrir la terminal en el ap
artado "View": 

![alt text](image-1.png)

Dentro de la terminal al escribir docker compose up -d:

![alt text](image-2.png)
![alt text](image-3.png)

Si tienes algun problema recuerda tener la aplicacion de Docker abierta o mirar si hay algo mal escrito en el codigo.

## Servicios levantados hasta ahora:
Si quieres levantar cualquiera de estos servicios solamente copia tu codigo en tu documento yml.

### Portaner:
```yml
portainer:
    image: portainer/portainer-ce:latest
    restart: always
    ports:
      - "9443:9443"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
```

### Excalidraw:
```yml
excalidraw:
    container_name: excalidraw
    image: excalidraw/excalidraw:latest
    ports:
      - "3030:80"
    restart: on-failure
```

### Apache:
```yml
  apache:
    image: httpd:2.4-alpine
    container_name: apache
    ports:
      - 8081:80
    volumes:
      - ./public:/usr/local/apache2/htdocs:ro
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost/ || exit 1"]
      interval: 10s
      timeout: 3s
      retries: 5
```

### VSCode:
```yml
  code:
    image: lscr.io/linuxserver/code-server:latest
    restart: always
    environment:
      PUID: 1000
      PGID: 1000
      TZ: Europe/Madrid
      PASSWORD: examplepass
    volumes:
      - ./:/config/workspace
      - code_config:/config
    ports:
      - "8443:8443"


volumes:
  code_config:
```

### Duplicati:
```yml
duplicati:
    image: lscr.io/linuxserver/duplicati:latest
    container_name: duplicati
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - SETTINGS_ENCRYPTION_KEY=pon_algo_aquí
      - CLI_ARGS= #optional
      - DUPLICATI__WEBSERVICE_PASSWORD=micontraseña
    volumes:
      - ./duplicati:/config
      - ./micarpetasegura:/soruce/micarpetasegura

    ports:
      - 8200:8200
    restart: unless-stopped

```

### Snapdrop:
```yml
  snapdrop:
    image: linuxserver/snapdrop:version-b8b78cc2
    container_name: snapdrop
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - ./config:/config
    ports:
      - 8081:80
      - 443:443
    restart: unless-stopped
```

### Kali linux:
```yml
  alpine-bash:
    image: mikeldalmauc/alpine-bash:v1.0
    container_name: alpine-bash
    restart: unless-stopped
    tty: true
    stdin_open: true

  kali:
    image: ibonfernandez/kali-nmap-ip
    container_name: kali
    restart: unless-stopped
    tty: true
    stdin_open: true
    cap_add:
    - NET_ADMIN
    - NET_RAW
    # Puedes montar herramientas o scripts locales:
    # volumes:
    #   - ./tools:/root/tools

  ftp:
    image: fauria/vsftpd
    container_name: ftp
    restart: unless-stopped
    environment:
      - FTP_USER=test
      - FTP_PASS=test123
      - PASV_ADDRESS=127.0.0.1
    ports:
      - "21:21"
      - "21000-21010:21000-21010"
    volumes:
      - ./ftpdata:/home/vsftpd
    tty: true

  ssh:
    image: linuxserver/openssh-server
    container_name: ssh
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Madrid
      - PASSWORD_ACCESS=true
      - USER_NAME=labuser
      - USER_PASSWORD=lab1234
    ports:
      - "2222:2222"
    tty: true

  dvwa:
    image: vulnerables/web-dvwa
    container_name: dvwa
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      - MYSQL_PASS=p@ssw0rd
    tty: true


  # OPCIONAL: Metasploitable 2 (vulnerable system completo)
  # metasploitable:
  #   image: tleemcjr/metasploitable2
  #   container_name: metasploitable
  #   restart: unless-stopped
  #   tty: true

networks:
  default:
    name: lab-net
```

### Wordpress y db:
```yml
  wordpress:
    image: wordpress
    restart: always
    
    ports:
      - 8080:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: exampleuser
      WORDPRESS_DB_PASSWORD: examplepass
      WORDPRESS_DB_NAME: exampledb
    volumes:
      - wordpress:/var/www/html

  db:
    image: mysql:8.0
    restart: always
    environment:
      MYSQL_DATABASE: exampledb
      MYSQL_USER: exampleuser
      MYSQL_PASSWORD: examplepass
      MYSQL_RANDOM_ROOT_PASSWORD: '1'
    volumes:
      - db:/var/lib/mysql

volumes:
  wordpress:
  db:
```

### File-browser:
```yml
  uploader:
    image: filebrowser/filebrowser:v2.23.0
    container_name: uploader
    volumes:
      - ${RUTA_BASE}:/srv
      - filebrowser_db:/database
    ports:
      - "60:80"   # ERROR DE SEGURIDAD: Puerto 80 reservado por sistema, cambialo a variable
    restart: unless-stopped
volumes:
  filebrowser_db:

```

### Firefox:
```yml
  firefox:
    image: jlesage/firefox  # OJO: ¿Esta imagen existe así tal cual? Verificad en Docker Hub.
    ports:
      - "${PORT_FIREFOX}:5800"
    volumes:
      - firefox_config:/config:rw
      -  ${RUTA_BASE}:/config/downloads
volumes:
  firefox_config:

```


### Obsidian:
```yml
  obsidian:
    image: lscr.io/linuxserver/obsidian:latest
    container_name: obsidian  
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=Etc/UTC
    volumes:
      - obsidian_config:/config
      -  ${RUTA_BASE}:/data
    ports:
      - "3000:3000" # ERROR: Conflictos de puerto si no se parametrizan. Usad variables.
      - "3001:3001" 
    shm_size: "1gb"
    restart: unless-stopped
volumes:
  obsidian_config:

```

### Libreoffice:
```yml
  libreoffice:
    image: lscr.io/linuxserver/libreoffice:latest
    container_name: libreoffice
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
      - TZ=Etc/UTC
    volumes:
      - libreoffice_config:/config
      - ${RUTA_BASE}:/data
    ports:
      - "23:3000" # ERROR LÓGICO: ¡Este puerto ya lo usa Obsidian!
      - "43:3001" 
    shm_size: "1gb"
    restart: unless-stopped
volumes:
  libreoffice_config:

```

### Jelllyfin:
```yml
  jellyfin:
    image: jellyfin/jellyfin:latest
    container_name: jellyfin
    ports:
      - "${PORT_JELLYFIN}:8096"
    environment:
      - TZ=Etc/UTC
    volumes:
      - jellyfin_config:/config
      - ${RUTA_BASE}:/media
    restart: unless-stopped
volumes:
  jellyfin_config:
```

