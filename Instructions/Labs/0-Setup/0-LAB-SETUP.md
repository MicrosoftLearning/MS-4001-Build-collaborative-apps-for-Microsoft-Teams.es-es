# Configuración del laboratorio

Complete las tareas siguientes para preparar el entorno de desarrollo antes de completar los laboratorios:

## Requisitos previos del laboratorio

Necesita las siguientes herramientas para completar los laboratorios de este curso:

- Acceso de administrador a un inquilino de Microsoft 365.
- Suscripción a Azure.
- Visual Studio Code.
- Extensión de Visual Studio Code de Teams Toolkit: versión 5.8.1 o posteriores. (Lo instalará durante el laboratorio)
- Cliente de Microsoft Teams (para trabajo o escuela) o acceso a Microsoft Teams a través de un explorador web.
- Node.js versión 18.20.4.

## Instalación de nvm-windows

Usará esta herramienta para instalar Node.js y, opcionalmente, cambiar las versiones de Node según sea necesario para los proyectos.

1. En un explorador web, ve a [https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases).
2. Busca la versión más reciente y selecciona el archivo **nvm-setup.zip** para descargarlo.  El archivo se descargará en el equipo. ![Captura de pantalla de la descarga de nvm-setup.zip](../../media/download-nvm-setup.png)
3. Abre la carpeta de archivos y **extrae** el contenido de la carpeta ZIP en una carpeta del equipo.
4. En la nueva carpeta, selecciona **nvm-setup.exe** para abrir el archivo de instalación.
5. Sigue las indicaciones del instalador para instalar la herramienta mediante las opciones predeterminadas. ![Captura de pantalla de la configuración de instalación de nvm](../../media/install-nvm.png)
6. Nvm para Windows se instalará en el equipo.

## Instalación de Node.js

Instala Node.js versión 18.20.4, que es compatible con todas las soluciones de este curso.

1. Abre la aplicación del **símbolo del sistema**.
2. Escribe el comando `nvm install 18.20.4` para instalar Node.js.
3. La salida nvm debe confirmar que la instalación se ha completado.
4. Ejecuta el comando `nvm use 18.20.4` para usar esta versión de Node.js.
5. Ejecuta el comando `node -v` para confirmar que tienes instalada la versión 18.20.4.

Ya has instalado y configurado Node.js versión 18.20.4 ![Captura de pantalla de instalación de npm](../../media/nvm-install-npm.png)

## Suscripción de Azure

Tenga en cuenta que si se le ha proporcionado información de inicio de sesión de Azure, ya se ha creado un grupo de recursos para su uso.  En Tareas de aprovisionamiento en todos los laboratorios, cuando se le pida que "seleccione un grupo de recursos o cree un nuevo grupo de recursos", **seleccione el grupo de recursos proporcionado**.

## Depuración de aplicaciones de Teams

Al depurar la aplicación de Teams localmente, es posible que se le pida que instale un certificado de desarrollo para localhost.  Tendrá que hacerlo para depurar localmente.

Cuando se le solicite, seleccione **Instalar**.

![Captura de pantalla del mensaje para instalar el certificado de desarrollo.](../../media/install-certificate.png)

A continuación, seleccione **Sí** en el cuadro de diálogo Advertencia de seguridad.

![Captura de pantalla del cuadro de diálogo de seguridad.](../../media/development-certificate.png)

Visite la documentación de Teams para obtener más información: [Depurar la aplicación de Teams localmente](https://learn.microsoft.com/microsoftteams/platform/toolkit/debug-local?tabs=Windows&pivots=visual-studio-code-v5)
