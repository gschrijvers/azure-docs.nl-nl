---
title: Werk stromen gebruiken om uw Azure IoT Central-toepassing te integreren met andere Cloud Services | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u als een opbouw functie regels en acties configureert waarmee uw IoT Central-toepassing wordt geïntegreerd met andere Cloud Services. Als u een geavanceerde regel wilt maken, gebruikt u een IoT Central-connector in energie automatisering of Azure Logic Apps.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e2018f4d6f8e0813892a43c66975961356333bff
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665003"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Werk stromen gebruiken om uw Azure IoT Central-toepassing te integreren met andere Cloud Services

*Dit artikel is van toepassing op oplossingen bouwers.*

U kunt regels maken in IoT Central die acties activeren, zoals het verzenden van een e-mail bericht, als reactie op op telemetrie gebaseerde voor waarden, zoals de temperatuur van een apparaat overschrijden.

Met de IoT Central-connector voor energie automatisering en Azure Logic Apps kunt u geavanceerde regels maken voor het automatiseren van bewerkingen in IoT Central:

- Wanneer een regel wordt geactiveerd in uw Azure IoT Central-app, kan deze een werk stroom activeren in de automatische stroom of Azure Logic Apps. Deze werk stromen kunnen acties uitvoeren in andere Cloud Services, zoals Office 365 of een service van derden.
- Een gebeurtenis in een andere Cloud service, zoals Office 365, kan een werk stroom activeren in automatische stroom of Azure Logic Apps. Deze werk stromen kunnen acties uitvoeren of gegevens ophalen uit uw IoT Central-toepassing.

## <a name="trigger-a-workflow-from-a-rule"></a>Een werk stroom activeren vanuit een regel

Voordat u een werk stroom in Power Automatiseer of Azure Logic Apps kunt activeren, hebt u een regel in uw IoT Central toepassing nodig. Zie [regels en acties in Azure IOT Central configureren](./howto-configure-rules.md)voor meer informatie.

De **Azure IOT Central-preview-** connector toevoegen als een trigger in automatische stroom:

1. Selecteer **+ maken**in automatische stroom, selecteer het tabblad **aangepast** .
1. Zoek naar *IOT Central*en selecteer de connector **voor Azure IOT Central-preview** .
1. Selecteer in de lijst met triggers **Wanneer een regel wordt geactiveerd (preview)**.
1. Selecteer in de stap **Wanneer een regel wordt gestart** de IOT Central toepassing en de regel die u gebruikt.

De **Azure IOT Central-preview-** connector toevoegen als een trigger in azure Logic apps:

1. Selecteer in **Logic apps Designer**de sjabloon voor de **lege logische app** .
1. Selecteer in de ontwerp functie het tabblad **aangepast** .
1. Zoek naar *IOT Central*en selecteer de connector **voor Azure IOT Central-preview** .
1. Selecteer in de lijst met triggers **Wanneer een regel wordt geactiveerd (preview)**.
1. Selecteer in de stap **Wanneer een regel wordt gestart** de IOT Central toepassing en de regel die u gebruikt.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Zoek de Azure IoT Central-preview-connector en kies de trigger":::

U kunt nu meer stappen toevoegen aan uw werk stroom om uw integratie scenario samen te stellen.

## <a name="run-an-action"></a>Een actie uitvoeren

U kunt acties uitvoeren in een IoT Central-toepassing vanuit Automatische stroom en Azure Logic Apps werk stromen. Maak eerst uw werk stroom en gebruik een connector om een trigger te definiëren om de werk stroom te starten. Gebruik vervolgens de **Azure IOT Central-preview-** connector als een actie.

Als u de **Azure IOT Central-preview-** connector als actie wilt toevoegen in automatische stroom bewerkingen:

1. In energie automatisering, in het deel venster **Kies een actie** , selecteert u het tabblad **aangepast** .
1. Zoek naar *IOT Central* en selecteer de connector **voor Azure IOT Central-preview** .
1. Selecteer in de lijst met acties de IoT Central actie die u wilt gebruiken.
1. Voer in de stap actie de configuratie uit voor de actie die u hebt gekozen. Selecteer vervolgens **Opslaan**.

De **Azure IOT Central-preview-** connector toevoegen als actie in azure Logic apps:

1. Selecteer in **Logic apps Designer**in het deel venster **een actie kiezen** het tabblad **aangepast** .
1. Zoek naar *IOT Central*en selecteer de connector **voor Azure IOT Central-preview** .
1. Selecteer in de lijst met acties de IoT Central actie die u wilt gebruiken.
1. Voer in de stap actie de configuratie uit voor de actie die u hebt gekozen. Selecteer vervolgens **Opslaan**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Zoek de Azure IoT Central-preview-connector en kies een actie":::

