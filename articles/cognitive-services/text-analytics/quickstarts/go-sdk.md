---
title: 'Snelstartgids: Text Analytics-client bibliotheek voor Go | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start wordt taal gedetecteerd met behulp van de go Text Analytics-client bibliotheek van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: f9b5fb1837fdafe818fdb27221f8575d93ec304d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701623"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Snelstartgids: de Text Analytics-client bibliotheek voor Go gebruiken

[Referentie documentatie](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics)  |  [Bron code](https://github.com/Azure/azure-sdk-for-go/blob/090dc0ee4d8d2d60e2a9525774d967a4111a2b0c/services/cognitiveservices/v2.1/textanalytics/client.go)  |  van bibliotheek [Pakket (github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)  |  Voor [beelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Deze Quick start is alleen van toepassing op Text Analytics versie 2,1. Op dit moment is een v3-client bibliotheek voor Go niet beschikbaar.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* De nieuwste versie van [Go](https://golang.org/dl/)
* Wanneer u uw Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title=" maakt u een Text Analytics resource Maak "  target="_blank"> een Text Analytics resource <span class="docon docon-navigate-external x-hidden-focus"></span> </a> in de Azure Portal om uw sleutel en eind punt op te halen. 
    * U hebt de sleutel en het eind punt nodig van de resource die u maakt om de toepassing te verbinden met de Text Analytics-API. U gaat later in de Quick Start.
    * U kunt de gratis prijs categorie gebruiken om de service te proberen en later te upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-go-project"></a>Een nieuw go-project maken

Maak in een console venster (cmd, Power shell, Terminal, bash) een nieuwe werk ruimte voor uw Go-project en navigeer ernaar. Uw werk ruimte bevat drie mappen: 

* **src** -deze map bevat bron code en pakketten. Alle pakketten die met de `go get` opdracht zijn geïnstalleerd, worden hier opgeslagen.
* **pakket** -deze map bevat de gecompileerde go package-objecten. Deze bestanden hebben allemaal een `.a` extensie.
* **bin** : deze map bevat de binaire uitvoer bare bestanden die worden gemaakt wanneer u uitvoert `go install` .

> [!TIP]
> Meer informatie over de structuur van een [Go-werk ruimte](https://golang.org/doc/code.html#Workspaces). Deze hand leiding bevat informatie over het instellen van `$GOPATH` en `$GOROOT` .

Maak een werk ruimte `my-app` met de naam en de vereiste submappen voor `src` , en `pkg` `bin` :

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>De Text Analytics-client bibliotheek voor Go installeren

De client bibliotheek voor Go installeren: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

of, als u dep gebruikt, binnen de uitvoer van de opslagplaats:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Uw Go-toepassing maken

Maak vervolgens een bestand met de naam `src/quickstart.go` :

```bash
$ cd src
$ touch quickstart.go
```

Open `quickstart.go` in uw favoriete IDE-of tekst editor. Voeg vervolgens de naam van het pakket toe en importeer de volgende bibliotheken:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Object model 

De Text Analytics-client is een [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De-client biedt verschillende methoden voor het analyseren van tekst, als één teken reeks of een batch. 

Tekst wordt naar de API verzonden als een lijst met `documents` `dictionary` objecten die een combi natie van `id` , en kenmerken bevatten, afhankelijk van `text` `language` de gebruikte methode. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd in de oorsprong `language` , en `id` kan een wille keurige waarde zijn. 

Het antwoord object is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u het volgende kunt doen met de Text Analytics-client bibliotheek voor Go:

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Entiteit herkenning](#entity-recognition)
* [Extractie van sleutel woorden](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren


Maak in een nieuwe functie variabelen voor het Azure-eind punt en de abonnements sleutel van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Maak een nieuw [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) -object. Geef uw sleutel door aan de auto [rest. De functie NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) , die vervolgens wordt door gegeven aan de eigenschap van de client `authorizer` .

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Sentimentanalyse

Maak een nieuwe functie `SentimentAnalysis()` met de naam en maak een client met behulp van de `GetTextAnalyticsClient()` methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objecten die de documenten bevatten die u wilt analyseren. Elk object bevat een `id` , `Language` en een- `text` kenmerk. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document en de `id` kan een wille keurige waarde zijn. 

Aanroepen van de [sentiment ()-](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) functie van de client en het resultaat ophalen. Herhaal vervolgens de resultaten en druk de document-ID en sentiment Score af. Een score dichter bij 0 geeft aan dat er een negatieve sentiment is, terwijl een score dichter bij 1 wijst op een positieve sentiment.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

Bel `SentimentAnalysis()` in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie `LanguageDetection()` met de naam en maak een client met behulp van de `GetTextAnalyticsClient()` methode die u eerder hebt gemaakt. Maak een lijst met [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) -objecten die de documenten bevatten die u wilt analyseren. Elk object bevat een `id` en een- `text` kenmerk. Het `text` kenmerk slaat de tekst die moet worden geanalyseerd en de `id` kan een wille keurige waarde zijn. 

Aanroepen van [DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) van de client en het resultaat ophalen. Herhaal vervolgens de resultaten en druk de document-ID en de gedetecteerde taal af.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Bel `LanguageDetection()` in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Herkenning van entiteiten

Maak een nieuwe functie `ExtractEntities()` met de naam en maak een client met behulp van de `GetTextAnalyticsClient()` methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objecten die de documenten bevatten die u wilt analyseren. Elk object bevat een `id` -, `language` -en- `text` kenmerk. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document en de `id` kan een wille keurige waarde zijn. 

Roep de entiteiten van de client aan [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) en ontvang het resultaat. Herhaal vervolgens de resultaten en druk de Score van elk document en geëxtraheerde entiteiten af.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

Bel `ExtractEntities()` in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe functie `ExtractKeyPhrases()` met de naam en maak een client met behulp van de `GetTextAnalyticsClient()` methode die u eerder hebt gemaakt. Maak een lijst met [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objecten die de documenten bevatten die u wilt analyseren. Elk object bevat een `id` -, `language` -en- `text` kenmerk. Het `text` kenmerk slaat de tekst op die moet worden geanalyseerd, `language` is de taal van het document en de `id` kan een wille keurige waarde zijn.

Roep de woordgroepen van de client [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) aan en ontvang het resultaat. Herhaal vervolgens de resultaten en druk de document-ID en de geëxtraheerde sleutel zinnen af.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Bel `ExtractKeyPhrases()` in uw project.

### <a name="output"></a>Uitvoer

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
