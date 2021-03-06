---
title: Azure IoT Edge installeren op Windows | Microsoft Docs
description: Installatie-instructies Azure IoT Edge voor Windows 10, Windows Server en Windows IoT core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: kgremban
ms.openlocfilehash: e95f68610f8469a829255d6a16115dcf728ef612
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856750"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>De Azure IoT Edge-runtime op Windows installeren

Met de Azure IoT Edge runtime wordt een apparaat omgezet in een IoT Edge apparaat. De runtime kan worden geïmplementeerd op apparaten als een Raspberry Pi of zo groot als een industriële server. Zodra een apparaat is geconfigureerd met de IoT Edge runtime, kunt u beginnen met het implementeren van bedrijfs logica vanuit de Cloud.

Zie [inzicht in de Azure IOT Edge runtime en de architectuur](iot-edge-runtime.md)voor meer informatie over de IOT Edge runtime.

Dit artikel bevat de stappen voor het installeren van de Azure IoT Edge runtime op uw Windows x64-systeem (AMD/Intel) met behulp van Windows-containers.

> [!NOTE]
> Een bekende probleem met een Windows-besturings systeem verhindert de overgang naar de energie status van de slaap stand en sluimer stand wanneer IoT Edge modules (geïsoleerde Windows nano server-containers) worden uitgevoerd. Dit probleem heeft gevolgen voor de levens duur van de accu op het apparaat.
>
> Als tijdelijke oplossing gebruikt u de opdracht `Stop-Service iotedge` om alle actieve IOT Edge modules te stoppen voordat u deze energie status gebruikt.

Het gebruik van Linux-containers op Windows-systemen is geen aanbevolen of ondersteunde productie configuratie voor Azure IoT Edge. Het kan echter worden gebruikt voor ontwikkelings-en test doeleinden. Zie [IOT Edge gebruiken in Windows om Linux-containers uit te voeren](how-to-install-iot-edge-windows-with-linux.md)voor meer informatie.

Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor informatie over wat er in de nieuwste versie van IOT Edge is opgenomen.

## <a name="prerequisites"></a>Vereisten

Gebruik deze sectie om te controleren of uw Windows-apparaat IoT Edge kan ondersteunen en om dit voor te bereiden voor een container engine vóór de installatie.

### <a name="supported-windows-versions"></a>Ondersteunde Windows-versies

