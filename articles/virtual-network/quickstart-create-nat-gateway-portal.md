---
title: 'Snelstartgids: een NAT-gateway maken-Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: In deze Quick start ziet u hoe u een NAT-gateway maakt met behulp van de Azure Portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80066389"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Snelstartgids: een NAT-gateway maken met behulp van de Azure Portal

In deze Quick start ziet u hoe u de NAT-service van Azure Virtual Network gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor een virtuele machine in Azure. 

Als u wilt, kunt u deze stappen uitvoeren met behulp van de [Azure cli](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md)of een [arm-sjabloon](quickstart-create-nat-gateway-powershell.md) implementeren in plaats van de portal.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en para meters

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resource groep en het virtuele netwerk maken.

In deze sectie moet u de volgende para meters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<de naam van het virtuele netwerk>** | myVNet          |
| **\<regio-naam>**          | VS - oost 2      |
| **\<IPv4-adres ruimte>**   | 192.168.0.0 \ 16          |
| **\<>van subnet naam**          | mySubnet        |
| **\<>van het subnet-adres bereik** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Een virtuele machine maken voor het gebruik van de NAT-gateway

We gaan nu een VM maken voor het gebruik van de NAT-service. Deze VM heeft een openbaar IP-adres dat kan worden gebruikt als een openbaar IP-adres op exemplaar niveau om toegang te krijgen tot de virtuele machine. De NAT-service is stroom richting, en vervangt de standaard Internet bestemming in uw subnet. Het open bare IP-adres van de virtuele machine wordt niet gebruikt voor uitgaande verbindingen.

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource** > **Compute** > **Ubuntu Server 18,04 LTS**maken of zoek naar **Ubuntu Server 18,04 LTS** in de zoek functie voor Marketplace.

2. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnements** > **resource groep**: Selecteer **myResourceGroupNAT**.
   - **Details** > van de naam van de**virtuele machine**: Typ **myVM**.
   - **Exemplaar Details** > **regio** > Selecteer **VS Oost 2**.
   - **Administrator account** > **Verificatie type**van Administrator-account: Selecteer **wacht woord**.
   - **Beheerders account** > Geef de **gebruikers naam**, het **wacht woord**en de **wachtwoord** gegevens op.
   - **Binnenkomende poort regels** > **open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Binnenkomende poort regels** > **Selecteer binnenkomende poorten**: Selecteer **SSH (22)**
   - Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.

3. Controleer op het tabblad **netwerken** of het volgende is geselecteerd:
   - **Virtueel netwerk**: **myVnet**
   - **Subnet**: **mySubnet**
   - **Open bare IP-** > Selecteer **nieuwe maken**.  Typ **myPublicIPVM** in het veld **naam** in het venster **openbaar IP-adres maken** en kies **standaard** voor de **SKU**.  Klik op **OK**.
   - **NIC-netwerk beveiligings groep**: Selecteer **basis**.
   - **Open bare binnenkomende poorten**: Selecteer **geselecteerde poorten toestaan**.
   - **Selecteer binnenkomende poorten**: Bevestig **SSH** is geselecteerd.

4. Stel op het tabblad **beheer** , onder **bewaking**, **Diagnostische gegevens over opstarten** in op **uit**.

5. Selecteer **controleren + maken**. 

6. Controleer de instellingen en klik op **maken**.

## <a name="create-the-nat-gateway"></a>De NAT-gateway maken

U kunt een of meer open bare IP-adres bronnen, open bare IP-voor voegsels of beide gebruiken. We voegen een open bare IP-resource, een openbaar IP-voor voegsel en een NAT-gateway bron toe.

In deze sectie wordt beschreven hoe u de volgende onderdelen van de NAT-service kunt maken en configureren met behulp van de NAT-gateway resource:
  - Een open bare IP-adres groep en een openbaar IP-voor voegsel dat moet worden gebruikt voor uitgaande stromen die worden vertaald door de NAT-gateway resource.
  - Wijzig de time-out voor inactiviteit van de standaard waarde van 4 minuten in 10 minuten.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource** > **netwerk** > **openbaar IP-adres**maken of zoek naar een **openbaar IP-adres** in de zoek actie voor Marketplace.

2. Voer in **openbaar IP-adres maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | IP-versie | Selecteer **IPv4**.
    | SKU | Selecteer **standaard**.
    | Naam | Voer **myPublicIP**in. |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | Locatie | Selecteer **VS - oost 2**.|

3. Laat de overige standaardwaarden staan en selecteer **Maken**.