## <a name="list-of-actions"></a>Lijst met acties

De volgende lijst bevat alle beschik bare IoT Central acties in de **Azure IOT Central-preview-** connector en de bijbehorende configuratie opties. Veel van de velden kunnen dynamisch gegenereerde inhoud bevatten. Een voor gaande stap kan bijvoorbeeld bepalen van de apparaat-ID waarop de huidige stap wordt uitgevoerd.

### <a name="create-or-update-a-device"></a>Een apparaat maken of bijwerken

Gebruik deze actie om een apparaat in uw IoT Central-toepassing te maken of bij te werken.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden gemaakt of bijgewerkt. |
| Goedgekeurd | Kies of het apparaat is goedgekeurd om verbinding te maken met IoT Central. |
| Beschrijving apparaat | Een gedetailleerde beschrijving van het apparaat. |
| Apparaatnaam | De weergave naam van het apparaat. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |
| Gesimuleerde | Kies of het apparaat is gesimuleerd. |

### <a name="delete-a-device"></a>Een apparaat verwijderen

Gebruik deze actie om een apparaat te verwijderen uit uw IoT Central-toepassing.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |

### <a name="execute-a-device-command"></a>Een apparaat-opdracht uitvoeren

Gebruik deze actie om een opdracht uit te voeren die is gedefinieerd in een van de interfaces van het apparaat.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Onderdeel apparaat | De interface in de sjabloon van het apparaat dat de opdracht bevat. |
| Apparaatopdracht | Kies een van de opdrachten op de geselecteerde interface. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |
| Payload van opdracht apparaat aanvragen | Als voor de opdracht een nettolading voor aanvragen is vereist, voegt u deze hier toe.  |

> [!NOTE]
> U kunt pas een apparaat onderdeel kiezen als u een sjabloon voor het apparaat hebt gekozen.

### <a name="get-a-device-by-id"></a>Een apparaat ophalen op basis van id

Gebruik deze actie om de details van het apparaat op te halen.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |

U kunt de geretourneerde Details in de dynamische expressies in andere acties gebruiken. De details van het apparaat zijn als volgt: **goedgekeurd**, **hoofd tekst** **, apparaatbeschrijving,** apparaatnaam, **apparaatprofiel** **,** **ingericht**en **gesimuleerd**.

### <a name="get-device-cloud-properties"></a>Eigenschappen van de apparaat-Cloud ophalen

Gebruik deze actie om de waarden van de Cloud eigenschappen voor een specifiek apparaat op te halen.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |

U kunt de geretourneerde waarden van de Cloud eigenschap in de dynamische expressies in andere acties gebruiken.

### <a name="get-device-properties"></a>Apparaateigenschappen ophalen

Gebruik deze actie om de eigenschaps waarden voor een specifiek apparaat op te halen.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |

U kunt de geretourneerde eigenschaps waarden in de dynamische expressies in andere acties gebruiken.

### <a name="get-device-telemetry-value"></a>De telemetrische waarde van het apparaat ophalen

Gebruik deze actie om de telemetrie-waarden voor een specifiek apparaat op te halen.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |

U kunt de geretourneerde telemetrie-waarden in de dynamische expressies in andere acties gebruiken.

### <a name="update-device-cloud-properties"></a>Eigenschappen van de cloud van het apparaat bijwerken

Gebruik deze actie om waarden van de Cloud eigenschappen voor een specifiek apparaat bij te werken.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |
| Eigenschappen van Cloud | Nadat u een sjabloon voor het apparaat hebt gekozen, wordt er een veld toegevoegd voor elke Cloud eigenschap die in de sjabloon is gedefinieerd. |

### <a name="update-device-properties"></a>Apparaateigenschappen bijwerken

Gebruik deze actie om Beschrijf bare eigenschaps waarden voor een specifiek apparaat bij te werken.

| Veld | Beschrijving |
| ----- | ----------- |
| Toepassing | Kies uit de lijst met IoT Central toepassingen. |
| Apparaat | De unieke ID van het apparaat dat moet worden verwijderd. |
| Apparaatprofiel | Kies uit de lijst met apparaatprofielen in uw IoT Central-toepassing. |
| Beschrijf bare eigenschappen | Nadat u een sjabloon hebt gekozen, wordt er een veld toegevoegd voor elke Beschrijf bare eigenschap die in de sjabloon is gedefinieerd. |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een geavanceerde regel maakt in uw Azure IoT Central-toepassing, kunt u leren hoe u [apparaatgegevens in uw azure IOT Central-toepassing analyseert](howto-create-analytics.md)