Voor IoT Edge voor Windows is Windows versie 1809/build 17763 vereist. Dit is de meest recente [build van Windows lange termijn ondersteuning](https://docs.microsoft.com/windows/release-information/). Zie wat wordt ondersteund op basis van de voor bereiding op productie scenario's of ontwikkelings-en test scenario's voor Windows SKU-ondersteuning:

* **Productie**: Zie [Azure IOT Edge ondersteunde systemen](support.md#operating-systems)voor de meest recente informatie over welke besturings systemen momenteel worden ondersteund voor productie scenario's.
* **Ontwikkelen en testen**: voor ontwikkelings-en test scenario's kan Azure IOT Edge met Windows-containers worden geïnstalleerd op elke versie van Windows 10 of windows server 2019 die ondersteuning biedt voor de functie containers.

IoT core-apparaten moeten de optionele functie van IoT core Windows-containers voor de ondersteuning van de IoT Edge runtime bevatten. Gebruik de volgende opdracht in een [externe Power shell-sessie](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) om te controleren of Windows-containers op uw apparaat worden ondersteund:

```powershell
Get-Service vmcompute
```

Als de service aanwezig is, moet u een geslaagde reactie krijgen met de status van de service die **wordt uitgevoerd**. Als de `vmcompute` service niet wordt gevonden, voldoet het apparaat niet aan de vereisten voor IOT Edge. Neem contact op met uw hardwareprovider om te vragen over ondersteuning voor deze functie.

### <a name="prepare-for-a-container-engine"></a>Voorbereiden op een container engine

Azure IoT Edge is afhankelijk van een met [OCI compatibele](https://www.opencontainers.org/) container engine. Gebruik voor productie scenario's de Moby-engine die is opgenomen in het installatie script om Windows-containers uit te voeren op uw Windows-apparaat.

## <a name="install-iot-edge-on-a-new-device"></a>IoT Edge installeren op een nieuw apparaat

>[!NOTE]
>Azure IoT Edge software pakketten zijn onderworpen aan de licentie voorwaarden in de pakketten (in de licentie lijst). Lees de licentie voorwaarden voordat u het pakket gebruikt. Uw installatie en het gebruik van het pakket zijn uw acceptatie van deze voor waarden. Als u niet akkoord gaat met de licentie voorwaarden, mag u het pakket niet gebruiken.

Met een Power shell-script wordt de Azure IoT Edge Security daemon gedownload en geïnstalleerd. De Security daemon start vervolgens de eerste van twee runtime modules, de IoT Edge-agent, waarmee externe implementaties van andere modules worden ingeschakeld.

>[!TIP]
>Voor IoT core-apparaten kunt u het beste de installatie opdrachten uitvoeren met behulp van een RemotePowerShell-sessie. Zie [using Power shell for Windows IOT](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)(Engelstalig) voor meer informatie.

Wanneer u de IoT Edge runtime voor de eerste keer op een apparaat installeert, moet u het apparaat inrichten met een identiteit van een IoT-hub. Eén IoT Edge apparaat kan hand matig worden ingericht met behulp van een apparaat connection string dat door IoT Hub wordt geleverd. U kunt ook gebruikmaken van de Device Provisioning Service (DPS) voor het automatisch inrichten van apparaten. Dit is handig als u veel apparaten hebt om in te stellen. Afhankelijk van uw inrichtings keuze, kiest u het juiste installatie script.

In de volgende secties worden de algemene use cases en para meters voor het IoT Edge-installatie script op een nieuw apparaat beschreven.

### <a name="option-1-install-and-manually-provision"></a>Optie 1: installeren en hand matig inrichten

In deze eerste optie geeft u een **apparaat Connection String** gegenereerd door IOT hub om het apparaat in te richten.

In dit voor beeld ziet u een hand matige installatie met Windows-containers:

1. Als u dit nog niet hebt gedaan, registreert u een nieuw IoT Edge apparaat en haalt u het **apparaat Connection String**op. Kopieer de connection string voor gebruik verderop in deze sectie. U kunt deze stap volt ooien met behulp van de volgende hulpprogram ma's:

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure-CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. Voer PowerShell uit als beheerder.

   >[!NOTE]
   >Gebruik een AMD64-sessie van Power shell om IoT Edge, niet Power shell (x86), te installeren. Als u niet zeker weet welk sessie type u gebruikt, voert u de volgende opdracht uit:
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. Met de opdracht **Deploy-IoTEdge** wordt gecontroleerd of de Windows-computer een ondersteunde versie heeft, wordt de functie containers ingeschakeld, waarna de Moby-runtime en de IOT Edge-runtime worden gedownload. De opdracht wordt standaard ingesteld op het gebruik van Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Op dit moment kunnen IoT-kern apparaten automatisch opnieuw worden opgestart. Op andere Windows 10-of Windows Server-apparaten wordt u mogelijk gevraagd om opnieuw op te starten. Als dit het geval is, start u het apparaat nu opnieuw op. Zodra het apparaat klaar is, voert u Power shell als beheerder opnieuw uit.

5. De **initialisatie-IoTEdge-** opdracht configureert de IOT Edge runtime op de computer. De opdracht wordt standaard ingesteld op hand matig inrichten met Windows-containers.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Wanneer u hierom wordt gevraagd, geeft u de connection string op van het apparaat dat u in stap 1 hebt opgehaald. Het apparaat connection string het fysieke apparaat koppelt aan een apparaat-ID in IoT Hub.

   De connection string van het apparaat heeft de volgende indeling en mag geen aanhalings tekens bevatten:`HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Volg de stappen in [verifiëren geslaagde installatie](#verify-successful-installation) om de status van IOT Edge op het apparaat te controleren.

Wanneer u een apparaat hand matig installeert en inricht, kunt u extra para meters gebruiken voor het wijzigen van de installatie, waaronder:

* Direct verkeer om een proxy server te passeren
* Het installatie programma naar een offline Directory laten verwijzen
* Een specifieke agent container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Meer informatie over deze installatie opties kunt u overs Laan om meer te weten te komen over [alle installatie parameters](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Optie 2: installeren en automatisch inrichten

In deze tweede optie richt u het apparaat in met behulp van de IoT Hub Device Provisioning Service. Geef de **bereik-id** van een Device Provisioning service-exemplaar op, samen met alle andere informatie die specifiek is voor uw voorkeurs [Attestation-mechanisme](../iot-dps/concepts-security.md#attestation-mechanism):

* [Een gesimuleerd IoT Edge-apparaat maken en inrichten met een virtuele TPM in Windows](how-to-auto-provision-simulated-device-windows.md)
* [Een gesimuleerd IoT Edge-apparaat maken en inrichten met X. 509-certificaten](how-to-auto-provision-x509-certs.md)
* [Een IoT Edge apparaat maken en inrichten met behulp van symmetrische sleutel attest](how-to-auto-provision-symmetric-keys.md)

Wanneer u een apparaat automatisch installeert en inricht, kunt u extra para meters gebruiken voor het wijzigen van de installatie, waaronder:

* Direct verkeer om een proxy server te passeren
* Het installatie programma naar een offline Directory laten verwijzen
* Een specifieke agent container installatie kopie declareren en referenties opgeven als deze zich in een persoonlijk REGI ster bevinden

Voor meer informatie over deze installatie opties gaat u verder met het lezen van dit artikel of gaat u naar meer informatie over [de installatie parameters](#all-installation-parameters).

## <a name="offline-or-specific-version-installation"></a>Installatie van offline of specifieke versie

Tijdens de installatie worden drie bestanden gedownload:

* Een Power shell-script, dat de installatie-instructies bevat
* Microsoft Azure IoT Edge cab, dat de IoT Edge Security daemon (iotedged), Moby container engine en Moby CLI bevat
* Installatie programma voor het Visual C++ Redistributable-pakket (VC runtime)

Als uw apparaat offline is tijdens de installatie, of als u een specifieke versie van IoT Edge wilt installeren, kunt u deze bestanden van tevoren downloaden naar het apparaat. Wanneer het tijd is om te installeren, plaatst u het installatie script in de map met de gedownloade bestanden. Het installatie programma controleert eerst de Directory en downloadt alleen de onderdelen die niet worden gevonden. Als alle bestanden offline beschikbaar zijn, kunt u installeren zonder Internet verbinding.

U kunt ook de para meter Offline installatiepad gebruiken om IoT Edge bij te werken. Zie [IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)voor meer informatie.

1. Zie [Azure IOT Edge releases](https://github.com/Azure/azure-iotedge/releases)voor de nieuwste IOT Edge installatie bestanden samen met vorige versies.

2. Zoek de versie die u wilt installeren en down load de volgende bestanden van het gedeelte **assets** van de release opmerkingen op uw IOT-apparaat:

   * IoTEdgeSecurityDaemon. ps1
   * Microsoft-Azure-IoTEdge-amd64. cab van Releases 1.0.9 of recenter of Microsoft-Azure-IoTEdge. cab van Releases 1.0.8 en ouder.

   Microsoft-Azure-IotEdge-arm32. cab is ook beschikbaar vanaf 1.0.9 alleen voor test doeleinden. IoT Edge wordt momenteel niet ondersteund op Windows ARM32-apparaten.

   Het is belang rijk dat u het Power shell-script uit dezelfde versie gebruikt als het CAB-bestand dat u gebruikt, omdat de functionaliteit wordt gewijzigd zodat de functies in elke release worden ondersteund.

3. Als het CAB-bestand dat u hebt gedownload een architectuur achtervoegsel heeft, wijzigt u de naam van het bestand in net **Microsoft-Azure-IoTEdge. cab**.

4. U kunt eventueel ook een installatie programma downloaden voor het herdistribueerbare pakket van Visual C++. Het Power shell-script gebruikt bijvoorbeeld deze versie: [vc_redist. x64. exe](https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe). Sla het installatie programma in dezelfde map op uw IoT-apparaat op als de IoT Edge bestanden.

5. Als u wilt installeren met offline onderdelen, [punt bron](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) de lokale kopie van het Power shell-script. Vervolgens gebruikt u de `-OfflineInstallationPath` para meter als onderdeel van `Deploy-IoTEdge` de opdracht en geeft u het absolute pad naar de bestands directory op. Bijvoorbeeld:

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Deploy-IoTEdge -OfflineInstallationPath <path>
   ```

   De implementatie opdracht gebruikt alle onderdelen die zijn gevonden in de lokale bestands directory. Als het CAB-bestand of het Visual C++-installatie programma ontbreekt, wordt geprobeerd deze te downloaden.

6. Voer de `Initialize-IoTEdge` opdracht uit om uw apparaat in te richten met een identiteit in IOT hub. Geef een apparaat connection string op voor hand matige inrichting of kies een van de methoden die worden beschreven in de vorige sectie voor [automatische inrichting](#option-2-install-and-automatically-provision) .

   Als het apparaat opnieuw is opgestart `Deploy-IoTEdge`nadat het is uitgevoerd, moet u het Power `Initialize-IoTEdge`shell-script opnieuw starten voordat u het uitvoert.

Meer informatie over de offline-installatie optie kunt u overs Laan om meer te weten te komen over [alle installatie parameters](#all-installation-parameters).

## <a name="verify-successful-installation"></a>Geslaagde installatie controleren

Controleer de status van de IoT Edge-service. Deze moet worden weer gegeven als actief.  

```powershell
Get-Service iotedge
```

Bekijk service Logboeken in de afgelopen vijf minuten. Als u de IoT Edge runtime zojuist hebt geïnstalleerd, ziet u mogelijk een lijst met fouten uit de tijd tussen het uitvoeren van **Deploy-IoTEdge** en **Initialize-IoTEdge**. Deze fouten worden verwacht, omdat de service probeert te starten voordat deze wordt geconfigureerd.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Voer het [hulp programma voor probleem oplossing](troubleshoot.md#run-the-check-command) uit om te controleren op de meest voorkomende configuratie-en netwerk fouten.

```powershell
iotedge check
```

De **$edgeHub** systeem module wordt niet geïmplementeerd op het apparaat totdat u uw eerste module implementeert voor IOT Edge op het apparaat. Als gevolg hiervan geeft de geautomatiseerde controle een fout melding voor de `Edge Hub can bind to ports on host` connectiviteits controle. Deze fout kan worden genegeerd, tenzij deze optreedt nadat een module op het apparaat is geïmplementeerd.

Ten slotte lijst met actieve modules:

```powershell
iotedge list
```

Na een nieuwe installatie is de enige module die u moet zien, **edgeAgent**. Nadat u [IOT Edge-modules](how-to-deploy-modules-portal.md) voor de eerste keer hebt geïmplementeerd, wordt de andere systeem module, **edgeHub**, ook op het apparaat gestart.

## <a name="manage-module-containers"></a>Module containers beheren

De IoT Edge-service vereist een container engine die op uw apparaat wordt uitgevoerd. Wanneer u een module implementeert op een apparaat, gebruikt de IoT Edge runtime de container-Engine om de container installatie kopie uit een REGI ster in de cloud te halen. De IoT Edge-service stelt u in staat om te communiceren met uw modules en logboeken op te halen, maar soms wilt u de container engine gebruiken om met de container zelf te communiceren.

Zie voor meer informatie over module concepten [begrijpen Azure IOT Edge-modules](iot-edge-modules.md).

Als u Windows-containers op uw Windows IoT Edge-apparaat uitvoert, bevat de IoT Edge installatie de Moby-container engine. De Moby-engine is gebaseerd op dezelfde standaarden als docker en is ontworpen om parallel te worden uitgevoerd op dezelfde computer als docker Desktop. Daarom moet u, als u de containers wilt behalen die worden beheerd door de Moby-engine, deze engine specifiek richten in plaats van docker.

Als u bijvoorbeeld alle docker-installatie kopieën wilt weer geven, gebruikt u de volgende opdracht:

```powershell
docker images
```

Als u alle Moby-installatie kopieën wilt weer geven, wijzigt u dezelfde opdracht met een verwijzing naar de Moby-Engine:

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

De engine-URI wordt vermeld in de uitvoer van het installatie script of u kunt deze vinden in de sectie container runtime-instellingen voor het bestand config. yaml.

![moby_runtime URI in config. yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Zie [docker-opdracht regel interfaces](https://docs.docker.com/engine/reference/commandline/docker/)voor meer informatie over de opdrachten die u kunt gebruiken om te communiceren met containers en installatie kopieën die op uw apparaat worden uitgevoerd.

## <a name="uninstall-iot-edge"></a>IoT Edge verwijderen

Als u de IoT Edge-installatie wilt verwijderen van uw Windows-apparaat, gebruikt u de volgende opdracht in een beheer-Power shell-venster. Met deze opdracht wordt de IoT Edge runtime verwijderd, samen met uw bestaande configuratie en de Moby-Engine gegevens.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

De opdracht uninstall-IoTEdge werkt niet in Windows IoT core. Als u IoT Edge van Windows IoT core-apparaten wilt verwijderen, moet u uw Windows IoT Core-installatie kopie opnieuw implementeren.

Voor meer informatie over verwijderings opties gebruikt u de opdracht `Get-Help Uninstall-IoTEdge -full`.

## <a name="verify-installation-script"></a>Installatie script controleren

De installatie opdrachten die in dit artikel worden gegeven, gebruiken de cmdlet invoke-webaanvraag om het installatie `aka.ms/iotedge-win`script aan te vragen bij. Deze koppeling wijst naar het`IoTEdgeSecurityDaemon.ps1` script vanuit de meest recente [IOT Edge versie](https://github.com/Azure/azure-iotedge/releases). U kunt dit script of een versie van het script van een specifieke versie ook downloaden om de installatie opdrachten op uw IoT Edge-apparaat uit te voeren.

Het gegeven script is ondertekend om de beveiliging te verbeteren. U kunt de hand tekening controleren door het script te downloaden naar uw apparaat en vervolgens de volgende Power shell-opdracht uit te voeren:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

De uitvoer status is **geldig** als de hand tekening wordt geverifieerd.

## <a name="all-installation-parameters"></a>Alle installatie parameters

In de vorige secties werden veelvoorkomende installatie scenario's geïntroduceerd met voor beelden van het gebruik van para meters voor het wijzigen van het installatie script. Deze sectie bevat naslag tabellen van de algemene para meters die worden gebruikt om IoT Edge te installeren, bij te werken of te verwijderen.

### <a name="deploy-iotedge"></a>Implementeren-IoTEdge

Met de opdracht Deploy-IoTEdge worden de IoT Edge Security daemon en de bijbehorende afhankelijkheden gedownload en geïmplementeerd. De implementatie opdracht accepteert deze algemene para meters, onder andere. Gebruik de opdracht `Get-Help Deploy-IoTEdge -full`voor de volledige lijst.  

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturings systeem is opgegeven, is Windows de standaard waarde.<br><br>Voor Windows-containers maakt IoT Edge gebruik van de Moby-container engine die is opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **Proxy** | Proxy-URL | Neem deze para meter op als uw apparaat een proxy server moet door lopen om het Internet te bereiken. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallationPath** | Mappad | Als deze para meter is opgenomen, controleert het installatie programma de vermelde map voor de IoT Edge cab-en VC runtime-bestanden die nodig zijn voor de installatie. Bestanden die niet in de map zijn gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, kunt u IoT Edge installeren zonder een Internet verbinding. U kunt deze para meter ook gebruiken om een specifieke versie te gebruiken. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **RestartIfNeeded** | geen | Met deze vlag kan het implementatie script de computer opnieuw opstarten zonder te vragen, indien nodig. |

### <a name="initialize-iotedge"></a>Initialisatie-IoTEdge

De initialisatie-IoTEdge-opdracht configureert IoT Edge met uw apparaat connection string en operationele gegevens. Veel van de gegevens die door deze opdracht worden gegenereerd, worden vervolgens opgeslagen in het iotedge\config.yaml-bestand. De initialisatie opdracht accepteert deze algemene para meters, onder andere. Gebruik de opdracht `Get-Help Initialize-IoTEdge -full`voor de volledige lijst.

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **Handmatig** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matig de standaard waarde.<br><br>Declareert dat u een apparaat connection string opgeeft om het apparaat hand matig in te richten |
| **DPS** | Geen | **Switch-para meter**. Als er geen inrichtings type is opgegeven, is hand matig de standaard waarde.<br><br>Declareert dat u een DPS-scope-ID (Device Provisioning Service) opgeeft en de registratie-ID van uw apparaat moet worden ingericht via DPS.  |
| **DeviceConnectionString** | Een connection string van een IoT Edge apparaat dat is geregistreerd in een IoT Hub, in enkele aanhalings tekens | **Vereist** voor hand matige inrichting. Als u geen connection string in de script parameters opgeeft, wordt u gevraagd om een. |
| **Scope** | Een bereik-ID van een instantie van Device Provisioning Service die is gekoppeld aan uw IoT Hub. | **Vereist** voor DPS-inrichting. Als u geen bereik-ID opgeeft in de script parameters, wordt u gevraagd om een scope nummer. |
| **Registratie** | Een registratie-ID die is gegenereerd door uw apparaat | **Vereist** voor DPS-inrichting bij gebruik van TPM of symmetrische sleutel Attestation. **Optioneel** als u X. 509-certificaat attest gebruikt. |
| **X509IdentityCertificate** | Het URI-pad naar het X. 509 apparaat-identiteits certificaat op het apparaat. | **Vereist** voor het INRICHTEN van DPS als het gebruik van X. 509-certificaat Attestation. |
| **X509IdentityPrivateKey** | Het URI-pad naar de X. 509 apparaat-ID certificaat sleutel op het apparaat. | **Vereist** voor het INRICHTEN van DPS als het gebruik van X. 509-certificaat Attestation. |
| **SymmetricKey** | De symmetrische sleutel die wordt gebruikt om de IoT Edge apparaat-id in te richten wanneer DPS wordt gebruikt | **Vereist** voor DPS-inrichting bij gebruik van symmetrische sleutel Attestation. |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturings systeem is opgegeven, is Windows de standaard waarde.<br><br>Voor Windows-containers maakt IoT Edge gebruik van de Moby-container engine die is opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **AgentImage** | URI-installatie kopie van IoT Edge agent | Een nieuwe IoT Edge-installatie maakt standaard gebruik van de nieuwste roulerende tag voor de installatie kopie van de IoT Edge agent. Gebruik deze para meter om een specifieke tag voor de installatie kopie versie in te stellen of om uw eigen agent installatie kopie op te geven. Zie voor meer informatie [IOT Edge Tags begrijpen](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Gebruikers** | Gebruikers naam container register | Gebruik deze para meter alleen als u de para meter-AgentImage instelt op een container in een persoonlijk REGI ster. Geef een gebruikers naam op die toegang heeft tot het REGI ster. |
| **Wachtwoord** | Teken reeks met beveiligd wacht woord | Gebruik deze para meter alleen als u de para meter-AgentImage instelt op een container in een persoonlijk REGI ster. Geef het wacht woord op voor toegang tot het REGI ster. |

### <a name="update-iotedge"></a>Update-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** of **Linux** | Als er geen container besturingssysteem is opgegeven, is Windows de standaard waarde. Voor Windows-containers wordt een container engine opgenomen in de installatie. Voor Linux-containers moet u een container Engine installeren voordat u de installatie start. |
| **Proxy** | Proxy-URL | Neem deze para meter op als uw apparaat een proxy server moet door lopen om het Internet te bereiken. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **InvokeWebRequestParameters** | Hashtabel van para meters en waarden | Tijdens de installatie worden er diverse webaanvragen gedaan. Gebruik dit veld om para meters in te stellen voor deze webaanvragen. Deze para meter is handig voor het configureren van referenties voor proxy servers. Zie [een IOT edge apparaat configureren om te communiceren via een proxy server](how-to-configure-proxy-support.md)voor meer informatie. |
| **OfflineInstallationPath** | Mappad | Als deze para meter is opgenomen, controleert het installatie programma de vermelde map voor de IoT Edge cab-en VC runtime-bestanden die nodig zijn voor de installatie. Bestanden die niet in de map zijn gevonden, worden gedownload. Als beide bestanden zich in de map bevinden, kunt u IoT Edge installeren zonder een Internet verbinding. U kunt deze para meter ook gebruiken om een specifieke versie te gebruiken. |
| **RestartIfNeeded** | geen | Met deze vlag kan het implementatie script de computer opnieuw opstarten zonder te vragen, indien nodig. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parameter | Geaccepteerde waarden | Opmerkingen |
| --------- | --------------- | -------- |
| **Verkopers** | geen | Met deze vlag wordt de verwijdering afgedwongen als de vorige poging om te verwijderen is mislukt.
| **RestartIfNeeded** | geen | Met deze vlag kan het uninstall-script de computer opnieuw opstarten zonder te vragen, indien nodig. |

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingericht terwijl de runtime is geïnstalleerd, kunt u [IOT Edge modules implementeren](how-to-deploy-modules-portal.md).

Als u problemen ondervindt met de installatie van IoT Edge op de juiste wijze, raadpleegt u de pagina [probleem oplossing](troubleshoot.md) .

Zie [de IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)als u een bestaande installatie wilt bijwerken naar de nieuwste versie van IOT Edge.
