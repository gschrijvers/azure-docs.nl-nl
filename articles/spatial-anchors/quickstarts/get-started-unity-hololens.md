---
title: 'Snelstartgids: een HoloLens-app maken met Unity'
description: In deze quickstart leert u een HoloLens-app maken met Unity en met behulp van Spatial Anchors.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9e946d7f4616799768711ee8c18584a2a8fcff2a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81482012"
---
# <a name="quickstart-create-a-unity-hololens-app-that-uses-azure-spatial-anchors"></a>Snelstartgids: een unit HoloLens-app maken die gebruikmaakt van ruimtelijke ankers van Azure

In deze Quick Start maakt u een unit HoloLens-app die gebruikmaakt van [Azure spatiale ankers](../overview.md). Ruimtelijke ankers is een platformoverschrijdende ontwikkelaars service die u in staat stelt om toepassingen met gemengde realiteit te maken met objecten die hun locatie in de loop van de tijd op verschillende apparaten blijven zetten. Als u klaar bent, hebt u een HoloLens-app met Unity gemaakt waarmee een ruimtelijk anker kan worden opgeslagen en teruggehaald.

U leert het volgende:

- Maak een ruimtelijke ankers-account.
- Bereid instellingen voor eenheids constructie voor.
- Configureer de account-id en de account sleutel van de ruimtelijke ankers.
- Exporteer het project HoloLens Visual Studio.
- Implementeer de app en voer deze uit op een HoloLens-apparaat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Dit zijn de vereisten voor het voltooien van deze snelstart:

- U hebt een Windows-computer met <a href="https://unity3d.com/get-unity/download" target="_blank">unit 2019,1 of 2019,2</a> en <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> of hoger nodig. Uw Visual Studio-installatie moet de **universeel Windows-platform Development** -werk belasting en het onderdeel **Windows 10 SDK (10.0.18362.0 of recenter)** bevatten. U moet ook <a href="https://git-scm.com/download/win" target="_blank">git installeren voor Windows</a> en <a href="https://git-lfs.github.com/">Git LF</a>.
- U hebt een HoloLens-apparaat nodig waarop de [ontwikkelaars modus](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) is ingeschakeld. [Windows 10 oktober 2018-update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018) (ook wel bekend als RS5) moet op het apparaat zijn geïnstalleerd. Als u wilt bijwerken naar de nieuwste versie van HoloLens, opent u de app **instellingen** , gaat u naar **Update & Security**en selecteert u vervolgens **controleren op updates**.
- In uw app moet u de **SpatialPerception** -mogelijkheid inschakelen. Deze instelling bevindt zich**in instellingen voor** > het **maken** > **van instellingen voor**de**publicatie** > -instellingen.
- In uw app moet u **Virtual Reality** inschakelen die wordt ondersteund met de **SDK voor Windows Mixed Reality**. Deze instelling bevindt zich**in instellingen voor** >  **Build Settings** > **XR Settings**.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Het unit-voorbeeld project downloaden en openen

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

Open **Build Settings** door **File** > **Build Settings** te selecteren.

in de sectie **Platform** selecteert u **Universeel Windows-platform**. Wijzig het **doel apparaat** in **HoloLens**.

Selecteer **Switch Platform** om het platform te wijzigen in **Universeel Windows-platform**. Unit-eenheid vraagt u mogelijk UWP-ondersteunings onderdelen te installeren als deze ontbreken.

![Venster instellingen voor eenheids opbouw](./media/get-started-unity-hololens/unity-build-settings.png)

Sluit het venster **Build Settings**.

## <a name="configure-the-account-identifier-and-key"></a>De account-id en-sleutel configureren

Ga in het deel venster **project** naar `Assets/AzureSpatialAnchors.Examples/Scenes` het scène bestand `AzureSpatialAnchorsBasicDemo.unity` en open het.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Sla de scène op door **bestand** > **Opslaan**te selecteren.

## <a name="export-the-hololens-visual-studio-project"></a>Het HoloLens Visual Studio-project exporteren

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Selecteer **Build**. Selecteer in het dialoog venster een map waarnaar u het project van HoloLens Visual Studio wilt exporteren.

Wanneer het exporteren is voltooid, wordt er een map met het geëxporteerde HoloLens-project weer gegeven.

## <a name="deploy-the-hololens-application"></a>HoloLens-toepassing implementeren

Dubbel klik in de map op **HELLOAR U3D. SLN** om het project te openen in Visual Studio.

Wijzig de **oplossings configuratie** in **release**, wijzig het platform van de **oplossing** in **x86**en selecteer **apparaat** in de doel opties voor de implementatie.

Als u HoloLens 2 gebruikt, gebruikt u **ARM64** als het **oplossings platform**in plaats van **x86**.

   ![Visual Studio-configuratie](./media/get-started-unity-hololens/visual-studio-configuration.png)

Schakel het apparaat HoloLens in, Meld u aan en verbind het apparaat met de PC met behulp van een USB-kabel.

Selecteer **debug** > **Start Debugging** om uw app te implementeren en de fout opsporing te starten.

Volg de instructies in de app om een anker te plaatsen en terug te halen.

In Visual Studio stopt u de app door **fout opsporing** of SHIFT + F5 te selecteren.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unity-20193"></a>Unity 2019,3

Als gevolg van het verbreken van wijzigingen, wordt unit 2019,3 momenteel niet ondersteund. Gebruik unit 2019,1 of 2019,2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Zelf studie: ruimtelijke ankers delen op meerdere apparaten](../tutorials/tutorial-share-anchors-across-devices.md)
