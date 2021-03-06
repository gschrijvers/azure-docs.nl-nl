---
title: 'Zelf studie: uw virtuele WAN beveiligen met Azure Firewall Manager-preview'
description: In deze zelf studie leert u hoe u uw virtuele WAN kunt beveiligen met Azure Firewall Manager met behulp van de Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: victorh
ms.openlocfilehash: b13f3b4eeb57c34f51152bb6d1914f6c80f31be1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691023"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Zelf studie: uw virtuele WAN beveiligen met Azure Firewall Manager-preview 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Met Azure Firewall Manager preview kunt u beveiligde virtuele hubs maken voor het beveiligen van uw Cloud netwerk verkeer dat is bestemd voor privé-IP-adressen, Azure PaaS en het internet. Verkeer routeren naar de firewall is geautomatiseerd, dus u hoeft geen door de gebruiker gedefinieerde routes (Udr's) te maken.

![het Cloud netwerk beveiligen](media/secure-cloud-network/secure-cloud-network.png)

Firewall beheer biedt ook ondersteuning voor de architectuur van een virtuele hub-netwerk. Zie [Wat zijn de opties voor de Azure firewall Manager-architectuur?](vhubs-and-vnets.md) voor een vergelijking van de beveiligde virtuele hub-en hub-architectuur typen van het virtuele netwerk.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het virtuele spoke-netwerk maken
> * Een beveiligde virtuele hub maken
> * De hub en spoke VNets verbinden
> * Een firewall beleid maken en uw hub beveiligen
> * Verkeer naar uw hub routeren
> * De firewall testen

## <a name="create-a-hub-and-spoke-architecture"></a>Een hub-en spoke-architectuur maken

Maak eerst een spoke-virtueel netwerk waar u uw servers kunt plaatsen.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Een spoke-virtueel netwerk en-subnetten maken

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
2. Onder **netwerken**, selecteert u **virtueel netwerk**.
2. Bij **Abonnement** selecteert u uw abonnement.
1. Voor **resource groep**selecteert u **nieuwe maken**en typt u **FW-Manager** voor de naam en selecteert u **OK**.
2. Typ voor **naam** **spoke-01**.
3. Selecteer voor **regio** **(VS) vs-Oost**.
4. Selecteer **volgende: IP-adressen**.
1. Accepteer voor **adres ruimte**de standaard **10.0.0.0/16**.
3. Selecteer bij **subnetnaam**de optie **standaard**.
4. Wijzig de naam van het subnet in **workload-SN**.
5. Typ **10.0.1.0/24**voor het **adres bereik**van het subnet.
6. Selecteer **Opslaan**..

Maak vervolgens een subnet voor een jumper server.

1. Selecteer **subnet toevoegen**.
4. Typ voor **subnet naam** **Jump-SN**.
5. Typ **10.0.2.0/24**voor het **adres bereik**van het subnet.
6. Selecteer **Toevoegen**.

Maak nu het virtuele netwerk.

1. Selecteer **controleren + maken**.
2. Selecteer **Maken**.

### <a name="create-the-secured-virtual-hub"></a>De beveiligde virtuele hub maken

Maak uw beveiligde virtuele hub met behulp van Firewall-beheer.

1. Selecteer op de start pagina van Azure Portal **alle services**.
2. Typ in het zoekvak **firewall beheer** en Selecteer Firewall- **Manager**.
3. Op de pagina **firewall beheer** selecteert u **beveiligde virtuele hubs weer geven**.
4. In **firewall beheer | Pagina beveiligde virtuele hubs** , selecteer **nieuwe beveiligde virtuele hub maken**.
5. Selecteer voor **resource groep** **FW-Manager**.
7. Selecteer voor **regio** **VS Oost**.
1. Typ **hub-01**voor de naam van de **beveiligde virtuele hub**.
2. Voor de **adres ruimte**van de hub typt u **10.1.0.0/16**.
3. Typ **vWAN-01**voor de nieuwe vWAN-naam.
4. Laat het selectie vakje **VPN-gateway toevoegen om vertrouwde beveiligings partners in te scha kelen** uitgeschakeld.
5. Selecteer **volgende: Azure firewall**.
6. Accepteer de standaard instelling voor **Azure firewall** **ingeschakeld** en selecteer **volgende: vertrouwde beveiligings partner**.
7. Accepteer de standaard instelling voor **vertrouwde beveiligings partner** **uitgeschakeld** en selecteer **volgende: controleren + maken**.
8. Selecteer **Maken**. Het duurt ongeveer 30 minuten om te implementeren.

