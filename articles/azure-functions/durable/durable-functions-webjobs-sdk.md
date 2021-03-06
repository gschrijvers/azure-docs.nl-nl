---
title: Durable Functions uitvoeren als webjobs-Azure
description: Meer informatie over het coderen en configureren van Durable Functions om uit te voeren in webjobs met de webjobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: ad498bdbc4e6dc9745c6ef45b3dc601ad36c0a62
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82733410"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Durable Functions uitvoeren als webjobs

Durable Functions maakt standaard gebruik van de Azure Functions runtime voor het hosten van Orchestrator. Er kunnen echter bepaalde scenario's zijn waarin u meer controle nodig hebt over de code die luistert naar gebeurtenissen. In dit artikel wordt beschreven hoe u de indeling implementeert met de webjobs SDK. Zie [functies en Webjobs vergelijken](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)voor een gedetailleerde vergelijking tussen functies en webjobs.

[Azure functions](../functions-overview.md) en de uitbrei ding [Durable functions](durable-functions-overview.md) zijn gebaseerd op de [webjobs SDK](../../app-service/webjobs-sdk-how-to.md). De beveiligingshost in de webjobs SDK is de runtime in Azure Functions. Als u het gedrag wilt beheren op manieren die niet mogelijk zijn in Azure Functions, kunt u Durable Functions ontwikkelen en uitvoeren met behulp van de webjobs SDK zelf.

In versie 3. x van de webjobs SDK is de host een implementatie van `IHost`en in versie 2. x gebruikt u het `JobHost` -object.

