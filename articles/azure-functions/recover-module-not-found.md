---
title: Problemen met python ModuleNotFoundError in Azure Functions oplossen
description: Meer informatie over het oplossen van problemen met Azure Functions module niet gevonden in Python-functies.
author: Hazhzeng
ms.topic: article
ms.date: 05/12/2020
ms.author: hazeng
ms.openlocfilehash: 191cde0f90b4968ca230db72bad68cca8b1db3fd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691227"
---
# <a name="troubleshoot-python-module-errors-in-azure-functions"></a>Problemen met python-module fouten in Azure Functions oplossen

Dit artikel helpt u bij het oplossen van problemen met module fouten in uw python-functie-app. Deze fouten resulteren doorgaans in het volgende Azure Functions fout bericht:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Dit fout probleem treedt op wanneer een python-functie-app geen python-module kan laden. De hoofd oorzaak van deze fout is een van de volgende problemen:

- [Kan het pakket niet vinden](#the-package-cant-be-found)
- [Het pakket is niet opgelost met het juiste Linux-wiel](#the-package-isnt-resolved-with-proper-linux-wheel)
- [Het pakket is niet compatibel met de Python-interpreter versie](#the-package-is-incompatible-with-the-python-interpreter-version)
- [Het pakket conflicteert met andere pakketten](#the-package-conflicts-with-other-packages)
- [Het pakket ondersteunt alleen Windows-of macOS-platforms](#the-package-only-supports-windows-or-macos-platforms)

## <a name="view-project-files"></a>Project bestanden weer geven

Als u de daad werkelijke oorzaak van het probleem wilt achterhalen, moet u de python-project bestanden ophalen die worden uitgevoerd in uw functie-app. Als u de project bestanden niet op uw lokale computer hebt, kunt u ze op een van de volgende manieren ophalen:

- Als de functie-app `WEBSITE_RUN_FROM_PACKAGE` app-instelling heeft en de waarde ervan een URL is, downloadt u het bestand door de URL te kopiëren en in uw browser te plakken.
- Als de functie-app is `WEBSITE_RUN_FROM_PACKAGE` ingesteld op `1` , navigeert u naar `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` en downloadt u het bestand van de meest recente `href` URL.
- Als de app-instelling die hierboven wordt genoemd, niet in de functie-app wordt vermeld, gaat u naar `https://<app-name>.scm.azurewebsites.net/api/settings` de URL en zoekt u deze `SCM_RUN_FROM_PACKAGE` . Down load het bestand door de URL te kopiëren en in uw browser te plakken.
- Als geen van deze voor u geschikt is, gaat u naar `https://<app-name>.scm.azurewebsites.net/DebugConsole` en onthult u de inhoud onder `/home/site/wwwroot` .

De rest van dit artikel helpt u om mogelijke oorzaken van deze fout op te lossen door de inhoud van uw functie-app te controleren, de hoofd oorzaak te identificeren en het specifieke probleem op te lossen.

## <a name="diagnose-modulenotfounderror"></a>ModuleNotFoundError diagnosticeren

In deze sectie vindt u meer informatie over mogelijke hoofd oorzaken van module fouten. Nadat u hebt nagegaan wat de oorzaak van het probleem is, kunt u naar de gerelateerde oplossing gaan.

### <a name="the-package-cant-be-found"></a>Kan het pakket niet vinden

Blader naar `.python_packages/lib/python3.6/site-packages/<package-name>` of `.python_packages/lib/site-packages/<package-name>` . Als het bestandspad niet bestaat, is dit waarschijnlijk de hoofd oorzaak.

Dit probleem kan worden veroorzaakt door het gebruik van hulpprogram ma's van derden of verouderde hulp middelen tijdens de implementatie.

Zie [externe build inschakelen](#enable-remote-build) of [native afhankelijkheden bouwen](#build-native-dependencies) voor een oplossing.

### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>Het pakket is niet opgelost met het juiste Linux-wiel

Ga naar `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` of `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Gebruik uw favoriete tekst editor om het **wiel** bestand te openen en controleer de sectie **Tag:** . Als de waarde van de tag geen **Linux**bevat, kan dit het probleem zijn.

Python-functies worden alleen uitgevoerd op Linux in Azure: functions runtime v2. x wordt uitgevoerd op Debian stretch en de v3. x-runtime op Debian Buster. Het artefact moet naar verwachting de juiste binaire bestanden voor Linux bevatten. Door gebruik te maken `--build local` van vlag in kern hulpprogramma's, derden of verouderde hulpprogram ma's kunnen oudere binaire bestanden worden gebruikt.

Zie [externe build inschakelen](#enable-remote-build) of [native afhankelijkheden bouwen](#build-native-dependencies) voor een oplossing.

### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>Het pakket is niet compatibel met de Python-interpreter versie

Ga naar `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` of `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` . Open met een tekst editor het meta gegevensbestand en controleer de sectie **classificaties:** . Als de sectie geen `Python :: 3` ,, `Python :: 3.6` , of bevat, `Python :: 3.7` `Python :: 3.8` betekent dit dat de pakket versie te oud is of dat het pakket waarschijnlijk al buiten het onderhoud valt.

U kunt de python-versie van uw functie-app controleren vanuit het [Azure Portal](https://portal.azure.com). Navigeer naar uw functie-app, kies **resource Verkenner**en selecteer **Go**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Open Resource Explorer voor de functie-app in de portal":::

Nadat de Explorer is geladen, zoekt u naar **LinuxFxVersion**, waarin de python-versie wordt weer gegeven.

Zie [uw pakket bijwerken naar de nieuwste versie](#update-your-package-to-the-latest-version) of [het pakket vervangen door equivalenten](#replace-the-package-with-equivalents) voor risico beperking.

### <a name="the-package-conflicts-with-other-packages"></a>Het pakket conflicteert met andere pakketten

Als u hebt gecontroleerd of het pakket correct is opgelost met de juiste Linux-wielen, is er mogelijk een conflict met andere pakketten. In bepaalde pakketten kunnen de PyPi-documenten de incompatibele modules verduidelijken. [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/)Er is bijvoorbeeld een instructie:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

U kunt de documentatie voor uw pakket versie vinden in `https://pypi.org/project/<package-name>/<package-version>` .

Zie [uw pakket bijwerken naar de nieuwste versie](#update-your-package-to-the-latest-version) of [het pakket vervangen door equivalenten](#replace-the-package-with-equivalents) voor risico beperking.

### <a name="the-package-only-supports-windows-or-macos-platforms"></a>Het pakket ondersteunt alleen Windows-of macOS-platforms

Open de `requirements.txt` met een tekst editor en controleer het pakket in `https://pypi.org/project/<package-name>` . Sommige pakketten worden alleen uitgevoerd op Windows-of macOS-platforms. Pywin32 wordt bijvoorbeeld alleen uitgevoerd in Windows.

De `Module Not Found` fout treedt mogelijk niet op wanneer u Windows of macOS gebruikt voor lokale ontwikkeling. Het pakket kan echter niet worden geïmporteerd op Azure Functions, dat gebruikmaakt van Linux tijdens runtime. Dit is waarschijnlijk het gevolg van `pip freeze` het exporteren van een virtuele omgeving naar requirements. txt vanaf uw Windows-of macOS-computer tijdens de initialisatie van het project.

Zie [het pakket vervangen door equivalenten](#replace-the-package-with-equivalents) of [samen requirements. txt](#handcraft-requirementstxt) voor een oplossing.

## <a name="mitigate-modulenotfounderror"></a>ModuleNotFoundError beperken

Hieronder vindt u mogelijke oplossingen voor problemen met betrekking tot modules. Gebruik de [bovenstaande diagnoses](#diagnose-modulenotfounderror) om te bepalen welke van deze oplossingen u moet proberen.

### <a name="enable-remote-build"></a>Externe build inschakelen

Zorg ervoor dat externe build is ingeschakeld. Hoe u dit doet, is afhankelijk van uw implementatie methode.

# <a name="visual-studio-code"></a>[Visual Studio code](#tab/vscode)
Zorg ervoor dat de meest recente versie van de [uitbrei ding van de Azure functions voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) is geïnstalleerd. Controleer of `.vscode/settings.json` Exists bestaat en de instelling bevat `"azureFunctions.scmDoBuildDuringDeployment": true` . Als dat niet het geval is, maakt u dit bestand met de `azureFunctions.scmDoBuildDuringDeployment` instelling ingeschakeld en implementeert u het project opnieuw.

# <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Zorg ervoor dat de meest recente versie van [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools/releases) is geïnstalleerd. Ga naar de map van uw lokale functie project en gebruik deze `func azure functionapp publish <app-name>` voor implementatie.

# <a name="manual-publishing"></a>[Hand matig publiceren](#tab/manual)

Als u uw pakket hand matig in het `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` eind punt wilt publiceren, moet u ervoor zorgen dat zowel **SCM_DO_BUILD_DURING_DEPLOYMENT** als **ENABLE_ORYX_BUILD** zijn ingesteld op **waar**. Zie [How to werken with Application Settings](functions-how-to-use-azure-function-app-settings.md#settings)(Engelstalig) voor meer informatie.

---

### <a name="build-native-dependencies"></a>Systeem eigen afhankelijkheden bouwen

Zorg ervoor dat de nieuwste versie van zowel **docker** als [Azure functions core tools](https://github.com/Azure/azure-functions-core-tools/releases) is geïnstalleerd. Ga naar de map van uw lokale functie project en gebruik deze `func azure functionapp publish <app-name> --build-native-deps` voor implementatie.

### <a name="update-your-package-to-the-latest-version"></a>Werk uw pakket bij naar de nieuwste versie

Blader door de nieuwste pakket versie in `https://pypi.org/project/<package-name>` en controleer de sectie **classificaties:** . Het pakket moet `OS Independent` of compatibel zijn met `POSIX` of in `POSIX :: Linux` het **besturings systeem**. Daarnaast moet de programmeer taal `Python :: 3` , `Python :: 3.6` , of bevatten `Python :: 3.7` `Python :: 3.8` .

Als deze juist zijn, kunt u het pakket bijwerken naar de meest recente versie door de regel te wijzigen `<package-name>~=<latest-version>` in requirements. txt.

### <a name="handcraft-requirementstxt"></a>Samen-vereisten. txt

Sommige ontwikkel aars gebruiken `pip freeze > requirements.txt` om de lijst met Python-pakketten voor hun ontwikkel omgevingen te genereren. Hoewel dit gebruiks gemak in de meeste gevallen zou moeten werken, kunnen er problemen optreden in scenario's voor verschillende platforms, zoals het ontwikkelen van functies lokaal op Windows of macOS, maar het publiceren naar een functie-app die wordt uitgevoerd op Linux. In dit scenario `pip freeze` kunnen onverwachte afhankelijkheden of afhankelijkheden van besturings systemen worden geïntroduceerd voor uw lokale ontwikkel omgeving. Met deze afhankelijkheden kan de python-functie-app worden onderbroken wanneer deze wordt uitgevoerd op Linux.

De best practice is om de import instructie van elk. py-bestand in de project bron code te controleren en deze modules alleen te controleren in het bestand requirements. txt. Dit garandeert dat de oplossing van pakketten goed kan worden afgehandeld op verschillende besturings systemen.

### <a name="replace-the-package-with-equivalents"></a>Vervang het pakket door equivalenten

Eerst moet u de nieuwste versie van het pakket bekijken in `https://pypi.org/project/<package-name>` . Normaal gesp roken heeft dit pakket een eigen GitHub-pagina. Ga naar de sectie **problemen** op github en zoek naar het probleem. Als dit het geval is, werkt u het pakket bij naar de meest recente versie.

Het is ook mogelijk dat het pakket is geïntegreerd in de [python-standaard bibliotheek](https://docs.python.org/3/library/) (zoals pathlib). Als dat het geval is, moet het pakket in uw vereiste. txt worden verwijderd omdat we een bepaalde python-distributie hebben in Azure Functions (python 3,6, python 3,7 en python 3,8).

Als u echter een probleem ondervindt dat niet is opgelost en u een deadline hebt bereikt. Ik raden u aan om onderzoek uit te voeren en een soortgelijk pakket voor uw project te vinden. Normaal gesp roken biedt de python-Community u een groot aantal vergelijk bare bibliotheken die u kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem met de module niet kunt oplossen, meldt u dit aan het team functions:

> [!div class="nextstepaction"]
> [Een onopgelost probleem melden](https://github.com/Azure/azure-functions-python-worker/issues)
