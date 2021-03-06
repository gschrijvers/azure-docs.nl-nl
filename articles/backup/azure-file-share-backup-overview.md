---
title: Over back-ups van Azure-bestands share
description: Meer informatie over het maken van back-ups van Azure-bestands shares in de Recovery Services kluis
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: c79100724b882c0682c86070ee74a8726d6b049f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82105740"
---
# <a name="about-azure-file-share-backup"></a>Over back-ups van Azure-bestands share

Azure file share Backup is een systeem eigen, op de cloud gebaseerde back-upoplossing die uw gegevens in de Cloud beschermt en extra onderhouds overhead voor on-premises back-upoplossingen verwijdert. De Azure Backup-service kan naadloos worden geïntegreerd met Azure file sync en biedt u de mogelijkheid om uw bestands share gegevens en uw back-ups te centraliseren. Met deze eenvoudige, betrouw bare en veilige oplossing kunt u de beveiliging van uw zakelijke bestands shares in een paar eenvoudige stappen configureren met een garantie dat u uw gegevens kunt herstellen in het geval van een nood geval.

## <a name="key-benefits-of-azure-file-share-backup"></a>Belangrijkste voor delen van back-ups van Azure-bestands share

* Geen infra structuur: er is geen implementatie nodig om de beveiliging voor uw bestands shares te configureren.
* Aangepaste Bewaar periode: u kunt back-ups configureren met een dagelijks/wekelijks/maandelijks/jaarlijks Bewaar periode op basis van uw vereisten.
* Ingebouwde beheer mogelijkheden: u kunt back-ups plannen en de gewenste Bewaar periode opgeven zonder de extra overhead voor het weghalen van gegevens.
* Direct terugzetten: back-up van Azure-bestands share maakt gebruik van moment opnamen van bestands shares, zodat u alleen de bestanden kunt selecteren die u onmiddellijk wilt herstellen.
* Waarschuwingen en rapportage: u kunt waarschuwingen configureren voor back-up-en herstel fouten en de rapportage oplossing van Azure Backup gebruiken om inzicht te krijgen in de back-ups van uw bestands shares.

## <a name="architecture"></a>Architectuur

![Azure file share-back-uparchitectuur](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

1. De eerste stap bij het configureren van de back-up voor Azure-bestands shares is het maken van een Recovery Services-kluis. De kluis biedt een geconsolideerde weer gave van de back-ups die zijn geconfigureerd voor verschillende werk belastingen.

2. Wanneer u een kluis hebt gemaakt, detecteert de Azure Backup-service de opslag accounts die kunnen worden geregistreerd bij de kluis. U kunt het opslag account selecteren dat als host fungeert voor de bestands shares die u wilt beveiligen.

3. Nadat u het opslag account hebt geselecteerd, wordt in de Azure Backup-service de set bestands shares weer gegeven die aanwezig zijn in het opslag account en worden de namen opgeslagen in de catalogus met beheer lagen.

4. Vervolgens configureert u het back-upbeleid (planning en retentie) op basis van uw vereisten en selecteert u de bestands shares waarvan u een back-up wilt maken. De Azure Backup-service registreert de planningen in het besturings vlak voor geplande back-ups.

5. Op basis van het opgegeven beleid activeert de Azure Backup Scheduler op het geplande tijdstip back-ups. Als onderdeel van deze taak wordt de moment opname van de bestands share gemaakt met behulp van de bestands share-API. Alleen de URL van de moment opname wordt opgeslagen in het meta gegevens archief.

    >[!NOTE]
    >De bestands share gegevens worden niet overgebracht naar de back-upservice omdat de back-upservice moment opnamen maakt en beheert die deel uitmaken van uw opslag account en back-ups niet worden overgedragen naar de kluis.

6. U kunt de inhoud van de Azure-bestands share (afzonderlijke bestanden of de volledige share) herstellen op basis van moment opnamen die beschikbaar zijn op de bron bestands share. Zodra de bewerking is geactiveerd, wordt de URL van de moment opname opgehaald uit het meta gegevens archief en worden de gegevens weer gegeven en overgebracht van de bron momentopname naar de doel bestands share van uw keuze.

7. De bewakings gegevens van de taak back-up en herstel worden gepusht naar de Azure Backup monitoring-service. Hierdoor kunt u Cloud back-ups voor uw bestands shares in één dash board bewaken. Daarnaast kunt u ook waarschuwingen of e-mail meldingen configureren wanneer de status van de back-up wordt beïnvloed. E-mails worden verzonden via de e-mail service van Azure.

## <a name="backup-costs"></a>Back-upkosten

Op dit moment worden alleen moment opnamen in rekening gebracht, omdat Azure file share Backup een oplossing op basis van moment opnamen is. De opslag kosten die worden gemaakt voor moment opnamen worden gefactureerd samen met Azure Files gebruik volgens de [hier](https://azure.microsoft.com/pricing/details/storage/files/)genoemde prijs gegevens.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [maken van back-ups van Azure-bestands shares](backup-afs.md)
* Vind antwoorden op [vragen over het maken van back-ups van Azure files](backup-azure-files-faq.md)
