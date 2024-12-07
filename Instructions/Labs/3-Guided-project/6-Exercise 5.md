---
lab:
  title: Compilación de un bot con IA
  module: Exercise 4
---

# Ejercicio 4: Compilar un bot con IA

## Escenario

Imagina que eres miembro del equipo de soporte técnico de TI. Te das cuenta de que la compilación del informe semanal es un proceso muy mecánico y lento. Quieres crear un bot de IA en MS Teams. Con solo discutir los elementos de trabajo semanales y las tareas de la próxima semana con el bot en una conversación, puede generar un informe semanal con formato correcto. Esto podría mejorar significativamente la eficiencia del trabajo.

## Tareas de ejercicio

Debe realizar las siguientes tareas para finalizar el ejercicio:

1. Crear un bot mediante la biblioteca de inteligencia artificial de Teams
1. Conectar a OpenAI Service
1. Implementar la funcionalidad de código
1. Actualizar solicitudes

**Tiempo estimado de finalización:** 20 minutos

## Requisitos previos

Para ejecutar la plantilla bot de chat de IA en la máquina de desarrollo local, no solo necesitas cumplir los requisitos de recursos mencionados en el laboratorio anterior, sino que también se requiere una cuenta de OpenAI. Esta cuenta puede ser de [OpenAI](https://platform.openai.com/) o [Azure OpenAI](https://aka.ms/oai/access).

## Tarea 1: Crear un bot mediante la biblioteca de inteligencia artificial de Teams

**Objetivo:** configurar un nuevo proyecto de bot mediante la biblioteca de inteligencia artificial de Teams y familiarizarte con la estructura y los archivos del proyecto.  

Usa la plantilla de bot de chat de IA para crear un nuevo bot:

1. Abra Visual Studio Code.
1. En la barra lateral, seleccione el icono de **Microsoft Teams** para abrir el panel **KIT DE HERRAMIENTAS DE TEAMS**.
1. Selecciona el botón **Crear una nueva aplicación**.
1. En el menú **Nuevo proyecto**, selecciona **Copiloto personalizado** y, después, selecciona **Bot de chat de IA básico** para compilar un bot de comandos.
1. En Lenguaje de programación, seleccione **TypeScript**.
1. En **Servicio para modelo de lenguaje grande (LLM),** elige **Azure OpenAI** u **OpenAI** en función de tu cuenta de LLM.
1. Presiona **Entrar** para omitir primero la configuración de modelo de lenguaje grande (LLM). Configurarás la clave OpenAI en el paso siguiente.
1. Para **carpeta del área de trabajo** seleccione o cree una carpeta para almacenar los archivos de su proyecto en su equipo.
1. En **Nombre de la aplicación**, escribe **WeeklyReportChatBot** y presiona **Entrar**. El Kit de herramientas de Teams aplicará scaffolding a una nueva aplicación y abrirá la carpeta del proyecto en Visual Studio Code.
1. Puede recibir un mensaje de Visual Studio Code que le pregunte si confía en los autores de los archivos de esta carpeta. Seleccione el botón **Sí, confío en los autores** para continuar.
1. Revise los directorios y archivos del proyecto mediante el Explorador de Visual Studio Code para familiarizarse con el código fuente.

## Tarea 2: Conectar al servicio OpenAI

**Objetivo:** configurar el bot para conectarte a un servicio OpenAI, ya sea a través de OpenAI directamente o mediante Azure OpenAI, mediante la configuración de las claves de API y los puntos de conexión necesarios.  

### Usar la cuenta de OpenAI
1. Abra el archivo `.env.local.user` desde la carpeta `env`.
1. En el archivo *env/.env.local.user*, rellena tu clave de OpenAI `SECRET_OPENAI_API_KEY=<your-key>`.
1. Abra el archivo `app.ts` desde la carpeta `src`.
1. (Opcional) Dado que esta demostración usa las funcionalidades de planeación del modelo, gpt-4 ofrece una mejora significativa con respecto al resultado predeterminado de gpt-3.5. En el archivo *src/config.ts*, actualiza el valor de propiedad *openAIModelName* de `"gpt-3.5-turbo"` a `"gpt-4"` o `"gpt-4-turbo"`.

### Usar Azure OpenAI
1. Abra el archivo `.env.local.user` desde la carpeta `env`.
1. En el archivo *env/.env.local.user*, rellena la clave de Azure OpenAI `SECRET_AZURE_OPENAI_API_KEY=<azure-openai-api-key>`, el punto de conexión de Azure OpenAI `AZURE_OPENAI_ENDPOINT=<azure-openai-endpoint>` y el nombre de implementación de Azure OpenAI `AZURE_OPENAI_DEPLOYMENT_NAME=<azure-openai-deployment-name>`.

## Tarea 3: Implementar la funcionalidad de código

**Objetivo:** desarrollar las funcionalidades principales del bot mediante la modificación del archivo app.ts, con la adición de importaciones necesarias y la implementación de lógica de respuesta para controlar las interacciones del usuario.  

1. Abre el archivo *src/app/app.ts*. Modificaremos este archivo según los pasos siguientes. Se puede hacer referencia al archivo final en [app.ts](../../../Allfiles/Labs/Guided-Exercise5/app.ts).
1. Agregar importación de `TurnContext` desde `botbuilder` 
    ```typescript
    import { MemoryStorage, TurnContext } from "botbuilder";
    ```
1. Agregar importaciones de `DefaultConversationState` y `TurnState` desde `@microsoft/teams-ai`
    ```typescript
    // See https://aka.ms/teams-ai-library to learn more about the Teams AI library.
    import { Application, ActionPlanner, OpenAIModel, PromptManager, DefaultConversationState, TurnState } from "@microsoft/teams-ai";
    ```
1. En el archivo *src/app.ts* antes de la sección *Crear componentes de IA*, agrega la interfaz `ProjectInformation` y la definición `ApplicationTurnState`.
    ```typescript
    // Register project information item related handlers
    interface ProjectInformation {
      projectName: string;
      tasksAccomplished: string;
      tasksComing: string;
      blockingIssues: string;
    }

    // Strongly type the applications turn state
    interface ConversationState extends DefaultConversationState {
      greeted: boolean;
      projectInformation: ProjectInformation;
    }
    type ApplicationTurnState = TurnState<ConversationState>;

    // Create AI components
    ```
1. En el archivo *src/app.ts* después de la sección *Definir almacenamiento y aplicación*, agrega la respuesta del bot a los mensajes.
    ```typescript
    // List for /reset command and then delete the conversation state
    app.message('/reset', async (context: TurnContext, state: ApplicationTurnState) => {
      state.deleteConversationState();
      await context.sendActivity("Your project information has been cleared.");
    });

    // Define the method for updating project information
    app.ai.action("updateProjectInformation", async (context: TurnContext, state: ApplicationTurnState, parameters: ProjectInformation) => {
      const conversation = ensureStateInitialized(state);
      if (parameters){
        if (parameters.projectName) {
          conversation.projectInformation.projectName = parameters.projectName;
        }
        if (parameters.tasksAccomplished) {
          conversation.projectInformation.tasksAccomplished = parameters.tasksAccomplished;
        } 
        if (parameters.tasksComing) {
          conversation.projectInformation.tasksComing = parameters.tasksComing;
        }
        if (parameters.blockingIssues) {
          conversation.projectInformation.blockingIssues = parameters.blockingIssues;
        }
        return `Project information was updated. Think about your next action`;
      }
    });

    // This method is used to make sure that the conversation state is initialized.
    function ensureStateInitialized(state: ApplicationTurnState): ConversationState {
      if (state.conversation.projectInformation == undefined) {
        state.conversation.projectInformation = {
          projectName: "",
          tasksAccomplished: "",
          tasksComing: "",
          blockingIssues: "",
        };
      }
      return state.conversation;
    }
    ```

## Tarea 4: Solicitar actualizaciones

**Objetivo:** refinar los mensajes conversacionales del bot y configurar las llamadas a funciones para mejorar la calidad y eficacia de la interacción en la generación de informes semanales.

1. Actualiza el archivo `skprompt.txt` en la carpeta *src/prompts/chat*.  Se puede hacer referencia al archivo final en [skprompt.txt](../../../Allfiles/Labs/Guided-Exercise5/skprompt.txt)
    ```txt
    You are a Teams Bot. Here is how you will act.
    Team Bot will adopt an encouraging and positive tone in all its interactions. This will be reflected in the creation of status report emails, ensuring that they are not only informative but also boost morale and foster a joyous team spirit. The language used will be engaging and supportive, aiming to excite and inspire the team while maintaining a professional undercurrent appropriate for the communication between a project manager and their team and stakeholders in a professional corporate setting. The Teams Bot will always ask for information from the user when it is not provided. 
    # Teams Bot will ask for the following project information to make status report emails:
    1. project name
    2. tasks accomplished this week
    3. tasks coming next week
    4. blocking issues

    # Status report task description like SCRUM style summary

    # Then, the users will type in the parameters and the bot will make the email.

    # For the first time, users are informed that they can clear the entered ProjectInformation with /reset command.

    # THE SUMMARY MUST BE:
    - G RATED
    - WORKPLACE / FAMILY SAFE
    NO SEXISM, RACISM OR OTHER BIAS/BIGOTRY.

    project information:
    {{$conversation.projectInfomation}}

    Typescript Interfaces:
    interface ProjectInformation {
        projectName: string;
        tasksAccomplished: string;
        tasksComing: string;
        blockingIssues: string;
    }
    ```
1. Actualiza los parámetros `max_tokens` y `temperature` en el archivo `src/prompts/chat/config.json`. Además, agrega un parámetro de nodo de aumento. El resultado final es el siguiente.  Y se le puede hacer referencia en [config.json](../../../Allfiles/Labs/Guided-Exercise5/config.json)
    ```json
    {
      "schema": 1,
      "description": "AI Bot",
      "type": "completion",
      "completion": {
        "max_tokens": 2500,
        "temperature": 0.1,
        "top_p": 0.0,
        "presence_penalty": 0.6,
        "frequency_penalty": 0.0
      },
      "augmentation": {
        "augmentation_type": "monologue"
      }
    }
    ```
1. Crea un nuevo archivo denominado `actions.json` en la carpeta *src/prompts/chat* El contenido del archivo es el siguiente. Este archivo define los métodos para que use la inteligencia artificial. Y se le puede hacer referencia en [actions.json](../../../Allfiles/Labs/Guided-Exercise5/actions.json)
    ```json
    [
        {
            "name": "updateProjectInformation",
            "description": "updates the information for the existing project",
            "parameters": {
                "type": "object",
                "properties": {
                    "projectName": {
                        "type": "string",
                        "description": "The name of the project"
                    },
                    "tasksAccomplished": {
                        "type": "string",
                        "description": "tasks that have been accomplished"
                    },
                    "tasksComing": {
                        "type": "string",
                        "description": "tasks that are coming up"
                    },
                    "blockingIssues": {
                        "type": "string",
                        "description": "any blocking issues"
                    }
                }
            }
        }
    ]
    ```

## Comprobar el trabajo

Ejecute la aplicación localmente para probar la funcionalidad:

1. Abra el panel del **KIT DE HERRAMIENTAS DE TEAMS**. En el menú **DESARROLLO**, seleccione **Vista previa de su aplicación de Teams** (o use la clave `F5`) y, a continuación, seleccione **Depurar en Teams ()** con su explorador preferido.  
2. El kit de herramientas de Teams aprovisionará y ejecutará la aplicación localmente en un explorador.
3. En el cuadro de diálogo de instalación de la aplicación en el explorador, seleccione **Agregar** para instalar la aplicación de Teams.  Teams abre una conversación con el bot instalado.
4. Después de escribir la información de saludo, sigue las indicaciones para escribir el nombre del proyecto, las tareas completadas, las tareas no completadas y las tareas de bloqueo. Después, el bot de IA generará un informe semanal similar a la captura de pantalla siguiente.
5. Comprueba que el bot responde a la respuesta correcta como se indica a continuación. ![Captura de pantalla de informe semanal generado con IA.](../../media/ai-weekly-report.png)
