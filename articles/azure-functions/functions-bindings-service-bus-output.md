---
title: Azure Service Bus bindingen voor Azure Functions
description: Meer informatie over het verzenden van Azure Service Bus berichten vanuit Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: d6817ac4ebc272747776eab8b11dba62f318e4ed
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690726"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus uitvoer binding voor Azure Functions

Gebruik Azure Service Bus uitvoer binding om berichten uit de wachtrij of het onderwerp te verzenden.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een service bus wachtrij bericht verzendt:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *Function. json* -bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Dit is de C#-script code waarmee één bericht wordt gemaakt:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Dit is de C#-script code waarmee meerdere berichten worden gemaakt:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voor beeld ziet u een Service Bus uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie gebruikt een timer trigger om elke 15 seconden een wachtrij bericht te verzenden.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Hier volgt een Java script-script code waarmee één bericht wordt gemaakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Dit is de Java script-script code waarmee meerdere berichten worden gemaakt:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u kunt schrijven naar een Service Bus wachtrij in python.

Een definitie van een Service Bus binding is gedefinieerd in *Function. json* waarbij *type* is `serviceBus`ingesteld op.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

`set` In * _ \_init_\_. py*kunt u een bericht naar de wachtrij schrijven door een waarde door te geven aan de-methode.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die een bericht naar `myqueue` een service bus wachtrij verzendt wanneer het wordt geactiveerd door een HTTP-aanvraag.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 Gebruik in de [runtime-bibliotheek van Java](/java/api/overview/azure/functions/runtime)- `@QueueOutput` functies de annotatie voor functie parameters waarvan de waarde zou worden geschreven naar een service bus wachtrij.  Het parameter type moet zijn `OutputBinding<T>`, waarbij T een systeem eigen Java-type is van een POJO.

Java-functies kunnen ook worden geschreven naar een Service Bus onderwerp. In het volgende voor beeld `@ServiceBusTopicOutput` wordt met behulp van de aantekening de configuratie voor de uitvoer binding beschreven. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en het abonnement. U kunt ook de toegangs rechten van de verbinding opgeven. Het kiezen van de instelling voor toegangs rechten wordt uitgelegd in het gedeelte [output-Configuration](#configuration) . Hier volgt een voor beeld waarin het kenmerk wordt weer gegeven dat wordt toegepast op de geretourneerde waarde van de functie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om de naam op te geven van een app-instelling die het service bus connection string bevat dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer-voor beeld](#example).

U kunt het `ServiceBusAccount` -kenmerk gebruiken om het service bus-account op te geven dat moet worden gebruikt bij klasse, methode of parameter niveau.  Zie [trigger-Attributes](functions-bindings-service-bus-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

De `ServiceBusQueueOutput` aantekeningen `ServiceBusTopicOutput` en zijn beschikbaar om een bericht te schrijven als een functie-uitvoer. De para meter die is gedecoreerd met deze annotaties moet worden gedeclareerd als een `OutputBinding<T>` WHERE `T` is het type dat overeenkomt met het type van het bericht.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het `ServiceBus` -kenmerk.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**voert** | n.v.t. | Moet worden ingesteld op ' serviceBus '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**draaien** | n.v.t. | Moet worden ingesteld op out. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | n.v.t. | De naam van de variabele die de wachtrij of het onderwerp van het bericht in de functie code vertegenwoordigt. Ingesteld op ' $return ' om te verwijzen naar de retour waarde van de functie. |
|**queueName**|**QueueName**|De naam van de wachtrij.  Stel deze waarde alleen in als u wachtrij berichten verzendt, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het onderwerp. Stel deze waarde alleen in als er onderwerp-berichten worden verzonden, niet voor een wachtrij.|
|**verbinding**|**Combi**|De naam van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met "AzureWebJobs", kunt u alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' MyServiceBus ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u leeg `connection` laat, gebruikt de functions runtime de standaard service bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De connection string moet voor een Service Bus naam ruimte zijn, niet beperkt tot een specifieke wachtrij of een specifiek onderwerp.|
|**accessRights** (alleen v1)|**Toegang**|Toegangs rechten voor de connection string. Beschik bare `manage` waarden `listen`zijn en. De standaard waarde `manage`is, waarmee wordt aangegeven `connection` dat de de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt `accessRights` u in op ' Luis teren '. Anders kan het uitvoeren van de functions-runtime geen bewerkingen uitvoeren waarvoor het beheer van rechten nodig is. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

In Azure Functions 1. x wordt de wachtrij door de runtime gemaakt als deze nog niet bestaat en is `accessRights` ingesteld `manage`op. In functies versie 2. x en hoger moet de wachtrij of het onderwerp al bestaan; Als u een wachtrij of onderwerp opgeeft dat niet bestaat, mislukt de functie. 

# <a name="c"></a>[C#](#tab/csharp)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T`Dit kan elk type JSON-serialiseerbaar zijn. Als de waarde van de para meter null is wanneer de functie wordt afgesloten, worden met functies het bericht met een null-object gemaakt.
* `out string`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 1. x)
* `out Message`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 2. x en hoger)
* `ICollector<T>`of `IAsyncCollector<T>` -voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de `Add` -methode aanroept.

