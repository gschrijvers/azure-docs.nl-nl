---
title: 'Zelf studie: verificatie met Azure Functions-Azure-Signaalr'
description: In deze zelf studie leert u hoe u Azure signalerings service-clients kunt verifiëren voor Azure Functions binding
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dfa17720b34962611d240aa7c35ba8092bf99082
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74158146"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Zelfstudie: Verificatie van Azure SignalR Service met Azure Functions

Een stapsgewijze zelfstudie voor het maken van een chatruimte met verificatie en privéberichten met Azure Functions, App Service-verificatie en SignalR Service.

## <a name="introduction"></a>Inleiding

### <a name="technologies-used"></a>Gebruikte technologieën

* [Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): back-end-API voor het verifiëren van gebruikers en het verzenden van chatberichten
* [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): nieuwe berichten uitzenden naar verbonden chatclients
* [Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): de statische website voor de gebruikersinterface van de chatclient hosten

### <a name="prerequisites"></a>Vereisten

De volgende software is vereist voor het bouwen van deze zelfstudie.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (Versie 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (versie 2.x, vereist is voor Functions-extensies)
* [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools) (versie 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) met de volgende extensies
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions): werk met Azure Functions in VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer): webpagina's voor lokaal testen

## <a name="sign-into-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan met uw referenties.

## <a name="create-an-azure-signalr-service-instance"></a>Een Azure SignalR Service-exemplaar maken

U gaat de Azure Functions-app lokaal bouwen en testen. De app heeft toegang tot een exemplaar van SignalR Service in Azure die vooraf moet worden gemaakt.

1. Klik op de knop **een resource maken** (**+**) voor het maken van een nieuwe Azure-resource.

