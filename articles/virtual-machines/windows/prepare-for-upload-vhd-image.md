---
title: Een Windows-VHD voorbereiden om te uploaden naar Azure
description: Meer informatie over het voorbereiden van een Windows VHD of VHDX om deze te uploaden naar Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 04/28/2020
ms.author: genli
ms.openlocfilehash: bf96cea2f64c52714ed6c63b0e973d0d26999856
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864382"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure

Voordat u een virtuele Windows-machine (VM) van on-premises naar Azure uploadt, moet u de virtuele harde schijf (VHD of VHDX) voorbereiden. Azure biedt ondersteuning voor virtuele machines van de eerste en tweede generatie die in VHD-bestands indeling zijn en die een schijf met een vaste grootte hebben. De Maxi maal toegestane grootte voor de VHD is 2 TB.

In een virtuele machine van de eerste generatie kunt u een VHDX-bestands systeem converteren naar VHD. U kunt ook een dynamisch uitbreid bare schijf converteren naar een schijf met een vaste grootte. Maar u kunt de generatie van een virtuele machine niet wijzigen. Zie [moet ik een generatie 1 of 2 virtuele machine in Hyper-V maken](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) en [ondersteuning voor virtuele machines van de tweede generatie op Azure](generation-2.md).

Zie [micro soft-server software ondersteuning voor Azure-vm's](https://support.microsoft.com/help/2721672/)voor meer informatie over het ondersteunings beleid voor virtuele Azure-machines.

> [!NOTE]
> De instructies in dit artikel zijn van toepassing op:
>
> - De 64-bits versie van Windows Server 2008 R2 en latere Windows Server-besturings systemen. Voor informatie over het uitvoeren van een 32-bits besturings systeem in azure, Zie [ondersteuning voor 32-bits besturings systemen in azure vm's](https://support.microsoft.com/help/4021388/).
> - Als een hulp programma voor herstel na nood gevallen wordt gebruikt voor het migreren van de werk belasting, zoals Azure Site Recovery of Azure Migrate, is dit proces nog vereist op het gast besturingssysteem om de installatie kopie voor te bereiden voor de migratie.

## <a name="system-file-checker"></a> Systeembestandscontrole

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Windows System File Checker-hulp programma uitvoeren vóór generalisatie van installatie kopie van besturings systeem

De System File Checker (SFC) wordt gebruikt om Windows-systeem bestanden te controleren en te vervangen.

> [!IMPORTANT]
> Gebruik een Power shell-sessie met verhoogde bevoegdheden om de voor beelden in dit artikel uit te voeren.

Voer de SFC-opdracht uit:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Nadat de SFC-scan is voltooid, installeert u Windows-updates en start u de computer opnieuw op.

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>De virtuele schijf converteren naar een VHD met een vaste grootte

Gebruik een van de methoden in deze sectie om de virtuele schijf te converteren naar de vereiste indeling voor Azure:

1. Maak een back-up van de VM voordat u het conversie proces voor de virtuele schijf uitvoert.

1. Zorg ervoor dat de VHD met Windows correct werkt op de lokale server. Los eventuele fouten op in de virtuele machine zelf voordat u deze converteert of uploadt naar Azure.

1. VHD-grootte:

   1. Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op 1 MB. Wanneer u van een onbewerkte schijf naar een VHD converteert, moet u ervoor zorgen dat de onbewerkte schijf grootte een meervoud van 1 MB is vóór de conversie.
      Fracties van een Mega byte veroorzaken fouten bij het maken van installatie kopieën van de geüploade VHD.

   1. De Maxi maal toegestane grootte voor de VHD van het besturings systeem is 2 TB.

Nadat u de schijf hebt geconverteerd, maakt u een virtuele machine die gebruikmaakt van de schijf. Start en meld u aan bij de virtuele machine om deze voor te bereiden voor het uploaden.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V-beheer gebruiken om de schijf te converteren

1. Open Hyper-V-beheer en selecteer uw lokale computer aan de linkerkant. Selecteer in het menu boven de computer lijst **actie** > **bewerken schijf**.
1. Selecteer uw virtuele schijf op de pagina **virtuele harde schijf zoeken** .
1. Selecteer op de pagina **actie kiezen** de optie**volgende** **omzetten** > .
1. Als u wilt converteren van VHDX, selecteert u **VHD** > **volgende**.
1. Als u wilt converteren van een dynamisch uitbreid bare schijf, selecteert u de optie **vaste grootte** > **volgende**.
1. Zoek en selecteer een pad om het nieuwe VHD-bestand op te slaan.
1. Selecteer **Finish**.

### <a name="use-powershell-to-convert-the-disk"></a>Power shell gebruiken om de schijf te converteren

U kunt een virtuele schijf converteren met behulp van de cmdlet [Convert-VHD](/powershell/module/hyper-v/convert-vhd) in Power shell.

In het volgende voor beeld wordt de schijf van VHDX naar VHD geconverteerd. De schijf wordt ook geconverteerd van een dynamisch uitbreid bare schijf naar een schijf met een vaste grootte.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Vervang in dit voor beeld de waarde voor **pad** door het pad naar de virtuele harde schijf die u wilt converteren. Vervang de waarde voor **doelpad** door het nieuwe pad en de naam van de geconverteerde schijf.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Converteren van VMware VMDK-schijf indeling

Als u een Windows VM-installatie kopie in de [VMDK-bestands indeling](https://en.wikipedia.org/wiki/VMDK)hebt, gebruikt u het [conversie programma van micro soft virtual machine](https://www.microsoft.com/download/details.aspx?id=42497) om het te converteren naar de VHD-indeling. Zie [How to convert a VMware VMDK to Hyper-V VHD](/archive/blogs/timomta/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd)(Engelstalig) voor meer informatie.

## <a name="set-windows-configurations-for-azure"></a>Windows-configuraties instellen voor Azure

> [!NOTE]
> Met het Azure-platform wordt een ISO-bestand gekoppeld aan de DVD-ROM wanneer een Windows-VM wordt gemaakt op basis van een gegeneraliseerde installatie kopie. Daarom moet de DVD-ROM in het besturings systeem in de gegeneraliseerde installatie kopie worden ingeschakeld. Als deze is uitgeschakeld, blijft de Windows-VM in de out-of-Box Experience (OOBE) zitten.

1. Verwijder alle statische permanente routes in de routerings tabel:

   - Als u de routerings tabel wilt `route.exe print`weer geven, voert u uit.
   - Controleer de sectie **persistentie routes** . Als er een permanente route is, gebruikt u `route.exe delete` de opdracht om deze te verwijderen.

1. De WinHTTP-proxy verwijderen:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Als de virtuele machine moet werken met een specifieke proxy, voegt u een proxy uitzondering toe voor het Azure IP-adres ([168.63.129.16](/azure/virtual-network/what-is-ip-address-168-63-129-16)) zodat de virtuele machine verbinding kan maken met Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Open Disk part:

   ```powershell
   diskpart.exe
   ```

   Stel het SAN-beleid van [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11))de schijf in op:

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. UTC-tijd (Coordinated Universal Time) instellen voor Windows. Stel ook het opstart type van de Windows Time-service **W32Time** in op **automatisch**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Stel het energie profiel in op hoge prestaties:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Zorg ervoor dat de omgevings variabelen **temp** en **tmp** zijn ingesteld op hun standaard waarden:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>De Windows-services controleren

Zorg ervoor dat elk van de volgende Windows-Services is ingesteld op de standaard waarde van Windows. Deze services zijn het minimale aantal dat moet worden geconfigureerd om verbinding met de virtuele machine te garanderen. Voer het volgende voor beeld uit om de opstart instellingen in te stellen:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Register instellingen voor extern bureau blad bijwerken

Controleer of de volgende instellingen juist zijn geconfigureerd voor externe toegang:

> [!NOTE]
> Als er een fout bericht wordt weer gegeven `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>`wanneer deze wordt uitgevoerd, kunt u dit veilig negeren. Dit betekent dat de configuratie niet door het domein wordt ingesteld via een groepsbeleid-object.

1. Remote Desktop Protocol (RDP) is ingeschakeld:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. De RDP-poort is correct ingesteld met de standaard poort 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Wanneer u een virtuele machine implementeert, worden de standaard regels gemaakt voor poort 3389. Als u het poort nummer wilt wijzigen, doet u dat nadat de virtuele machine in Azure is geïmplementeerd.

1. De listener luistert op elke netwerk interface:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Verificatie op netwerk niveau configureren voor de RDP-verbindingen:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Keep-Alive-waarde instellen:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Stel de opties voor opnieuw verbinden in:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Het aantal gelijktijdige verbindingen beperken:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Alle zelfondertekende certificaten die zijn gekoppeld aan de RDP-listener verwijderen:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Deze code zorgt ervoor dat u verbinding kunt maken wanneer u de virtuele machine implementeert. U kunt deze instellingen ook controleren nadat de virtuele machine in Azure is geïmplementeerd.

1. Als de virtuele machine deel uitmaakt van een domein, controleert u het volgende beleid om ervoor te zorgen dat de vorige instellingen niet worden teruggedraaid.

    |                 Doel                  |                                                                            Beleid                                                                            |                           Waarde                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP is ingeschakeld                        | Computer Ga Settings\Administrative Templates\Components\Remote Desktop, bureaublad-services\Extern Desktop Session Host\Connections         | Gebruikers toestaan om extern verbinding te maken met behulp van Extern bureaublad    |
    | NLA-groeps beleid                      | Settings\Administrative Templates\Components\Remote Desktop Host\Security                                                    | Gebruikers verificatie vereisen voor externe toegang met behulp van NLA |
    | Keep-Alive instellingen                   | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Keep-Alive-verbindings interval configureren                   |
    | Instellingen opnieuw verbinden                    | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Automatisch opnieuw verbinding maken                                    |
    | Beperkt aantal verbindings instellingen | Computer Ga Settings\Administrativee bureaublad-services\Extern bureau blad-sessie Host\Connections | Aantal verbindingen beperken                                |

## <a name="configure-windows-firewall-rules"></a>Windows Firewall-regels configureren

1. Schakel Windows Firewall in voor de drie profielen (domein, standaard en openbaar):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Voer het volgende voor beeld uit om WinRM toe te staan via de drie Firewall profielen (domein, privé en openbaar) en de externe Power shell-service in te scha kelen:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Schakel de volgende firewall regels in om het RDP-verkeer toe te staan:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Schakel de regel voor bestands-en printer deling in zodat de virtuele machine kan reageren op ping-aanvragen in het virtuele netwerk:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Maak een regel voor het Azure-platform netwerk:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Als de virtuele machine deel uitmaakt van een domein, controleert u het volgende Azure AD-beleid om ervoor te zorgen dat de vorige instellingen niet worden teruggedraaid.

    |                 Doel                 |                                                                         Beleid                                                                          |                  Waarde                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | De Windows Firewall profielen inschakelen | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Alle netwerk verbindingen beveiligen         |
    | RDP inschakelen                           | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Uitzonde ringen voor binnenkomende Extern bureaublad toestaan |
    |                                      | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Uitzonde ringen voor binnenkomende Extern bureaublad toestaan |
    | Enable ICMP-v4                       | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | ICMP-uitzonde ringen toestaan                   |
    |                                      | Computer Ga Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | ICMP-uitzonde ringen toestaan                   |

## <a name="verify-the-vm"></a>De VM controleren

Zorg ervoor dat de VM in orde, veilig en RDP toegankelijk is:

1. Controleer de schijf bij de volgende VM opnieuw opstarten om te controleren of de schijf in orde en consistent is.

   ```powershell
   chkdsk.exe /f
   ```

   Zorg ervoor dat het rapport een schone en gezonde schijf bevat.

1. Stel de instellingen voor de Boot Configuration Data (BCD) in.

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Het dump logboek kan nuttig zijn bij het oplossen van problemen met Windows-crashes. De logboek verzameling dump inschakelen:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Controleer of de opslag plaats van de Windows Management Instrumentation (WMI) consistent is:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Als de opslag plaats is beschadigd, raadpleegt u [WMI: beschadiging van opslag plaats of niet](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Zorg ervoor dat er geen andere toepassing gebruikmaakt van poort 3389. Deze poort wordt gebruikt voor de RDP-service in Azure. Voer het volgende uit `netstat.exe -anob`om te zien welke poorten op de VM worden gebruikt:

   ```powershell
   netstat.exe -anob
   ```

1. Een Windows-VHD uploaden die een domein controller is:

   - Volg [deze extra stappen](https://support.microsoft.com/kb/2904015) om de schijf voor te bereiden.

   - Zorg ervoor dat u het wacht woord van de Directory Services Restore Mode (DSRM) weet voor het geval u de virtuele machine in DSRM moet starten. Zie [een DSRM-wacht woord instellen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11))voor meer informatie.

1. Zorg ervoor dat u het ingebouwde Administrator-account en-wacht woord kent. Mogelijk wilt u het huidige lokale beheerders wachtwoord opnieuw instellen en ervoor zorgen dat u dit account kunt gebruiken om u aan te melden bij Windows via de RDP-verbinding. Deze toegangs machtiging wordt bepaald door het groepsbeleid-object ' Aanmelden via Extern bureaublad-services toestaan '. Dit object weer geven in de Lokale groepsbeleidsobjecteditor:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat de RDP-toegang niet wordt geblokkeerd:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Controleer het volgende Azure AD-beleid om ervoor te zorgen dat de vereiste toegangs accounts niet worden verwijderd:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   Het beleid moet de volgende groepen vermelden:

   - Beheerders

   - Back-upoperators

   - Iedereen

   - Gebruikers

1. Start de VM opnieuw op om er zeker van te zijn dat Windows nog steeds in orde is en kan worden bereikt via de RDP-verbinding. Op dit moment kunt u een virtuele machine op de lokale Hyper-V-server maken om ervoor te zorgen dat de VM volledig wordt gestart. Test vervolgens om te controleren of u de virtuele machine via RDP kunt bereiken.

1. Verwijder eventuele filters voor extra Transport Driver Interface (TDI). Verwijder bijvoorbeeld software waarmee TCP-pakketten of extra firewalls worden geanalyseerd. Als u dit later wilt bekijken, kunt u dit doen nadat de virtuele machine in Azure is geïmplementeerd.

1. Verwijder alle software of stuur Programma's van derden die betrekking hebben op fysieke onderdelen of andere virtualisatiesoftware.

### <a name="install-windows-updates"></a>Windows-updates installeren

In het ideale geval moet u de computer bijwerken op het niveau van de *patch*. Als dit niet mogelijk is, controleert u of de volgende updates zijn geïnstalleerd. Als u de meest recente updates wilt downloaden, gaat u naar de pagina Windows Update-geschiedenis: [Windows 10 en Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8,1 en Windows Server 2012 R2](https://support.microsoft.com/help/4009470) en [Windows 7 SP1 en Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Onderdeel        |     Binair     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8,1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10-v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Storage                 | schijf. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | Storport. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | NTFS. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg. dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401-KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr. sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx. sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm. sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | MPIO. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi. dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase. dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Netwerk                 | netvsc. sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | Mrxsmb. sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip. sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch. sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Kern                    | Ntoskrnl. exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Extern bureaublad-services | rdpcorets. dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv. dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415-KB3000850          | 10.0.14393.0 - KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | TermDD. sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd. dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd. sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Beveiliging                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Om te voor komen dat een onbedoeld opnieuw wordt opgestart tijdens het inrichten van de VM, is het raadzaam om ervoor te zorgen dat alle Windows Update-installaties zijn voltooid en dat er geen updates in behandeling zijn. Een manier om dit te doen is door alle mogelijke Windows-updates te installeren en één keer opnieuw `sysprep.exe` op te starten voordat u de opdracht uitvoert.

### <a name="determine-when-to-use-sysprep"></a>Bepalen wanneer u Sysprep wilt gebruiken

Het hulp programma voor`sysprep.exe`systeem voorbereiding () is een proces dat u kunt uitvoeren om een Windows-installatie opnieuw in te stellen.
Sysprep biedt een "out-of-Box"-ervaring door alle persoonlijke gegevens te verwijderen en verschillende onderdelen opnieuw in te stellen.

Normaal gesp roken `sysprep.exe` voert u uit om een sjabloon te maken van waaruit u verschillende virtuele machines kunt implementeren die een specifieke configuratie hebben. De sjabloon wordt een *gegeneraliseerde installatie kopie*genoemd.

Als u slechts één VM van één schijf wilt maken, hoeft u geen Sysprep te gebruiken. In plaats daarvan kunt u de virtuele machine maken op basis van een *gespecialiseerde installatie kopie*. Zie voor informatie over het maken van een virtuele machine op basis van een gespecialiseerde schijf:

- [Een VM maken van een gespecialiseerde schijf](create-vm-specialized.md)
- [Een virtuele machine maken op basis van een speciale VHD-schijf](/azure/virtual-machines/windows/create-vm-specialized-portal)

Als u een gegeneraliseerde installatie kopie wilt maken, moet u Sysprep uitvoeren. Zie [Sysprep gebruiken: een inleiding](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10))voor meer informatie.

Niet elke rol of toepassing die is geïnstalleerd op een Windows-computer, ondersteunt gegeneraliseerde installatie kopieën. Voordat u deze procedure gebruikt, moet u controleren of Sysprep de rol van de computer ondersteunt. Zie [Sysprep-ondersteuning voor Server functies](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles)voor meer informatie.

### <a name="generalize-a-vhd"></a>Een VHD generaliseren

>[!NOTE]
> Nadat u in `sysprep.exe` de volgende stappen hebt uitgevoerd, schakelt u de virtuele machine uit. Schakel deze optie niet weer in totdat u een installatie kopie maakt in Azure.

1. Meld u aan bij de Windows-VM.
1. Een Power shell-sessie uitvoeren als beheerder.
1. Wijzig de Directory in `%windir%\system32\sysprep`. Voer vervolgens `sysprep.exe` uit.
1. Selecteer in het dialoog venster **hulp programma voor systeem voorbereiding** de optie **systeem out-of-Box Experience (OOBE) opgeven**en zorg ervoor dat het selectie vakje **generalize** is geselecteerd.

    ![Hulp programma voor systeem voorbereiding](media/prepare-for-upload-vhd-image/syspre.png)
1. Selecteer **Afsluiten**in het **afsluit opties**.
1. Selecteer **OK**.
1. Sluit de virtuele machine af wanneer Sysprep is voltooid. Gebruik niet **opnieuw opstarten** om de virtuele machine af te sluiten.

De VHD is nu klaar om te worden geüpload. Zie [een gegeneraliseerde VHD uploaden en deze gebruiken om een nieuwe virtuele machine in azure te maken](sa-upload-generalized.md)voor meer informatie over het maken van een virtuele machine op basis van een gegeneraliseerde schijf.

>[!NOTE]
> Een aangepast bestand *Unattend. XML* wordt niet ondersteund. Hoewel we de eigenschap **additionalUnattendContent** ondersteunen, biedt dit alleen beperkte ondersteuning voor het toevoegen van opties voor [micro soft-Windows-Shell-Setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) in het bestand *Unattend. XML* dat door de inrichtings agent van Azure wordt gebruikt. U kunt bijvoorbeeld [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) gebruiken om FirstLogonCommands en LogonCommands toe te voegen. Zie [voor beeld van AdditionalUnattendContent FirstLogonCommands](https://github.com/Azure/azure-quickstart-templates/issues/1407)voor meer informatie.

## <a name="complete-the-recommended-configurations"></a>De aanbevolen configuraties volt ooien

De volgende instellingen zijn niet van invloed op het uploaden van de VHD. We raden u echter ten zeerste aan om deze te configureren.

- Installeer de [agent van de virtuele Azure-machine](https://go.microsoft.com/fwlink/?LinkID=394789). Vervolgens kunt u VM-extensies inschakelen. De VM-extensies implementeren de meeste essentiële functionaliteit die u mogelijk wilt gebruiken met uw virtuele machines. U hebt de uitbrei dingen nodig, bijvoorbeeld om wacht woorden opnieuw in te stellen of RDP te configureren. Zie overzicht van de [Azure virtual machine agent](../extensions/agent-windows.md)voor meer informatie.
- Nadat u de virtuele machine in azure hebt gemaakt, raden we u aan het wissel bestand op het *tijdelijke schijf volume* te plaatsen om de prestaties te verbeteren. U kunt de bestands plaatsing als volgt instellen:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Als een gegevens schijf aan de virtuele machine is gekoppeld, is de stationsletter van het tijdelijke station meestal *D*. Deze aanwijzing kan verschillen, afhankelijk van de instellingen en het aantal beschik bare stations.

  - Het is raadzaam om script blokken uit te scha kelen die mogelijk worden verschaft door antivirus software. Ze kunnen de Windows-inrichtings Agent-scripts die worden uitgevoerd wanneer u een nieuwe virtuele machine vanuit uw installatie kopie implementeert, interfereren en blok keren.

## <a name="next-steps"></a>Volgende stappen

- [Een Windows VM-installatie kopie uploaden naar Azure voor implementaties van Resource Manager](upload-generalized-managed.md)
- [Problemen met activering van Azure Windows VM oplossen](troubleshoot-activation-problems.md)
