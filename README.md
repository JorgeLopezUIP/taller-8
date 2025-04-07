Porfe no se por que no me deja subir nada. Yo lo hice usando wsl y github se me crashe al intentar subir los documentos.
Le dejo unas screenshots para que vea que si trabaje y que mi codigo funcionaba tanto gunicorn como nginx

Aqui le dejo los pasos para crear el codigo 

Pasos para configurar Gunicorn y Nginx con WSL y VS Code:
1. Preparar el entorno de desarrollo en WSL:

    Instalar WSL:

        Primero, instalé WSL (Windows Subsystem for Linux) en mi máquina si no lo tenía, ejecutando wsl --install en PowerShell (en caso de que no lo tuviera ya).

        Después, instalé una distribución de Linux (como Ubuntu) en WSL desde la Microsoft Store.

    Acceder a WSL con VS Code:

        Instalé la extensión WSL de VS Code.

        Abrí VS Code y me conecté al entorno WSL usando la opción “Abrir en WSL” en VS Code, lo que me permitió trabajar directamente en la terminal de Linux desde Windows.

2. Instalar Gunicorn y configurar el entorno virtual:

    Instalar Gunicorn:

        Abrí la terminal en WSL y me dirigí al directorio de mi proyecto:
        cd /home/jorge/taller-6/Taller_6/.

        Instalé un entorno virtual (si aún no lo tenía) usando python3 -m venv venv.

        Activé el entorno virtual con source venv/bin/activate.

        Instalé Gunicorn dentro del entorno virtual con:
        pip install gunicorn.

    Verificar que Gunicorn funciona:

        Probé si Gunicorn estaba correctamente instalado ejecutando:
        gunicorn --version.

3. Configurar el archivo .service para Gunicorn:

    Crear un servicio systemd para Gunicorn:

        Usé sudo nano /etc/systemd/system/Taller_6.service para crear y editar el archivo del servicio de Gunicorn.

        Copié el siguiente contenido, asegurándome de reemplazar los valores de ruta y usuario:

    [Unit]
    Description=Gunicorn para Taller 6 Flask App
    After=network.target

    [Service]
    User=jorge
    Group=www-data
    WorkingDirectory=/home/jorge/taller-6/Taller_6
    ExecStart=/home/jorge/taller-6/Taller_6/venv/bin/gunicorn --workers 3 --bind unix:/home/jorge/taller-6/Taller_6/Taller_6.sock app:app

    [Install]
    WantedBy=multi-user.target

Recargar y activar el servicio:

    Ejecuté los siguientes comandos para recargar los servicios y habilitar mi servicio:

        sudo systemctl daemon-reexec
        sudo systemctl daemon-reload
        sudo systemctl start Taller_6
        sudo systemctl enable Taller_6

    Verificar que Gunicorn esté corriendo:

        Usé el comando sudo systemctl status Taller_6 para verificar el estado de mi servicio.

4. Instalar y configurar Nginx:

    Instalar Nginx:

        Instalé Nginx dentro de WSL ejecutando:
        sudo apt update && sudo apt install nginx.

    Configurar Nginx para trabajar con Gunicorn:

        Creé un archivo de configuración para mi sitio en /etc/nginx/sites-available/Taller_6.conf con el siguiente contenido:

    server {
        listen 80;
        server_name localhost:8000;  

        location / {
            proxy_pass http://unix:/home/jorge/taller-6/Taller_6/Taller_6.sock;
            
        }
    }

Activar la configuración de Nginx:

    Creé un enlace simbólico para habilitar la configuración:

        sudo ln -s /etc/nginx/sites-available/Taller_6.conf /etc/nginx/sites-enabled/

    Reiniciar Nginx:

        Reinicié Nginx para aplicar los cambios con:
        sudo systemctl restart nginx.

5. Verificar que todo esté funcionando correctamente:

    Comprobar el estado de Nginx:

        Ejecuté sudo systemctl status nginx para asegurarme de que Nginx estuviera corriendo correctamente.

    Probar el acceso al servidor:

        Abrí el navegador y me aseguré de que la aplicación de Flask estuviera accesible en http://localhost o en la IP correspondiente.