Wanneer u werkt met C#-functies:

* Asynchrone functies hebben een retour waarde nodig `IAsyncCollector` of in plaats `out` van een para meter.

* Als u toegang wilt krijgen tot de sessie- [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) id, maakt u `sessionId` een binding met een type en gebruikt u de eigenschap.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Gebruik de volgende parameter typen voor de uitvoer binding:

* `out T paramName` - `T`Dit kan elk type JSON-serialiseerbaar zijn. Als de waarde van de para meter null is wanneer de functie wordt afgesloten, worden met functies het bericht met een null-object gemaakt.
* `out string`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out byte[]`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, wordt door functies geen bericht gemaakt.
* `out BrokeredMessage`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 1. x)
* `out Message`-Als de waarde van de para meter null is wanneer de functie wordt afgesloten, maakt functies geen bericht (voor de functies 2. x en hoger)
* `ICollector<T>`of `IAsyncCollector<T>` -voor het maken van meerdere berichten. Er wordt een bericht gemaakt wanneer u de `Add` -methode aanroept.

Wanneer u werkt met C#-functies:

* Asynchrone functies hebben een retour waarde nodig `IAsyncCollector` of in plaats `out` van een para meter.

* Als u toegang wilt krijgen tot de sessie- [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) id, maakt u `sessionId` een binding met een type en gebruikt u de eigenschap.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp `context.bindings.<name from function.json>`met behulp van. U kunt een teken reeks, een byte matrix of een Java Script-object (gedeserialiseerd in JSON) toewijzen aan `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

# <a name="java"></a>[Java](#tab/java)

Gebruik de [Azure service bus SDK](https://docs.microsoft.com/azure/service-bus-messaging) in plaats van de ingebouwde uitvoer binding.

---

## <a name="exceptions-and-return-codes"></a>Uitzonde ringen en retour codes

| Binding | Naslaginformatie |
|---|---|
| Service Bus | [Service Bus fout codes](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Service Bus limieten](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>instellingen voor de host. json

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. Het onderstaande voor beeld van host. JSON-bestand bevat alleen de instellingen voor deze binding. Zie voor meer informatie over globale configuratie-instellingen [host. json Reference voor Azure functions versie](functions-host-json.md).

> [!NOTE]
> Zie [host. json Reference voor Azure functions 1. x](functions-host-json-v1.md)voor een verwijzing naar de host. json in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": true,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:05:00"
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```
Als u hebt `isSessionsEnabled` ingesteld op `true`, wordt `sessionHandlerOptions` de gehonoreerd.  Als u hebt `isSessionsEnabled` ingesteld op `false`, wordt `messageHandlerOptions` de gehonoreerd.

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|prefetchCount|0|Hiermee wordt het aantal berichten opgehaald of ingesteld dat de ontvanger van het bericht tegelijk kan aanvragen.|
|maxAutoRenewDuration|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.|
|Automatisch aanvullen|waar|Hiermee wordt aangegeven of de trigger automatisch moet worden voltooid na de verwerking, of dat de functie code hand matig wordt aangeroepen.|
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de call back dat de bericht pomp moet initiëren per geschaald exemplaar. De functie runtime verwerkt standaard meerdere berichten tegelijk.|
|maxConcurrentSessions|2000|Het maximum aantal sessies dat gelijktijdig kan worden verwerkt per geschaald exemplaar.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Service Bus wachtrij of onderwerp bericht wordt gemaakt (trigger)](./functions-bindings-service-bus-trigger.md)
