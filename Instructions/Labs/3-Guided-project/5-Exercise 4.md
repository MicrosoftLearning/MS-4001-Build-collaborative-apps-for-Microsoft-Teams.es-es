---
lab:
  title: Creación de un bot
  module: Exercise 4
---

# Ejercicio 4: Creación de un bot

## Escenario

Supongamos que el equipo de soporte informático al que usted presta apoyo recibe un gran volumen de consultas comunes y repetitivas de los empleados de toda la organización. Estas consultas suelen implicar problemas simples, como los restablecimientos de contraseña, las instrucciones de instalación de software o la solución de errores comunes.

Para simplificar el proceso y reducir la carga de trabajo en el equipo, decide crear un bot que pueda controlar estas consultas comunes en Microsoft Teams.

Decide agregar un comando inicial al bot denominado "resetPassword". Cuando un usuario escriba este comando, el bot responderá con instrucciones paso a paso sobre cómo restablecer su contraseña. Esto permite a los usuarios resolver su problema sin necesidad de ponerse en contacto directamente con el equipo de soporte informático, dejando libre a su equipo para encargarse de cuestiones más complejas.

Además del comando "resetPassword", tiene previsto agregar más comandos para controlar otras consultas comunes, convirtiendo el bot en una herramienta completa de autoservicio para los empleados de la organización.

## Tareas de ejercicio

Debe realizar las siguientes tareas para finalizar el ejercicio:

1. Creación del bot usando el kit de herramientas de Teams
2. Configuración del manifiesto
3. Creación de una tarjeta adaptable
4. Control del comando

**Tiempo estimado de finalización:** 17 minutos

## Tarea 1: Creación de un bot usando el kit de herramientas de Teams

Use la plantilla Bot de comandos para crear un bot:

1. Abra Visual Studio Code.
1. En la barra lateral, seleccione el icono de **Microsoft Teams** para abrir el panel **KIT DE HERRAMIENTAS DE TEAMS**.
1. Haga clic en el botón **Crear aplicación**.
1. En el menú **Nuevo proyecto**, seleccione **Bot** y después **Comando de chat** para desarrollar un bot de comandos.
1. En Lenguaje de programación, seleccione **TypeScript**.
1. Para **carpeta del área de trabajo** seleccione o cree una carpeta para almacenar los archivos de su proyecto en su equipo.
1. En **Nombre de la aplicación**, escriba **SupportCommandBot** y presione **Entrar**. El Kit de herramientas de Teams aplicará scaffolding a una nueva aplicación y abrirá la carpeta del proyecto en Visual Studio Code.
1. Puede recibir un mensaje de Visual Studio Code que le pregunte si confía en los autores de los archivos de esta carpeta. Seleccione el botón **Sí, confío en los autores** para continuar.
1. Revise los directorios y archivos del proyecto mediante el Explorador de Visual Studio Code para familiarizarse con el código fuente.

## Tarea 2: Configuración del manifiesto

Defina un comando `ResetPassword` en el manifiesto de la aplicación:

1. Abra el archivo `manifest.json` desde la carpeta `appPackage`.
2. En el objeto `bots`, busque `commandLists`.  Actualmente hay un único comando titulado `helloWorld`.
3. Reemplace `commands` por el código siguiente, de modo que incluya un nuevo comando `ResetPassword` de la siguiente manera:

    ```typescript
            "commands": [
                {
                    "title": "helloWorld",
                    "description": "A helloworld command to send a welcome message"
                },
                {
                    "title": "resetPassword",
                    "description": "Request instructions to reset your password"
                }
            ]
    ```

## Tarea 3: Creación de una tarjeta adaptable

Cree una tarjeta adaptable que se enviará en respuesta al comando `ResetPassword` :

