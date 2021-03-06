---
title: Virtuele Hyper-V-machines evalueren voor migratie naar Azure met Azure Migrate | Microsoft Docs
description: Hierin wordt beschreven hoe u on-premises virtuele Hyper-V-machines kunt beoordelen voor migratie naar Azure met behulp van Azure Migrate server-evaluatie.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: c627902268af3a91e172223c1741dd24ea21fa92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535448"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Virtuele Hyper-V-machines beoordelen met Azure Migrate server-evaluatie

Dit artikel laat u zien hoe u on-premises virtuele Hyper-V-machines kunt beoordelen met behulp van het [Azure migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) tool.


Deze zelf studie is de tweede in een serie die laat zien hoe u virtuele Hyper-V-machines kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Stel een Azure Migrate project in.
> * Een Azure Migrate apparaat instellen en registreren.
> * Start doorlopende detectie van on-premises Vm's.
> * Gedetecteerde Vm's groeperen en de groep beoordelen.
> * Bekijk de evaluatie.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de artikelen met procedures voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

- [Voltooi](tutorial-prepare-hyper-v.md) de eerste zelf studie in deze serie. Als dat niet het geval is, werken de instructies in deze zelf studie niet.
- In de eerste zelf studie hebt u het volgende gedaan:
    - [Bereid Azure](tutorial-prepare-hyper-v.md#prepare-azure) voor op het werken met Azure Migrate.
    - Bereid de evaluatie van [Hyper-V-](tutorial-prepare-hyper-v.md#prepare-hyper-v-for-assessment) hosts en vm's voor.
    - [Controleer](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) wat u nodig hebt om het Azure migrate apparaat voor Hyper-V-evaluatie te implementeren.

## <a name="set-up-an-azure-migrate-project"></a>Een Azure Migrate project instellen

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Selecteer in de zoek resultaten **Azure migrate**.
3. In **Overzicht**, onder **Servers ontdekken, evalueren en migreren**, klikt u op **Servers evalueren en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Klik in **Aan de slag** op **Hulpprogramma's toevoegen**.
5. Selecteer uw Azure-abonnement op het tabblad **project migreren** en maak een resource groep als u er nog geen hebt.
6. Geef in **Project Details**de project naam en de regio op waarin u het project wilt maken. Bekijk ondersteunde geografies voor [open bare](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheids Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

    - De project regio wordt alleen gebruikt om de meta gegevens op te slaan die zijn verzameld van on-premises Vm's.
    - U kunt een andere Azure-doel regio selecteren wanneer u de virtuele machines migreert. Alle Azure-regio's worden ondersteund voor het migratie doel.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Klik op **Volgende**.
8. Selecteer in **hulp programma voor beoordeling selecteren**de optie **Azure migrate: Server analyse** > **volgende**.

    ![Een Azure Migrate-project maken](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. In **Migratieprogramma selecteren** selecteert u **Het toevoegen van een migratieprogramma voorlopig overslaan** > **Volgende**.
10. Controleer de instellingen in **hulp middelen voor beoordeling en toevoegen**en klik op **hulp middelen toevoegen**.
11. Wacht een paar minuten tot het Azure Migrate-project is geïmplementeerd. U wordt naar de projectpagina geleid. Als u het project niet ziet, kunt u het openen vanuit **Servers** in het Azure Migrate-dashboard.

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate apparaat instellen


Azure Migrate: Server evaluatie maakt gebruik van een licht gewicht Azure Migrate apparaat. Het apparaat voert VM-detectie uit en verzendt meta gegevens en prestatie gegevens van de virtuele machine naar Azure Migrate. Het apparaat kan op verschillende manieren worden ingesteld.

- Ingesteld op een Hyper-V-VM met behulp van een gedownloade Hyper-V VHD. Dit is de methode die in deze zelf studie wordt gebruikt.
- Ingesteld op een Hyper-V-VM of fysieke machine met een Power shell-installatie script. [Deze methode](deploy-appliance-script.md) moet worden gebruikt als u geen virtuele machine kunt instellen met behulp van de VHD of als u zich in azure Government bevindt.

Nadat u het apparaat hebt gemaakt, controleert u of er verbinding kan worden gemaakt met Azure Migrate: Server evaluatie, het voor de eerste keer configureren en registreren bij het Azure Migrate-project.

### <a name="download-the-vhd"></a>De VHD downloaden

Down load de sjabloon voor de gezipte VHD voor het apparaat.

1. In **migratie doelen** > **servers** > **Azure migrate: Server evaluatie**, klikt u op **detecteren**.
2. Zijn uw machines in **Discover-computers** > **gevirtualiseerde?** Klik op **Ja, met Hyper-V**.
3. Klik op **downloaden** om het VHD-bestand te downloaden.

    ![Virtuele machine downloaden](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Beveiliging controleren

Controleer of het gecomprimeerde bestand is beveiligd, voordat u het implementeert.

1. Open op de machine waarop u het bestand hebt gedownload een opdrachtvenster voor beheerders.

2. Voer de volgende Power shell-opdracht uit om de hash voor het ZIP-bestand te genereren
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gebruiksvoorbeeld: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Voor de toestel versie 2.19.07.30 moet de gegenereerde hash overeenkomen met deze instellingen.

  **Algoritme** | **Hash-waarde**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31

### <a name="create-the-appliance-vm"></a>De apparaat-VM maken

Importeer het gedownloade bestand en maak de virtuele machine.

1. Na het downloaden van het gecomprimeerde VHD-bestand naar de Hyper-V-host waarop de apparaat-VM wordt geplaatst, pakt u het zip-bestand uit.
    - In de geëxtraheerde locatie wordt het bestand uitgepakt naar een map met de naam **AzureMigrateAppliance_VersionNumber**.
    - Deze map bevat een submap, ook wel **AzureMigrateAppliance_VersionNumber**genoemd.
    - Deze submap bevat drie verdere submappen: **moment opnamen**, **virtuele harde schijven**en **virtual machines**.

2. Open Hyper-V-beheer. Klik in **acties**op **virtuele machine importeren**.

    ![VHD implementeren](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. Klik in de wizard virtuele machine importeren > **voordat u begint**op **volgende**.
3. Selecteer in **map zoeken**de map **virtual machines** . Klik vervolgens op **Volgende**.
1. Klik in **virtuele machine selecteren**op **volgende**.
2. In **import type kiezen**klikt u op **de virtuele machine kopiëren (een nieuwe unieke id maken)**. Klik vervolgens op **Volgende**.
3. Laat in **doel kiezen**de standaard instelling ongewijzigd. Klik op **Volgende**.
4. In **opslag mappen**, behoud de standaard instelling. Klik op **Volgende**.
5. Geef in **netwerk kiezen**de virtuele switch op die door de VM moet worden gebruikt. De switch heeft Internet connectiviteit nodig om gegevens naar Azure te verzenden. [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) over het maken van een virtuele switch.
6. Controleer de instellingen in **samen vatting**. Klik vervolgens op **Voltooien**.
7. Start de virtuele machine in Hyper-V-beheer > **virtual machines**.


## <a name="verify-appliance-access-to-azure"></a>Toestel toegang tot Azure controleren

Zorg ervoor dat de virtuele machine van het apparaat verbinding kan maken met Azure-Url's voor [open bare](migrate-appliance.md#public-cloud-urls) en [overheids](migrate-appliance.md#government-cloud-urls) Clouds.

### <a name="configure-the-appliance"></a>Het apparaat configureren

Het apparaat voor de eerste keer instellen.

> [!NOTE]
> Als u het apparaat instelt met behulp van een [Power shell-script](deploy-appliance-script.md) in plaats van de gedownloade VHD, zijn de eerste twee stappen in deze procedure niet relevant.

1. Klik in Hyper-V-beheer > **virtual machines**met de rechter muisknop op de virtuele machine > **verbinding maken**.
2. Geef de taal, de tijd zone en het wacht woord op voor het apparaat.
3. Open een browser op een computer die verbinding kan maken met de virtuele machine en open de URL van de Web-App van het apparaat: **https://*-apparaatnaam of IP-adres*: 44368**.

   U kunt de app ook vanuit het toestel bureau blad openen door te klikken op de snelkoppeling naar de app.
1. Ga als volgt te werk in de web-app > vereisten in te **stellen**:
    - **Licentie**: accepteer de licentie voorwaarden en lees de informatie van derden.
    - **Connectiviteit**: de app controleert of de virtuele machine toegang heeft tot internet. Als de virtuele machine gebruikmaakt van een proxy:
      - Klik op **proxy-instellingen**en geef het proxy adres en de luister poort op in http://ProxyIPAddress het http://ProxyFQDNformulier of.
      - Geef referenties op als de proxy verificatie nodig heeft.
      - Alleen HTTP-proxy wordt ondersteund.
    - **Tijd synchronisatie**: tijd wordt gecontroleerd. De tijd op het apparaat moet zijn gesynchroniseerd met internet tijd zodat de machine detectie goed werkt.
    - **Updates installeren**: Azure migrate server beoordeling controleert of de meest recente updates zijn geïnstalleerd op het apparaat.

### <a name="register-the-appliance-with-azure-migrate"></a>Het apparaat registreren bij Azure Migrate

1. Klik op **Aanmelden**. Als deze niet wordt weer gegeven, controleert u of de pop-upblokkering in de browser is uitgeschakeld.
2. Meld u aan met uw Azure-referenties op het tabblad Nieuw.
    - Meld u aan met uw gebruikers naam en wacht woord.
    - Aanmelden met een pincode wordt niet ondersteund.
3. Nadat u zich hebt aangemeld, gaat u terug naar de web-app.
4. Selecteer het abonnement waarin het Azure Migrate-project is gemaakt. Selecteer vervolgens het project.
5. Geef een naam op voor het apparaat. De naam moet alfanumeriek zijn met 14 tekens of minder.
6. Klik op **registreren**.


### <a name="delegate-credentials-for-smb-vhds"></a>Referenties voor SMB-Vhd's delegeren

Als u virtuele harde schijven uitvoert op Smb's, moet u de overdracht van referenties van het apparaat naar de Hyper-V-hosts inschakelen. Hiervoor moet u het volgende doen:

- U schakelt elke host in als gemachtigde voor het apparaat. Als u de zelf studies in volg orde hebt gevolgd, hebt u dit gedaan in de vorige zelf studie, wanneer u Hyper-V voor de evaluatie en migratie hebt voor bereid. U moet CredSSP [hand matig](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts)instellen voor de hosts of door [een script](tutorial-prepare-hyper-v.md#prepare-with-a-script) uit te voeren dat dit doet.
- Schakel CredSSP-delegering in, zodat het Azure Migrate apparaat kan fungeren als de client, en de referenties voor een host delegeren.

Schakel op het apparaat als volgt in:

#### <a name="option-1"></a>Mogelijkheid 1

Voer de volgende opdracht uit op de apparaat-VM. HyperVHost1/HyperVHost2 zijn voor beelden van hostnamen.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Voorbeeld: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Optie 2

U kunt dit ook doen in het Lokale groepsbeleidsobjecteditor op het apparaat:

1. Klik in de**computer configuratie**van het **lokale computer beleid** > op **Beheersjablonen** > **System** > **delegering**van systeem referenties.
2. Dubbel klik op **delegeren van nieuwe referenties toestaan**en selecteer **ingeschakeld**.
3. Klik in **Opties**op **weer geven**en voeg elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.
4. Dubbel klik vervolgens bij het **delegeren van referenties**op toestaan van het **delegeren van nieuwe referenties met NTLM-Server verificatie**. Voeg nogmaals elke Hyper-V-host die u wilt detecteren, toe aan de lijst met **wsman/** als voor voegsel.

## <a name="start-continuous-discovery"></a>Continue detectie starten

Maak verbinding van het apparaat met Hyper-V-hosts of-clusters en start de VM-detectie.

1. Geef bij **gebruikers naam** en **wacht woord**de account referenties op die door het apparaat worden gebruikt voor het detecteren van vm's. Geef een beschrijvende naam op voor de referenties en klik op **Details opslaan**.
2. Klik op **host toevoegen**en geef de gegevens van de Hyper-V-host/het cluster op.
3. Klik op **Valideren**. Na validatie wordt het aantal Vm's weer gegeven dat op elke host/het cluster kan worden gedetecteerd.
    - Als de validatie voor een host mislukt, controleert u de fout door de muis aanwijzer boven het pictogram in de kolom **status** te bewegen. Los problemen op en valideer opnieuw.
    - Als u hosts of clusters wilt verwijderen, selecteert u > **verwijderen**.
    - U kunt een specifieke host niet verwijderen uit een cluster. U kunt alleen het hele cluster verwijderen.
    - U kunt een cluster toevoegen, zelfs als er problemen zijn met specifieke hosts in het cluster.
4. Klik na validatie op **opslaan en start de detectie** om het detectie proces te starten.

De detectie wordt gestart. Het duurt ongeveer 1,5 minuten per host voor de meta gegevens van gedetecteerde servers die worden weer gegeven in de Azure Portal.

### <a name="verify-vms-in-the-portal"></a>VM's verifiëren in de portal

Nadat de detectie is voltooid, kunt u controleren of de virtuele machines in de portal worden weer gegeven.

1. Open het Azure Migrate dash board.
2. Klik op de pagina **Azure migrate servers** > **Azure migrate: Server beoordeling** op het pictogram met het aantal voor **gedetecteerde servers**.

## <a name="set-up-an-assessment"></a>Een evaluatie instellen

Er zijn twee soorten evaluaties die u kunt uitvoeren met behulp van Azure Migrate server-evaluatie.

**Beoordeling** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens | **Aanbevolen VM-grootte**: op basis van gegevens van CPU en geheugen gebruik.<br/><br/> **Aanbevolen schijf type (Standard of Premium Managed disk)**: op basis van de IOPS en door Voer van de on-premises schijven.
**Als on-premises** | Evaluaties op basis van on-premises grootte. | **Aanbevolen VM-grootte**: op basis van de on-PREMISes VM-grootte<br/><br> **Aanbevolen schijf type**: op basis van de instelling voor het opslag type die u voor de evaluatie selecteert.



### <a name="run-an-assessment"></a>Een evaluatie uitvoeren

Voer een evaluatie als volgt uit:

1. Bekijk de [Aanbevolen procedures](best-practices-assessment.md) voor het maken van evaluaties.
2. Klik in **servers** > **Azure migrate: Server evaluatie**op **evalueren**.

    ![Evalueren](./media/tutorial-assess-hyper-v/assess.png)

3. Geef in **servers beoordelen**een naam op voor de evaluatie.
4. Klik op **Alles weergeven** om de evaluatie-eigenschappen te controleren.

    ![Eigenschappen van beoordeling](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. In **een groep selecteren of maken**, selecteert u **nieuwe maken**en geeft u een groeps naam op. Een groep verzamelt een of meer Vm's samen voor evaluatie.
4. Selecteer op **computers toevoegen aan de groep**de optie vm's die u aan de groep wilt toevoegen.
5. Klik op **evaluatie maken** om de groep te maken en de evaluatie uit te voeren.

    ![Een evaluatie maken](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Nadat de evaluatie is gemaakt, bekijkt u deze in **servers** > **Azure migrate: Server Assessment**.
7. Klik op **Evaluatie exporteren** om deze te downloaden als een Excel-bestand.


## <a name="review-an-assessment"></a>Een evaluatie controleren

Een evaluatie beschrijft:

- **Azure-gereedheid**: of vm's geschikt zijn voor migratie naar Azure.
- **Schatting van maandelijkse kosten**: de geschatte maandelijkse reken-en opslag kosten voor het uitvoeren van de virtuele machines in Azure.
- **Schatting maandelijkse opslag kosten**: geschatte kosten voor schijf opslag na migratie.


### <a name="view-an-assessment"></a>Een evaluatie weer geven

1. In **migratie doelen** >  **servers** > **Azure migrate: Server evaluatie**klikt u op **evaluaties**.
2. Klik in **beoordelingen**op een evaluatie om deze te openen.

    ![Evaluatie samenvatting](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Azure-gereedheid controleren

1. Controleer in **Azure Readiness**of de vm's gereed zijn voor migratie naar Azure.
2. Controleer de status van de virtuele machine:
    - **Gereed voor Azure**: Azure migrate RAADT een VM-grootte en schattingen voor de kosten aan voor vm's in de evaluatie.
    - **Klaar met voor waarden**: toont problemen en voorgestelde herbemiddeling.
    - **Niet gereed voor Azure**: toont problemen en voorgestelde herbemiddeling.
    - **Gereedheid onbekend**: wordt gebruikt wanneer Azure migrate de gereedheid niet kan beoordelen door problemen met de beschik baarheid van gegevens.

2. Klik op de status van een **Azure-gereedheid** . U kunt details van de VM-gereedheid bekijken en inzoomen op de details van de virtuele machine, met inbegrip van compute-, opslag-en netwerk instellingen.

### <a name="review-cost-details"></a>Details van kosten bekijken

In deze weer gave ziet u de geschatte berekenings-en opslag kosten voor het uitvoeren van Vm's in Azure.

1. Bekijk de maandelijkse reken-en opslag kosten. De kosten worden geaggregeerd voor alle virtuele machines in de geraamde groep.

    - Schattingen van kosten zijn gebaseerd op de grootte aanbevelingen voor een machine en de schijven en eigenschappen.
    - De geschatte maandelijkse kosten voor Compute en opslag worden weer gegeven.
    - De kosten raming is voor het uitvoeren van de on-premises Vm's als IaaS Vm's. Azure Migrate server-evaluatie houdt geen rekening met PaaS-of SaaS-kosten.

2. U kunt de maandelijkse schattingen voor de opslag kosten bekijken. In deze weer gave worden geaggregeerde opslag kosten voor de geschatte groep weer gegeven, gesplitst over verschillende typen opslag schijven.
3. U kunt inzoomen om de details van specifieke Vm's te bekijken.


### <a name="review-confidence-rating"></a>Betrouwbaarheidsclassificatie controleren

Wanneer u evaluaties op basis van prestaties uitvoert, wordt een betrouwbaarheids classificatie aan de evaluatie toegewezen.

![Betrouwbaarheidsclassificatie](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Er wordt een classificatie van 1 ster (laagste) tot 5 sterren (hoogst) toegekend.
- De betrouwbaarheids classificatie helpt u bij het schatten van de betrouw baarheid van de grootte aanbevelingen van de evaluatie.
- De betrouwbaarheids classificatie is gebaseerd op de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.

De betrouwbaarheids classificaties voor een evaluatie zijn als volgt.

**Beschik baarheid van gegevens punt** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

Meer [informatie](best-practices-assessment.md#best-practices-for-confidence-ratings) over best practices voor betrouwbaarheids classificaties.





## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

> [!div class="checklist"]
> * Een Azure Migrate apparaat instellen
> * Een evaluatie gemaakt en geëvalueerd

Ga door naar de derde zelf studie in de reeks om te leren hoe u virtuele Hyper-V-machines naar Azure migreert met Azure Migrate server migratie.

> [!div class="nextstepaction"]
> [Hyper-V-VM's migreren](./tutorial-migrate-hyper-v.md)
