---
title: Java-toepassingen bewaken op elke omgeving-Azure Monitor Application Insights
description: Bewaking van toepassings prestaties voor Java-toepassingen die worden uitgevoerd in een wille keurige omgeving zonder de app te instrumenteren. Gedistribueerde tracering en toepassings toewijzing.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 591cfad0f4719595835f212b9205354aad7cb9e8
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508068"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Bewaking van Java-toepassingen Azure Monitor Application Insights-open bare preview-versie

Bewaking van Java-toepassingen is heel eenvoudig: er zijn geen code wijzigingen, de Java-Agent kan worden ingeschakeld via slechts een paar configuratie wijzigingen.

 De Java-agent werkt in elke omgeving en biedt u de mogelijkheid om al uw Java-toepassingen te bewaken. Met andere woorden, of u nu uw java-apps uitvoert op Vm's, on-premises, in AKS, op Windows, Linux-u de naam geeft, de Java 3,0-agent bewaakt uw app.

Het is niet meer nodig om de Application Insights Java-SDK toe te voegen aan uw toepassing, omdat de 3,0-agent autoverzoekt, afhankelijkheden en logboeken zelf.

U kunt nog steeds aangepaste telemetrie verzenden vanuit uw toepassing. De 3,0-agent volgt deze samen met alle automatisch verzamelde telemetrie en correleert deze.

## <a name="quickstart"></a>Snelstartgids

**1. de agent downloaden**

[Applicationinsights-agent-3.0.0-Preview. 4. jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.4/applicationinsights-agent-3.0.0-PREVIEW.4.jar) downloaden

**2. Wijs de JVM naar de agent**

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.4.jar` aan de JVM-argumenten van uw toepassing

Typische argumenten voor JVM `-Xmx512m` zijn `-XX:+UseG1GC`onder andere en. Als u weet waar u deze toevoegt, weet u dus al waar u dit kunt toevoegen.

Voor meer informatie over het configureren van de JVM-argumenten van uw toepassing raadpleegt [u 3,0 Preview: tips voor het bijwerken van uw JVM-argumenten](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. Wijs de agent naar uw Application Insights-resource**

Als u nog geen Application Insights resource hebt, kunt u een nieuw item maken door de stappen in de [hand leiding](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)voor het maken van resources te volgen.

Wijs de agent naar uw Application Insights-bron, door een omgevings variabele in te stellen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

U kunt ook een configuratie bestand maken `ApplicationInsights.json`met de naam en dit in dezelfde map plaatsen `applicationinsights-agent-3.0.0-PREVIEW.4.jar`als met de volgende inhoud:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

U kunt uw connection string vinden in uw Application Insights-resource:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindings reeks Application Insights":::

**4. dat is alles!**

Start nu uw toepassing en ga naar uw Application Insights-resource in de Azure Portal om uw bewakings gegevens weer te geven.

> [!NOTE]
> Het kan enkele minuten duren voordat uw bewakings gegevens in de portal worden weer gegeven.


## <a name="configuration-options"></a>Configuratie-opties

In het `ApplicationInsights.json` bestand kunt u verder configureren:

* Rolnaam van Cloud
* Cloud rolinstantie
* Toepassings logboeken vastleggen
* Metrische gegevens van JMX
* Micrometer
* Hartslag
* Steekproeven
* HTTP-proxy
* Zelf diagnostische gegevens

Bekijk de Details voor de [open bare preview van 3,0: configuratie opties](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Autogeïncasseerde aanvragen, afhankelijkheden, logboeken en metrische gegevens

### <a name="requests"></a>Aanvragen

* JMS consumenten
* Kafka consumenten
* Netty/webstroom
* Servlets
* Lente planning

### <a name="dependencies-with-distributed-trace-propagation"></a>Afhankelijkheden met gedistribueerde traceer doorgifte

* Apache httpclient maakt en HttpAsyncClient
* gRPC
* Java. net. HttpURLConnection
* JMS
* Kafka
* Netty-client
* OkHttp

### <a name="other-dependencies"></a>Andere afhankelijkheden

* Cassandra
* JDBC
* MongoDB (async en sync)
* Redis (sla en jedis)

### <a name="logs"></a>Logboeken

* Java. util. Logging
* Log4j
* SLF4J/logback

### <a name="metrics"></a>Metrische gegevens

* Micrometer (met inbegrip van gegevens over de veer boot-klep)
* Metrische gegevens van JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Aangepaste telemetrie van uw toepassing verzenden

Met ons doel in 3.0 + kunt u uw aangepaste telemetrie verzenden met behulp van standaard-Api's.

We ondersteunen micrometer, OpenTelemetry-API en de populaire logboek registratie raamwerken. Application Insights Java 3,0 wordt de telemetrie automatisch vastgelegd en correleert dit samen met alle automatisch verzamelde telemetrie.

Daarom is het niet mogelijk om op dit moment een SDK uit te geven met Application Insights 3,0.

Application Insights Java 3,0 wordt al geluisterd naar telemetrie die wordt verzonden naar de Application Insights Java SDK 2. x. Deze functionaliteit is een belang rijk onderdeel van het upgrade verhaal voor bestaande 2. x-gebruikers, en het vult een belang rijke tussen ruimte in onze aangepaste telemetrie-ondersteuning totdat de OpenTelemetry-API GA is.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Aangepaste telemetrie verzenden met Application Insights Java SDK 2. x

Toevoegen `applicationinsights-core-2.6.0.jar` aan uw toepassing (alle 2. x-versies worden ondersteund door Application Insights Java 3,0, maar het is een goed idee om de nieuwste te gebruiken als u een keuze hebt):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Een TelemetryClient maken:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

en gebruiken voor het verzenden van aangepaste telemetrie.

### <a name="events"></a>Gebeurtenissen

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metrische gegevens

U kunt metrische telemetriegegevens verzenden via [micrometer](https://micrometer.io):

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Afhankelijkheden

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Logboeken
U kunt aangepaste logboek-telemetrie verzenden via uw favoriete Framework voor logboek registratie.

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Uitzonderingen
U kunt aangepaste telemetrie voor uitzonde ringen verzenden via uw favoriete Framework voor logboek registratie.

U kunt ook Application Insights Java SDK 2. x gebruiken:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgraden van Application Insights Java SDK 2. x

Als u Application Insights Java SDK 2. x al gebruikt in uw toepassing, hoeft u deze niet te verwijderen. De Java 3,0-agent detecteert deze en legt alle aangepaste telemetrie die u verzendt via de Java SDK 2. x vast en houdt deze bij, terwijl automatisch verzamelen van de Java SDK 2. x wordt onderdrukt om te voor komen dat er dubbele opnamen worden gemaakt.

> [!NOTE]
> Opmerking: Java SDK 2. x TelemetryInitializers en TelemetryProcessors worden niet uitgevoerd wanneer de 3,0-agent wordt gebruikt.
