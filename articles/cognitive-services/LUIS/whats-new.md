---
title: Wat is er nieuw-Language Understanding (LUIS)
description: Dit artikel wordt regel matig bijgewerkt met nieuws over de Azure Cognitive Services Language Understanding-API.
ms.topic: overview
ms.date: 05/19/2020
ms.openlocfilehash: b4d287c8d1ddbeeee63390fb397718de1234c93e
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83675953"
---
# <a name="whats-new-in-language-understanding"></a>Wat is er nieuw in Language Understanding

Meer informatie over wat er nieuw is in de service. Deze items zijn release opmerkingen, Video's, blog berichten en andere soorten informatie. Bladwijzer deze pagina om up-to-date te blijven met de service.

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="may-2020---build"></a>Mei 2020-build

* Uitgebracht als **algemeen beschikbaar** (ga):
    * [Language Understanding-container](luis-container-howto.md)
    * Voor beeld van portal gepromoveerd tot [huidige Portal](https://www.luis.ai), [vorige](https://previous.luis.ai) Portal is nog steeds beschikbaar
    * Nieuwe machine learning-entiteit maken en labelen
    * [Upgrade proces](migrate-from-composite-entity.md) van samengestelde en eenvoudige entiteiten naar machine learning-entiteiten
    * Ondersteuning voor het normaliseren van woord varianten [instellen](how-to-application-settings-portal.md)
* Voor beeld van ontwerp-API-wijzigingen
    * App-schema 7. x voor geneste machine learning-entiteiten
    * [Migratie naar vereiste functie](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nieuwe bronnen voor ontwikkel aars
    * [Hulpprogram ma's voor continue integratie](developer-reference-resource.md#continuous-integration-tools)
    * Workshop: Leer de best practices voor [ _natuurlijke language Understanding_ (NLU) met BEhulp van Luis](developer-reference-resource.md#workshops)
* Door de [klant beheerde sleutels](luis-encryption-of-data-at-rest.md) : alle gegevens die u in Luis gebruikt, versleutelen met behulp van uw eigen sleutel
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video): Bekijk de nieuwe functies in Luis



### <a name="march-2020"></a>Maart 2020

* TLS 1,2 wordt nu afgedwongen voor alle HTTP-aanvragen voor deze service. Zie [Azure Cognitive Services Security](../cognitive-services-security.md)(Engelstalig) voor meer informatie.

### <a name="november-4-2019---ignite"></a>4 november 2019-Ignite

* Video- [geavanceerde natuurlijke language Understanding-modellen (NLU) met Luis en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Verbeterde productiviteit van ontwikkel aars
    * Algemene Beschik baarheid van ons [Voorspellings eindpunt v3](luis-migration-api-v3.md).
    * De mogelijkheid om apps te importeren en exporteren met de `.lu` indeling ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)). Dit wordt de manier om een effectief CI/CD-proces uit te voeren.
* Taal uitbreiding
    * [Arabisch en Hindi](luis-language-support.md) in open bare preview.
* Vooraf gemaakte modellen
    * [Vooraf gebouwde domeinen](luis-reference-prebuilt-domains.md) zijn nu algemeen beschikbaar (ga)
    * Japanse [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md#japanese-entity-support) : leeftijd, valuta, getal en percentage worden niet ondersteund in v3.
    * Italiaanse [vooraf gemaakte entiteiten](luis-reference-prebuilt-entities.md#italian-entity-support) -leeftijd, valuta, dimensie, getal en percentage resolutie worden gewijzigd van v2.
* Verbeterde gebruikers ervaring in de [Preview.Luis.ai-Portal](https://preview.luis.ai) , vernieuwd, waarmee u complexe modellen kunt bouwen en fouten kunt opsporen. Probeer de preview-portal zelf studies:
    * [Alleen intentie](tutorial-intents-only.md)
    * [Ontwaak bare machine-leer entiteit](tutorial-machine-learned-entity.md)
* Geavanceerde taal mogelijkheden: [Geavanceerde taal modellen bouwen](luis-concept-entity-types.md) met minder inspanning.
* Definieer machine learning functies op model niveau en zorg ervoor dat modellen kunnen worden gebruikt als signalen voor andere modellen, bijvoorbeeld door entiteiten te gebruiken als functies voor intenties en andere entiteiten.
* Nieuwe, uitgebreide [limieten](luis-limits.md) : hoger maximum voor woordgroepen lijsten en totaal aantal zinsdelen, nieuw model als functie limieten
* Haal informatie op uit tekst in de indeling van een diepe hiërarchie structuur, zodat conversatie toepassingen krachtiger worden.

    ![afbeelding van de entiteit voor machine learning](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure authoring resource- [nu migreren](luis-migration-authoring.md).
    * 500-apps per Azure-resource
    * 100-versies per app
* Turkse ondersteuning voor vooraf gemaakte entiteiten
* Italiaanse ondersteuning voor datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* De [recognizers bijwerken-tekst](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) naar 1.2.3
    * De kenmerken voor leeftijd, Tempe ratuur, dimensie en valuta in het Italiaans.
    * Verbetering van de erkenning van feest dagen in het Engels om op juiste wijze op Thanksgiving gebaseerde datums te berekenen.
    * Verbeteringen in Franse datum tijd om de fout-positieven van niet-datum-en niet-tijd-entiteiten te verminderen.
    * Ondersteuning voor calendar/school/boek jaar en acroniemen in de Engelse DateRange.
    * Verbeterde PhoneNumber-herkenning in Chinees en Japans.
    * Verbeterde ondersteuning voor NumberRange in het Engels.
    * Prestatie verbeteringen.

### <a name="june-24-2019"></a>24 juni 2019

* [OrdinalV2 preconstrueerde entiteit](luis-reference-prebuilt-ordinal-v2.md) ter ondersteuning van de volg orde zoals volgende, vorige en laatste. Alleen Engelse cultuur.

### <a name="may-6-2019---build-conference"></a>6 mei 2019-build-conferentie

De volgende functies zijn uitgebracht op de build 2019-conferentie:

* [Preview van de V3 API-migratie handleiding](luis-migration-api-v3.md)
* [Verbeterd analyse dashboard](luis-how-to-use-dashboard.md)
* [Verbeterde, vooraf ontwikkelde domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijst entiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video's

### <a name="2019-ignite-videos"></a>2019 Ignite-Video's

[Geavanceerde natuurlijke Language Understanding-modellen (NLU) met LUIS en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Video's bouwen

[Azure-conversatie AI gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Service-updates

[Meldingen voor Azure update voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
