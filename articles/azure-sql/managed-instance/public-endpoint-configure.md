---
title: Openbaar met een eind punt beheerd exemplaar configureren
description: Meer informatie over het configureren van een openbaar eind punt voor Azure SQL Managed instance
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/07/2019
ms.openlocfilehash: e11e8181ba17f9833cd4add7650ad9f81a158fd9
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118752"
---
# <a name="configure-public-endpoint-in-azure-sql-managed-instance"></a>Open bare eind punt in Azure SQL Managed instance configureren
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Het open bare eind punt voor een [beheerd exemplaar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) maakt gegevens toegang tot uw beheerde exemplaar mogelijk buiten het [virtuele netwerk](../../virtual-network/virtual-networks-overview.md). U hebt toegang tot uw beheerde exemplaar vanuit multi tenant Azure-Services, zoals Power BI, Azure App Service of een on-premises netwerk. Als u het open bare eind punt op een beheerd exemplaar gebruikt, hoeft u geen VPN te gebruiken. Dit kan helpen om problemen met de door Voer van VPN te voor komen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> - Open bare eind punt inschakelen voor uw beheerde instantie in de Azure Portal
> - Open bare eind punt voor uw beheerde instantie inschakelen met Power shell
> - De beveiligings groep voor een beheerd exemplaar netwerk configureren om verkeer naar het open bare eind punt van het beheerde exemplaar toe te staan
> - Het open bare endpoint van het beheerde exemplaar verkrijgen connection string

## <a name="permissions"></a>Machtigingen

Als gevolg van de gevoeligheid van gegevens die zich in een beheerd exemplaar bevindt, vereist de configuratie voor het inschakelen van het open bare eind punt van het beheerde exemplaar een proces dat uit twee stappen bestaat. Deze veiligheids maatregel voldoet aan de schei ding van taken (SoD):

