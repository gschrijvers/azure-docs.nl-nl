---
title: AVERE vFXT cluster tuning-Azure
description: Overzicht van aangepaste instellingen voor het optimaliseren van de prestaties in avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: df20f050ff87fdb59a3e5cca373098240f8bfbb9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76152932"
---
# <a name="cluster-tuning"></a>Cluster afstemmen

De meeste vFXT-clusters kunnen profiteren van aangepaste instellingen voor prestaties. Deze instellingen helpen het cluster optimaal te laten werken met uw specifieke werk stroom, gegevensset en hulpprogram ma's.

Deze aanpassing moet worden uitgevoerd met behulp van een ondersteunings medewerker, omdat hiervoor functies kunnen worden geconfigureerd die niet beschikbaar zijn via het configuratie scherm van AVERE.

In deze sectie wordt een aantal van de aangepaste afstemming beschreven die u kunt uitvoeren.

## <a name="general-optimizations"></a>Algemene optimalisaties

Deze wijzigingen kunnen worden aanbevolen op basis van de kenmerken van de gegevensset of de stijl van de werk stroom.

* Als de werk belasting write-Heavy is, verg root u de grootte van de schrijf cache van de standaard waarde van 20%.
* Als de gegevensset veel kleine bestanden omvat, verhoogt u de limiet voor het aantal bestanden van de cluster cache.
* Als voor het werk het kopiëren of verplaatsen van gegevens tussen twee opslag plaatsen is vereist, past u het aantal threads aan dat wordt gebruikt voor het verplaatsen van gegevens:
  * Als u de snelheid wilt verhogen, kunt u het aantal gebruikte parallelle threads verhogen.
  * Als het opslag volume van de back-end overbelast wordt, moet u mogelijk het aantal gebruikte parallelle threads verlagen.
* Als het cluster gegevens in de cache opslaat voor een kern bestand die gebruikmaakt van NFSv4 Acl's, schakelt u in cache opslaan in de toegangs modus in om bestands autorisatie voor bepaalde clients te stroom lijnen.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Cloud-NAS-of Cloud gateway-optimalisaties

In een Cloud-of Gateway scenario biedt het vFXT-cluster toegang tot een Cloud container in NAS-stijl. Als u wilt profiteren van hogere gegevens snelheden tussen het vFXT-cluster en de Cloud opslag, kunt u het beste de instellingen wijzigen zodat de gegevens naar het opslag volume worden gepusht vanuit de cache. Bijvoorbeeld:

* Verhoog het aantal TCP-verbindingen tussen het cluster en de opslag container

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting of hybride WAN-optimalisaties

In een scenario met een hoofdletter gevoelig scenario of een WAN-optimalisatie voor hybride opslag biedt het vFXT-cluster integratie tussen de Cloud en on-premises hardwarematige opslag. Deze wijzigingen kunnen nuttig zijn:

* Verhoog het aantal TCP-verbindingen dat is toegestaan tussen het cluster en de kern bestand
* Schakel de WAN-optimalisatie-instelling voor de externe kern bestand in (deze instelling kan worden gebruikt voor een externe on-premises bestand of een Cloud kern bestand in een andere Azure-regio.)
* De buffer grootte voor TCP-socket verhogen<sup>*</sup>
* De instelling ' altijd door sturen ' inschakelen om overbodige bestanden in cache te verminderen<sup>*</sup>

<sup>*</sup>Deze aanpassingen zijn mogelijk niet van toepassing op alle systemen, afhankelijk van de behoeften van de werk belasting en de prestaties.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Help uw avere vFXT voor Azure te optimaliseren

Als u contact wilt opnemen met de ondersteunings medewerkers over deze optimalisaties, gebruikt u de procedure die wordt beschreven in [hulp bij uw systeem opvragen](avere-vfxt-open-ticket.md).