1. En `src`/`adaptiveCards`, cree un nuevo archivo denominado `resetPassword.json`.
2. Agregue el contenido siguiente al archivo y guárdelo:

   ```json
        {
            "type": "AdaptiveCard",
            "body": [
                {
                    "type": "TextBlock",
                    "size": "Medium",
                    "weight": "Bolder",
                    "text": "Reset Password Instructions"
                },
                {
                    "type": "TextBlock",
                    "text": "1. Navigate to the login page and select 'Forgot Password'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "2. Enter your email or username and select 'Submit'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "3. Check your email for a password reset link and select it.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "4. Enter and confirm your new password, then select 'Save'.",
                    "wrap": true
                },
                {
                    "type": "TextBlock",
                    "text": "5. Log in with your new password.",
                    "wrap": true
                }
            ],
            "actions": [
                {
                    "type": "Action.OpenUrl",
                    "title": "Go to Login Page",
                    "url": "https://www.adaptivecards.io/designer/"
                }
            ],
            "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
            "version": "1.4"
        }
   ```

## Tarea 4: Control del comando

A continuación, controle el comando en el código fuente del bot mediante la clase `TeamsFxBotCommandHandler`.  Importe la `resetPasswordCard` desde el archivo json y represente el comando cuando se invoque el comando:

1. En la carpeta `src`, cree un nuevo archivo denominado `resetPasswordCommandHandler.ts`.
2. Agregue las siguientes instrucciones de importación al archivo, incluida una instrucción para importar la tarjeta `resetPassword` sin procesar que ha creado:

   ```typescript
   import { Activity, CardFactory, MessageFactory, TurnContext } from "botbuilder";
    import { CommandMessage, TeamsFxBotCommandHandler, TriggerPatterns } from "@microsoft/teamsfx";
    import { AdaptiveCards } from "@microsoft/adaptivecards-tools";
    import rawResetPasswordCard from "./adaptiveCards/resetPassword.json";
   ```
3. Debajo de las instrucciones de importación, agregue el siguiente código para implementar el controlador de comandos, después guarde el archivo:

   ```typescript
       export class ResetPasswordCommandHandler implements TeamsFxBotCommandHandler {
          triggerPatterns: TriggerPatterns = "resetPassword";
        
          async handleCommandReceived(
            context: TurnContext,
            message: CommandMessage
          ): Promise<string | Partial<Activity> | void> {
            console.log(`App received message: ${message.text}`);
        
            const resetPasswordCard = AdaptiveCards.declareWithoutData(rawResetPasswordCard).render();
            await context.sendActivity({ attachments: [CardFactory.adaptiveCard(resetPasswordCard)] });
          }
        }
   ```

## Tarea 5: Registro del nuevo comando

Cada nuevo comando debe configurarse en `ConversationBot`, que impulsa el flujo conversacional de la plantilla de bot de comandos.

1. Vaya al archivo `src/internal/initialize.ts`.
2. Agregue la siguiente instrucción import en la línea 2:

    `import { ResetPasswordCommandHandler } from "../resetPasswordCommandHandler";`
3. En la línea 20, actualice la matriz `commands` de la propiedad `command` para incluir una instrucción para inicializar el nuevo controlador: `new ResetPasswordCommandHandler()`.  El objeto `command` actualizado debe ser el siguiente:

   ```json
   command: {    enabled: true,    commands: [new HelloWorldCommandHandler(), new ResetPasswordCommandHandler()],  },
    ```

## Tarea 6: Cambiar a ngrok desde un túnel dev (opcional)

Si su entorno de desarrollo no admite el túnel dev del kit de herramientas de Teams, puede reemplazar dicho túnel por ngrok.

