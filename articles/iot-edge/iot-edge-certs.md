---
title: Certificaten voor de beveiliging van apparaten-Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge maakt gebruik van een certificaat om apparaten, modules en Leaf-apparaten te valideren en beveiligde verbindingen ertussen tot stand te brengen.
author: stevebus
manager: philmea
ms.author: stevebus
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: mqtt
ms.openlocfilehash: 1b299cf21652c23451aed735b10597adb85dc3db
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982725"
---
# <a name="understand-how-azure-iot-edge-uses-certificates"></a>Meer informatie over het gebruik van Azure IoT Edge certificaten

IoT Edge certificaten worden gebruikt door de modules en downstream IoT-apparaten om de identiteit en rechtmatigheid van de module [IOT Edge hub](iot-edge-runtime.md#iot-edge-hub) runtime te controleren. Deze verificaties zorgen ervoor dat een TLS-verbinding (trans port Layer Security) wordt beveiligd tussen de runtime, de modules en IoT-apparaten. Net als IoT Hub, vereist IoT Edge een beveiligde en versleutelde verbinding van IoT downstream-(of Leaf) apparaten en IoT Edge modules. Om een beveiligde TLS-verbinding tot stand te brengen, presenteert de IoT Edge hub-module een server certificaat keten om clients te verbinden, zodat ze hun identiteit kunnen verifiëren.

In dit artikel wordt uitgelegd hoe IoT Edge certificaten kunnen werken in productie-, ontwikkelings-en test scenario's. Terwijl de scripts verschillend zijn (Power shell versus bash), zijn de concepten hetzelfde tussen Linux en Windows.

## <a name="iot-edge-certificates"></a>IoT Edge-certificaten

Meestal zijn fabrikanten niet de eind gebruikers van een IoT Edge apparaat. Soms is de enige relatie tussen de twee, wanneer de eind gebruiker of de operator, een algemeen apparaat koopt dat door de fabrikant is gemaakt. Andere keren werkt de fabrikant onder contract om een aangepast apparaat voor de operator te bouwen. Het IoT Edge certificaat ontwerp probeert beide scenario's in rekening te brengen.

In de volgende afbeelding ziet u het gebruik van IoT Edge van certificaten. Er kunnen zich nul, één of veel tussenliggende handtekening certificaten bevinden tussen het basis-CA-certificaat en het CA-certificaat van het apparaat, afhankelijk van het aantal betrokken entiteiten. Hier zien we één geval.

![Diagram van typische certificaat relaties](./media/iot-edge-certs/edgeCerts-general.png)

### <a name="certificate-authority"></a>Certificeringsinstantie

De certificerings instantie of ' CA ' voor korte, is een entiteit die digitale certificaten uitgeeft. Een certificerings instantie fungeert als een vertrouwde derde partij tussen de eigenaar en de ontvanger van het certificaat. Een digitaal certificaat verklaart het eigendom van een open bare sleutel door de ontvanger van het certificaat. De certificaat vertrouwens keten werkt in eerste instantie een basis certificaat, dat de basis vormt voor vertrouwen in alle certificaten die door de autoriteit worden verleend. Daarna kan de eigenaar het basis certificaat gebruiken om aanvullende tussenliggende certificaten (' Leaf-certificaten ') uit te geven.

### <a name="root-ca-certificate"></a>Basis-CA-certificaat

Een basis-CA-certificaat is de basis van de vertrouwens relatie van het hele proces. In productie scenario's wordt dit CA-certificaat meestal gekocht van een vertrouwde commerciële certificerings instantie, zoals Baltimore, VeriSign of DigiCert. Als u volledige controle hebt over de apparaten die verbinding maken met uw IoT Edge apparaten, is het mogelijk om een certificerings instantie op bedrijfs niveau te gebruiken. In beide gevallen moet de volledige certificaat keten van de IoT Edge-hub ernaar worden geroot, zodat de Blade IoT-apparaten het basis certificaat moeten vertrouwen. U kunt het basis-CA-certificaat opslaan in het archief van de vertrouwde basis certificerings instantie of de certificaat gegevens opgeven in de code van uw toepassing.

### <a name="intermediate-certificates"></a>Tussenliggende certificaten

Bij een typisch productie proces voor het maken van beveiligde apparaten worden basis-CA-certificaten zelden rechtstreeks gebruikt, voornamelijk vanwege het risico op lekkage of bloot stelling. Met het basis-CA-certificaat worden een of meer tussenliggende CA-certificaten gemaakt en ondertekend. Er kan slechts één of een keten van deze tussenliggende certificaten zijn. Scenario's die een keten van tussenliggende certificaten vereisen, zijn onder andere:

* Een hiërarchie van afdelingen binnen een fabrikant.

* Meerdere bedrijven betrokken op serie nummer in de productie van een apparaat.

* Een klant die een basis certificerings instantie koopt en een handtekening certificaat voor de fabrikant afleidt om de apparaten te ondertekenen die zij namens de klant nemen.

In elk geval gebruikt de fabrikant een tussenliggend CA-certificaat aan het einde van deze keten om het CA-certificaat van het apparaat op het eind apparaat te ondertekenen. Over het algemeen worden deze tussenliggende certificaten nauw beschermd bij de productie-fabriek. Ze ondergaan strenge processen, zowel fysiek als elektronisch voor hun gebruik.

### <a name="device-ca-certificate"></a>CA-certificaat van apparaat

Het CA-certificaat van het apparaat wordt gegenereerd op basis van en ondertekend door het laatste tussenliggende CA-certificaat in het proces. Dit certificaat is geïnstalleerd op het IoT Edge apparaat zelf, bij voor keur in beveiligde opslag, zoals een Hardware Security module (HSM). Daarnaast duidt een CA-certificaat van een apparaat een unieke identificatie van een IoT Edge apparaat aan. Het CA-certificaat van het apparaat kan andere certificaten ondertekenen.

### <a name="iot-edge-workload-ca"></a>CA van IoT Edge-workload

De [IOT Edge Security Manager](iot-edge-security-manager.md) GENEREERT het CA-certificaat van de werk belasting, de eerste op de ' operator '-kant van het proces, wanneer IOT Edge voor het eerst wordt gestart. Dit certificaat wordt gegenereerd op basis van en ondertekend door het CA-certificaat van het apparaat. Dit certificaat, dat slechts een ander tussenliggend handtekening certificaat is, wordt gebruikt voor het genereren en ondertekenen van andere certificaten die worden gebruikt door de IoT Edge runtime. En dat is het hoofd zakelijk het IoT Edge hub-server certificaat dat in de volgende sectie wordt besproken, maar in de toekomst kunnen ook andere certificaten voor het verifiëren van IoT Edge-onderdelen bevatten.

### <a name="iot-edge-hub-server-certificate"></a>IoT Edge hub-server certificaat

Het IoT Edge hub-server certificaat is het daad werkelijke certificaat dat wordt aangeboden aan Leaf-apparaten en-modules voor identiteits verificatie tijdens het instellen van de TLS-verbinding die is vereist voor IoT Edge. Dit certificaat bevat de volledige keten van handtekening certificaten die worden gebruikt voor het genereren van het basis certificaat van de certificerings instantie, die het blad IoT-apparaat moet vertrouwen. Wanneer het is gegenereerd door IoT Edge Security Manager, wordt de algemene naam (CN) van dit IoT Edge hub-certificaat ingesteld op de eigenschap hostname in het bestand config. yaml na conversie naar kleine letters. Deze configuratie is een veelvoorkomende bron van Verwar ring met IoT Edge.

## <a name="production-implications"></a>Gevolgen voor de productie

Een redelijke vraag kan zijn: Waarom heeft IoT Edge het extra certificaat voor de workload-CA nodig? Kan het CA-certificaat van het apparaat niet gebruiken om het IoT Edge hub-server certificaat direct te genereren? '. Technisch gezien is dit mogelijk. Het doel van dit tussenliggende certificaat van de werk belasting bestaat echter uit het scheiden van de problemen tussen de fabrikant van het apparaat en de exploitant van het apparaat. Stel dat een IoT Edge apparaat wordt verkocht of overgedragen van de ene klant naar een andere. Waarschijnlijk wilt u dat het CA-certificaat van de fabrikant onveranderbaar is. De "workload"-certificaten die specifiek zijn voor de werking van het apparaat moeten echter worden gewist en opnieuw worden gemaakt voor de nieuwe implementatie.

Omdat productie-en bewerkings processen worden gescheiden, moet u rekening houden met de volgende implicaties bij het voorbereiden van productie-apparaten:

* Bij elk op certificaten gebaseerd proces moeten het basis-CA-certificaat en alle tussenliggende CA-certificaten worden beveiligd en gecontroleerd tijdens het hele proces van het uitrollen van een IoT Edge apparaat. De fabrikant van het IoT Edge apparaat moet sterke processen hebben voor de juiste opslag en het gebruik van hun tussenliggende certificaten. Daarnaast moet het CA-certificaat van het apparaat als mogelijk worden bewaard op het apparaat zelf, bij voor keur een Hardware Security-module.

* Het IoT Edge hub-server certificaat wordt door IoT Edge hub gepresenteerd aan de client apparaten en-modules die verbinding maken. De algemene naam (CN) van het CA-certificaat van het apparaat **mag niet hetzelfde zijn** als de hostnaam die wordt gebruikt in config. yaml op het IOT edge-apparaat. De naam die door clients wordt gebruikt om verbinding te maken met IoT Edge (bijvoorbeeld via de para meter GatewayHostName van de connection string of de opdracht CONNECT in MQTT), **mag niet hetzelfde zijn** als de algemene naam die wordt gebruikt in het certificaat van de certificerings instantie. Deze beperking is omdat de IoT Edge hub de volledige certificaat keten geeft voor verificatie door clients. Als het IoT Edge hub-server certificaat en het CA-certificaat van het apparaat dezelfde CN hebben, krijgt u een verificatie-lus en wordt het certificaat ongeldig.

* Omdat het certificaat van de certificerings instantie van het apparaat wordt gebruikt door de IoT Edge Security daemon voor het genereren van de definitieve IoT Edge certificaten, moet het een handtekening certificaat zijn, wat betekent dat het een certificaat heeft voor de ondertekening van certificaten. Als ' v3 Basic-beperkingen CA: True ' wordt toegepast op het apparaat-CA-certificaat, worden de vereiste eigenschappen voor sleutel gebruik automatisch ingesteld.

>[!Tip]
> Als u de installatie van IoT Edge al hebt door lopen als een transparante gateway in een dev/test-scenario met behulp van onze ' gebruiks vriendelijke scripts (zie volgende sectie) en dezelfde hostnaam gebruikt bij het maken van het certificaat van de certificerings instantie voor de host in config. yaml, vraagt u zich mogelijk af waarom dit is gedaan. Om de ervaring van de ontwikkel aars te vereenvoudigen, voegt de gebruiks vriendelijke scripts een '. ca ' toe aan het einde van de naam die u doorgeeft aan het script. Als u bijvoorbeeld ' mygateway ' hebt gebruikt voor zowel de naam van het apparaat in de scripts en de hostnaam in config. yaml, wordt de eerste in mygateway.ca gezet voordat deze wordt gebruikt als de CN voor het certificaat van de CA-apparaat.

## <a name="devtest-implications"></a>Implicaties voor dev/test

Om ontwikkel-en test scenario's te vereenvoudigen, biedt micro soft een aantal [gebruiks vriendelijke scripts](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) voor het genereren van niet-productie certificaten die geschikt zijn voor IOT Edge in het transparante Gateway scenario. Zie [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md)voor voor beelden van de werking van de scripts.

>[!Tip]
> Als u uw apparaat IoT-Leaf-apparaten en-toepassingen wilt verbinden die gebruikmaken van onze IoT Device SDK via IoT Edge, moet u de optionele para meter GatewayHostName toevoegen aan het einde van de connection string van het apparaat. Wanneer het Edge hub-server certificaat wordt gegenereerd, is het gebaseerd op een kleine versie van de hostnaam van config. yaml. Daarom moet u de para meter GatewayHostName in kleine letters invoeren om de namen te vergelijken en de verificatie van het TLS-certificaat te volt ooien.

## <a name="example-of-iot-edge-certificate-hierarchy"></a>Voor beeld van een IoT Edge-certificaat hiërarchie

Ter illustratie van een voor beeld van dit certificaatpad is de volgende scherm afbeelding van een werkende IoT Edge apparaat ingesteld als een transparante gateway. OpenSSL wordt gebruikt om verbinding te maken met de IoT Edge hub, de certificaten te valideren en te dumpen.

![Scherm afbeelding van de certificaat hiërarchie op elk niveau](./media/iot-edge-certs/iotedge-cert-chain.png)

U kunt de hiërarchie van certificaat diepte zien die wordt weer gegeven in de scherm opname:

| Basis-CA-certificaat         | Alleen test van Azure IoT Hub CA-certificaat                                                                           |
|-----------------------------|-----------------------------------------------------------------------------------------------------------|
| Tussenliggend CA-certificaat | Alleen Azure IoT Hub tussenliggende certificerings test                                                                 |
| CA-certificaat van apparaat       | iotgateway.ca ("iotgateway" is door gegeven als de hostnaam van de < gateway > aan de gebruiks scripts)   |
| CA-certificaat voor werk belasting     | CA voor iotedge-werk belasting                                                                                       |
| IoT Edge hub-server certificaat | iotedgegw. local (komt overeen met de hostnaam in config. yaml)                                            |

## <a name="next-steps"></a>Volgende stappen

[Informatie over Azure IoT Edge-modules](iot-edge-modules.md)

[Een IoT Edge-apparaat configureren zodat deze werkt als een transparante gateway](how-to-create-transparent-gateway.md)
