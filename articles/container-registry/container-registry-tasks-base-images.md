---
title: Basis installatie kopie-updates-taken
description: Meer informatie over basis installatie kopieën voor installatie kopieën van de toepassings container en over hoe een update van een basis installatie kopie een Azure Container Registry taak kan activeren.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77617929"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Over updates van basis installatie kopieën voor ACR-taken

Dit artikel bevat achtergrond informatie over updates voor de basis installatie kopie van een toepassing en hoe deze updates een Azure Container Registry taak kunnen activeren.

## <a name="what-are-base-images"></a>Wat zijn basis installatie kopieën?

Dockerfiles het definiëren van de meeste container installatie kopieën geven een bovenliggende installatie kopie op van waaruit de installatie kopie is gebaseerd, dikwijls aangeduid met de *basis installatie kopie*. Basisinstallatiekopieën bevatten doorgaans het besturingssysteem, bijvoorbeeld [Alpine Linux][base-alpine] of [Windows Nano Server][base-windows], waarop de overige lagen van de container worden toegepast. Ook kunnen basisinstallatiekopieën toepassingsframeworks zoals [Node.js][base-node] of [.NET Core][base-dotnet] bevatten. Deze basis installatie kopieën zijn gewoonlijk gebaseerd op open bare upstream-installatie kopieën. Diverse installatie kopieën van uw toepassing kunnen een gemeen schappelijke basis installatie kopie delen.

Een basisinstallatiekopie wordt vaak door de installatiekopie-maintainer bijgewerkt om nieuwe functies of verbeteringen toe te voegen aan het besturingssysteem of framework in de installatiekopie. Beveiligingspatches zijn een andere veelvoorkomende oorzaak van een update van de basisinstallatiekopie. Wanneer deze upstream-updates worden uitgevoerd, moet u ook uw basis installatie kopieën bijwerken om de essentiële oplossing op te nemen. Elke toepassings installatie kopie moet vervolgens ook opnieuw worden opgebouwd om deze upstream-oplossingen op te nemen die nu zijn opgenomen in uw basis installatie kopie.

In sommige gevallen, zoals een team van een persoonlijke ontwikkeling, kan een basis installatie kopie meer dan het besturings systeem of het Framework opgeven. Een basis installatie kopie kan bijvoorbeeld een installatie kopie van een gedeeld service onderdeel zijn die moet worden bijgehouden. Leden van een team moeten deze basis afbeelding volgen voor het testen of moeten de installatie kopie regel matig bijwerken bij het ontwikkelen van installatie kopieën van toepassingen.

## <a name="track-base-image-updates"></a>Updates van basis installatie kopieën bijhouden

Met ACR Tasks hebt u de mogelijkheid om automatisch installatiekopieën te maken wanneer een containerinstallatiekopie wordt bijgewerkt.

Met ACR-taken worden afhankelijkheden van basis installatie kopieën dynamisch gedetecteerd bij het samen stellen van een container installatie kopie. Als gevolg hiervan kan worden gedetecteerd wanneer de basis installatie kopie van een toepassings afbeelding wordt bijgewerkt. Met één vooraf geconfigureerde build-taak kan ACR-taken elke toepassings installatie kopie die verwijst naar de basis installatie kopie automatisch opnieuw samen stellen. Met deze automatische detectie en het opnieuw samen stellen van ACR-taken bespaart u de tijd en inspanningen die normaal gesp roken nodig zijn voor het hand matig bijhouden en bijwerken van elke toepassings installatie kopie die verwijst naar de bijgewerkte basis installatie kopie.

## <a name="base-image-locations"></a>Basis installatie kopie locaties

Voor installatie kopieën die zijn gebaseerd op een Dockerfile, detecteert een ACR-taak afhankelijkheden op basis installatie kopieën op de volgende locaties:

* Hetzelfde Azure container Registry waarin de taak wordt uitgevoerd
* Een ander persoonlijk Azure-container register in dezelfde of een andere regio 
* Een open bare opslag plaats in docker hub 
* Een open bare opslag plaats in micro soft Container Registry

Als de basis installatie kopie die in `FROM` de instructie is opgegeven, zich op een van deze locaties bevindt, voegt de ACR-taak een hook toe om ervoor te zorgen dat de installatie kopie opnieuw wordt opgebouwd op het moment dat de basis wordt bijgewerkt.

## <a name="additional-considerations"></a>Aanvullende overwegingen

* **Basis installatie kopieën voor toepassings installatie** kopieën: op dit moment wordt met een ACR-taak alleen de basis installatie kopieën van updates voor de toepassing (*runtime*) bijgehouden. Updates voor tussenliggende installatie kopieën (*buildtime*) die worden gebruikt in de multi-fase Dockerfiles, worden niet bijgehouden.  

* **Standaard ingeschakeld** : wanneer u een ACR-taak maakt met de opdracht [AZ ACR Task Create][az-acr-task-create] , wordt de taak standaard *ingeschakeld* voor trigger door een update van de basis installatie kopie. Dat wil zeggen, `base-image-trigger-enabled` de eigenschap is ingesteld op True. Als u dit gedrag in een taak wilt uitschakelen, werkt u de eigenschap bij op ONWAAR. Voer bijvoorbeeld de volgende opdracht [AZ ACR Task update][az-acr-task-update] uit:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Trigger voor het bijhouden van afhankelijkheden** : om een ACR-taak in te scha kelen om de afhankelijkheden van een container installatie kopie te bepalen en bij te houden, zoals de basis installatie kopie, moet u eerst de taak activeren om de installatie kopie **ten minste één keer**te bouwen. Activeer de taak bijvoorbeeld hand matig met behulp van de opdracht [AZ ACR Task run][az-acr-task-run] .

* **Stabiele tag voor basis installatie kopie** : als u een taak wilt activeren bij het bijwerken van de basis installatie kopie *stable* , moet de basis installatie `node:9-alpine`kopie een stabiele tag hebben, zoals. Deze code ring is gebruikelijk voor een basis installatie kopie die wordt bijgewerkt met OS-en Framework patches naar een laatste stabiele release. Als de basis installatie kopie wordt bijgewerkt met een nieuwe versie label, wordt er geen taak geactiveerd. Zie de [Best practices-richt lijnen](container-registry-image-tag-version.md)voor meer informatie over afbeeldings codes. 

* **Andere taak triggers** : in een taak die wordt geactiveerd door basis installatie kopie-updates, kunt u triggers ook inschakelen op basis van de [door Voer van de bron code](container-registry-tutorial-build-task.md) of [een planning](container-registry-tasks-scheduled.md). Een basis installatie kopie-update kan ook een [taak met meerdere stappen](container-registry-tasks-multi-step.md)activeren.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende zelf studies voor scenario's voor het automatiseren van installatie kopieën van toepassingen nadat een basis installatie kopie is bijgewerkt:

* [De build van container installatie kopieën automatiseren wanneer een basis installatie kopie in hetzelfde REGI ster wordt bijgewerkt](container-registry-tutorial-base-image-update.md)

* [De build van container installatie kopieën automatiseren wanneer een basis installatie kopie in een ander REGI ster wordt bijgewerkt](container-registry-tutorial-base-image-update.md)


<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
