---
title: Verwijderde gebruikers in de Azure Active Directory Portal bulksgewijs herstellen | Microsoft Docs
description: Verwijderde gebruikers bulksgewijs herstellen in het Azure AD-beheer centrum in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1db23bb1176a41b4b9bac548b737fbd13fbe82c4
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83685239"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Verwijderde gebruikers bulksgewijs herstellen in Azure Active Directory

Azure Active Directory (Azure AD) ondersteunt het herstellen van bewerkingen voor bulk gebruikers en ondersteunt het downloaden van lijsten van gebruikers, groepen en groeps leden.

## <a name="understand-the-csv-template"></a>De CSV-sjabloon begrijpen

Down load en vul de CSV-sjabloon in om Azure AD-gebruikers bulksgewijs te herstellen. De CSV-sjabloon die u downloadt, kan eruitzien als in dit voor beeld:

![Werk blad voor upload-en aanroep-outs waarin het doel en de waarden voor elke rij en kolom worden uitgelegd](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Structuur van CSV-sjabloon

De rijen in een gedownloade CSV-sjabloon zijn als volgt:

- **Versie nummer**: de eerste rij met het versie nummer moet worden opgenomen in de upload CSV.
- **Kolom koppen**: de indeling van de kolom koppen is de &lt; *item naam* &gt; [propertyname] &lt; *vereist of leeg* &gt; . Bijvoorbeeld `Object ID [objectId] Required`. Sommige oudere versies van de sjabloon hebben mogelijk een kleine variatie.
- **Voor beelden van rij**: we hebben in de sjabloon een rij met voor beelden van geldige waarden voor elke kolom opgenomen. U moet de voor beelden van de rij verwijderen en vervangen door uw eigen vermeldingen.

### <a name="additional-guidance"></a>Aanvullende richtlijnen

- De eerste twee rijen van de upload sjabloon mogen niet worden verwijderd of gewijzigd, of de upload kan niet worden verwerkt.
- De vereiste kolommen worden eerst weer gegeven.
- Het is niet raadzaam om nieuwe kolommen toe te voegen aan de sjabloon. Alle extra kolommen die u toevoegt, worden genegeerd en worden niet verwerkt.
- U wordt aangeraden de meest recente versie van de CSV-sjabloon zo vaak mogelijk te downloaden.

## <a name="to-bulk-restore-users"></a>Gebruikers bulksgewijs herstellen

1. [Meld u aan bij uw Azure AD-organisatie](https://aad.portal.azure.com) met een account dat een gebruikers beheerder is in de Azure AD-organisatie.
1. Selecteer in azure AD de optie **gebruikers**  >  **verwijderd**.
1. Op de pagina **Verwijderde gebruikers** selecteert u **bulk herstel** om een geldig CSV-bestand met eigenschappen te uploaden van de gebruikers die u wilt herstellen.

    ![Selecteer de opdracht bulk herstel op de pagina verwijderde gebruikers](./media/users-bulk-restore/bulk-restore.png)

1. Open de CSV-sjabloon en voeg een regel toe voor elke gebruiker die u wilt herstellen. De enige vereiste waarde is **ObjectID**. Sla het bestand op.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Selecteer een lokaal CSV-bestand waarin de gebruikers worden vermeld die u wilt toevoegen":::

1. Blader op de pagina **bulk herstel** onder **uw CSV-bestand uploaden**naar het bestand. Wanneer u het bestand selecteert en op **verzenden**klikt, wordt de validatie van het CSV-bestand gestart.
1. Wanneer de bestands inhoud is gevalideerd, ziet u dat het **bestand is geüpload**. Als er fouten zijn, moet u deze oplossen voordat u de taak kunt indienen.
1. Wanneer de validatie van uw bestand wordt door gegeven, selecteert u **verzenden** om de bulk bewerking van Azure te starten waarmee de gebruikers worden hersteld.
1. Wanneer de herstel bewerking is voltooid, ziet u een melding dat de bulk bewerking is geslaagd.

Als er fouten zijn, kunt u het bestand met resultaten downloaden en weer geven op de pagina **resultaten van bulk bewerking** . Het bestand bevat de reden voor elke fout.

## <a name="check-status"></a>Status controleren

U kunt de status van al uw bulk aanvragen in behandeling bekijken op de pagina **resultaten van bulk bewerking** .

[![](media/users-bulk-restore/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-restore/bulk-center.png#lightbox)

Vervolgens kunt u controleren of de gebruikers die u hebt hersteld, bestaan in de Azure AD-organisatie in de Azure Portal of met behulp van Power shell.

## <a name="view-restored-users-in-the-azure-portal"></a>Herstelde gebruikers weer geven in de Azure Portal

1. [Meld u aan bij het Azure AD-beheer centrum](https://aad.portal.azure.com) met een account dat een gebruikers beheerder in de organisatie is.
1. Selecteer **Azure Active Directory**in het navigatie deel venster.
1. Onder **Beheren**, selecteer **Gebruikers**.
1. Selecteer onder **weer geven** **alle gebruikers** en controleer of de gebruikers die u hebt hersteld, worden weer gegeven.

### <a name="view-users-with-powershell"></a>Gebruikers weer geven met Power shell

Voer de volgende opdracht uit:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

U ziet dat de gebruikers die u hebt hersteld, worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers bulksgewijs importeren](users-bulk-add.md)
- [Gebruikers bulksgewijs verwijderen](users-bulk-delete.md)
- [Lijst met gebruikers downloaden](users-bulk-download.md)
