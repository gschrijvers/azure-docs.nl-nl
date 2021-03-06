---
title: Veelgestelde vragen-Azure dedicated HSM | Microsoft Docs
description: Veelgestelde vragen met betrekking tot verschillende onderwerpen over een speciale Azure-HSM
services: dedicated-hsm
author: johncdawson
manager: rkarlin
tags: azure-resource-manager
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 8f9e759372f01d2a1b48562aef2bace1e8435a67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81683313"
---
# <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vind antwoorden op veelgestelde vragen over Microsoft Azure specifieke HSM.

## <a name="the-basics"></a>De basis beginselen

### <a name="q-what-is-a-hardware-security-module-hsm"></a>V: wat is een Hardware Security module (HSM)?

Een Hardware Security module (HSM) is een fysiek computer apparaat dat wordt gebruikt voor het beveiligen en beheren van cryptografische sleutels. Sleutels die zijn opgeslagen in Hsm's kunnen worden gebruikt voor cryptografische bewerkingen. Het sleutel materiaal blijft veilig in verknoei bestendige hardware-modules. Met de HSM kunnen geverifieerde en geautoriseerde toepassingen alleen de sleutels gebruiken. Het sleutel materiaal verlaat nooit de HSM-beschermings grens.

### <a name="q-what-is-the-azure-dedicated-hsm-offering"></a>V: wat is de voor Azure speciale HSM-aanbieding?

De toegewezen HSM van Azure is een Cloud service die Hsm's ondervindt in azure-data centers die rechtstreeks zijn verbonden met het virtuele netwerk van een klant. Deze Hsm's zijn speciale netwerk apparaten (Gemalto SafeNet Network HSM 7 model A790). Ze worden direct geïmplementeerd in de privé-IP-adres ruimte van een klant en micro soft heeft geen toegang tot de cryptografische functionaliteit van de Hsm's. Alleen de klant heeft volledige beheer-en cryptografische controle over deze apparaten. Klanten zijn verantwoordelijk voor het beheer van het apparaat en ze kunnen volledige activiteiten logboeken rechtstreeks vanaf hun apparaten ontvangen. Gespecialiseerde Hsm's helpen klanten te voldoen aan nalevings vereisten, zoals FIPS 140-2 level 3, HIPAA, PCI-DSS en eIDAS en vele andere.

### <a name="q-what-hardware-is-used-for-dedicated-hsm"></a>V: welke hardware wordt gebruikt voor een toegewezen HSM?

Micro soft heeft een partnerschap met Gemalto voor het leveren van de toegewezen HSM-service van Azure. Het specifieke apparaat dat wordt gebruikt, is het [SafeNet Luna Network HSM 7 model A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/). Dit apparaat biedt niet alleen de FIPS 140-2-gevalideerde firmware op niveau 3, maar biedt ook lage latentie, hoge prestaties en hoge capaciteit via 10 partities. 

### <a name="q-what-is-an-hsm-used-for"></a>V: waarvoor wordt een HSM gebruikt?

Hsm's worden gebruikt voor het opslaan van cryptografische sleutels die worden gebruikt voor cryptografische functionaliteit, zoals TLS (trans port Layer Security), het versleutelen van gegevens, PKI (open bare-sleutel infrastructuur), DRM (Digital Rights Management) en het ondertekenen van documenten.

### <a name="q-how-does-dedicated-hsm-work"></a>V: Hoe werkt speciale HSM?

Klanten kunnen Hsm's in specifieke regio's inrichten met behulp van Power shell of de opdracht regel interface. De klant geeft aan met welk virtueel netwerk de Hsm's verbinding wordt gemaakt en nadat de Hsm's is ingericht, wordt in het aangewezen subnet op toegewezen IP-adressen in de privé-IP-adres ruimte van de klant beschikbaar gesteld. Vervolgens kunnen klanten via SSH verbinding maken met de Hsm's voor het beheer van apparaten en het beheer, het instellen van HSM-client verbindingen, het initialiseren van Hsm's, het maken van partities, het definiëren en toewijzen van rollen zoals de partitie-Officer, de versleutelingsset en de gebruiker van crypto grafie. De klant gebruikt vervolgens Gemalto die de HSM-client hulpprogramma's/SDK/software heeft verschaft om cryptografische bewerkingen uit hun toepassingen uit te voeren.