Het voor beeld van koppelen Durable Functions is beschikbaar in een webjobs SDK 2. x-versie: down load of kloon de [Durable functions opslagplaats](https://github.com/azure/azure-functions-durable-extension/)en check *v1* -vertakking uit en ga naar de map met voor *beelden\\van webjobssdk\\* -koppeling.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de basis principes van de webjobs SDK, de ontwikkeling van C#-klassen bibliotheek voor Azure Functions en Durable Functions. Als u een inleiding tot deze onderwerpen nodig hebt, raadpleegt u de volgende bronnen:

* [Aan de slag met de webjobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Uw eerste functie maken met Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

Als u de stappen in dit artikel wilt uitvoeren:

* [Installeer Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/) met de werk belasting van **Azure Development** .

  Als u Visual Studio al hebt, maar deze werk belasting niet hebt, voegt u de werk belasting toe door **extra** > hulp**middelen en functies**te selecteren.

  (In plaats daarvan kunt u [Visual Studio code](https://code.visualstudio.com/) gebruiken, maar sommige instructies zijn specifiek voor Visual Studio.)

* Installeer [Azure Storage emulator](../../storage/common/storage-use-emulator.md) versie 5,2 of hoger en voer deze uit. Een andere is het bijwerken van het bestand *app. config* met een Azure Storage Connection String.

## <a name="webjobs-sdk-versions"></a>SDK-versies van webjobs

In dit artikel wordt uitgelegd hoe u een webjobs SDK 2. x-project ontwikkelt (gelijk aan Azure Functions versie 1. x). Zie [Webjobs SDK 3. x](#webjobs-sdk-3x) verderop in dit artikel voor meer informatie over versie 3. x.

## <a name="create-a-console-app"></a>Een console-app maken

Als u Durable Functions als webjobs wilt uitvoeren, moet u eerst een console-app maken. Een webjobs SDK-project is slechts een console-app-project waarop de juiste NuGet-pakketten zijn geïnstalleerd.

Selecteer in het dialoog venster Visual Studio **New Project** de optie **Windows Classic Desktop** > **console app (.NET Framework)**. In het project bestand `TargetFrameworkVersion` moet zijn. `v4.6.1`

Visual Studio heeft ook een project sjabloon voor webtaaksen, die u kunt gebruiken door **Cloud** > **Azure Webtaak (.NET Framework)** te selecteren. Met deze sjabloon worden veel pakketten geïnstalleerd, wat u mogelijk niet nodig hebt.

## <a name="install-nuget-packages"></a>NuGet-pakketten installeren

U hebt NuGet-pakketten nodig voor de webjobs SDK, kern bindingen, het registratie raamwerk en de duurzame taak extensie. Hier volgen de pakket opdrachten voor de **Package Manager-console** voor die pakketten, met de laatste stabiele versie nummers op de datum waarop dit artikel is geschreven:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

U hebt ook registratie providers nodig. Met de volgende opdrachten installeert u de Azure-toepassing Insights- `ConfigurationManager`provider en de. `ConfigurationManager` Hiermee kunt u de Application Insights instrumentatie sleutel ophalen uit de app-instellingen.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Met de volgende opdracht wordt de console provider geïnstalleerd:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-code

Nadat u de console-app hebt gemaakt en de NuGet-pakketten hebt geïnstalleerd die u nodig hebt, kunt u Durable Functions gaan gebruiken. U kunt dit doen met behulp van JobHost-code.

Als u de extensie Durable Functions wilt gebruiken `UseDurableTask` , roept `JobHostConfiguration` u het object `Main` op in uw methode:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Zie [host. json](../functions-host-json.md#durabletask)voor een lijst met eigenschappen die u `DurableTaskExtension` in het object kunt instellen.

De `Main` methode is ook de locatie voor het instellen van logboek registratie providers. In het volgende voor beeld worden de-console en de Application Insights providers geconfigureerd.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions in de context van webjobs verschilt enigszins van Durable Functions in de context van Azure Functions. Het is belang rijk om rekening te houden met de verschillen tijdens het schrijven van code.

De webjobs SDK biedt geen ondersteuning voor de volgende Azure Functions-functies:

* [Functie naam kenmerk](#functionname-attribute)
* [HTTP-trigger](#http-trigger)
* [API voor HTTP-beheer Durable Functions](#http-management-api)

### <a name="functionname-attribute"></a>Functie naam kenmerk

In een webjobs SDK-project is de naam van de methode van een functie de naam van de functie. Het `FunctionName` kenmerk wordt alleen gebruikt in azure functions.

### <a name="http-trigger"></a>HTTP-trigger

De webjobs SDK heeft geen HTTP-trigger. De Orchestration-client van het voorbeeld project gebruikt een timer trigger:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API voor HTTP-beheer

Omdat er geen HTTP-trigger is, heeft de webjobs SDK geen [API voor http-beheer](durable-functions-http-api.md).

In een webjobs SDK-project kunt u methoden aanroepen op het object van de Orchestration-client, in plaats van HTTP-aanvragen te verzenden. De volgende methoden komen overeen met de drie taken die u kunt uitvoeren met de HTTP-beheer-API:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

De functie Orchestration client in het voorbeeld project start de Orchestrator-functie en gaat vervolgens naar een lus die elke 2 seconden `GetStatusAsync` aanroept:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U hebt Durable Functions ingesteld om te worden uitgevoerd als Webtaak en u hebt nu een uitleg over hoe dit verschilt van het uitvoeren van Durable Functions als zelfstandige Azure Functions. Op dit moment kan het nuttig zijn om de IT-afdeling te bekijken in een voor beeld.

Deze sectie bevat een overzicht van hoe u het [voorbeeld project](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)uitvoert. Zie [aan de slag met de SDK voor Webjobs](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)voor gedetailleerde instructies voor het lokaal uitvoeren van een webjobs SDK-project en het implementeren ervan in een Azure-Webtaak.

### <a name="run-locally"></a>Lokaal uitvoeren

1. Controleer of de opslag emulator actief is (Zie [vereisten](#prerequisites)).

1. Als u de logboeken in Application Insights wilt zien wanneer u het project lokaal uitvoert:

    a. Maak een Application Insights resource en gebruik het **algemene** app-type hiervoor.

    b. Sla de instrumentatie sleutel op in het bestand *app. config* .

1. Voer het project uit.

### <a name="run-in-azure"></a>Uitvoeren in azure

1. Maak een web-app en een opslag account.

1. Sla in de Web-App de opslag connection string op in een app- `AzureWebJobsStorage`instelling met de naam.

1. Maak een Application Insights resource en gebruik het **algemene** app-type hiervoor.

1. Sla de instrumentatie sleutel op in een app- `APPINSIGHTS_INSTRUMENTATIONKEY`instelling met de naam.

1. Implementeren als Webtaak.

## <a name="webjobs-sdk-3x"></a>Webjobs SDK 3. x

In dit artikel wordt uitgelegd hoe u een webjobs SDK 2. x-project ontwikkelt. Als u een [Webjobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) -project ontwikkelt, helpt deze sectie u bij het begrijpen van de verschillen.

De belangrijkste wijziging die wordt geïntroduceerd, is het gebruik van .NET core in plaats van .NET Framework. Als u een webjobs SDK 3. x-project wilt maken, zijn de instructies hetzelfde, met de volgende uitzonde ringen:

1. Een .NET Core-Console-app maken. Selecteer in het dialoog venster Visual Studio **Nieuw project** de optie **.net core** > **-console-app (.net core)**. In het project bestand wordt `TargetFramework` opgegeven `netcoreapp2.x`dat.

1. Kies de release versie webjobs SDK 3. x van de volgende pakketten:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Stel de opslag connection string en de Application Insights instrumentatie sleutel in het bestand *appSettings. json* in met behulp van het .net core-configuratie raamwerk. Hier volgt een voorbeeld:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Wijzig de `Main` methode code om dit te doen. Hier volgt een voorbeeld:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Volgende stappen

Zie [de Webjobs SDK gebruiken](../../app-service/webjobs-sdk-how-to.md)voor meer informatie over de webjobs SDK.