### <a name="connect-the-hub-and-spoke-vnets"></a>De hub en spoke VNets verbinden

Nu kunt u de hub en spoke VNets peeren.

1. Selecteer de resource groep **FW-Manager** en selecteer vervolgens de **Vwan-01** virtuele WAN.
2. Onder **connectiviteit**selecteert u **virtuele netwerk verbindingen**.
3. Selecteer **verbinding toevoegen**.
4. Typ **hub-spoke**bij **verbindings naam**.
5. Selecteer voor **hubs** **hub-01**.
6. Selecteer voor **resource groep** **FW-Manager**.
7. Selecteer voor **virtueel netwerk** **spoke-01**.
8. Selecteer **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Een firewall beleid maken en uw hub beveiligen

Een firewall beleid definieert verzamelingen regels voor het omleiden van verkeer op een of meer beveiligde virtuele hubs. U maakt uw firewall beleid en beveilig vervolgens uw hub.

1. Selecteer in Firewall beheer **Azure firewall-beleid weer geven**.
2. Selecteer **Azure firewall beleid maken**.
3. Onder **beleids Details**, voor het **naam** type **beleid-01** en voor **regio** Selecteer **VS-Oost**.
4. Selecteer **volgende: regels**.
5. Selecteer op het tabblad **regels** **een regel verzameling toevoegen**.
6. Typ op de pagina **een verzameling van regels toevoegen** **RC-01** voor de **naam**.
7. Selecteer voor **regel verzamelings type**de optie **toepassing**.
8. Typ **100**bij **prioriteit**.
9. Zorg ervoor dat de actie voor de **regel verzameling** is **toegestaan**.
10. Voor het regel **naam** type **toestaan-MSFT**.
11. Typ **\*** voor het **bron adres**.
12. Typ **http, https**voor **protocol**.
13. Zorg ervoor dat het bestemmings type * * **FQDN**is.
14. **Typ \*. Microsoft.com**bij **doel**.
15. Selecteer **Toevoegen**.
16. Selecteer **volgende: hubs**.
17. Op het tabblad **hubs** selecteert u **virtuele hubs koppelen**.
18. Selecteer **hub-01** en selecteer vervolgens **toevoegen**.
1. Selecteer **controleren + maken**.
2. Selecteer **Maken**.

Dit kan vijf minuten of langer duren.

## <a name="route-traffic-to-your-hub"></a>Verkeer naar uw hub routeren

Nu moet u ervoor zorgen dat het netwerk verkeer via uw firewall wordt gerouteerd.

1. Selecteer in Firewall beheer **beveiligde virtuele hubs**.
2. Selecteer **hub-01**.
3. Selecteer bij **instellingen**de optie **route-instellingen**.
4. Onder **Internet verkeer**, **verkeer van virtuele netwerken**, selecteert u **verzenden via Azure firewall**.
5. Onder **Azure private Traffic**, **verkeer naar virtuele netwerken**, selecteert u **verzenden via Azure firewall**.
6. Selecteer **IP-adres voorvoegsel (sen) bewerken**.
8. Typ **10.0.1.0/24** als adres van het subnet van de workload en selecteer **Opslaan**.
9. Onder **instellingen**selecteert u **verbindingen**.
10. Controleer of de **hub-spoke-** verbinding **Internet verkeer** als **beveiligd**weergeeft.


## <a name="test-your-firewall"></a>Uw firewall testen

