---
title: Azure Service Bus integreren met Azure Private Link service
description: Meer informatie over het integreren van Azure Service Bus met Azure Private Link service
services: service-bus-messaging
author: spelluru
ms.author: spelluru
ms.date: 03/13/2020
ms.service: service-bus-messaging
ms.topic: article
ms.openlocfilehash: a78375a3acf5c56d9a59c0f4b6113a063f8c431a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650963"
---
# <a name="integrate-azure-service-bus-with-azure-private-link"></a>Azure Service Bus integreren met een persoonlijke Azure-koppeling

Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Service Bus, Azure Storage en Azure Cosmos DB) en Azure hostende klanten/partner services via een **persoonlijk eind punt** in uw virtuele netwerk.

Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het persoonlijke eind punt maakt gebruik van een privé-IP-adres uit uw VNet, waardoor de service effectief in uw VNet wordt gezet. Al het verkeer naar de service kan worden gerouteerd via het persoonlijke eind punt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of open bare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulatie niveau krijgt in toegangs beheer.

Zie [Wat is Azure private link?](../private-link/private-link-overview.md) voor meer informatie.

>[!WARNING]
> Het implementeren van persoonlijke eind punten kan verhinderen dat andere Azure-Services communiceren met Service Bus.
>
> Vertrouwde micro soft-services worden niet ondersteund bij het gebruik van virtuele netwerken.
>
> Algemene scenario's voor Azure die niet met virtuele netwerken werken (Houd er rekening mee dat de lijst **niet** volledig is)-
> - Integratie met Azure Event Grid
> - Azure-IoT Hub routes
> - Azure IoT-Device Explorer
>
> De onderstaande micro soft-services moeten zich in een virtueel netwerk bevinden
> - Azure App Service
> - Azure Functions

> [!IMPORTANT]
> Deze functie wordt ondersteund met de **Premium** -laag van Azure service bus. Zie het artikel [service Buss voor Premium en Standard Messa ging](service-bus-premium-messaging.md) voor meer informatie over de Premium-laag.


## <a name="add-a-private-endpoint-using-azure-portal"></a>Een persoonlijk eind punt toevoegen met Azure Portal

### <a name="prerequisites"></a>Vereisten

Als u een Service Bus naam ruimte met een persoonlijke Azure-koppeling wilt integreren, hebt u de volgende entiteiten of machtigingen nodig:

- Een Service Bus naam ruimte.
- Een virtueel Azure-netwerk.
- Een subnet in het virtuele netwerk.
- Eigenaar-of Inzender machtigingen voor zowel de Service Bus naam ruimte als het virtuele netwerk.

Uw persoonlijke eind punt en het virtuele netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het persoonlijke eind punt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. De naam ruimte van uw Service Bus kan zich in een andere regio bevinden. En uw persoonlijke eind punt maakt gebruik van een privé-IP-adres in uw virtuele netwerk.

### <a name="steps"></a>stappen

Als u al een bestaande naam ruimte hebt, kunt u een persoonlijk eind punt maken door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Typ **Service Bus**in de zoek balk.
3. Selecteer de **naam ruimte** in de lijst waaraan u een persoonlijk eind punt wilt toevoegen.
4. Selecteer het tabblad **netwerken** onder **instellingen**.
5. Selecteer het tabblad **verbindingen met privé-eind punten** boven aan de pagina
6. Selecteer de knop **+ privé-eind punt** boven aan de pagina.

    ![Knop persoonlijk eind punt toevoegen](./media/private-link-service/private-link-service-3.png)
