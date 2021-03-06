---
title: 'Quick Start: een toepassing starten met maven met Azure veer Cloud'
description: In deze Snelstartgids start u een voorbeeld toepassing met behulp van Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: f374e0611b887e2a2c787569d9fe58bc1a5c84e9
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594063"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snelstartgids: een Azure lente-Cloud-app starten met de Maven-invoeg toepassing

Met de Azure veer Cloud maven-invoeg toepassing kunt u eenvoudig uw Azure lente-Cloud toepassingen maken en bijwerken. Door een configuratie vooraf te definiëren, kunt u toepassingen implementeren in uw bestaande Azure lente-Cloud service. In dit artikel gebruikt u een voorbeeld toepassing met de naam PiggyMetrics om deze functie te demonstreren.

In deze Quick Start leert u het volgende:

> [!div class="checklist"]
> * Een service-exemplaar inrichten
> * Een configuratie server voor een exemplaar instellen
> * Micro Services-toepassing lokaal klonen en bouwen
> * Implementeer elke micro service
> * Een openbaar eind punt toewijzen voor uw toepassing

>[!Note]
> Azure lente Cloud wordt momenteel aangeboden als een open bare preview. Met open bare preview-aanbiedingen kunnen klanten experimenteren met nieuwe functies vóór hun officiële release.  Open bare preview-functies en-services zijn niet bedoeld voor gebruik in productie omgevingen.  Raadpleeg voor meer informatie over ondersteuning tijdens previews onze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/) of bestand a [ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) voor meer informatie.


>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de opdrachten in dit artikel uit te voeren. Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, de Java Development Kit (JDK), maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u [Azure Cloud shell](https://shell.azure.com). Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Dit zijn de vereisten voor het voltooien van deze snelstart:

1. [Installeer Git](https://git-scm.com/).
2. [Installeer jdk 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installeer Maven 3,0 of hoger](https://maven.apache.org/download.cgi).
4. [Meld u aan voor een gratis Azure-abonnement](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open in een webbrowser [deze koppeling naar Azure lente-Cloud in het Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)en meld u aan bij uw account.

1. Geef de **Project Details** voor de voorbeeld toepassing als volgt op:

    1. Selecteer het **abonnement** waarmee de toepassing wordt gekoppeld.
    1. Selecteer of maak een resource groep voor de toepassing. We raden u aan een nieuwe resource groep te maken.  In het onderstaande voor beeld ziet u een nieuwe `myspringservice`resource groep met de naam.
    1. Geef een naam op voor de nieuwe Azure lente-Cloud service.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.  De service in het onderstaande voor beeld heeft de `contosospringcloud`naam.
    1. Selecteer een locatie voor uw toepassing uit de beschik bare opties.  In dit voor beeld selecteren `East US`we.
    1. Selecteer **beoordeling + maken** om een samen vatting van de nieuwe service te bekijken.  Als alles er goed uitziet, selecteert u **maken**.

    > [!div class="mx-imgBorder"]
    > ![Selecteer controleren + maken](media/maven-qs-review-create.jpg)

Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd. Nadat de service is geïmplementeerd, selecteert **u Ga naar resource** en de pagina **overzicht** voor het service-exemplaar wordt weer gegeven.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Selecteer op de pagina service **overzicht** de optie **Configuratie server**.
1. Stel in de sectie **standaard opslagplaats** de **URI** optie URI **https://github.com/Azure-Samples/piggymetrics-config**in op en selecteer vervolgens **Toep assen** om uw wijzigingen op te slaan.

    > [!div class="mx-imgBorder"]
    > ![Configuratie-instellingen definiëren en Toep assen](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>De voor beeld-toepassings opslagplaats klonen en bouwen

1. Start de [Azure Cloud shell](https://shell.azure.com).

1. Kloon de Git-opslag plaats door de volgende opdracht uit te voeren:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Configuraties genereren en implementeren in de Azure lente-Cloud

1. Genereer configuraties door de volgende opdracht uit te voeren in de hoofdmap van PiggyMetrics met de bovenliggende POM:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Selecteer de modules `gateway`,`auth-service`en `account-service`.

    b. Selecteer uw abonnement en Azure lente-Cloud service cluster.

    c. Voer in de lijst met opgegeven projecten het nummer in dat overeenkomt `gateway` met om de open bare toegang te geven.
    
    d. Bevestig de configuratie.

1. De POM bevat nu de afhankelijkheden en configuraties van de invoeg toepassing. Implementeer de apps met behulp van de volgende opdracht:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Nadat de implementatie is voltooid, kunt u toegang krijgen tot PiggyMetrics met behulp van de URL die wordt vermeld in de uitvoer van de voor gaande opdracht.

> [!div class="nextstepaction"]
> [Ik heb een probleem ondertreden](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lente-Cloud toepassing geïmplementeerd vanuit een Maven-opslag plaats. Voor meer informatie over Azure lente-Cloud gaat u verder met de zelf studie over het voorbereiden van uw app voor implementatie.

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
> meer[informatie over maven-invoeg toepassingen voor Azure](https://github.com/microsoft/azure-maven-plugins)

Meer voor beelden zijn beschikbaar op GitHub: [Azure lente-Cloud voorbeelden](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