Als u uw firewall regels wilt testen, moet u een paar servers implementeren. U implementeert workload-SRV in het workload-SN-subnet om de firewall regels te testen en sprong-SRV zodat u Extern bureaublad kunt gebruiken om verbinding te maken via internet en vervolgens verbinding te maken met Workload-SRV.

### <a name="deploy-the-servers"></a>De servers implementeren

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer **Windows Server 2016 Data Center** in de lijst **populair** .
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**FW-Manager**|
   |Naam van de virtuele machine     |**Sprong-SRV**|
   |Regio     |**VS VS-Oost)**|
   |Gebruikers naam van beheerder     |Typ een gebruikers naam|
   |Wachtwoord     |Typ een wacht woord|

4. Onder **Binnenkomende poort regels**, voor **open bare binnenkomende poorten**, selecteert u **geselecteerde poorten toestaan**.
5. Selecteer **RDP (3389)** voor **Binnenkomende poorten selecteren**.
6. Accepteer de andere standaard waarden en selecteer **volgende: schijven**.
7. Accepteer de standaard waarden voor de schijf en selecteer **volgende: netwerken**.
8. Zorg ervoor dat **spoke-01** is geselecteerd voor het virtuele netwerk en dat het subnet **Jump-SN**is.
9. Accepteer voor **openbaar IP**de standaard naam van het nieuwe open bare IP-adres (Jump-SRV-IP).
11. Accepteer de andere standaard waarden en selecteer **volgende: beheer**.
12. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen. Accepteer de andere standaard waarden en selecteer **controleren + maken**.
13. Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine met de naam **workload-SRV**te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine Srv-Jump.

|Instelling  |Waarde  |
|---------|---------|
|Subnet|**Workload-SN**|
|Openbare IP|**Geen**|
|Openbare poorten voor inkomend verkeer|**Geen**|

### <a name="add-a-route-table-and-default-route"></a>Een route tabel en standaard route toevoegen

Als u een Internet verbinding met sprong-SRV wilt toestaan, moet u een route tabel en een standaard gateway route naar Internet maken vanuit het **Jump-SN-** subnet.

1. Selecteer **Een resource maken** in de Azure-portal.
2. Typ **route tabel** in het zoekvak en selecteer vervolgens **route tabel**.
3. Selecteer **Maken**.
4. Typ **RT-01** als **naam**.
5. Selecteer uw abonnement, **FW-Manager** voor de resource groep en **(VS) vs-Oost** voor de regio.
6. Selecteer **Maken**.
7. Wanneer de implementatie is voltooid, selecteert u de **RT-01-** route tabel.
8. Selecteer **routes** en selecteer vervolgens **toevoegen**.
9. Typ **Jump-to-inet** voor de **route naam**.
10. Typ **0.0.0.0/0** voor het **adres voorvoegsel**.
11. Selecteer **Internet** voor het **type van de volgende hop**.
12. Selecteer **OK**.
13. Wanneer de implementatie is voltooid, selecteert u **subnetten**en selecteert u vervolgens **koppelen**.
14. Selecteer **spoke-01** voor het **virtuele netwerk**.
15. Selecteer **Jump-SN** voor **subnet**.
16. Selecteer **OK**.

### <a name="test-the-rules"></a>De regels testen

Test nu de firewall regels om te controleren of deze werkt zoals verwacht.

1. Controleer de netwerk instellingen voor de virtuele machine van de **werk belasting-SRV** en noteer het privé-IP-adres uit het Azure Portal.
2. Verbind een extern bureau blad met een virtuele machine met **Hyper-SRV** en meld u aan. Open vanaf daar een verbinding met een extern bureau blad met het privé IP-adres van de **werk belasting-SRV** .

3. Open Internet Explorer en blader naar https://www.microsoft.com.
4. Selecteer **OK** > **sluiten** op de beveiligings waarschuwingen van Internet Explorer.

   De start pagina van micro soft wordt weer geven.

5. Blader naar https://www.google.com.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu hebt u gecontroleerd of de firewall regels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligings partners](trusted-security-partners.md)