1. Siga estos pasos para instalar ngrok:
   1. Vaya al [sitio web de ngrok](https://ngrok.com/) y regístrese para obtener una cuenta.
   1. Descargue el archivo ejecutable ngrok para su sistema operativo.
   1. Extraiga el archivo descargado en un directorio de su elección.
   1. En un entorno de Windows, agregue el directorio donde se encuentra `ngrok.exe` a la variable de entorno PATH del sistema. 
      ```powershell
      setx PATH "$Env:path;<ngrok_full_path>"
      ```
      _En un entorno de PowerShell, reemplace `<ngrok_full_path>` por la ruta de acceso donde se encuentra `ngrok.exe`._
      > Para aplicar este cambio de variable de entorno, debe reiniciar los terminales y **Visual Studio Code** para el proyecto actual.

   1. Abra un terminal o símbolo del sistema y ejecute el siguiente comando para autenticar la cuenta de ngrok:
      ```shell
      ngrok config add-authtoken <your_auth_token>
      ```
      _Reemplace `<your_auth_token>` por el token de autenticación que se proporciona en el sitio web de ngrok._
   1. Para iniciar un túnel en el puerto 3978, ejecute el siguiente comando:
      ```shell
      ngrok http 3978
      ```
   1. Ngrok generará una dirección URL de reenvío que puede usar para acceder a la aplicación desde Internet.
      ```shell
      Forwarding      http://<random_string>.ngrok-free.app -> http://localhost:3978
      ```
   1. Haga clic en `Ctrl + C` para desconectar el túnel de ngrok.
1. Vaya a la carpeta `.vscode` y abra el archivo `task.json`. Actualice la tarea `Start local tunnel`:
   ```json
    {
        "label": "Start local tunnel",
        "type": "shell",
        "command": "ngrok http 3978 --log=stdout --log-format=logfmt",
        "isBackground": true,
        "problemMatcher": {
            "pattern": [
                {
                    "regexp": "^.*$",
                    "file": 0,
                    "location": 1,
                    "message": 2
                }
            ],
            "background": {
                "activeOnStart": true,
                "beginsPattern": "starting web service",
                "endsPattern": "started tunnel|failed to reconnect session"
            }
        }
    }
   ```
1. Vaya al archivo `teamsapp.local.yml` en la carpeta raíz. Agregue la siguiente acción en el primer paso del ciclo de vida de aprovisionamiento.
   - Windows
     ```yml
     provision:
       - uses: script
         with:
           shell: powershell
           run: |
                for ($i = 1; $i -le 10; $i++) {
                    $endpoint = (Invoke-WebRequest -Uri "http://localhost:4040/api/tunnels" | Select-String -Pattern 'https://[a-zA-Z0-9 -\.]*\.ngrok-free\.app').Matches.Value
                    if ($endpoint) {
                        break
                    }
                    sleep 10
                }
                if (-not $endpoint) {
                    echo "ERROR: Failed to find tunnel endpoint after 10 attempts."
                    exit 1
                } else {
                    echo "::set-teamsfx-env BOT_ENDPOINT=$endpoint"
                    echo "::set-teamsfx-env BOT_DOMAIN=$($endpoint.Substring(8))"
                }
     ```
   - Linux y macOS
     ```yml
     provision:
        - uses: script
            with:
            run: |
                for i in {1..10}; do
                    endpoint=$(curl -s localhost:4040/api/tunnels | grep -o 'https://[a-zA-Z0-9 -\.]*\.ngrok-free\.app')
                    if [ -n "$endpoint" ]; then
                        break
                    fi
                    sleep 10
                done
                if [ -z "$endpoint" ]; then
                    echo "ERROR: Failed to find tunnel endpoint after 10 attempts."
                    exit 1
                else
                    echo "::set-teamsfx-env BOT_ENDPOINT=$endpoint"
                    echo "::set-teamsfx-env BOT_DOMAIN=${endpoint:8}"
                fi
     ```
     
## Comprobar el trabajo

Ejecute la aplicación localmente para probar la funcionalidad:

1. Abra el panel del **KIT DE HERRAMIENTAS DE TEAMS**. En el menú **DESARROLLO**, seleccione **Vista previa de su aplicación de Teams** (o use la clave `F5`) y, a continuación, seleccione **Depurar en Teams ()** con su explorador preferido.  
2. El kit de herramientas de Teams aprovisionará y ejecutará la aplicación localmente en un explorador.
3. En el cuadro de diálogo de instalación de la aplicación en el explorador, seleccione **Agregar** para instalar la aplicación de Teams.  Teams abre una conversación con el bot instalado.
4. Escriba o seleccione el comando `resetPassword`.
5. Compruebe que el bot responde con una tarjeta adaptable que contiene instrucciones de restablecimiento de contraseña.