7. Voer de volgende stappen uit op de pagina **basis beginselen** : 
    1. Selecteer het **Azure-abonnement** waarin u het persoonlijke eind punt wilt maken. 
    2. Selecteer de **resource groep** voor de persoonlijke eindpunt resource.
    3. Voer een **naam** in voor het persoonlijke eind punt. 
    5. Selecteer een **regio** voor het persoonlijke eind punt. Uw persoonlijke eind punt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan zich in een andere regio bevinden dan de resource van de persoonlijke koppeling waarmee u verbinding maakt. 
    6. Selecteer **volgende: Resource >** knop onder aan de pagina.

        ![Privé-eind punt maken-pagina basis beginselen](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Voer de volgende stappen uit op de pagina **resource** :
    1. Als u **verbinding maken met een Azure-resource in mijn Directory**selecteert, voert u de volgende stappen uit om de verbindings methode te selecteren:   
        1. Selecteer het **Azure-abonnement** waarin uw **Service Bus naam ruimte** bestaat. 
        2. Selecteer voor **resource type**de optie **micro soft. ServiceBus/naam ruimten** voor het **bron type**.
        3. Selecteer voor **resource**een service bus naam ruimte in de vervolg keuzelijst. 
        4. Controleer of de **doel-subresource** is ingesteld op **naam ruimte**.
        5. Selecteer **volgende: configuratie >** knop onder aan de pagina. 
        
            ![Privé-eind punt maken-resource pagina](./media/private-link-service/create-private-endpoint-resource-page.png)
    2. Als u **verbinding maken met een Azure-resource selecteert op resource-id of alias**, voert u de volgende stappen uit:
        1. Voer de **resource-id** of **alias**in. Dit kan de resource-ID of alias zijn die iemand met u heeft gedeeld. De eenvoudigste manier om de resource-ID op te halen, is door te navigeren naar de Service Bus naam ruimte in de Azure Portal en het gedeelte van de URI te kopiëren vanaf `/subscriptions/` . Zie de volgende afbeelding voor een voor beeld. 
        2. Voer een **naam ruimte**in voor de **subresource**van het doel. Het is het type van de subbron waartoe uw persoonlijke eind punt toegang heeft. 
        3. Beschrijving Voer een **aanvraag bericht**in. De resource-eigenaar ziet dit bericht tijdens het beheer van de verbinding met een privé-eind punt. 
        4. Selecteer vervolgens **volgende: configuratie >** knop onder aan de pagina. 

            ![Persoonlijk eind punt maken-verbinden via Resource-ID](./media/private-link-service/connect-resource-id.png)
9. Selecteer op de pagina **configuratie** het subnet in een virtueel netwerk waarop u het persoonlijke eind punt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. In de vervolg keuzelijst worden alleen virtuele netwerken in het geselecteerde abonnement en de huidige locatie weer gegeven. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **volgende: labels >** knop onder aan de pagina. 

        ![Privé-eind punt maken-configuratie pagina](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Maak op de pagina **Tags** een wille keurige labels (namen en waarden) die u wilt koppelen aan de persoonlijke eindpunt resource. Selecteer vervolgens de knop **controleren + maken** onder aan de pagina. 
11. Controleer alle instellingen in het **overzicht en maken**en selecteer **maken** om het persoonlijke eind punt te maken.
    
    ![Privé-eind punt maken-pagina controleren en maken](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Controleer of het persoonlijke eind punt is gemaakt. Als u de eigenaar van de resource bent en de optie **verbinding maken met een Azure-resource in mijn Directory** hebt geselecteerd voor de **verbindings methode**, moet de eindpunt verbinding **automatisch worden goedgekeurd**. Zie de sectie [privé-eind punten beheren met Azure Portal](#manage-private-endpoints-using-azure-portal) als de status in **behandeling** is.

    ![Persoonlijk eind punt gemaakt](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Een persoonlijk eind punt toevoegen met Power shell
In het volgende voor beeld ziet u hoe u Azure PowerShell kunt gebruiken om een persoonlijke eindpunt verbinding te maken met een Service Bus naam ruimte.

Uw persoonlijke eind punt en het virtuele netwerk moeten zich in dezelfde regio bevinden. De naam ruimte van uw Service Bus kan zich in een andere regio bevinden. En uw persoonlijke eind punt maakt gebruik van een privé-IP-adres in uw virtuele netwerk.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create premium service bus namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Sku @{name = "Premium"; capacity = 1} -Properties @{} -ResourceType "Microsoft.ServiceBus/namespaces" -

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```


## <a name="manage-private-endpoints-using-azure-portal"></a>Privé-eind punten beheren met Azure Portal

Wanneer u een persoonlijk eind punt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die resource uw verbindings aanvraag goed keuren.

Er zijn vier inrichtings provincies:

| Service actie | Status privé-eind punt service gebruiker | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt hand matig gemaakt en in afwachting van goed keuring van de resource-eigenaar van de persoonlijke koppeling. |
| Goedkeuren | Goedgekeurd | De verbinding is automatisch of hand matig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Geweigerd | De verbinding is geweigerd door de resource-eigenaar van de persoonlijke koppeling. |
| Verwijderen | De verbinding verbroken | De verbinding is verwijderd door de resource-eigenaar van de persoonlijke koppeling, het persoonlijke eind punt wordt informatieve en moet worden verwijderd voor opschoning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren, afwijzen of verwijderen

1. Meld u aan bij Azure Portal.
1. Typ **Service Bus**in de zoek balk.
1. Selecteer de **naam ruimte** die u wilt beheren.
1. Selecteer het tabblad **netwerken** .
5. Ga naar de toepasselijke sectie hieronder op basis van de bewerking die u wilt: goed keuren, afwijzen of verwijderen. 

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren

1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met in **behandeling** in de inrichtings status. 
2. Selecteer het **privé-eind punt** dat u wilt goed keuren
3. Selecteer de knop **goed keuren** .

    ![Persoonlijk eind punt goed keuren](./media/private-link-service/private-endpoint-approve.png)
4. Voer op de pagina **verbinding goed keuren** een optionele **Opmerking**in en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 

    ![Verbindings pagina goed keuren](./media/private-link-service/approve-connection-page.png)
5. Als het goed is, ziet u de status van de verbinding in de lijst gewijzigd in **goedgekeurd**. 

    ![Verbindings status-goedgekeurd](./media/private-link-service/connection-status-approved.png)

### <a name="reject-a-private-endpoint-connection"></a>Een persoonlijke eindpunt verbinding weigeren

1. Als er particuliere endpoint-verbindingen zijn die u wilt weigeren, selecteert u de eindpunt verbinding en klikt u op de knop **afwijzen** , ongeacht of deze een aanvraag in behandeling of bestaande verbinding is die eerder is goedgekeurd.

    ![Knop afwijzen](./media/private-link-service/private-endpoint-reject.png)
2. Voer op de pagina **verbinding afwijzen** een optionele opmerking in en selecteer **Ja**. Als u **Nee**selecteert, gebeurt er niets. 

    ![Pagina verbinding weigeren](./media/private-link-service/reject-connection-page.png)
3. U ziet de status van de verbinding in de lijst is gewijzigd **afgewezen**. 

    ![Eind punt geweigerd](./media/private-link-service/endpoint-rejected.png)


### <a name="remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt verwijderen

1. Als u een verbinding met een privé-eind punt wilt verwijderen, selecteert u deze in de lijst en selecteert u **verwijderen** op de werk balk. 

    ![Knop verwijderen](./media/private-link-service/remove-endpoint.png)
2. Selecteer op de pagina **verbinding verwijderen** de optie **Ja** om het verwijderen van het persoonlijke eind punt te bevestigen. Als u **Nee**selecteert, gebeurt er niets. 

    ![Verbindings pagina verwijderen](./media/private-link-service/delete-connection-page.png)
3. U ziet dat de status is gewijzigd in **verbroken**. Vervolgens ziet u dat het eind punt verdwijnt uit de lijst. 

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de persoonlijke verbinding werkt

U moet controleren of de resources binnen hetzelfde subnet van de persoonlijke eindpunt resource verbinding maken met uw Service Bus naam ruimte via een privé-IP-adres en dat ze de juiste integratie van de persoonlijke DNS-zone hebben.

Maak eerst een virtuele machine aan de hand van de stappen in [een virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Op het tabblad **netwerk** : 

1. Geef het **virtuele netwerk** en het **subnet**op. U moet het Virtual Network selecteren waarop u het persoonlijke eind punt hebt geïmplementeerd.
2. Geef een **open bare IP-** resource op.
3. Selecteer voor **NIC-netwerk beveiligings groep**de optie **geen**.
4. Selecteer **Nee**voor **taak verdeling**.

Maak verbinding met de virtuele machine, open de opdracht regel en voer de volgende opdracht uit:

```console
nslookup <service-bus-namespace-name>.servicebus.windows.net
```

U ziet een resultaat dat er als volgt uitziet. 

```console
Non-authoritative answer:
Name:    <service-bus-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <service-bus-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerp overwegingen

**Prijzen**: Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link/)voor prijs informatie.

**Beperkingen**: deze functie is beschikbaar in alle open bare Azure-regio's.

**Maximum aantal privé-eind punten per service bus naam ruimte**: 120.

Zie voor meer informatie [Azure Private Link service: beperkingen](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [persoonlijke Azure-koppelingen](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure service bus](service-bus-messaging-overview.md)
