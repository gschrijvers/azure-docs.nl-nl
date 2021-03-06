---
title: Een HPC-cache van Azure maken
description: Een Azure HPC-cache-exemplaar maken
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/23/2020
ms.author: v-erkel
ms.openlocfilehash: 4ff31ca6a171beece1672802367f08768676efbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195006"
---
# <a name="create-an-azure-hpc-cache"></a>Een HPC-cache van Azure maken

Gebruik de Azure Portal om uw cache te maken.

![scherm opname van het cache-overzicht in Azure Portal, met de knop maken onderaan](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Basis Details definiëren

![scherm afbeelding van de pagina project details in Azure Portal](media/hpc-cache-create-basics.png)

Selecteer in **Project Details**het abonnement en de resource groep die als host moet fungeren voor de cache. Zorg ervoor dat het abonnement op de [toegangs](hpc-cache-prereqs.md#azure-subscription) lijst staat.

Stel in **service Details**de naam van de cache en de andere kenmerken in:

* Locatie: Selecteer een van de [ondersteunde regio's](hpc-cache-overview.md#region-availability).
* Virtueel netwerk: u kunt een bestaande selecteren of een nieuw virtueel netwerk maken.
* Subnet: Kies of maak een subnet met ten minste 64 IP-adressen (/24). Dit subnet moet alleen worden gebruikt voor deze Azure HPC-cache-instantie.

## <a name="set-cache-capacity"></a>Cache capaciteit instellen
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Op de pagina **cache** moet u de capaciteit van uw cache instellen. De waarden die u hier instelt, bepalen hoeveel gegevens uw cache kan bevatten en hoe snel deze client aanvragen kunnen worden verwerkt.

De capaciteit is ook van invloed op de kosten van de cache.

Kies de capaciteit door deze twee waarden in te stellen:

* De maximale snelheid van gegevens overdracht voor de cache (door Voer), in GB/seconde
* De hoeveelheid opslag ruimte die is toegewezen voor gegevens in de cache, in TB

Kies een van de beschik bare doorvoer waarden en cache opslag grootten.

Houd er wel voor dat de werkelijke gegevens overdrachts snelheid afhankelijk is van de werk belasting, de netwerk snelheid en het type opslag doelen. Met de waarden die u kiest, stelt u de maximale door Voer voor het hele cache systeem in, maar een aantal dat wordt gebruikt voor overhead taken. Als een client bijvoorbeeld een bestand aanvraagt dat niet al in de cache is opgeslagen, of als het bestand is gemarkeerd als verouderd, gebruikt de cache een deel van de door Voer om het op te halen uit de back-end-opslag.

Met Azure HPC cache kunt u beheren welke bestanden in de cache worden opgeslagen en vooraf worden geladen om het aantal cache treffers te maximaliseren. De cache-inhoud wordt voortdurend beoordeeld en bestanden worden verplaatst naar lange termijn opslag wanneer ze minder vaak worden gebruikt. Kies een cache opslag grootte die de actieve set van werk bestanden kan bevatten, plus extra ruimte voor meta gegevens en andere overhead.

![scherm afbeelding van de pagina cache grootte](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Versleuteling van Azure Key Vault inschakelen (optioneel)

Als uw cache deel uitmaakt van een regio die door de klant beheerde versleutelings sleutels ondersteunt, wordt de pagina **schijf versleutelings sleutels** weer gegeven tussen de tabbladen **cache** en **Tags** . Op het moment van publicatie wordt deze optie ondersteund in VS-Oost, Zuid-Centraal en VS-West 2.

Als u de versleutelings sleutels wilt beheren die bij uw cache opslag worden gebruikt, geeft u uw Azure Key Vault informatie op de pagina **schijf versleutelings sleutels** . De sleutel kluis moet zich in dezelfde regio en in hetzelfde abonnement bevinden als de cache.

U kunt deze sectie overs Laan als u geen door de klant beheerde sleutels nodig hebt. Azure versleutelt standaard gegevens met door micro soft beheerde sleutels. Lees de [Azure Storage-versleuteling](../storage/common/storage-service-encryption.md) voor meer informatie.

> [!NOTE]
>
> * U kunt geen andere door micro soft beheerde sleutels en door de klant beheerde sleutels wijzigen nadat u de cache hebt gemaakt.
> * Nadat de cache is gemaakt, moet u deze autoriseren voor toegang tot de sleutel kluis. Klik op de knop **versleuteling inschakelen** op de pagina **overzicht** van de cache om versleuteling in te scha kelen. Neem deze stap binnen 90 minuten na het maken van de cache.
> * Na deze autorisatie worden cache schijven gemaakt. Dit betekent dat de initiële tijd voor het maken van de cache kort is, maar dat de cache tien minuten of meer niet kan worden gebruikt nadat u toegang hebt geautoriseerd.

Lees voor een volledige uitleg van het door de klant beheerde sleutel versleutelings proces een door de klant beheerde versleutelings sleutel [gebruiken voor Azure HPC-cache](customer-keys.md).

![scherm afbeelding van de pagina versleutelings sleutels met de selectie ' door de klant beheerd ' geselecteerde en sleutel kluis velden die worden weer gegeven](media/create-encryption.png)

Selecteer door de **klant beheerd** voor het kiezen van door de klant beheerde sleutel versleuteling. De sleutel kluis specificatie velden worden weer gegeven. Selecteer de Azure Key Vault die u wilt gebruiken en selecteer vervolgens de sleutel en versie die u voor deze cache wilt gebruiken. De sleutel moet een 2048-bits RSA-sleutel zijn. U kunt op deze pagina een nieuwe sleutel kluis, sleutel of sleutel versie maken.

Nadat u de cache hebt gemaakt, moet u deze machtigen voor het gebruik van de sleutel kluis-service. Lees [Azure Key Vault versleuteling autoriseren vanuit de cache](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor meer informatie.

## <a name="add-resource-tags-optional"></a>Resource Tags toevoegen (optioneel)

Op de pagina **Tags** kunt u [resource Tags](https://go.microsoft.com/fwlink/?linkid=873112) toevoegen aan uw Azure HPC-cache-exemplaar.

## <a name="finish-creating-the-cache"></a>Het maken van de cache volt ooien

Nadat u de nieuwe cache hebt geconfigureerd, klikt u op het tabblad **controleren + maken** . In de portal worden uw selecties gevalideerd en kunt u uw keuzes controleren. Als alles correct is, klikt u op **maken**.

Het maken van de cache duurt ongeveer 10 minuten. U kunt de voortgang volgen in het deel venster meldingen van de Azure Portal.

![scherm opname van pagina's voor het maken van de implementatie van de cache en meldingen in de portal](media/hpc-cache-deploy-status.png)

Wanneer het maken is voltooid, wordt er een melding weer gegeven met een koppeling naar het nieuwe Azure HPC-cache-exemplaar. de cache wordt weer gegeven in de lijst met **resources** van uw abonnement.

![scherm opname van het Azure HPC-cache-exemplaar in Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Als uw cache door de klant beheerde versleutelings sleutels gebruikt, kan de cache worden weer gegeven in de lijst met resources voordat de implementatie status wordt gewijzigd in voltooid. Zodra de status van de cache wacht op de sleutel, kunt u [deze machtigen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) voor het gebruik **van** de sleutel kluis.

## <a name="next-steps"></a>Volgende stappen

Als uw cache wordt weer gegeven in de lijst met **resources** , kunt u door gaan naar de volgende stap.

* [Definieer opslag doelen](hpc-cache-add-storage.md) om uw cache toegang te geven tot uw gegevens bronnen.
* Als u versleutelings sleutels van door de klant beheerde code ring gebruikt, moet u Azure Key Vault versleuteling van de cache van de-overzichts pagina [machtigen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) om de cache-instellingen te volt ooien. U moet deze stap uitvoeren voordat u opslag kunt toevoegen. Lees door de [klant beheerde versleutelings sleutels gebruiken](customer-keys.md) voor meer informatie.
