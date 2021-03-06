---
title: Registreren voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u zich registreert om Azure NetApp Files te gebruiken.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79274058"
---
# <a name="register-for-azure-netapp-files"></a>Registreren voor Azure NetApp Files

> [!IMPORTANT] 
> Voordat u de Azure NetApp Files Resource provider registreert, moet u een e-mail adres hebben ontvangen van het Azure NetApp Files team bevestigen dat u toegang hebt gekregen tot de service. 

In dit artikel wordt beschreven hoe u zich registreert voor Azure NetApp Files, zodat u de service kunt gaan gebruiken.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Een Waitlist-aanvraag indienen voor toegang tot de service

1. Een Waitlist-aanvraag indienen om toegang te krijgen tot de Azure NetApp Files-service via de [inzendings pagina van Azure NetApp files Waitlist](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Waitlist-aanmelding biedt geen garantie voor directe toegang tot de service. 

2. Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u doorgaat met andere taken. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>De NetApp-resourceprovider registreren

Voor het gebruik van de service moet u de Azure-resourceprovider registreren voor Azure NetApp Files.

> [!NOTE] 
> U kunt de NetApp-resource provider ook registreren zonder dat hiervoor toegang is verleend voor de service. Zonder toegangs autorisatie wordt elke Azure Portal-of API-aanvraag voor het maken van een NetApp-account of een andere Azure NetApp Files resource echter afgewezen met de volgende fout:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Klik op het pictogram voor Azure Cloud Shell in de rechterbovenhoek van Azure Portal:

      ![Pictogram voor Azure Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Als u meerdere abonnementen voor uw Azure-account hebt, selecteert u het abonnement dat is opgenomen in de whitelist voor Azure NetApp Files:
    
        az account set --subscription <subscriptionId>

3. Voer de volgende opdracht uit in de Azure Cloud Shell-console om te controleren of uw abonnement in de whitelist is opgenomen:
    
        az feature list | grep NetApp

   De uitvoer van de opdracht ziet er als volgt uit:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` is uw abonnements-id.

    Als u de naam `Microsoft.NetApp/ANFGA`van de functie niet ziet, hebt u geen toegang tot de service. Stop bij deze stap. Volg de instructies in [een Waitlist-aanvraag verzenden om toegang te krijgen tot de service](#waitlist) om service toegang aan te vragen voordat u doorgaat. 

4. Voer de volgende opdracht uit om de Azure-resourceprovider te registreren in de Azure Cloud Shell-console: 
    
        az provider register --namespace Microsoft.NetApp --wait

   Met de parameter `--wait` wordt de console geïnstrueerd te wachten totdat het registratieproces is voltooid. Dit kan enige tijd duren.

5. Voer de volgende opdracht uit in de Azure Cloud Shell-console om te controleren of de Azure-resourceprovider is geregistreerd: 
    
        az provider show --namespace Microsoft.NetApp

   De uitvoer van de opdracht ziet er als volgt uit:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` is uw abonnements-id.  De parameterwaarde `state` geeft `Registered` aan.

6. Klik in Azure Portal op de blade **Abonnementen**.
7. Klik in de blade Abonnementen op uw abonnements-id. 
8. Klik in de instellingen van het abonnement op **Resourceproviders** om te controleren of de Microsoft.NetApp-provider de status Geregistreerd heeft: 

      ![Microsoft.NetApp geregistreerd](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Volgende stappen

[Een NetApp-account maken](azure-netapp-files-create-netapp-account.md)