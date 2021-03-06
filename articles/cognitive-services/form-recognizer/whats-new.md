---
title: Wat is er nieuw in Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Meer informatie over de meest recente wijzigingen in de API voor formulier herkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: e3bf279142383ccdb3e82ffee49bd593640bb937
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996819"
---
# <a name="whats-new-in-form-recognizer"></a>Wat is er nieuw in Form Recognizer?

De Form Recognizer-service wordt doorlopend bijgewerkt. Gebruik dit artikel om op de hoogte te blijven van de functie verbeteringen, oplossingen en documentatie-updates.

> [!NOTE]
> De Quick starts en hand leidingen voor de formulier Recognizer gebruiken altijd de nieuwste versie van de API, tenzij opgegeven.

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nieuwe functies
* **SDK-ondersteuning voor de open bare preview van de Form Recognizer API v 2.0** Deze maand breidden onze service ondersteuning uit om een preview-SDK voor de versie van de formulier Recognizer v 2.0 (preview) op te nemen. Gebruik de onderstaande koppelingen om aan de slag te gaan met de taal van uw keuze: 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer?view=azure-dotnet-preview)
   * [Java-SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer?view=azure-java-preview)
   * [Python-SDK](https://docs.microsoft.com/python/api/overview/azure/formrecognizer?view=azure-python-previewr)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer?view=azure-node-preview)

  De nieuwe SDK ondersteunt alle functies van de v 2.0 REST API voor de formulier herkenner. U kunt bijvoorbeeld een model trainen met of zonder labels en extra tekst, sleutel waardeparen en tabellen uit uw formulieren extra heren, gegevens uit de bevestigingen ophalen met de vooraf gemaakte ontvangst bevestigingen en tekst en tabellen met de lay-outservice uit uw documenten ophalen. U kunt uw feedback op de Sdk's delen via het [SDK-feedback formulier](https://aka.ms/FR_SDK_v1_feedback).
 
* **Aangepast model kopiëren** U kunt nu modellen kopiëren tussen regio's en abonnementen met behulp van de nieuwe functie voor het kopiëren van aangepaste modellen. Voordat u de API voor het kopiëren van aangepaste modellen aanroept, moet u eerst autorisatie aanvragen voor het kopiëren naar de doel bron door de bewerking Copy Authorization aan te roepen voor het doel bron eindpunt.
   * [Een Kopieer autorisatie genereren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModelAuthorization) REST API
   * [Een aangepast model kopiëren](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Verbeterde beveiliging

* Door de klant beheerde sleutels zijn nu beschikbaar voor FormRecognizer. Zie voor meer informatie [gegevens versleuteling in rust voor de formulier herkenner](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Gebruik beheerde identiteiten voor toegang tot Azure-resources met Azure Active Directory. Zie [toegang verlenen voor beheerde identiteiten](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities)voor meer informatie.

## <a name="march-2020"></a>Maart 2020 

### <a name="new-features"></a>Nieuwe functies

* **Waardetypen voor labelen** U kunt nu de typen waarden opgeven die u wilt labelen met het hulp programma voor het labelen van het voor beeld van de formulier herkenning. De volgende waardetypen en variaties worden momenteel ondersteund:
  * `string`
    * standaard, `no-whitespaces` ,`alphanumeric`
  * `number`
    * prijs`currency`
  * `date` 
    * standaard, `dmy` , `mdy` ,`ymd`
  * `time`
  * `integer`

  Raadpleeg de hand leiding voor het [labelen van labels](./quickstarts/label-tool.md#specify-tag-value-types) voor meer informatie over het gebruik van deze functie.


* **Tabel visualisatie** Het hulp programma labelen wordt nu weer gegeven met tabellen die in het document zijn herkend. Hiermee kunt u de tabellen weer geven die zijn herkend en geëxtraheerd uit het document, vóór het labelen en analyseren. U kunt deze functie in-of uitschakelen met de optie lagen.

  Dit is een voor beeld van hoe tabellen worden herkend en geëxtraheerd:

  > [!div class="mx-imgBorder"]
  > ![Tabel visualisatie met behulp van het voor beeld-programma labelen](./media/whats-new/formre-table-viz.png)

    De uitgepakte tabellen zijn beschikbaar in de JSON-uitvoer onder `"pageResults"` .

  > [!IMPORTANT]
  > Labels van tabellen worden niet ondersteund. Als tabellen niet automatisch worden herkend en extrated, kunt u ze alleen labelen als sleutel/waarde-paren. Bij het labelen van tabellen als sleutel/waarde-paren, labelt u elke cel als een unieke waarde.

### <a name="extraction-enhancements"></a>Uitbrei dingen voor extractie

Deze release bevat verbeteringen voor extractie en nauw keurigheid, met name de mogelijkheid om meerdere sleutel-waardeparen in dezelfde tekst regel te labelen en uit te pakken. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Voor beeld van labelen hulp programma is nu open-source

Het hulp programma voor het labelen van het voorbeeld formulier Recognizer is nu beschikbaar als een open-source project. U kunt dit integreren in uw oplossingen en klantspecifieke wijzigingen aanbrengen om aan uw behoeften te voldoen.

Raadpleeg de documentatie die beschikbaar is op [github](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)voor meer informatie over het hulp programma voor het labelen van het voor beeld van een formulier herkenning.

### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen

TLS 1,2 wordt nu afgedwongen voor alle HTTP-aanvragen voor deze service. Zie [Azure Cognitive Services Security](../cognitive-services-security.md)(Engelstalig) voor meer informatie.

## <a name="january-2020"></a>Januari 2020

Deze release introduceert de formulier Recognizer 2,0 (preview). In de volgende secties vindt u meer informatie over nieuwe functies, verbeteringen en wijzigingen. 

### <a name="new-features"></a>Nieuwe functies

* **Aangepast model**
  * **Trainen met labels** U kunt nu een aangepast model trainen met hand matig gelabelde gegevens. Dit resulteert in betere uitvoering van modellen en kan modellen produceren die met complexe formulieren of formulieren met waarden zonder sleutels werken.
  * **ASYNCHRONE API** U kunt asynchrone API-aanroepen gebruiken om met grote gegevens sets en bestanden te trainen en te analyseren.
  * **Ondersteuning voor TIFF-bestanden** U kunt nu gegevens uit TIFF-documenten trainen en ophalen.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Gebaseerd ontvangst model**
  * **Fooien** U kunt nu fooie bedragen en andere handgeschreven waarden extra heren.
  * **Extractie van regel items** U kunt waarden van het regel item extra heren uit de bevestigingen.
  * **Betrouwbaarheids waarden** U kunt het vertrouwen van het model voor elke geëxtraheerde waarde weer geven.
  * **Verbeteringen in de extractie nauwkeurigheid**

* **Indelings extractie** U kunt nu de indelings-API gebruiken om tekst gegevens en tabel gegevens op te halen uit uw formulieren.

### <a name="custom-model-api-changes"></a>Wijzigingen in het aangepaste model-API

Alle Api's voor training en het gebruik van aangepaste modellen zijn hernoemd en sommige synchrone methoden zijn nu asynchroon. De volgende belang rijke wijzigingen zijn:

* Het proces voor het trainen van een model is nu asynchroon. U initieert training via de API-aanroep van **/Custom/models** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}** om de resultaten van de training te retour neren.
* De extractie van sleutel/waarde wordt nu geïnitieerd door de API-aanroep van **/Custom/models/{modelID}/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **aangepaste/modellen/{modelID}/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.
* Bewerkings-Id's voor de trein bewerking zijn nu gevonden in de **locatie** header van http-antwoorden, niet op de locatie van de **bewerking** .

### <a name="receipt-api-changes"></a>Wijzigingen in de ontvangst-API

De naam van de Api's voor het lezen van de verkoop ontvangst is gewijzigd.

* Het uitpakken van ontvangst gegevens wordt nu geïnitieerd door de API-aanroep van **/prebuilt/Receipt/analyze** . Deze aanroep retourneert een bewerkings-ID, die u kunt door geven aan **/prebuilt/Receipt/analyzeResults/{resultID}** om de resultaten van de extractie te retour neren.

### <a name="output-format-changes"></a>Wijzigingen in de uitvoer indeling

De JSON-antwoorden voor alle API-aanroepen hebben nieuwe notaties. Sommige sleutels en waarden zijn toegevoegd, verwijderd of de naam ervan is gewijzigd. Bekijk de Quick starts voor voor beelden van de huidige JSON-indelingen.

## <a name="next-steps"></a>Volgende stappen

Voer een [Snelstartgids](quickstarts/curl-train-extract.md) uit om aan de slag te gaan met de [API voor formulier herkenning](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).