### <a name="create-a-public-ip-prefix"></a>Een openbaar IP-voor voegsel maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource** > **netwerk** > **openbaar IP-voor voegsel**maken of zoek naar een **openbaar IP-voor voegsel** in de zoek opdracht voor Marketplace. 

2. Typ of Selecteer in het **voor voegsel een openbaar IP-adres maken**de volgende waarden op het tabblad **basis beginselen** :
   - **Abonnements** > **resource groep**: Selecteer **myResourceGroupNAT**>
   - **Instance details** > **Naam**van instantie Details: Typ **myPublicIPprefix**.
   - **Exemplaar Details** > **regio**: Selecteer **VS Oost 2**.
   - **Instance details** > **Grootte van het voor voegsel**van instantie Details: Select **/31 (2 adressen)**

3. Laat de overige standaard instellingen ongewijzigd en selecteer **controleren + maken**.

4. Controleer de instellingen en selecteer vervolgens **Maken**.
   

### <a name="create-a-nat-gateway-resource"></a>Een NAT-gateway resource maken

1. Selecteer in de linkerbovenhoek van de Portal de optie **een resource** > **netwerk** > **NAT gateway**of zoek naar de NAT- **Gateway** in de zoek functie op Marketplace.

2. Typ of Selecteer in de **Gateway Create Network Address Translation (NAT)** de volgende waarden op het tabblad **basis beginselen** :
   - **Abonnements** > **resource groep**: Selecteer **myResourceGroupNAT**.
   - **Exemplaar Details** > **NAT-gateway naam**: Typ **myNATgateway**.
   - **Exemplaar Details** > **regio**: Selecteer **VS Oost 2**.
   - **Instance details** > **Time-out voor exemplaar gegevens inactiviteit (minuten)**: type **10**.
   - Selecteer het tabblad **openbaar IP-adres** of selecteer **volgende: openbaar IP-adres**.

3. Typ of selecteer de volgende waarden op het tabblad **openbaar IP** :
   - **Open bare IP-adressen**: Selecteer **myPublicIP**.
   - Voor **voegsels van open bare IP-adressen**: Selecteer **myPublicIPprefix**.
   - Selecteer het tabblad **subnet** of selecteer **volgende: subnet**.

4. Op het tabblad **subnet** typt of selecteert u de volgende waarden:
   - **Virtual Network**: Selecteer **myResourceGroupNAT** > **myVnet**.
   - **Subnetnaam**: Schakel het selectie vakje in naast **mySubnet**.

5. Selecteer **controleren + maken**.

6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="discover-the-ip-address-of-the-vm"></a>Het IP-adres van de virtuele machine detecteren

1. Selecteer op de linkerkant van de portal **resource groepen**.
2. Selecteer **myResourceGroupNAT**.
3. Selecteer **myVM**.
4. Kopieer in het **overzicht**de waarde voor het **open bare IP-adres** en plak deze in Klad blok, zodat u deze kunt gebruiken om toegang te krijgen tot de virtuele machine.

>[!IMPORTANT]
>Kopieer het open bare IP-adres en plak het in een Klad blok, zodat u het kunt gebruiken om toegang te krijgen tot de virtuele machine.

## <a name="sign-in-to-vm"></a>Aanmelden bij de VM

Open een [Azure Cloud shell](https://shell.azure.com) in uw browser. Gebruik het IP-adres dat in de vorige stap is opgehaald om SSH naar de virtuele machine te gaan.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

U bent nu klaar om de NAT-service te gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze niet meer nodig hebt, verwijdert u de resource groep, de NAT-gateway en alle gerelateerde resources. Selecteer de **myResourceGroupNAT** van de resource groep die de NAT-gateway bevat en selecteer vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een NAT-gateway en een virtuele machine gemaakt om deze te gebruiken. 

Controleer de metrische gegevens in Azure Monitor om uw NAT-service weer te geven. Problemen vaststellen, zoals de bron uitputting van de beschik bare SNAT-poorten.  Bron uitputting van de SNAT-poorten wordt opgelost door extra open bare IP-adres bronnen of open bare IP-prefix bronnen of beide toe te voegen.


- Meer informatie over [Azure Virtual Network NAT](./nat-overview.md)
- Meer informatie over de [NAT gateway-resource](./nat-gateway-resource.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure cli](./quickstart-create-nat-gateway-cli.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Quick start voor het implementeren van [NAT-gateway resource met behulp van Azure Portal](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

