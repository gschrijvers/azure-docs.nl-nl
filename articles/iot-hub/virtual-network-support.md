---
title: Azure IoT Hub-ondersteuning voor virtuele netwerken
description: Het gebruik van een verbindings patroon voor virtuele netwerken met IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: jlian
ms.openlocfilehash: 7d7e04c526f7327a000ac26e255d2c8363c01f5c
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83871238"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Ondersteuning voor virtuele netwerken IoT Hub met persoonlijke koppelingen en beheerde identiteit

Standaard worden de hostnamen van IoT Hub toegewezen aan een openbaar eind punt met een openbaar routeerbaar IP-adres via internet. Verschillende klanten delen dit IoT Hub open bare eind punt en IoT-apparaten in meer dan Wide Area Networks en on-premises netwerken hebben toegang tot het netwerk.

![IoT Hub openbaar eind punt](./media/virtual-network-support/public-endpoint.png)

IoT Hub functies, waaronder [bericht routering](./iot-hub-devguide-messages-d2c.md), het [uploaden van bestanden](./iot-hub-devguide-file-upload.md)en [bulksgewijs importeren/exporteren van apparaten](./iot-hub-bulk-identity-mgmt.md) , moet u ook een verbinding van IOT hub hebben met een Azure-resource die eigendom is van een klant over het open bare eind punt. Deze connectiviteits paden vormen samen het uitgaande verkeer van IoT Hub naar klant resources.

Mogelijk wilt u de verbinding met uw Azure-resources (inclusief IoT Hub) beperken via een VNet dat u bezit en gebruikt. Dit zijn de volgende redenen:

* Introductie van de netwerk isolatie voor uw IoT-hub door te voor komen dat de connectiviteit bloot staat aan het open bare Internet.

* Het inschakelen van een persoonlijke connectiviteits ervaring van uw on-premises netwerk assets zorgt ervoor dat uw gegevens en verkeer rechtstreeks naar het Azure-backbone netwerk worden verzonden.

* Exfiltration-aanvallen van gevoelige on-premises netwerken voor komen. 

* Hieronder zijn de Azure-brede connectiviteits patronen ingesteld met behulp van [privé-eind punten](../private-link/private-endpoint-overview.md).

In dit artikel wordt beschreven hoe u deze doelen kunt bereiken met behulp van een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) voor ingangs connectiviteit met IOT hub en een vertrouwde micro soft Services-uitzonde ring gebruiken voor het afleiden van connectiviteit vanuit IOT hub met andere Azure-resources.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Connectiviteit met IoT Hub met behulp van een persoonlijke Azure-koppeling