### <a name="q-what-software-is-provided-with-the-dedicated-hsm-service"></a>V: welke software wordt meegeleverd met de speciale HSM-service?

Gemalto levert alle software voor het HSM-apparaat nadat het is ingericht door micro soft. De software is beschikbaar op de [Gemalto-portal voor klant ondersteuning](https://supportportal.gemalto.com/csm/). Klanten die de speciale HSM-service gebruiken, moeten worden geregistreerd voor Gemalto-ondersteuning en beschikken over een klant-ID waarmee toegang kan worden gezocht en de relevante software kan worden gedownload. De ondersteunde client software is versie 7,2, die compatibel is met de gevalideerde firmware versie van de FIPS 140-2 level 3-7.0.3. 

### <a name="q-does-azure-dedicated-hsm-offer-password-based-and-ped-based-authentication"></a>V: heeft Azure toegewezen HSM een op wacht woord gebaseerde en PED verificatie aangeboden?

Op dit moment biedt Azure dedicated HSM alleen Hsm's met verificatie op basis van wacht woorden.

### <a name="q-will-azure-dedicated-hsm-host-my-hsms-for-me"></a>V: zal Azure dedicated HSM host mijn Hsm's voor mij?

Micro soft biedt alleen de Gemalto SafeNet Luna Network HSM via de toegewezen HSM-service en kan geen door klanten geleverde apparaten hosten.

### <a name="q-does-azure-dedicated-hsm-support-payment-pineft-features"></a>V: heeft Azure exclusieve HSM-functies voor de ondersteuning van betalingen (PINCODEs/EFT)?

De specifieke HSM-service van Azure maakt gebruik van SafeNet Luna Network HSM 7-apparaten (model A790). Deze apparaten bieden geen ondersteuning voor de specifieke functionaliteit voor betalings-HSM (zoals pincode of EFT) of certificeringen. Als u wilt dat de exclusieve HSM-service van Azure in de toekomst betaling Hsm's ondersteunt, geeft u de feedback door aan uw micro soft-account vertegenwoordiger.

### <a name="q-which-azure-regions-is-dedicated-hsm-available-in"></a>V: in welke Azure-regio's is een speciale HSM beschikbaar?

Vanaf eind maart 2019 is toegewezen HSM beschikbaar in de volgende 14 regio's. Verdere regio's worden gepland en kunnen worden besproken via de vertegenwoordiger van uw micro soft-account.

* VS - oost
* VS - oost 2
* VS - west
* VS - west 2
* VS - zuid-centraal
* Azië - zuidoost
* Azië - oost
* India - centraal
* India - zuid
* Japan - oost
* Japan - west
* Europa - noord
* Europa -west
* Verenigd Koninkrijk Zuid
* Verenigd Koninkrijk West
* Canada - midden
* Canada - oost
* Australië - oost
* Australië - zuidoost
* Zwitserland - noord
* Zwitserland - west
* VS (overheid) - Virginia
* VS (overheid) - Texas

## <a name="interoperability"></a>Interoperabiliteit

### <a name="q-how-does-my-application-connect-to-a-dedicated-hsm"></a>V: Hoe maakt mijn toepassing verbinding met een toegewezen HSM?

U gebruikt Gemalto voor HSM-client hulpprogramma's/SDK/software voor het uitvoeren van cryptografische bewerkingen vanuit uw toepassingen. De software is beschikbaar op de [Gemalto-portal voor klant ondersteuning](https://supportportal.gemalto.com/csm/). Klanten die de speciale HSM-service gebruiken, moeten worden geregistreerd voor Gemalto-ondersteuning en beschikken over een klant-ID waarmee toegang kan worden gezocht en de relevante software kan worden gedownload.

### <a name="q-can-an-application-connect-to-dedicated-hsm-from-a-different-vnet-in-or-across-regions"></a>V: kan een toepassing verbinding maken met een specifieke HSM vanuit een ander VNET in of tussen verschillende regio's?

Ja, u moet [VNET-peering](../virtual-network/virtual-network-peering-overview.md) binnen een regio gebruiken om verbinding te maken tussen virtuele netwerken. Voor connectiviteit tussen regio's moet u [VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)gebruiken.

### <a name="q-can-i-synchronize-dedicated-hsm-with-on-premises-hsms"></a>V: kan ik een specifieke HSM synchroniseren met on-premises Hsm's?

Ja, u kunt on-premises Hsm's synchroniseren met toegewezen HSM. [Point-to-Point VPN-verbinding of punt-naar-site](../vpn-gateway/vpn-gateway-about-vpngateways.md) -connectiviteit kan worden gebruikt om verbinding te maken met uw on-premises netwerk.

### <a name="q-can-i-encrypt-data-used-by-other-azure-services-using-keys-stored-in-dedicated-hsm"></a>V: kan ik gegevens die worden gebruikt door andere Azure-Services versleutelen met sleutels die zijn opgeslagen in een toegewezen HSM?

Nee. Toegewezen Azure-Hsm's zijn alleen toegankelijk vanuit uw virtuele netwerk.

### <a name="q-can-i-import-keys-from-an-existing-on-premises-hsm-to-dedicated-hsm"></a>V: kan ik sleutels uit een bestaande HSM op locatie importeren in een speciale HSM?

Ja, als u on-premises Gemalto SafeNet Hsm's hebt. Er zijn meerdere methoden. Raadpleeg de Gemalto HSM-documentatie.

### <a name="q-what-operating-systems-are-supported-by-dedicated-hsm-client-software"></a>V: welke besturings systemen worden ondersteund door toegewezen HSM-client software?

* Windows, Linux, Solaris, AIX, HP-UX, FreeBSD
* Virtual: VMware, Hyper, xen, KVM

### <a name="q-how-do-i-configure-my-client-application-to-create-a-high-availability-configuration-with-multiple-partitions-from-multiple-hsms"></a>V: Hoe kan ik mijn client toepassing configureren om een configuratie met een hoge Beschik baarheid te maken met meerdere partities van meerdere Hsm's?

Als u maximale Beschik baarheid wilt, moet u de configuratie van de HSM-client toepassing instellen op het gebruik van partities van elke HSM. Raadpleeg de documentatie van de Gemalto HSM-client software.

### <a name="q-what-authentication-mechanisms-are-supported-by-dedicated-hsm"></a>V: welke verificatie mechanismen worden ondersteund door een toegewezen HSM?

Azure dedicated HSM maakt gebruik van SafeNet netwerk HSM 7-apparaten (model A790) en biedt ondersteuning voor verificatie op basis van wacht woorden.

### <a name="q-what-sdks-apis-client-software-is-available-to-use-with-dedicated-hsm"></a>V: welke Sdk's, Api's, client software is beschikbaar voor gebruik met toegewezen HSM?

PKCS # 11, java (JCA/JCE), micro soft CAPI en CNG, OpenSSL

### <a name="q-can-i-importmigrate-keys-from-luna-56-hsms-to-azure-dedicated-hsms"></a>V: kan ik sleutels importeren/migreren van Luna 5/6 Hsm's naar toegewezen Hsm's van Azure?

Ja. Raadpleeg de Gemalto-migratie handleiding. 

## <a name="using-your-hsm"></a>Uw HSM gebruiken

### <a name="q-how-do-i-decide-whether-to-use-azure-key-vault-or-azure-dedicated-hsm"></a>V: Hoe kan ik beslissen of u Azure Key Vault of een specifieke HSM van Azure wilt gebruiken?

De specifieke HSM van Azure is de juiste keuze voor ondernemingen die migreren naar Azure on-premises toepassingen die gebruikmaken van Hsm's. De toegewezen Hsm's bevat een optie voor het migreren van een toepassing met minimale wijzigingen. Als cryptografische bewerkingen worden uitgevoerd in de code van de toepassing die wordt uitgevoerd in een virtuele machine of Web-App van Azure, kunnen ze speciale HSM gebruiken. In het algemeen is het mogelijk om software die wordt uitgevoerd in IaaS (Infrastructure as a Service), te gebruiken voor het gebruik van Hsm's als een sleutel archief die ondersteuning biedt voor de HSM, zoals Application Gateway of Traffic Manager voor minder dan TLS, ADCS (Active Directory Certificate Services) of vergelijk bare PKI-hulpprogram ma's, hulpprogram ma's/toepassingen die worden gebruikt voor het ondertekenen van documenten, ondertekening van programma code of een SQL Server (IaaS) die is geconfigureerd met TDE (transparante database versleuteling) met hoofd sleutel in een HSM met een EKM-provider (Extensible Key Management). Azure Key Vault is geschikt voor toepassingen die in de cloud worden uitgevoerd of voor versleuteling bij rest scenario's waarin klant gegevens worden verwerkt door PaaS (platform as a Service) of SaaS-scenario's (Software as a Service) zoals Office 365-klant sleutel, Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store versleuteling met door de klant beheerde sleutel, Azure Storage versleuteling met door de klant beheerde sleutel en Azure SQL met door de klant beheerde sleutel.

### <a name="q-what-usage-scenarios-best-suit-azure-dedicated-hsm"></a>V: welke gebruiks scenario's Best passend zijn voor de specifieke HSM van Azure?

De specifieke HSM van Azure is het meest geschikt voor migratie scenario's. Dit betekent dat als u on-premises toepassingen naar Azure migreert, al gebruikmaken van Hsm's. Dit biedt een laag gerichte optie voor het migreren naar Azure met minimale wijzigingen in de toepassing. Als cryptografische bewerkingen worden uitgevoerd in de code van de toepassing die wordt uitgevoerd in een Azure-VM of web-app, kan specifieke HSM worden gebruikt. In het algemeen kan software die wordt uitgevoerd in IaaS-modellen (Infrastructure as a Service), die Hsm's als een sleutel archief ondersteunen, gebruikmaken van een speciale HSM, zoals:

* Application Gateway of Traffic Manager voor minder dan TLS
* ADCS (Active Directory Certificate Services)
* Vergelijk bare PKI-hulpprogram ma's
* Hulpprogram ma's/toepassingen die worden gebruikt voor het ondertekenen van documenten
* Hand tekening bij programma code
* SQL Server (IaaS) geconfigureerd met TDE (transparante database versleuteling) met hoofd sleutel in een HSM met behulp van een EKM-provider (Extensible Key Management)

### <a name="q-can-dedicated-hsm-be-used-with-office-365-customer-key-azure-information-protection-azure-data-lake-store-disk-encryption-azure-storage-encryption-azure-sql-tde"></a>V: kan specifieke HSM worden gebruikt met de klant sleutel van Office 365, Azure Information Protection, Azure Data Lake Store, schijf versleuteling, Azure Storage versleuteling, Azure SQL TDE?

Nee. Toegewezen HSM wordt rechtstreeks ingericht in de privé-IP-adres ruimte van een klant zodat deze niet toegankelijk is voor andere Azure-of micro soft-Services.

## <a name="administration-access-and-control"></a>Beheer, toegang en beheer

### <a name="q-does-the-customer-get-full-exclusive-control-over-the-hsms-with-dedicated-hsms"></a>V: heeft de klant volledige exclusieve controle over de Hsm's met de toegewezen Hsm's?

Ja. Elk HSM-apparaat is volledig toegewezen aan één klant en niemand heeft een beheer beheer na de inrichting en het beheerders wachtwoord is gewijzigd.

### <a name="q-what-level-of-access-does-microsoft-have-to-my-hsm"></a>V: welk toegangs niveau heeft micro soft voor mijn HSM?

Micro soft heeft geen beheer-of cryptografische controle over de HSM. Micro soft heeft toegang op monitor niveau via een seriële poort verbinding om eenvoudige telemetrie te verkrijgen, zoals de status van de Tempe ratuur en het onderdeel. Hierdoor kan micro soft proactieve meldingen over status problemen bieden. Als dat nodig is, kan de klant dit account uitschakelen.

### <a name="q-what-is-the-tenantadmin-account-microsoft-uses-i-am-used-to-the-admin-user-being-admin-on-safenet-hsms"></a>V: wat is het ' tenantadmin-account dat wordt gebruikt door micro soft, ik heb de beheerder van de gebruiker ' admin ' op SafeNet Hsm's?

Het HSM-apparaat wordt geleverd met een standaard gebruiker van de beheerder met het gebruikelijke standaard wachtwoord. Micro soft wil geen standaard wachtwoorden gebruiken terwijl een apparaat in een groep wacht om te worden ingericht door klanten. Dit voldoet niet aan onze strikte beveiligings vereisten. Daarom stellen we een sterk wacht woord in dat wordt verwijderd bij de inrichtings tijd. Bij het inrichten van de tijd maken we ook een nieuwe gebruiker in de rol Admin met de naam ' tenantadmin '. Deze gebruiker heeft het standaard wachtwoord en klanten wijzigen dit als de eerste actie wanneer u zich voor het eerst aanmeldt bij het nieuw ingerichte apparaat. Dit proces garandeert een hoge mate van beveiliging en onderhoudt onze belofte van exclusieve administratieve controle voor onze klanten. Houd er rekening mee dat de gebruiker ' tenantadmin ' kan worden gebruikt om het wacht woord van de gebruikers beheerder opnieuw in te stellen als een klant dat account liever gebruikt. 

### <a name="q-can-microsoft-or-anyone-at-microsoft-access-keys-in-my-dedicated-hsm"></a>V: kunnen micro soft of iemand bij micro soft Access Keys in mijn toegewezen HSM?

Nee. Micro soft heeft geen toegang tot de sleutels die zijn opgeslagen in de toegewezen HSM van de klant.

### <a name="q-can-i-upgrade-softwarefirmware-on-hsms-allocated-to-me"></a>V: kan ik een upgrade uitvoeren van software/firmware op Hsm's die aan mij zijn toegewezen?

Micro soft raadt u ten zeerste aan om de software/firmware op de HSM niet bij te werken. De klant heeft echter volledige administratieve controle, inclusief het upgraden van software/firmware als er specifieke functies zijn vereist uit verschillende firmware versies. Voordat u wijzigingen aanbrengt, moeten de implicaties worden begrepen, omdat dit bijvoorbeeld van invloed is op de FIPS-gevalideerde status. 

### <a name="q-how-do-i-manage-dedicated-hsm"></a>V: Hoe kan ik specifieke HSM beheren?

U kunt toegewezen Hsm's beheren door ze te openen via SSH.

### <a name="q-how-do-i-manage-partitions-on-the-dedicated-hsm"></a>V: Hoe kan ik partities op de toegewezen HSM beheren?

De Gemalto HSM-client software wordt gebruikt voor het beheren van de Hsm's en partities.

### <a name="q-how-do-i-monitor-my-hsm"></a>V: Hoe kan ik bewaken van mijn HSM?

Een klant heeft volledige toegang tot HSM-activiteiten logboeken via syslog en SNMP. Een klant moet een syslog-server of SNMP-server instellen voor het ontvangen van de logboeken of gebeurtenissen van de Hsm's.

### <a name="q-can-i-get-full-access-log-of-all-hsm-operations-from-dedicated-hsm"></a>V: kan ik een volledig toegangs logboek krijgen van alle HSM-bewerkingen van een toegewezen HSM?

Ja. U kunt Logboeken van het HSM-apparaat naar een syslog-server verzenden

## <a name="high-availability"></a>Hoge beschikbaarheid

### <a name="q-is-it-possible-to-configure-high-availability-in-the-same-region-or-across-multiple-regions"></a>V: is het mogelijk om hoge Beschik baarheid te configureren in dezelfde regio of in meerdere regio's?

Ja. Configuratie van hoge Beschik baarheid en Setup worden uitgevoerd in de HSM-client software van Gemalto. Hsm's van hetzelfde VNET of andere VNETs in dezelfde regio of in verschillende regio's, of on-premises Hsm's die zijn verbonden met een VNET met behulp van site-naar-site-of Point-to-Point VPN, kunnen worden toegevoegd aan dezelfde configuratie voor hoge Beschik baarheid. U moet erop gewezen dat het sleutel materiaal alleen synchroniseert en geen specifieke configuratie-items zoals rollen.

### <a name="q-can-i-add-hsms-from-my-on-premises-network-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: kan ik Hsm's van mijn on-premises netwerk toevoegen aan een groep met hoge Beschik baarheid met een toegewezen HSM van Azure?

Ja. Ze moeten voldoen aan de vereisten voor hoge Beschik baarheid voor SafeNet Luna Network HSM 7.

### <a name="q-can-i-add-luna-56-hsms-from-on-premises-networks-to-a-high-availability-group-with-azure-dedicated-hsm"></a>V: kan ik Luna 5/6 Hsm's van on-premises netwerken toevoegen aan een groep met hoge Beschik baarheid met een toegewezen HSM van Azure?

Nee.

### <a name="q-how-many-hsms-can-i-add-to-the-same-high-availability-configuration-from-one-single-application"></a>V: hoeveel Hsm's kan ik toevoegen aan dezelfde configuratie voor hoge Beschik baarheid vanuit één enkele toepassing?

16 leden van een HA-groep hebben onder-weg, Full-Throttle tests met uitstekende resultaten.

## <a name="support"></a>Ondersteuning

### <a name="q-what-is-the-sla-for-dedicated-hsm-service"></a>V: wat is de SLA voor dedicated HSM service?

Er is geen specifieke uptime-garantie opgegeven voor de speciale HSM-service. Micro soft zorgt voor toegang tot het apparaat op netwerk niveau en daarom gelden er standaard overeenkomsten voor Azure-netwerken.

### <a name="q-how-are-the-hsms-used-in-azure-dedicated-hsm-protected"></a>V: hoe wordt het Hsm's gebruikt in een door Azure toegewezen HSM?

Azure-data centers hebben uitgebreide fysieke en procedures voor beveiligings controles. Behalve dat specifieke Hsm's worden gehost in een verder beperkt toegangs gebied van het Data Center. Deze gebieden hebben extra fysieke toegangs controles en een video camera bewaking voor extra beveiliging.

### <a name="q-what-happens-if-there-is-a-security-breach-or-hardware-tampering-event"></a>V: wat gebeurt er als er sprake is van een inbreuk op de beveiliging of het knoeien met de hardware?

De speciale HSM-service maakt gebruik van SafeNet netwerk HSM 7-apparaten. Deze apparaten ondersteunen detectie van fysieke en logische manipulatie. Als er zich een onrecht matige gebeurtenis voordoet, worden de Hsm's automatisch nul.

### <a name="q-how-do-i-ensure-that-keys-in-my-dedicated-hsms-are-not-lost-due-to-error-or-a-malicious-insider-attack"></a>V: Hoe kan ik ervoor te zorgen dat sleutels in mijn toegewezen Hsm's niet verloren gaan als gevolg van een fout of een kwaadwillende Insider-aanval?

Het is raadzaam om een on-premises HSM-back-upapparaat te gebruiken om regel matig periodieke back-ups te maken van de Hsm's voor herstel na nood gevallen. U moet een peer-to-peer-of site-naar-site-VPN-verbinding gebruiken voor een on-premises werk station dat is verbonden met een HSM-back-upapparaat.

### <a name="q-how-do-i-get-support-for-dedicated-hsm"></a>V: Hoe kan ik ondersteuning voor toegewezen HSM verkrijgen?

Ondersteuning wordt geboden door zowel micro soft als Gemalto.  Als u een probleem hebt met de hardware-of netwerk toegang, verhoogt u een ondersteunings aanvraag met micro soft en als u een probleem ondervindt met HSM-configuratie, software en toepassings ontwikkeling, wordt een ondersteunings aanvraag met Gemalto gegenereerd. Als u een onbepaald probleem hebt, kunt u een ondersteunings aanvraag indienen bij micro soft en kan Gemalto indien nodig worden ingeschakeld. 

### <a name="q-how-do-i-get-the-client-software-documentation-and-access-to-integration-guidance-for-the-safenet-luna-7-hsm"></a>V: Hoe kan ik de client software, documentatie en toegang verkrijgen tot integratie richtlijnen voor de SafeNet Luna 7 HSM?

Nadat u zich hebt geregistreerd voor de service, wordt er een Gemalto-klant-ID gegeven die registratie toestaat in de Gemalto-portal voor klanten ondersteuning. Hiermee wordt toegang tot alle software en documentatie ingeschakeld en worden ondersteunings aanvragen rechtstreeks met Gemalto ingeschakeld.

### <a name="q-if-there-is-a-security-vulnerability-found-and-a-patch-is-released-by-gemalto-who-is-responsible-for-upgradingpatching-osfirmware"></a>V: als er een beveiligings probleem is gevonden en er een patch wordt uitgebracht door Gemalto, wie verantwoordelijk is voor het bijwerken/patchen van besturings systeem/firmware?

Micro soft heeft niet de mogelijkheid om verbinding te maken met Hsm's die aan klanten zijn toegewezen. Klanten moeten hun Hsm's bijwerken en patchen.

### <a name="q-what-if-i-need-to-reboot-my-hsm"></a>V: wat moet ik doen als ik mijn HSM opnieuw opstart?

De HSM heeft een optie voor opnieuw opstarten van de opdracht regel. er zijn echter problemen met het opnieuw opstarten van de computer en daarom wordt het aanbevolen om de veiligste keer dat u een ondersteunings aanvraag met micro soft hebt uitgevoerd om het apparaat fysiek opnieuw op te starten. 

## <a name="cryptography-and-standards"></a>Crypto grafie en standaarden

### <a name="q-is-it-safe-to-store-encryption-keys-for-my-most-important-data-in-dedicated-hsm"></a>V: is het veilig om versleutelings sleutels voor mijn belangrijkste gegevens op te slaan in een toegewezen HSM?

Ja, specifieke HSM-bepalingen SafeNet netwerk HSM 7-apparaten die gebruikmaken van FIPS 140-2 level 3-gevalideerde Hsm's. 

### <a name="q-what-cryptographic-keys-and-algorithms-are-supported-by-dedicated-hsm"></a>V: welke cryptografische sleutels en algoritmen worden ondersteund door een toegewezen HSM?

Speciale HSM-service bepalingen SafeNet netwerk HSM 7-apparaten. Ze bieden ondersteuning voor een breed scala aan cryptografische sleutel typen en algoritmen, waaronder: volledige Suite B-ondersteuning

* Asymmetrisch
  * RSA
  * DSA
  * Diffie-Hellman
  * Elliptische curve
  * Crypto grafie (ECDSA, ECDH, Ed25519, ECIES) met benoemde, door de gebruiker gedefinieerde en Brainpool curven, KCDSA
* Symmetrisch
  * AES-GCM
  * Triple DES
  * DES
  * ARIA, SEED
  * RC2
  * RC4
  * RC5
  * CAST
  * Hash/Message Digest/HMAC: SHA-1, SHA-2, SM3
  * Sleutel afleiding: SP800-108 meter modus
  * Sleutel terugloop: SP800-38F
  * Het genereren van wille keurige getallen: door FIPS 140-2 goedgekeurde DRBG (SP 800-90-modus), die voldoet aan Institution DRG. 4

### <a name="q-is-dedicated-hsm-fips-140-2-level-3-validated"></a>V: is toegewezen HSM FIPS 140-2 level 3 gevalideerd?

Ja. Speciale HSM-service bepalingen SafeNet netwerk HSM 7-apparaten die gebruikmaken van FIPS 140-2 level 3-gevalideerde Hsm's.

### <a name="q-what-do-i-need-to-do-to-make-sure-i-operate-dedicated-hsm-in-fips-140-2-level-3-validated-mode"></a>V: wat moet ik doen om ervoor te zorgen dat ik specifieke HSM in de modus voor de gevalideerde FIPS 140-2 level 3 heb?

De speciale HSM-service bepalingen SafeNet Luna Network HSM 7 Appliances. Op deze apparaten wordt gebruikgemaakt van FIPS 140-2 level 3-gevalideerde Hsm's. De standaard geïmplementeerde configuratie, het besturings systeem en de firmware zijn ook FIPS-validatie. U hoeft geen actie te ondernemen voor naleving van FIPS 140-2 level 3.

### <a name="q-how-does-a-customer-ensure-that-when-an-hsm-is-deprovisioned-all-the-key-material-is-wiped-out"></a>V: Hoe zorgt een klant ervoor dat wanneer de inrichting van een HSM ongedaan wordt gemaakt, het hoofd materiaal wordt gewist?

Voordat de inrichting wordt aangevraagd, moet een klant de HSM hebben gezerod met behulp van Gemalto geleverde HSM-client hulpprogramma's.

## <a name="performance-and-scale"></a>Prestaties en schaal

### <a name="q-how-many-cryptographic-operations-are-supported-per-second-with-dedicated-hsm"></a>V: hoeveel cryptografische bewerkingen worden per seconde ondersteund met een toegewezen HSM?

Toegewezen HSM-voorzieningen SafeNet netwerk HSM 7-apparaten (model A790). Hier volgt een samen vatting van de maximale prestaties voor bepaalde bewerkingen: 

* RSA-2048:10.000 trans acties per seconde
* ECC-P256:20.000 trans acties per seconde
* AES-GCM: 17.000 trans acties per seconde

### <a name="q-how-many-partitions-can-be-created-in-dedicated-hsm"></a>V: Hoeveel partities kunnen worden gemaakt in een toegewezen HSM?

Het SafeNet Luna HSM 7 model A790 gebruikt een licentie voor 10 partities in de kosten van de service. Het apparaat heeft een limiet van 100 partities en het toevoegen van partities tot deze limiet zou extra licentie kosten in beslag nemen en de installatie van een nieuw licentie bestand op het apparaat vereisen.

### <a name="q-how-many-keys-can-be-supported-in-dedicated-hsm"></a>V: Hoeveel sleutels kunnen worden ondersteund in een toegewezen HSM?

Het maximum aantal sleutels is een functie van het beschik bare geheugen. Het SafeNet Luna 7 model A790 in gebruik heeft 32 MB geheugen. De volgende nummers zijn ook van toepassing op sleutel paren als asymmetrische sleutels worden gebruikt.

* RSA-2048-19.000
* ECC-P256-91.000

De capaciteit is afhankelijk van de specifieke sleutel kenmerken die zijn ingesteld in de sjabloon voor het genereren van sleutels en het aantal partities.
