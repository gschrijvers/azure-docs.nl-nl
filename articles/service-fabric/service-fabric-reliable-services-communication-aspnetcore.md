---
title: Service communicatie met de ASP.NET Core
description: Meer informatie over het gebruik van ASP.NET Core in stateless en stateful Azure Service Fabric Reliable Services-toepassingen.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75639629"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core in azure Service Fabric Reliable Services

ASP.NET Core is een open-source-en platformoverschrijdende Framework. Dit framework is ontworpen voor het bouwen van Cloud toepassingen die zijn verbonden met internet, zoals web apps, IoT apps en mobiele back-ends.

Dit artikel is een diep gaande hand leiding voor het hosten van ASP.NET Core Services in Service Fabric Reliable Services met behulp van de **micro soft. ServiceFabric. AspNetCore.** set NuGet-pakketten.

Zie [zelf studie: een toepassing maken en implementeren met een ASP.net core web API-front-end-service en een stateful back-end-service](service-fabric-tutorial-create-dotnet-app.md)voor een inleidende zelf studie over ASP.NET Core in service Fabric en instructies over het instellen van uw ontwikkel omgeving.

In de rest van dit artikel wordt ervan uitgegaan dat u al bekend bent met ASP.NET Core. Als dat niet het geval is, lees dan de [ASP.net core fundamentals](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core in de Service Fabric omgeving

Zowel ASP.NET Core-als Service Fabric-apps kunnen worden uitgevoerd op .NET core-of volledige .NET Framework. U kunt ASP.NET Core op twee verschillende manieren gebruiken in Service Fabric:
 - **Gehost als een gast-uitvoerbaar bestand**. Op deze manier wordt hoofd zakelijk gebruikt om bestaande ASP.NET Core-toepassingen uit te voeren op Service Fabric zonder code wijzigingen.
 - **Binnen een betrouw bare service worden uitgevoerd**. Op deze manier kunt u de integratie met de Service Fabric-runtime verbeteren en stateful ASP.NET Core-Services toestaan.

In de rest van dit artikel wordt uitgelegd hoe u ASP.NET Core in een betrouw bare service kunt gebruiken via de ASP.NET Core-integratie onderdelen die bij de Service Fabric SDK worden geleverd.

## <a name="service-fabric-service-hosting"></a>Hosting van Service Fabric-service

In Service Fabric worden een of meer instanties en/of replica's van de service uitgevoerd in een *hostproces*: een uitvoerbaar bestand dat uw service code uitvoert. Als auteur van een service kunt u eigenaar zijn van het proces van de servicehost en Service Fabric het activeren en controleren.

Traditionele ASP.NET (tot MVC 5) is nauw gekoppeld aan IIS via System. Web. dll. ASP.NET Core biedt een schei ding tussen de webserver en uw webtoepassing. Met deze schei ding kunnen webtoepassingen worden overdraagbaar tussen verschillende webservers. Ook kunnen webservers *zichzelf hosten*. Dit betekent dat u een webserver in uw eigen proces kunt starten, in tegens telling tot een proces dat eigendom is van toegewezen webserver software, zoals IIS.

Als u een Service Fabric-service en ASP.NET wilt combi neren als een uitvoerbaar gast bestand of in een betrouw bare service, moet u ASP.NET in uw hostproces van de service kunnen starten. Met ASP.NET Core self-hosting kunt u dit doen.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>ASP.NET Core hosten in een betrouw bare service
Zelf-hostende ASP.NET Core toepassingen maken meestal een WebHost in het ingangs punt van een toepassing, zoals `static void Main()` de methode `Program.cs`in. In dit geval is de levens cyclus van de WebHost gebonden aan de levens cyclus van het proces.

![ASP.NET Core in een proces hosten][0]

Maar het ingangs punt van de toepassing is niet de juiste plek om een WebHost in een betrouw bare service te maken. Dat komt doordat het ingangs punt van de toepassing alleen wordt gebruikt voor het registreren van een service type bij de runtime van Service Fabric, zodat het instanties van dat Service type kan maken. De WebHost moet worden gemaakt in een betrouw bare service zelf. Binnen het proces van de servicehost kunnen service-exemplaren en/of replica's meerdere levens cycli passeren. 

Een betrouwbaar service-exemplaar wordt vertegenwoordigd door de service klasse die is `StatelessService` afgeleid `StatefulService`van of. De communicatie stack voor een service is opgenomen in een `ICommunicationListener` implementatie in uw service klasse. De `Microsoft.ServiceFabric.AspNetCore.*` NuGet-pakketten bevatten implementaties `ICommunicationListener` van waarmee de ASP.net core WEBHOST voor Kestrel of http. sys in een betrouw bare service wordt gestart en beheerd.

![Diagram voor het hosten van ASP.NET Core in een betrouw bare service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
De `ICommunicationListener` implementaties voor Kestrel en http. sys in de `Microsoft.ServiceFabric.AspNetCore.*` NuGet-pakketten hebben vergelijk bare gebruiks patronen. Maar ze voeren enigszins verschillende acties uit die specifiek zijn voor elke webserver. 

Beide communicatie listeners bieden een constructor die de volgende argumenten gebruikt:
 - **`ServiceContext serviceContext`**: Dit is het `ServiceContext` object dat informatie bevat over de uitgevoerde service.
 - **`string endpointName`**: Dit is de naam van een `Endpoint` configuratie in ServiceManifest. XML. Het is hoofd zakelijk waar de twee communicatie-listeners verschillen. HTTP. sys *vereist* een `Endpoint` configuratie, terwijl Kestrel niet is.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Dit is een lambda die u implementeert, waarin u een `IWebHost`maakt en retourneert. Zo kunt u de manier `IWebHost` configureren waarop u normaal gesp roken zou doen in een ASP.net core-toepassing. De Lambda bevat een URL die voor u wordt gegenereerd, afhankelijk van de Service Fabric integratie opties die u gebruikt en de `Endpoint` configuratie die u opgeeft. U kunt deze URL vervolgens wijzigen of gebruiken om de webserver te starten.

## <a name="service-fabric-integration-middleware"></a>Middleware voor Service Fabric integratie
Het `Microsoft.ServiceFabric.AspNetCore` NuGet-pakket bevat `UseServiceFabricIntegration` de uitbreidings methode `IWebHostBuilder` voor het toevoegen van service Fabric bewuste middleware. Deze middleware configureert de Kestrel of HTTP. sys `ICommunicationListener` voor het registreren van een unieke service-URL met de service Fabric Naming Service. Vervolgens worden client aanvragen gevalideerd om ervoor te zorgen dat clients verbinding maken met de juiste service. 

Deze stap is nodig om te voor komen dat clients per ongeluk verbinding maken met de verkeerde service. Dat komt doordat meerdere webtoepassingen in een omgeving met gedeelde host, zoals Service Fabric, op dezelfde fysieke of virtuele machine kunnen worden uitgevoerd, maar geen unieke hostnamen. Dit scenario wordt uitgebreid beschreven in de volgende sectie.

### <a name="a-case-of-mistaken-identity"></a>Een geval van een onjuiste identiteit
Service replica's, ongeacht het Protocol, kunnen Luis teren naar een unieke combi natie van IP: poort. Zodra een service replica is begonnen met Luis teren op een IP: poort eindpunt, wordt het eind punt gerapporteerd aan de Service Fabric Naming Service. Daar kunnen clients of andere services het detecteren. Als services dynamisch toegewezen toepassings poorten gebruiken, kan een service replica het hetzelfde IP-adres van de poort van een andere service die eerder op dezelfde fysieke of virtuele machine wordt gebruikt, per keer gebruiken. Dit kan ertoe leiden dat een client per ongeluk verbinding maakt met de verkeerde service. Dit scenario kan optreden als de volgende reeks gebeurtenissen zich voordoet:

 1. Service A luistert op 10.0.0.1:30000 via HTTP. 
 2. De client lost Service A op en krijgt adres 10.0.0.1:30000.
 3. Service een verplaatst naar een ander knoop punt.
 4. Service B wordt op 10.0.0.1 geplaatst en maakt gebruik van dezelfde poort 30000.
 5. De client probeert verbinding te maken met de service A met het adres in de cache 10.0.0.1:30000.
 6. De client is nu verbonden met Service B, maar het is niet gelukt om verbinding te hebben met de verkeerde service.

Dit kan leiden tot fouten op wille keurige tijdstippen die lastig kunnen worden vastgesteld.

### <a name="using-unique-service-urls"></a>Unieke service-Url's gebruiken
Om deze fouten te voor komen, kunnen services een eind punt plaatsen op de Naming Service met een unieke id en vervolgens die unieke id valideren tijdens client aanvragen. Dit is een coöperatieve actie tussen services in een niet-vijandelijke, vertrouwde Tenant omgeving. Het biedt geen veilige verificatie van de service in een vijandelijke Tenant omgeving.

In een vertrouwde omgeving voegt de middleware die wordt toegevoegd door de `UseServiceFabricIntegration` methode automatisch een unieke id toe aan het adres dat wordt geplaatst op de Naming Service. De id controleert op elke aanvraag. Als de id niet overeenkomt, retourneert de middleware onmiddellijk een HTTP 410-antwoord.

Services die gebruikmaken van een dynamisch toegewezen poort, moeten gebruikmaken van deze middleware.

Services die gebruikmaken van een vaste unieke poort, hebben dit probleem niet in een samenwerkings omgeving. Een vaste unieke poort wordt doorgaans gebruikt voor extern gerichte services die een bekende poort nodig hebben voor client toepassingen om verbinding mee te maken. De meeste Internet gerichte webtoepassingen gebruiken bijvoorbeeld poort 80 of 443 voor webbrowser verbindingen. In dit geval moet de unieke id niet worden ingeschakeld.

In het volgende diagram ziet u de aanvraag stroom waarbij de middleware is ingeschakeld:

![Integratie van Service Fabric ASP.NET Core][2]

Zowel Kestrel-als HTTP. `ICommunicationListener` sys-implementaties gebruiken dit mechanisme op dezelfde manier. Hoewel HTTP. sys aanvragen intern kan onderscheiden op basis van unieke URL-paden door gebruik te maken van de onderliggende **http. sys-** functie *not* voor het delen van poorten, wordt `ICommunicationListener` deze functionaliteit niet gebruikt door de http. sys-implementatie. Dat komt doordat de HTTP 503-en HTTP 404-fout status codes in het scenario dat eerder is beschreven, worden veroorzaakt. Op die manier is het voor clients lastig om het doel van de fout te bepalen, omdat HTTP 503 en HTTP 404 vaak worden gebruikt om andere fouten aan te geven. 

Daarom wordt zowel Kestrel-als HTTP. `ICommunicationListener` sys-implementaties standaard gestandardization op middleware `UseServiceFabricIntegration` die door de uitbreidings methode wordt verschaft. Daarom hoeven clients alleen een actie voor het opnieuw oplossen van het service-eind punt uit te voeren op HTTP 410-reacties.

## <a name="httpsys-in-reliable-services"></a>HTTP. sys in Reliable Services
U kunt HTTP. sys in Reliable Services gebruiken door het pakket **micro soft. ServiceFabric. AspNetCore. HttpSys** NuGet te importeren. Dit pakket bevat `HttpSysCommunicationListener`een implementatie van `ICommunicationListener`. `HttpSysCommunicationListener`Hiermee kunt u een ASP.NET Core WebHost maken binnen een betrouw bare service met behulp van HTTP. sys als de webserver.

HTTP. sys is gebaseerd op de [Windows HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Deze API gebruikt het **http. sys-** kernelstuurprogramma om HTTP-aanvragen te verwerken en te routeren naar processen die webtoepassingen uitvoeren. Hierdoor kunnen meerdere processen op dezelfde fysieke of virtuele machine fungeren als host voor webtoepassingen op dezelfde poort, disambiguated door een unieke URL-pad of hostnaam. Deze functies zijn handig in Service Fabric voor het hosten van meerdere websites in hetzelfde cluster.

>[!NOTE]
>HTTP. sys-implementatie werkt alleen op het Windows-platform.

In het volgende diagram ziet u hoe HTTP. sys het stuur programma **http. sys** gebruikt in Windows voor het delen van poorten:

![HTTP. sys-diagram][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP. sys in een stateless service
Als u `HttpSys` wilt gebruiken in een stateless service, `CreateServiceInstanceListeners` overschrijft u de `HttpSysCommunicationListener` -methode en retourneert u een exemplaar:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP. sys in een stateful service

`HttpSysCommunicationListener`is momenteel niet ontworpen voor gebruik in stateful Services vanwege complicaties met de onderliggende **http. sys-** functie voor het delen van poorten. Zie de volgende sectie over dynamische poort toewijzing met HTTP. sys voor meer informatie. Voor stateful Services is Kestrel de voorgestelde webserver.

### <a name="endpoint-configuration"></a>Eindpunt configuratie

Een `Endpoint` configuratie is vereist voor webservers die gebruikmaken van de Windows http-server-API, met inbegrip van http. sys. Webservers die gebruikmaken van de Windows HTTP-Server-API moeten eerst hun URL reserveren met HTTP. sys (dit wordt normaal gesp roken uitgevoerd met het hulp programma [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) ). 

Voor deze actie zijn verhoogde bevoegdheden vereist die uw services niet standaard hebben. De opties http of HTTPS voor de `Protocol` eigenschap van de `Endpoint` configuratie in ServiceManifest. XML worden speciaal gebruikt om de service Fabric-runtime te instrueren om een URL met http. sys namens u te registreren. Dit wordt gedaan met behulp van het URL-voor voegsel voor [*sterke joker tekens*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) .

Als u bijvoorbeeld wilt reserveren `http://+:80` voor een service, gebruikt u de volgende configuratie in ServiceManifest. XML:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

En de naam van het eind punt moet worden `HttpSysCommunicationListener` door gegeven aan de constructor:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>HTTP. sys gebruiken met een statische poort
Geef het poort nummer op in de `Endpoint` configuratie als u een statische poort met http. sys wilt gebruiken:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>HTTP. sys gebruiken met een dynamische poort
Als u een dynamisch toegewezen poort met HTTP. sys wilt gebruiken, `Port` laat u de `Endpoint` eigenschap in de configuratie weg:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Een dynamische poort die door een `Endpoint` configuratie wordt toegewezen, biedt slechts één poort *per hostproces*. Met het huidige Service Fabric hosting model kunnen meerdere service-exemplaren en/of replica's worden gehost in hetzelfde proces. Dit betekent dat elke poort hetzelfde is wanneer deze wordt toegewezen via de `Endpoint` configuratie. Meerdere **http. sys-** exemplaren kunnen een poort delen met behulp van de onderliggende **http. sys-** functie voor het delen van poorten. Maar het wordt niet ondersteund door `HttpSysCommunicationListener` de complicaties die het biedt voor client aanvragen. Voor dynamisch poort gebruik is Kestrel de aanbevolen webserver.

## <a name="kestrel-in-reliable-services"></a>Kestrel in Reliable Services
U kunt Kestrel in Reliable Services gebruiken door het NuGet-pakket **micro soft. ServiceFabric. AspNetCore. Kestrel** te importeren. Dit pakket bevat `KestrelCommunicationListener`een implementatie van `ICommunicationListener`. `KestrelCommunicationListener`Hiermee kunt u een ASP.NET Core WebHost in een betrouw bare service maken met behulp van Kestrel als de webserver.

Kestrel is een cross-platform webserver voor ASP.NET Core. In tegens telling tot HTTP. sys gebruikt Kestrel geen gecentraliseerd eindpunt beheer. Ook in tegens telling tot HTTP. sys biedt Kestrel geen ondersteuning voor het delen van poorten tussen meerdere processen. Elk exemplaar van Kestrel moet een unieke poort gebruiken. Zie de [Implementatie Details](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)voor meer informatie over Kestrel.

![Kestrel diagram][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel in een stateless service
Als u `Kestrel` wilt gebruiken in een stateless service, `CreateServiceInstanceListeners` overschrijft u de `KestrelCommunicationListener` -methode en retourneert u een exemplaar:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel in een stateful service
Als u `Kestrel` wilt gebruiken in een stateful service, `CreateServiceReplicaListeners` overschrijft u de `KestrelCommunicationListener` -methode en retourneert u een exemplaar:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

In dit voor beeld wordt er een singleton `IReliableStateManager` -exemplaar van aan de container voor het inspuiten van de webhost-afhankelijkheids gegeven. Dit is niet strikt nood zakelijk, maar u kunt met `IReliableStateManager` behulp van betrouw bare verzamelingen in uw actie methoden van MVC-controller.

Er `Endpoint` is *geen* configuratie naam aan `KestrelCommunicationListener` een stateful service door gegeven. Dit wordt gedetailleerd beschreven in de volgende sectie.

### <a name="configure-kestrel-to-use-https"></a>Kestrel configureren voor gebruik van HTTPS
Wanneer u HTTPS inschakelt met Kestrel in uw service, moet u verschillende luisterende opties instellen. Werk de `ServiceInstanceListener` om een *EndpointHttps* -eind punt te gebruiken en luister naar een specifieke poort (zoals poort 443). Wanneer u de webhost configureert voor het gebruik van de Kestrel-webserver, moet u Kestrel configureren om te Luis teren naar IPv6-adressen op alle netwerk interfaces: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Zie [Kestrel configureren voor het gebruik van HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)voor een volledig voor beeld in een zelf studie.


### <a name="endpoint-configuration"></a>Eindpunt configuratie
Er `Endpoint` is geen configuratie vereist voor het gebruik van Kestrel. 

Kestrel is een eenvoudige, zelfstandige webserver. In tegens telling tot HTTP. sys (of HttpListener), hebt `Endpoint` u geen configuratie nodig in ServiceManifest. XML omdat hiervoor geen URL-registratie nodig is. 

#### <a name="use-kestrel-with-a-static-port"></a>Kestrel gebruiken met een statische poort
U kunt een statische poort configureren in de `Endpoint` configuratie van ServiceManifest. XML voor gebruik met Kestrel. Hoewel dit niet strikt nood zakelijk is, biedt dit twee mogelijke voor delen:
 - Als de poort niet in het poort bereik van de toepassing valt, wordt deze geopend via de firewall van het besturings systeem door Service Fabric.
 - De URL die u hebt ontvangen `KestrelCommunicationListener` , zal deze poort gebruiken.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Als er `Endpoint` een is geconfigureerd, moet de naam ervan worden door `KestrelCommunicationListener` gegeven aan de constructor: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Als ServiceManifest. XML geen `Endpoint` configuratie gebruikt, laat u de naam in de `KestrelCommunicationListener` constructor weg. In dit geval wordt een dynamische poort gebruikt. Zie de volgende sectie voor meer informatie hierover.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Kestrel gebruiken met een dynamische poort
Kestrel kan de automatische poort toewijzing niet gebruiken van `Endpoint` de configuratie in ServiceManifest. XML. Dat komt doordat bij automatische poort toewijzing van `Endpoint` een configuratie een unieke poort per *hostproces*wordt toegewezen en één hostproces meerdere Kestrel-exemplaren kan bevatten. Dit werkt niet met Kestrel omdat het geen ondersteuning biedt voor het delen van poorten. Daarom moet elk Kestrel-exemplaar op een unieke poort worden geopend.

Als u dynamische poort toewijzing met Kestrel wilt gebruiken, `Endpoint` laat u de configuratie in ServiceManifest. XML volledig weg en geeft u de naam `KestrelCommunicationListener` van het eind punt niet door aan de constructor. dit doet u als volgt:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

In deze configuratie `KestrelCommunicationListener` selecteert u automatisch een ongebruikte poort uit het poort bereik van de toepassing.

Voor HTTPS moet het eind punt zijn geconfigureerd met het HTTPS-protocol zonder een poort die is opgegeven in ServiceManifest. XML en de naam van het eind punt door geven aan KestrelCommunicationListener-constructor.


## <a name="service-fabric-configuration-provider"></a>Configuratie provider Service Fabric
App-configuratie in ASP.NET Core is gebaseerd op sleutel-waardeparen die zijn ingesteld door de configuratie provider. Lees de [configuratie in ASP.net core](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) voor meer informatie over algemene ondersteuning voor ASP.net core-configuratie.

In deze sectie wordt beschreven hoe de Service Fabric-configuratie provider integreert met ASP.NET Core configuratie `Microsoft.ServiceFabric.AspNetCore.Configuration` door het pakket NuGet te importeren.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration-opstart extensies
Nadat u het `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet-pakket hebt geïmporteerd, moet u de configuratie bron van de service Fabric registreren bij ASP.net Core Configuratie-API. U doet dit door **AddServiceFabricConfiguration** -uitbrei dingen `Microsoft.ServiceFabric.AspNetCore.Configuration` in de `IConfigurationBuilder`naam ruimte te controleren op basis van.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

De ASP.NET Core-Service heeft nu toegang tot de Service Fabric configuratie-instellingen, net als bij andere toepassings instellingen. U kunt bijvoorbeeld het patroon opties gebruiken om instellingen te laden in sterk getypte objecten.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Standaard sleutel toewijzing
Standaard bevat de Service Fabric-configuratie provider de naam van het pakket, de naam van de sectie en de naam van de eigenschap. Deze vormen samen de ASP.NET Core configuratie sleutel, als volgt:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Als u bijvoorbeeld een configuratie pakket `MyConfigPackage` met de volgende inhoud hebt, is de configuratie waarde beschikbaar op ASP.net core `IConfiguration` via *MyConfigPackage: MyConfigSection: MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Configuratie opties Service Fabric
De Service Fabric-configuratie provider biedt `ServiceFabricConfigurationOptions` ook ondersteuning voor het wijzigen van het standaard gedrag van sleutel toewijzing.

#### <a name="encrypted-settings"></a>Versleutelde instellingen
Service Fabric ondersteunt versleutelde instellingen, zoals de Service Fabric-configuratie provider. De versleutelde instellingen worden niet standaard `IConfiguration` naar ASP.net Core gedecodeerd. In plaats daarvan worden de versleutelde waarden opgeslagen. Maar als u de waarde die u wilt opslaan in ASP.NET Core IConfiguration wilt ontsleutelen, kunt *DecryptValue* u als volgt de vlag DecryptValue `AddServiceFabricConfiguration` in de extensie instellen op False:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Meerdere configuratie pakketten
Service Fabric ondersteunt meerdere configuratie pakketten. De naam van het pakket is standaard opgenomen in de configuratie sleutel. U kunt de `IncludePackageName` vlag echter als volgt instellen op False:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Aangepaste sleutel toewijzing, waarde extractie en gegevens populatie
De provider van de Service Fabric-configuratie biedt ook ondersteuning voor meer geavanceerde scenario's voor `ExtractKeyFunc` het aanpassen van de sleutel toewijzing met `ExtractValueFunc`en aangepast-extract de waarden met. U kunt zelfs het hele proces voor het vullen van gegevens van Service Fabric configuratie naar ASP.NET Core configuratie wijzigen met `ConfigAction`behulp van.

De volgende voor beelden laten zien hoe u `ConfigAction` kunt gebruiken om de gegevens populatie aan te passen:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Configuratie-updates
De configuratie provider Service Fabric ondersteunt ook configuratie-updates. U kunt ASP.NET Core `IOptionsMonitor` gebruiken om wijzigings meldingen te ontvangen en vervolgens `IOptionsSnapshot` gebruiken om configuratie gegevens opnieuw te laden. Zie [ASP.net core Options (opties](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)) voor meer informatie.

Deze opties worden standaard ondersteund. Er is geen verdere code ring nodig om configuratie-updates in te scha kelen.

## <a name="scenarios-and-configurations"></a>Scenario's en configuraties
In deze sectie vindt u een combi natie van webserver, poort configuratie, Service Fabric integratie opties en diverse instellingen voor het oplossen van de volgende scenario's:
 - Extern beschikbaar ASP.NET Core stateless Services
 - Alleen intern ASP.NET Core stateless Services
 - Alleen intern ASP.NET Core stateful Services

Een **extern** beschik bare service is een eind punt dat wordt aangeroepen van buiten het cluster, meestal via een Load Balancer.

Een service met **alleen interne** services waarvan het eind punt alleen binnen het cluster wordt aangeroepen.

> [!NOTE]
> Stateful service-eind punten mogen doorgaans niet worden blootgesteld aan Internet. Clusters achter de load balancers die geen inzicht hebben in Service Fabric service oplossing, zoals Azure Load Balancer, kunnen stateful Services niet weer geven. Dat komt doordat de load balancer geen verkeer naar de juiste stateful service replica kunt vinden en routeren. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Extern beschikbaar ASP.NET Core stateless Services
Kestrel is de voorgestelde webserver voor front-end-services die externe, Internet gerichte HTTP-eind punten beschikbaar maken. In Windows kan HTTP. sys mogelijkheden voor het delen van poorten bieden, waarmee u meerdere webservices op dezelfde set knoop punten kunt hosten met behulp van dezelfde poort. In dit scenario worden de webservices onderscheiden door de hostnaam of het pad, zonder dat hiervoor een front-end-proxy of-gateway nodig is om HTTP-route ring te bieden.
 
Wanneer een stateless service wordt blootgesteld aan Internet, moet een bekende en stabiele eind punt worden gebruikt dat bereikbaar is via een load balancer. U geeft deze URL naar de gebruikers van uw toepassing. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | Kestrel is de voorkeurs webserver, omdat deze wordt ondersteund in Windows en Linux. |
| Poort configuratie | statisch | Een bekende statische poort moet worden geconfigureerd in de `Endpoints` configuratie van ServiceManifest. XML, zoals 80 voor HTTP of 443 voor HTTPS. |
| ServiceFabricIntegrationOptions | Geen | Gebruik de `ServiceFabricIntegrationOptions.None` optie bij het configureren van service Fabric Integration-middleware, zodat de service niet probeert binnenkomende aanvragen voor een unieke id te valideren. Externe gebruikers van uw toepassing weten niet de unieke identiteits gegevens die door de middleware worden gebruikt. |
| Aantal instanties | -1 | Bij typische gebruiks voorbeelden moet de instelling voor het aantal instanties worden ingesteld op *-1*. Dit wordt gedaan, zodat er een exemplaar beschikbaar is op alle knoop punten die verkeer ontvangen van een load balancer. |

Als meerdere extern beschik bare Services dezelfde set knoop punten delen, kunt u HTTP. sys gebruiken met een uniek, maar een stabiel URL-pad. U kunt dit doen door de URL die u hebt ingevoerd bij het configureren van IWebHost te wijzigen. Houd er rekening mee dat dit alleen van toepassing is op HTTP. sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Alleen stateless ASP.NET Core Service met alleen interne status
Stateless services die alleen binnen het cluster worden aangeroepen, moeten unieke Url's en dynamisch toegewezen poorten gebruiken om de samen werking tussen meerdere services te garanderen. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | Hoewel u HTTP. sys kunt gebruiken voor interne stateless Services, is Kestrel de beste server waarmee meerdere service-exemplaren een host kunnen delen.  |
| Poort configuratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturings systeem delen, waardoor er unieke poorten nodig zijn. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Met dynamische poort toewijzing voor komt u met deze instelling dat eerder beschreven identiteits problemen worden opgelost. |
| InstanceCount | iedere | De instelling voor het aantal instanties kan worden ingesteld op elke waarde die nodig is om de service te kunnen uitvoeren. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Alleen stateful ASP.NET Core-Service
Stateful services die alleen binnen het cluster worden aangeroepen, moeten dynamisch toegewezen poorten gebruiken om de samen werking tussen meerdere services te garanderen. We raden u aan de volgende configuratie uit te voeren:

|  |  | **Opmerkingen** |
| --- | --- | --- |
| Webserver | Kestrel | De `HttpSysCommunicationListener` is niet bedoeld voor gebruik door stateful services waarin replica's een hostproces delen. |
| Poort configuratie | dynamisch toegewezen | Meerdere replica's van een stateful service kunnen een hostproces of host-besturings systeem delen, waardoor er unieke poorten nodig zijn. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Met dynamische poort toewijzing voor komt u met deze instelling dat eerder beschreven identiteits problemen worden opgelost. |

## <a name="next-steps"></a>Volgende stappen
[Foutopsporing uitvoeren in uw Service Fabric-toepassing met behulp van Visual Studio](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