Een persoonlijk eind punt is een privé-IP-adres dat is toegewezen in een VNet van de klant via welke een Azure-resource bereikbaar is. Met de persoonlijke koppeling van Azure kunt u een persoonlijk eind punt instellen voor uw IoT-hub zodat services in uw VNet IoT Hub kunnen bereiken zonder dat er verkeer naar het open bare eind punt van IoT Hub hoeft te worden verzonden. Op dezelfde manier kunnen uw on-premises apparaten gebruikmaken van [virtueel particulier netwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) peering om verbinding te krijgen met uw VNet en uw IOT hub (via het persoonlijke eind punt). Als gevolg hiervan kunt u de verbinding met de open bare eind punten van uw IoT-hub beperken of volledig blok keren met behulp van [IOT hub IP-filter](./iot-hub-ip-filtering.md) en [route ring configureren om geen gegevens naar het ingebouwde eind punt te verzenden](#built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint). Deze aanpak houdt connectiviteit met uw hub met behulp van het privé-eind punt voor apparaten. De hoofd focus van deze installatie is voor apparaten in een on-premises netwerk. Deze installatie wordt niet aanbevolen voor apparaten die zijn geïmplementeerd in een Wide Area-netwerk.

![IoT Hub openbaar eind punt](./media/virtual-network-support/virtual-network-ingress.png)

Voordat u doorgaat, controleert u of aan de volgende vereisten wordt voldaan:

* U hebt [een Azure VNet gemaakt](../virtual-network/quick-create-portal.md) met een subnet waarin het persoonlijke eind punt wordt gemaakt.

* Voor apparaten die in on-premises netwerken actief zijn, moet u [VPN (virtueel particulier netwerk)](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privé-peering in uw Azure VNet instellen.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Een persoonlijk eind punt voor IoT Hub ingang instellen

1. Selecteer in Azure Portal **netwerken**, verbindingen met een **persoonlijk eind punt**en klik op het **+ persoonlijke eind punt**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Scherm opname waarin wordt getoond waar een persoonlijk eind punt moet worden toegevoegd voor IoT Hub":::

1. Geef het abonnement, de resource groep, de naam en de regio op voor het maken van het nieuwe persoonlijke eind punt in. In het ideale geval moet het persoonlijke eind punt worden gemaakt in dezelfde regio als uw hub.

1. Klik op **volgende: resource**, geef het abonnement voor uw IOT hub resource op en selecteer **' micro soft. devices/IotHubs '** als resource type, uw IOT hub naam als **resource**en **iotHub** als doel subresource.

1. Klik op **volgende: Configuratie** en geef uw virtuele netwerk en subnet op om het persoonlijke eind punt in te maken. Selecteer de optie voor de integratie met de persoonlijke DNS-zone van Azure, indien gewenst.

1. Klik op **volgende: Tags**en geef eventueel labels voor uw resource op.

1. Klik op **beoordeling + maken** om uw persoonlijke koppelings bron te maken.

### <a name="built-in-event-hub-compatible-endpoint-doesnt-support-access-over-private-endpoint"></a>Het ingebouwde Event hub-compatibele eind punt biedt geen ondersteuning voor toegang via een persoonlijk eind punt

Het [ingebouwde Event hub-compatibele eind punt](iot-hub-devguide-messages-read-builtin.md) biedt geen ondersteuning voor toegang via een persoonlijk eind punt. Indien geconfigureerd, is het privé-eind punt van een IoT-hub alleen voor ingangs connectiviteit. Het gebruiken van gegevens van een ingebouwd Event hub-compatibel eind punt kan alleen worden uitgevoerd via het open bare Internet. 

Het [IP-filter](iot-hub-ip-filtering.md) van IOT hub beheert ook niet de open bare toegang tot het ingebouwde eind punt. Als u open bare netwerk toegang tot uw IoT-hub volledig wilt blok keren, moet u het volgende doen: 

1. Toegang tot privé-eind punten configureren voor IoT Hub
1. Open bare netwerk toegang uitschakelen door IP-filter te gebruiken om alle IP-adressen te blok keren
1. Het ingebouwde Event hub-eind punt uitschakelen door [route ring in te stellen zodat er geen gegevens naar worden verzonden](iot-hub-devguide-messages-d2c.md)
1. De [terugval route](iot-hub-devguide-messages-d2c.md#fallback-route) uitschakelen
1. Uitgaand verkeer configureren voor andere Azure-resources met behulp van [vertrouwde micro soft-Services](#egress-connectivity-from-iot-hub-to-other-azure-resources)

### <a name="pricing-for-private-link"></a>Prijs voor privé koppeling

Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link)voor prijs informatie.

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>De connectiviteit van IoT Hub met andere Azure-resources afbreken

IoT Hub kunt verbinding maken met uw Azure Blob-opslag, Event Hub, service bus-resources voor [bericht routering](./iot-hub-devguide-messages-d2c.md), het [uploaden van bestanden](./iot-hub-devguide-file-upload.md)en het [bulksgewijs importeren/exporteren van apparaten](./iot-hub-bulk-identity-mgmt.md) via het open bare eind punt van de resource. Het binden van uw bron aan een VNet blokkeert standaard de connectiviteit met de bron. Als gevolg hiervan voor komt deze configuratie dat IoT Hub van het werken met het verzenden van gegevens naar uw resources. U kunt dit probleem oplossen door de verbinding van uw IoT Hub-bron met uw opslag account, Event Hub of service bus-resources in te scha kelen via de optie **vertrouwde micro soft-service** .

### <a name="turn-on-managed-identity-for-iot-hub"></a>Beheerde identiteit inschakelen voor IoT Hub

Om ervoor te zorgen dat andere services uw IoT-hub als een vertrouwde micro soft-service kunnen vinden, moet deze een door het systeem toegewezen beheerde identiteit hebben.

1. Ga naar **identiteit** in uw IOT hub-Portal

1. Onder **status**selecteert u **aan**en klikt u vervolgens op **Opslaan**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Scherm afbeelding die laat zien hoe u de beheerde identiteit voor IoT Hub inschakelt":::

### <a name="pricing-for-managed-identity"></a>Prijzen voor beheerde identiteit

Betrouw bare micro soft-functie voor eerste partij uitzonde ringen is gratis. Kosten voor de ingerichte opslag accounts, Event hubs of service bus-resources worden afzonderlijk toegepast.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>De connectiviteit met de eind punten van het opslag account voor route ring

IoT Hub kunt berichten routeren naar een opslag account van de klant. Uw IoT Hub moet een [beheerde identiteit](#turn-on-managed-identity-for-iot-hub)hebben om de routerings functionaliteit toegang te geven tot een opslag account terwijl er firewall beperkingen zijn ingesteld. Nadat een beheerde identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw opslag account.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer de **gegevens bijdrager** van de opslag-BLOB ([*niet* Inzender of Inzender voor opslag accounts](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** , zoals **toegangs rechten toewijzen aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **opslag** als het type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam voor het eind punt op, selecteert u de container die u wilt gebruiken in uw Blob-opslag, geeft u code ring en indeling van de bestands naam op. Selecteer **systeem toegewezen** als **verificatie type** voor uw opslag eindpunt. Klik op de knop **maken** .

Nu is uw aangepaste opslag eindpunt ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en heeft deze toegang tot uw opslag Resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Connectiviteit met Event hubs-eind punten voor route ring

IoT Hub kunnen worden geconfigureerd voor het routeren van berichten naar een event hubs-naam ruimte die eigendom is van een klant. Uw IoT Hub moet een beheerde identiteit hebben om de routerings functionaliteit in staat te stellen om toegang te krijgen tot een event hubs-resource terwijl er firewall beperkingen zijn ingesteld. Nadat een beheerde identiteit is gemaakt, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw event hubs.

1. Ga in het Azure Portal naar uw **iam-tabblad (Event hubs Access Control)** en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **Event hubs gegevens afzender** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw event hubs en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in om **vertrouwde micro soft-Services toegang te geven tot Event hubs**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **Event hubs** als het type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam op voor uw eind punt, selecteert u uw event hubs-naam ruimte en-exemplaar en klikt u op de knop **maken** .

Uw aangepaste Event hubs-eind punt is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en heeft toegang tot uw event hubs-resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Connectiviteit met Service Bus-eind punten voor route ring

IoT Hub kunnen worden geconfigureerd voor het routeren van berichten naar een service bus-naam ruimte die eigendom is van een klant. Uw IoT Hub moet een beheerde identiteit hebben om de routerings functionaliteit toegang te geven tot een service bus-resource terwijl er firewall beperkingen zijn ingesteld. Nadat een beheerde identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw service bus.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van de service bus en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer **Service Bus-gegevens afzender** als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** als **toegangs toewijzing aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw service bus en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje **vertrouwde micro soft-Services toestaan voor toegang tot deze service bus**in. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bericht routering** .

5. Navigeer naar het gedeelte **aangepaste eind punten** en klik op **toevoegen**. Selecteer **Service Bus-wachtrij** of **Service Bus onderwerp** (indien van toepassing) als het type eind punt.

6. Op de pagina die wordt weer gegeven, geeft u een naam op voor uw eind punt, selecteert u de naam ruimte van de service bus en de wachtrij of het onderwerp (indien van toepassing). Klik op de knop **maken** .

Nu het aangepaste service bus-eind punt is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en de toegang heeft tot uw service bus-resource ondanks de firewall beperkingen. U kunt dit eind punt nu gebruiken om een routerings regel in te stellen.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>De connectiviteit met opslag accounts voor het uploaden van bestanden

Met de functie voor het uploaden van bestanden van IoT Hub kunnen apparaten bestanden uploaden naar een opslag account van de klant. Als u wilt toestaan dat het bestand kan worden geüpload, moeten zowel apparaten als IoT Hub verbinding hebben met het opslag account. Als er firewall beperkingen zijn ingesteld op het opslag account, moeten uw apparaten gebruikmaken van het mechanisme van het ondersteunde opslag account (inclusief [particuliere eind punten](../private-link/create-private-endpoint-storage-portal.md), [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md)of [directe firewall configuratie](../storage/common/storage-network-security.md)) om verbinding te kunnen maken. Als er firewall beperkingen zijn ingesteld op het opslag account, moet IoT Hub worden geconfigureerd voor toegang tot de opslag bron via de uitzonde ring vertrouwde micro soft-Services. Voor dit doel moet uw IoT Hub een beheerde identiteit hebben. Nadat een beheerde identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw opslag account.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer de **gegevens bijdrager** van de opslag-BLOB ([*niet* Inzender of Inzender voor opslag accounts](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** , zoals **toegangs rechten toewijzen aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

4. Ga op de pagina resource van uw IoT Hub naar het tabblad **bestand uploaden** .

5. Op de pagina die wordt weer gegeven, selecteert u de container die u wilt gebruiken in uw Blob-opslag, configureert u de **instellingen voor bestands meldingen**, de **SAS TTL**, de **standaard-TTL**en het **maximum aantal leverings aantallen** naar wens. Selecteer **systeem toegewezen** als **verificatie type** voor uw opslag eindpunt. Klik op de knop **maken** .

Nu uw opslag eindpunt voor het uploaden van bestanden is ingesteld voor het gebruik van de door het systeem toegewezen identiteit van uw hub en de toegang heeft tot uw opslag Resource ondanks de firewall beperkingen.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>De connectiviteit met opslag accounts voor bulk import/export

IoT Hub ondersteunt de functionaliteit voor het [importeren/exporteren](./iot-hub-bulk-identity-mgmt.md) van informatie over apparaten in bulk van/naar een door de klant verschafte opslag-blob. Als u de functie voor bulk import/export wilt gebruiken, moeten zowel apparaten als IoT Hub verbinding hebben met het opslag account.

Deze functionaliteit vereist connectiviteit van IoT Hub naar het opslag account. Als u toegang wilt krijgen tot een service bus-resource terwijl er firewall beperkingen zijn ingesteld, moet uw IoT Hub een beheerde identiteit hebben. Nadat een beheerde identiteit is ingericht, volgt u de onderstaande stappen om RBAC-machtigingen te geven aan de resource-identiteit van uw hub om toegang te krijgen tot uw service bus.

1. Navigeer in het Azure Portal naar het tabblad **toegangs beheer (IAM)** van uw opslag account en klik op **toevoegen** onder de sectie **een roltoewijzing toevoegen** .

2. Selecteer de **gegevens bijdrager** van de opslag-BLOB ([*niet* Inzender of Inzender voor opslag accounts](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues)) als **rol**, **Azure AD-gebruiker,-groep of Service-Principal** , zoals **toegangs rechten toewijzen aan** en selecteer de resource naam van uw IOT hub in de vervolg keuzelijst. Klik op de knop **Opslaan**.

3. Ga naar het tabblad **firewalls en virtuele netwerken** in uw opslag account en schakel de optie **toegang via geselecteerde netwerken toestaan** in. Schakel onder de lijst **uitzonde ringen** het selectie vakje in voor **vertrouwde micro soft-Services toegang geven tot dit opslag account**. Klik op de knop **Opslaan**.

U kunt nu de Azure IoT REST-Api's gebruiken voor het [maken van import-export taken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) voor informatie over het gebruik van de functie voor bulksgewijs importeren/exporteren. U moet de `storageAuthenticationType="identityBased"` in de hoofd tekst van de aanvraag opgeven en `inputBlobContainerUri="https://..."` de `outputBlobContainerUri="https://..."` invoer-en uitvoer-url's van uw opslag account gebruiken.

Azure IoT Hub Sdk's ondersteunen deze functionaliteit ook in het register beheer van de service-client. Het volgende code fragment laat zien hoe u een import-of export taak initieert in met behulp van de C#-SDK.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Voor het gebruik van deze versie van de Azure IoT Sdk's met ondersteuning voor virtuele netwerken voor C#, Java en node. js:

1. Maak een omgevings variabele `EnableStorageIdentity` met de naam en stel de waarde in op `1` .

2. De SDK downloaden: [Java](https://aka.ms/vnetjavasdk)  |  [C#](https://aka.ms/vnetcsharpsdk)  |  [node. js](https://aka.ms/vnetnodesdk)
 
Voor python downloadt u onze beperkte versie van GitHub.

1. Navigeer naar de [pagina release van github](https://aka.ms/vnetpythonsdk).

2. Down load het volgende bestand, dat u onder aan de pagina release onder de koptekst met de naam **assets**kunt vinden.
    > *azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL*

3. Open een Terminal en navigeer naar de map met het gedownloade bestand.

4. Voer de volgende opdracht uit om de python Service-SDK te installeren met ondersteuning voor virtuele netwerken:
    > PIP install./azure_iot_hub-2.2.0_limited-py2. py3-none-any. WHL


## <a name="next-steps"></a>Volgende stappen

Gebruik de onderstaande koppelingen voor meer informatie over IoT Hub-functies:

* [Berichtroutering](./iot-hub-devguide-messages-d2c.md)
* [Bestand uploaden](./iot-hub-devguide-file-upload.md)
* [Bulksgewijs importeren/exporteren van apparaten](./iot-hub-bulk-identity-mgmt.md) 
