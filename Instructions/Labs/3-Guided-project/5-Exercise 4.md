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

1. En Visual Studio Code, vaya a **Kit de herramientas de Teams** en la barra lateral.
2. En el kit de herramientas de Teams, en el menú **Desarrollo**, seleccione **Crear una nueva aplicación**.
3. En el menú **Nuevo proyecto**, seleccione **Bot** y después **Comando de chat** para desarrollar un bot de comandos.
4. En Lenguaje de programación, seleccione **TypeScript**.
5. Para **carpeta del área de trabajo** seleccione o cree una carpeta para almacenar los archivos de su proyecto en su equipo.
6. En **Nombre de la aplicación**, escriba **SupportCommandBot** y presione **Entrar**.  El kit de herramientas de Teams crea su proyecto de bots y aplica scaffolding.
7. Revise los directorios y archivos del proyecto mediante el Explorador de Visual Studio Code para familiarizarse con el código fuente.

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
3. En la línea 20, actualice la matriz `commands` de la propiedad `command` para incluir una instrucción para inicializar el nuevo controlador: el objeto del comando `new ResetPasswordCommandHandler().  The updated ` debe ser el siguiente:

   ```json
   command: {    enabled: true,    commands: [new HelloWorldCommandHandler(), new ResetPasswordCommandHandler()],  },
    ```

## Comprobar el trabajo

Ejecute la aplicación localmente para probar la funcionalidad:

1. En el menú **LIFECYCLE**, seleccione **Vista previa de su aplicación de Teams** (o use la tecla `F5`) y después seleccione **Depurar en Teams ()** con su navegador preferido.  
2. El kit de herramientas de Teams aprovisionará y ejecutará la aplicación localmente en un explorador.
3. En el cuadro de diálogo de instalación de la aplicación en el explorador, seleccione **Agregar** para instalar la aplicación de Teams.  Teams abre una conversación con el bot instalado.
4. Escriba o seleccione el comando `resetPassword`.
5. Compruebe que el bot responde con una tarjeta adaptable que contiene instrucciones de restablecimiento de contraseña.