- Het inschakelen van een openbaar eind punt op een beheerd exemplaar moet worden uitgevoerd door de beheerder van het beheerde exemplaar. De beheerder van het beheerde exemplaar kan worden gevonden op de **overzichts** pagina van de bron van het beheerde exemplaar.
- Verkeer toestaan met behulp van een netwerk beveiligings groep die moet worden uitgevoerd door een netwerk beheerder. Zie [machtigingen voor netwerk beveiligings groepen](../../virtual-network/manage-network-security-group.md#permissions)voor meer informatie.

## <a name="enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal"></a>Openbaar eind punt inschakelen voor een beheerd exemplaar in het Azure Portal

1. Start de Azure Portal op<https://portal.azure.com/.>
1. Open de resource groep met het beheerde exemplaar en selecteer het **SQL Managed instance** waarvoor u het open bare eind punt wilt configureren.
1. Selecteer op de pagina **beveiligings** instellingen het tabblad **virtueel netwerk** .
1. Selecteer op de pagina configuratie van virtueel netwerk de optie **inschakelen** en klik vervolgens op het pictogram **Opslaan** om de configuratie bij te werken.

![Mi-vnet-config. png](./media/public-endpoint-configure/mi-vnet-config.png)

## <a name="enabling-public-endpoint-for-a-managed-instance-using-powershell"></a>Openbaar eind punt inschakelen voor een beheerd exemplaar met behulp van Power shell

### <a name="enable-public-endpoint"></a>Openbaar eind punt inschakelen

Voer de volgende Power shell-opdrachten uit. Vervang **abonnement-id** door uw abonnements-id. Vervang ook de **naam van RG** door de resource groep voor uw beheerde exemplaar en vervang de **mi-naam** door de naam van uw beheerde exemplaar.

```powershell
Install-Module -Name Az

Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount

# Use your subscription ID in place of subscription-id below

Select-AzSubscription -SubscriptionId {subscription-id}

# Replace rg-name with the resource group for your managed instance, and replace mi-name with the name of your managed instance

$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}

$mi = $mi | Set-AzSqlInstance -PublicDataEndpointEnabled $true -force
```

### <a name="disable-public-endpoint"></a>Openbaar eind punt uitschakelen

Als u het open bare eind punt wilt uitschakelen met behulp van Power shell, voert u de volgende opdracht uit (en vergeet niet om de NSG voor de binnenkomende poort 3342 te sluiten als u deze hebt geconfigureerd):

```powershell
Set-AzSqlInstance -PublicDataEndpointEnabled $false -force
```

## <a name="allow-public-endpoint-traffic-on-the-network-security-group"></a>Openbaar eindpunt verkeer toestaan voor de netwerk beveiligings groep

1. Als u de configuratie pagina van het beheerde exemplaar nog steeds geopend hebt, gaat u naar het tabblad **overzicht** . anders gaat u terug naar de resource van het **SQL Managed instance** . Selecteer de koppeling **virtueel netwerk/subnet** , waarmee u naar de configuratie pagina van het virtuele netwerk gaat.

    ![Mi-overview. png](./media/public-endpoint-configure/mi-overview.png)

1. Selecteer het tabblad **subnetten** in het linkerdeel venster configuratie van het virtuele netwerk en noteer de **beveiligings groep** voor uw beheerde exemplaar.

    ![Mi-vnet-subnet. png](./media/public-endpoint-configure/mi-vnet-subnet.png)

1. Ga terug naar de resource groep die uw beheerde exemplaar bevat. De naam van de **netwerk beveiligings groep** die hierboven wordt vermeld, wordt weer gegeven. Selecteer de naam om naar de configuratie pagina voor de netwerk beveiligings groep te gaan.

1. Selecteer het tabblad **binnenkomende beveiligings regels** en voeg een regel **toe** met een hogere prioriteit dan de **deny_all_inbound** regel met de volgende instellingen: </br> </br>

    |Instelling  |Voorgestelde waarde  |Beschrijving  |
    |---------|---------|---------|
    |**Bron**     |Een IP-adres of service label         |<ul><li>Voor Azure-Services, zoals Power BI, selecteert u de Azure Cloud service-tag</li> <li>Gebruik NAT IP-adres voor uw computer of Azure-VM</li></ul> |
    |**Poortbereiken van bron**     |* |Dit op * (alle) laten staan als bron poorten worden meestal dynamisch toegewezen en als zodanig, onvoorspelbaar |
    |**Beoogde**     |Alle         |Bestemming weglaten om verkeer naar het subnet van het beheerde exemplaar toe te staan |
    |**Poortbereiken van doel**     |3342         |Doel poort van bereik tot 3342, wat het open bare TDS-eind punt van het beheerde exemplaar is |
    |**Protocol**     |TCP         |SQL Managed instance gebruikt TCP-protocol voor TDS |
    |**Actie**     |Toestaan         |Binnenkomend verkeer naar een beheerd exemplaar via het open bare eind punt toestaan |
    |**Prioriteiten**     |1300         |Zorg ervoor dat deze regel een hogere prioriteit heeft dan de **deny_all_inbound** regel |

    ![Mi-NSG-rules. png](./media/public-endpoint-configure/mi-nsg-rules.png)

    > [!NOTE]
    > Poort 3342 wordt gebruikt voor open bare eindpunt verbindingen met een beheerd exemplaar en kan op dit moment niet worden gewijzigd.

## <a name="obtaining-the-managed-instance-public-endpoint-connection-string"></a>Het open bare endpoint van het beheerde exemplaar verkrijgen connection string

1. Ga naar de configuratie pagina van het beheerde exemplaar die is ingeschakeld voor het open bare eind punt. Selecteer het tabblad **verbindings reeksen** onder de configuratie- **instellingen** .
1. Houd er rekening mee dat de hostnaam van het open bare eind punt de indeling <mi_name> bevat. **Public**. <dns_zone>. database.Windows.net en de poort die voor de verbinding wordt gebruikt, is 3342.

    ![Mi-Public-endpoint-Conn-string. png](./media/public-endpoint-configure/mi-public-endpoint-conn-string.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van [Azure SQL Managed instance veilig met een openbaar eind punt](public-endpoint-overview.md).