1. Zoek naar **SignalR Service** en selecteer deze. Klik op **maken**.

    ![Nieuwe SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Voer de volgende informatie in.

    | Naam | Waarde |
    |---|---|
    | Resourcenaam | Een unieke naam voor het SignalR Service-exemplaar |
    | Resourcegroep | Een nieuwe resource groep met een unieke naam maken |
    | Locatie | Selecteer een locatie dicht bij u in de buurt |
    | Prijscategorie | Gratis |

1. Klik op **maken**.

1. Nadat het exemplaar is geïmplementeerd, opent u het in de portal en zoekt u de pagina instellingen. Wijzig de instelling van de service modus in *serverloze*.

    ![Seingevings service-modus](media/signalr-concept-azure-functions/signalr-service-mode.png)


## <a name="initialize-the-function-app"></a>Initaliseer de functie-app

### <a name="create-a-new-azure-functions-project"></a>Een Azure Functions-project maken

1. Gebruik in een nieuw venster van VS Code `File > Open Folder` in het menu om een lege map op een goede locatie te maken en openen. Dit is de hoofdprojectmap voor de toepassing die u bouwt.

1. Gebruik de Azure Functions-extensie in VS Code en initialiseer een functie-app in de hoofdprojectmap.
   1. Open het opdrachtenpalet in VS Code door **Beeld > Opdrachtenpalet** te selecteren in het menu (sneltoets `Ctrl-Shift-P`, macOS: `Cmd-Shift-P`).
   1. Zoek de opdract **Azure Functions: nieuw project maken** en selecteer deze.
   1. De hoofdprojectmap wordt nu weergegeven. Selecteer deze (of gebruik Bladeren om de map te zoeken).
   1. Wanneer u wordt gevraagd om een taal te kiezen, selecteert u **JavaScript**.

      ![Een functie-app maken](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Extensies voor functie-apps installeren

In deze zelfstudie worden Azure Functions-bindingen gebruikt voor de interactie met Azure SignalR Service. De bindingen van SignalR Service zijn, zoals de meeste andere bindingen, beschikbaar als extensie die moeten worden geïnstalleerd met behulp van de Azure Functions Core Tools CLI voordat ze kunnen worden gebruikt.

1. Open een terminal in VS code door **>-Terminal weer geven** te selecteren in het menu\`(CTRL-).

1. Zorg dat de hoofdprojectmap de huidige map is.

1. Installeer de functie-app-extensie SignalR Service.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
    ```

### <a name="configure-application-settings"></a>Toepassingsinstellingen configureren

Bij het lokaal uitvoeren van en opsporen van fouten in de Azure Functions-runtime, worden de toepassingsinstellingen gelezen vanuit **local.settings.json**. Werk dit bestand bij met de verbindingsreeks van het SignalR Service-exemplaar dat u eerder hebt gemaakt.

1. Selecteer in VS Code **local.settings.json** in het deelvenster Explorer om dit te openen.

1. Vervang de inhoud van het bestand door het volgende.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Typ de verbindingsreeks van Azure SignalR Service in een instelling genaamd `AzureSignalRConnectionString`. Haal de waarde van de pagina **Sleutels** in de Azure SignalR Service-resource in Azure Portal; u kunt zowel de primaire als de secundaire reeks gebruiken.
   * De instelling `WEBSITE_NODE_DEFAULT_VERSION` wordt niet lokaal gebruikt, maar is vereist bij implementatie naar Azure.
   * In het gedeelte `Host` worden de poort- en CORS-instellingen voor de lokale Functions-host geconfigureerd (deze instelling heeft geen functie bij uitvoering in Azure).

       > [!NOTE]
       > Live server is doorgaans geconfigureerd voor het leveren van `http://127.0.0.1:5500`inhoud van. Als u merkt dat het een andere URL gebruikt of als u een andere HTTP-server gebruikt, wijzigt u `CORS` de instelling zodat deze overeenkomt met de juiste oorsprong.

     ![SignalR Service-sleutel ophalen](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Sla het bestand op.

    

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Een functie maken om gebruikerstoegang tot SignalR Service te verifiëren

Wanneer de chat-app de eerste keer wordt geopend in de browser, zijn geldige verbindingsreferenties vereist voor het maken van verbinding met Azure SignalR Service. U maakt een door HTTP geactiveerde functie met de naam *Negotiate* in uw functie-app om deze verbindings gegevens te retour neren.

> [!NOTE]
> Deze functie moet *onderhandelen* als de seingevings-client een eind punt nodig heeft dat eindigt op `/negotiate`.

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure Functions: functie maken**.

1. Geef de volgende informatie op wanneer u daarom wordt gevraagd.

    | Naam | Waarde |
    |---|---|
    | Map van de functie-app | Selecteer de hoofdprojectmap |
    | Template | HTTP-trigger |
    | Naam | negotiate |
    | Autorisatieniveau | Anoniem |

    Er wordt een map met de naam **Negotiate** gemaakt die de nieuwe functie bevat.

1. Open **Negotiate/function. json** om bindingen voor de functie te configureren. Wijzig de inhoud van het bestand in het volgende. Hiermee voegt u een invoerbinding toe die geldige referenties voor een client genereert, om verbinding te maken met een Azure SignalR Service-hub met de naam `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    De eigenschap `userId` in de binding `signalRConnectionInfo` wordt gebruikt voor het maken van een geverifieerde verbinding met SignalR Service. Laat de eigenschap leeg voor lokale ontwikkeling. U zult deze gebruiken bij het implementeren van de functie-app naar Azure.

1. Open **Negotiate/index. js** om de hoofd tekst van de functie weer te geven. Wijzig de inhoud van het bestand in het volgende.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Deze functie gebruikt de SignalR-verbindingsgegevens van de invoerbinding en retourneert die aan de client in de hoofdtekst van het HTTP-antwoord. De seingevings client gebruikt deze informatie om verbinding te maken met het signaal service-exemplaar.

## <a name="create-a-function-to-send-chat-messages"></a>Een functie maken voor het verzenden van chatberichten

De web-app vereist ook een HTTP-API voor het verzenden van chatberichten. Maakt u een HTTP-geactiveerde functie met de naam *SendMessage* die berichten verzendt naar alle verbonden clients via SignalR Service.

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure Functions: functie maken**.

1. Geef de volgende informatie op wanneer u daarom wordt gevraagd.

    | Naam | Waarde |
    |---|---|
    | Map van de functie-app | selecteer de hoofdprojectmap |
    | Template | HTTP-trigger |
    | Naam | SendMessage |
    | Autorisatieniveau | Anoniem |

    Er wordt een map genaamd **SendMessage** die de nieuwe functie bevat.

1. Open **SendMessage/function.json** om bindingen voor de functie te configureren. Wijzig de inhoud van het bestand in het volgende.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Hiermee brengt u twee wijzigingen aan in de oorspronkelijke functie:
    * De route naar `messages` wordt gewijzigd en de HTTP-trigger voor de **POST** HTTP-methode wordt beperkt.
    * Voegt een signaal/service-uitvoer binding toe die een bericht verzendt dat door de functie wordt geretourneerd naar alle clients die zijn verbonden met `chat`een signalerings service-hub met de naam.

1. Sla het bestand op.

1. Open **SendMessage/index.js** om de hoofdtekst van de functie weer te geven. Wijzig de inhoud van het bestand in het volgende.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Deze functie gebruikt de hoofdtekst van de HTTP-aanvraag en verzendt die naar de clients die zijn verbonden met SignalR Service, om een functie genaamd `newMessage` aan te roepen op elke client.

    De functie kan de identiteit van de afzender lezen en een waarde *ontvanger* in de hoofdtekst van het bericht accepteren, om een bericht privé te verzenden naar een enkele gebruiker. Deze functionaliteit wordt verderop in de zelfstudie gebruikt.

1. Sla het bestand op.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>De webinterface voor de chatclient maken en uitvoeren

De gebruikersinterface van de chattoepassing is een eenvoudige paginatoepassing (SPA) die is gemaakt met het Vue JavaScript-framework. Deze wordt afzonderlijk gehost van de functie-app. Lokaal voert u de webinterface uit met de Live Server VS Code-extensie.

1. Maak in VS Code een nieuwe map genaamd **inhoud** in de root van de hoofdprojectmap.

1. Maak in de map **inhoud** een nieuw bestand met de naam **index.html**.

1. Kopieer en plak de inhoud van **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

1. Sla het bestand op.

1. Druk op **F5** om de functie-app lokaal uit te voeren en voeg een debugger toe.

1. Met **index.html** open start u Live Server door het opdrachtenpalet van VS Code te openen (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`) en selecteert u **Live Server: openen met Live Server**. Live Server opent de toepassing in een browser.

1. De toepassing wordt geopend. Typ een bericht in het chatvak en druk op Enter. Vernieuw de toepassing om nieuwe berichten te zien. Omdat er geen verificatie is geconfigureerd worden alle berichten verzonden als ‘anoniem’.

## <a name="deploy-to-azure-and-enable-authentication"></a>Implementeren naar Azure en verificatie inschakelen

Tot nu toe voert u de functie-app en chattoepassing lokaal uit. U gaat ze nu implementeren naar Azure en verificatie en privéberichten inschakelen in de toepassing.

### <a name="log-into-azure-with-vs-code"></a>Aanmelden bij Azure met VS Code

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure: aanmelden**.

1. Volg de instructies voor het voltooien van het aanmeldingsproces in uw browser.

### <a name="create-a-storage-account"></a>Een Storage-account maken

Een Azure Storage-account is vereist voor een functie-app die wordt uitgevoerd in Azure. U kunt ook de webpagina voor de chat-gebruikers interface hosten met behulp van de functie voor statische websites van Azure Storage.

1. Klik in de Azure Portal op de knop **een resource maken** (**+**) voor het maken van een nieuwe Azure-resource.

1. Selecteer de categorie **opslag** en selecteer vervolgens **opslag account**.

1. Voer de volgende informatie in.

    | Naam | Waarde |
    |---|---|
    | Abonnement | Het abonnement selecteren dat het signaal service-exemplaar bevat |
    | Resourcegroep | Dezelfde resource groep selecteren |
    | Resourcenaam | Een unieke naam voor het opslag account |
    | Locatie | Selecteer dezelfde locatie als uw andere resources |
    | Prestaties | Standard |
    | Soort account | StorageV2 (general purpose v2) |
    | Replicatie | Lokaal redundante opslag (LRS) |
    | Toegangs niveau | Warm |

1. Klik op **beoordeling + maken**en vervolgens op **maken**.

### <a name="configure-static-websites"></a>Statische websites configureren

1. Nadat het opslag account is gemaakt, opent u het in de Azure Portal.

1. Selecteer **statische website**.

1. Selecteer **ingeschakeld** om de functie statische website in te scha kelen.

1. Voer *index. html*in bij naam van het **index document**.

1. Klik op **Opslaan**.

1. Er wordt een **primair eind punt** weer gegeven. Noteer deze waarde. Dit is vereist voor het configureren van de functie-app.

### <a name="configure-function-app-for-authentication"></a>Functie-app voor verificatie configureren

Tot nu toe werkt de chat-app anoniem. In Azure gebruikt u [App Service-verificatie](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) om de gebruiker te verifiëren. De gebruikers-ID of gebruikersnaam van de geverifieerde gebruiker kan worden doorgegeven aan de binding *SignalRConnectionInfo* voor het genereren van de verbindingsinformatie die als gebruiker wordt geverifieerd.

Bij het verzenden van een bericht kan de app beslissen om het te verzenden naar alle verbonden clients of alleen naar de clients die zijn geverifieerd voor een bepaalde gebruiker.

1. Open in VS code **Negotiate/function. json**.

1. Plaats een [bindingexpressie](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings) in de eigenschap *userId* van de binding *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Hiermee stelt u de waarde in op de gebruikersnaam van de geverifieerde gebruiker. Het kenmerk ziet er nu als volgt uit.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Sla het bestand op.


### <a name="deploy-function-app-to-azure"></a>Functie-app implementeren in azure

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`) en selecteer **Azure Functions: naar functie-app implementeren**.

1. Geef de volgende informatie op wanneer u daarom wordt gevraagd.

    | Naam | Waarde |
    |---|---|
    | Map voor implementatie | Selecteer de hoofdprojectmap |
    | Abonnement | Selecteer uw abonnement |
    | Function App | Selecteren **Nieuwe functie-app maken** |
    | Naam van de functie-app | Voer een unieke naam in |
    | Resourcegroep | Selecteer dezelfde resourcegroep als het SignalR Service-exemplaar |
    | Storage-account | Selecteer het opslag account dat u eerder hebt gemaakt |

    Er wordt een nieuwe functie-app gemaakt in Azure en de implementatie begint. Wacht totdat de installatie is voltooid.

### <a name="upload-function-app-local-settings"></a>Lokale instellingen voor functie-app uploaden

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure Functions: lokale instellingen uploaden**.

1. Geef de volgende informatie op wanneer u daarom wordt gevraagd.

    | Naam | Waarde |
    |---|---|
    | Lokale instellingsbestand | local.settings.json |
    | Abonnement | Selecteer uw abonnement |
    | Function App | Selecteer de eerder geïmplementeerde functie-app |

De lokale instellingen worden geüpload naar de functie-app in Azure. Als u wordt gevraagd de bestaande instellingen te overschrijven, selecteert u **Ja op alles**.


### <a name="enable-app-service-authentication"></a>App Service-verificatie inschakelen

App Service-verificatie biedt ondersteuning voor verificatie met Azure Active Directory, Facebook, Twitter, Microsoft-account en Google.

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure Functions: openen in portal**.

1. Selecteer het abonnement en de naam van de functie-app om de functie-app te openen in Azure Portal.

1. Ga in de functie-app die is geopend in de portal naar het tabblad **platform functies** en selecteer **verificatie/autorisatie**.

1. Schakel App Service-verificatie **in**.

1. Selecteer **Te ondernemen actie wanneer de aanvraag niet is geverifieerd** de optie Aanmelden met (verificatieprovider die u eerder hebt geselecteerd).

1. Voer in **Toegestane externe omleidings-URL's** de URL van het primaire webeindpunt van uw opslagaccount in, dat u eerder hebt genoteerd.

1. Volg de documentatie van de aanmeldingsprovider van uw keuze om de configuratie te voltooien.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Microsoft-account](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="update-the-web-app"></a>De web-app bijwerken

1. Navigeer naar het paginaoverzicht van de functie-app in Azure Portal.

1. Kopieer de URL van de functie-app.

    ![URL ophalen](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. Open **index.html** in VS Code en vervang de waarde van `apiBaseUrl` met de URL van de functie-app.

1. De toepassing kan worden geconfigureerd met verificatie via Azure Active Directory, Facebook, Twitter, Microsoft-account of Google. Selecteer de verificatieprovider die u wilt gebruiken door de waarde van `authProvider` in te stellen.

1. Sla het bestand op.

### <a name="deploy-the-web-application-to-blob-storage"></a>De webtoepassing implementeren naar blob-opslag

De web-App wordt gehost met behulp van de functie voor statische websites van Azure Blob Storage.

1. Open het opdrachtenpalet van VS Code (`Ctrl-Shift-P`, Mac OS: `Cmd-Shift-P`).

1. Zoek en selecteer de opdracht **Azure Storage: implementeren op statische website** .

1. Voer de volgende waarden in:

    | Naam | Waarde |
    |---|---|
    | Abonnement | Selecteer uw abonnement |
    | Storage-account | Selecteer het opslag account dat u eerder hebt gemaakt |
    | Map voor implementatie | Selecteer **Bladeren** en selecteer de *inhoudsmap* |

De bestanden in de map *inhoud* moeten nu worden geïmplementeerd op de statische website.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>CORS (Cross-Origin Resource Sharing) inschakelen voor de functie-app

Hoewel er een CORS-instelling staat in **local.settings.json**, wordt deze niet doorgegeven aan de functie-app in Azure. U moet deze afzonderlijk instellen.

1. Open de functie-app in de Azure-portal.

1. Ga naar het tabblad **platform functies** en selecteer **CORS**.

    ![CORS zoeken](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. Voeg in de sectie *toegestane oorsprong* een vermelding met het *primaire eind punt* van de statische website toe als waarde (Verwijder het */* navolgende).

1. Als u de functie-app vanuit een browser aanroept met de Signaale java script SDK, moet u ondersteuning voor referenties in CORS inschakelen. Schakel het selectie vakje Access-Control-Allow-referenties inschakelen in.

    ![Access Control-Allow-referenties inschakelen](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Klik op **Opslaan** om de CORS-instellingen vast te leggen.

### <a name="try-the-application"></a>De toepassing uitproberen

1. Navigeer in een browser naar het primaire webeindpunt van het opslagaccount.

1. Selecteer **Aanmelden** om u aan te melden met uw gekozen verificatieprovider.

1. Verstuur openbare berichten door ze in het hoofdchatvak te typen.

1. Verstuur privéberichten door op een gebruikersnaam te klikken in de chatgeschiedenis. Alleen de geselecteerde ontvanger ontvangt dergelijke berichten.

Gefeliciteerd! U hebt een realtime serverloze chat-app geïmplementeerd!

![Demo](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de in deze zelfstudie gemaakte resources wilt opschonen, verwijdert u de resourcegroep met Azure Portal.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd u hoe u Azure Functions gebruikt met de Azure SignalR Service. Meer informatie over het bouwen van realtime serverloze toepassingen met SignalR Service-bindingen voor Azure Functions.

> [!div class="nextstepaction"]
> [Realtime apps bouwen met Azure Functions](signalr-concept-azure-functions.md)
