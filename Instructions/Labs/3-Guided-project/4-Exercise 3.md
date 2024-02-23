---
lab:
  title: Creación de una pestaña de Teams
  module: Exercise 3
---

# Ejercicio 3: Creación de una pestaña de Teams

## Escenario

Supongamos que el equipo de soporte técnico de TI quiere crear una pestaña de Teams para ayudar a los usuarios a acceder a la información necesaria al enviar incidencias de soporte técnico. Por ejemplo, el equipo debe mostrar los códigos de configuración regional de los usuarios para el procesamiento y los informes de vales. La tarea actual consiste en crear esta pestaña para mostrar el código de configuración regional de un usuario. Se agregará información adicional a la pestaña en una fecha posterior.

## Tareas de ejercicio

La tarea consiste en crear una aplicación de pestañas de Teams que recupere la configuración regional del usuario, mediante el contexto de Teams y la muestre en una pestaña personal.

Tendrá que completar las siguientes tareas para completar el ejercicio:

1. Cree una aplicación de pestaña con el Kit de herramientas de Teams para Visual Studio Code.
1. Actualice la aplicación para recuperar y mostrar la configuración regional del usuario.

**Tiempo estimado de finalización:** 10 minutos

## Tarea 1: Cree una aplicación de pestaña con el Kit de herramientas de Teams para Visual Studio Code

1. Abra Visual Studio Code.
1. En la barra lateral, seleccione el icono de **Microsoft Teams** para abrir el panel **KIT DE HERRAMIENTAS DE TEAMS**.
1. Seleccione **Crear una nueva aplicación** y, a continuación, seleccione **Pestaña**.
1. Seleccione la **pestaña Básico** en la lista de opciones disponibles.
1. Como lenguaje de programación, seleccione **TypeScript**.
1. En la **carpeta Área de trabajo**, seleccione **Carpeta predeterminada**.
1. En **Nombre de la aplicación**, escriba **UserInfoApp**.

Aparecerá una notificación cuando se haya aplicado scaffolding correctamente en todas las carpetas y archivos y una nueva instancia de Visual Studio Code abrirá la nueva carpeta del proyecto.

En el panel **EXPLORER**, la carpeta *src* contiene el código fuente de la aplicación. Los archivos fuera de la carpeta *src* están relacionados con el servidor, como el bot.

## Tarea 2: Actualice la aplicación para recuperar y mostrar la configuración regional del usuario

Ahora, puede agregar la funcionalidad deseada a la aplicación de pestañas.

1. Vaya a la carpeta `src` > `views` y abra el archivo `hello.html`.
1. Busque el elemento `<div>` y actualícelo para que contenga los siguientes elementos entre las etiquetas `<div>` y `</div>`:

    ```html
        <h1>Hello, User</h1>
      <span>
        <h2>Review your locale code:</h2>
        <p id="locale"></p>
      </span>
    ```

1. Vaya a la carpeta `src` > `static` > `scripts` y abra el archivo `teamsapp.js`.
1. Reemplace el contenido del archivo  por el código siguiente:

    ```typescript
        (function () {
          "use strict";
        
          // Call the initialize API first
          microsoftTeams.app.initialize().then(function () {
            microsoftTeams.app.getContext().then(function (context) {
              if (context?.app?.locale) {
                updateLocale(context.app.locale);
              }
              else{
                updateLocale("unknown");
              }
            });
          });
        
          function updateLocale(locale) {
            if(locale){
              document.getElementById("locale").innerHTML = locale;
            }
          }
        })();
    ```

    Este código usa el **objeto de contexto** para recuperar la configuración regional del usuario y actualiza el código HTML para mostrar el código de configuración regional.

## Comprobar el trabajo

Ejecute la aplicación en modo de depuración para probar la funcionalidad.

1. En Visual Studio Code, seleccione el icono **Microsoft Teams** para abrir el panel **KIT DE HERRAMIENTAS DE TEAMS**.

2. Empiece a ejecutar la aplicación con el depurador asociado mediante uno de estos métodos:

   - Presione la tecla F5.
   - En Visual Studio Code, vaya al menú **Ejecutar y depurar**.  Seleccione **Depurar en Teams** con la opción del explorador que quiera y, después, seleccione el botón **Iniciar depuración**.
   - En la sección **ENTORNO** del Kit de herramientas de Teams, abra la *carpeta local* y, a continuación, seleccione el explorador que prefiera.

3. Una vez que Visual Studio Code realice algunas comprobaciones y con las acciones todavía visibles en la pestaña **Consola**, se abrirá una nueva ventana del explorador. En el cuadro de diálogo **UserInfoApp**, seleccione el botón **Agregar** para instalar la aplicación en Teams y obtener una vista previa.

La aplicación ahora se puede ver en la barra lateral. La aplicación está preconfigurada con dos pestañas: **Pestaña personal** y **Acerca de**. Compruebe que el código de configuración regional se muestra en la pestaña.
