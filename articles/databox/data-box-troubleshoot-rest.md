---
title: Azure Data Box probleem oplossing voor het gebruik van de REST-interface | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen kunt oplossen die worden weer gegeven in Azure Data Box wanneer het kopiëren van gegevens via de REST-interface is.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 7fe5afbc4984c430cbf393e4e2b44122bdd43983
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297129"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Problemen oplossen die verband houden met Azure Data Box Blob-opslag

In dit artikel vindt u informatie over het oplossen van problemen die kunnen optreden wanneer u de Data Box Blob-opslag gebruikt via de REST-interface op uw Data Box om gegevens te kopiëren. Deze problemen doen zich voor wanneer u Data Box Blob-opslag gebruikt met andere toepassingen of client Bibliotheken, zoals Azure Storage Explorer, AzCopy of Azure Storage bibliotheek voor python.

## <a name="errors-seen-in-azure-storage-explorer"></a>Fouten die worden weer gegeven in Azure Storage Explorer

In deze sectie vindt u een aantal problemen met het gebruik van Azure Storage Explorer met Data Box Blob Storage.

|Foutbericht  |Aanbevolen actie |
|---------|---------|
|Kan geen onderliggende resources ophalen. De waarde voor een van de HTTP-headers heeft niet de juiste indeling.|Selecteer in het menu **bewerken** de optie **doel-Azure stack-api's**. <br>Start Azure Storage Explorer opnieuw.|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |Controleer of de naam `<accountname>.blob.<serialnumber>.microsoftdatabox.com` van het eind punt is toegevoegd aan het hosts-bestand op dit pad: <li>`C:\Windows\System32\drivers\etc\hosts`in Windows of </li><li> `/etc/hosts`op Linux.</li>|
|Kan geen onderliggende resources ophalen. <br>Details: zelfondertekend certificaat |Importeer het TLS/SSL-certificaat voor uw apparaat in Azure Storage Explorer: <li>Down load het certificaat van de Azure Portal. Ga naar [het certificaat downloaden](data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.</li><li>Selecteer in het menu **bewerken** de optie **SSL-certificaten** en selecteer vervolgens **certificaten importeren**.</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>Fouten die worden weer gegeven in AzCopy voor Windows

In deze sectie vindt u een aantal problemen met het gebruik van AzCopy voor Windows met Data Box Blob-opslag.

|Foutbericht  |Aanbevolen actie |
|---------|---------|
|De AzCopy-opdracht lijkt een minuut vast te lopen voordat deze fout wordt weer gegeven: <br>Kan de map https://niet inventariseren... De externe naam kan niet worden omgezet`<accountname>.blob.<serialnumber>.microsoftdatabox.com`|Controleer of de naam `<accountname>.blob.<serialnumber>.microsoftdatabox.com` van het eind punt is toegevoegd aan het hosts `C:\Windows\System32\drivers\etc\hosts`-bestand op:.|
|De AzCopy-opdracht lijkt een minuut vast te lopen voordat deze fout wordt weer gegeven: <br>Fout bij het parseren van de bron locatie. De onderliggende verbinding is gesloten: er kan geen vertrouwens relatie tot stand worden gebracht voor het beveiligde SSL/TLS-kanaal.|Importeer het TLS/SSL-certificaat voor uw apparaat in het certificaat archief van het systeem. Ga naar [het certificaat downloaden](data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.|


## <a name="errors-seen-in-azcopy-for-linux"></a>Fouten die worden weer gegeven in AzCopy voor Linux

In deze sectie vindt u een aantal problemen met het gebruik van AzCopy voor Linux met Data Box Blob-opslag.

|Foutbericht  |Aanbevolen actie |
|---------|---------|
|De AzCopy-opdracht lijkt 20 minuten vast te lopen voordat deze fout wordt weer gegeven: <br>Fout bij het parseren van de bron locatie `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. Apparaat of adres is niet beschikbaar|Controleer of de naam `<accountname>.blob.<serialnumber>.microsoftdatabox.com` van het eind punt is toegevoegd aan het hosts `/etc/hosts`-bestand op:.|
|De AzCopy-opdracht lijkt 20 minuten vast te lopen voordat deze fout wordt weer gegeven: <br>Fout bij het parseren van de bron locatie... De SSL-verbinding kan niet tot stand worden gebracht.|Importeer het TLS/SSL-certificaat voor uw apparaat in het certificaat archief van het systeem. Ga naar [het certificaat downloaden](data-box-deploy-copy-data-via-rest.md#download-certificate)voor meer informatie.|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Fouten die worden weer gegeven in Azure Storage bibliotheek voor python

In deze sectie vindt u een aantal veelvoorkomende problemen tijdens de implementatie van Data Box Disk wanneer u een Linux-client gebruikt voor het kopiëren van gegevens.

|Foutbericht  |Aanbevolen actie |
|---------|---------|
|De waarde voor een van de HTTP-headers heeft niet de juiste indeling. |De geïnstalleerde versie van de Microsoft Azure Storage-bibliotheek voor python wordt niet ondersteund door Data Box. Zie Azure Data Box vereisten voor Blob Storage voor ondersteunde versies.|
|… [SSL: CERTIFICATE_VERIFY_FAILED]...|Voordat u python uitvoert, stelt u de omgevings variabele REQUESTS_CA_BUNDLE in op het pad van het met base64 gecodeerde TLS-certificaat bestand (Zie How to [down load the Certificate](data-box-deploy-copy-data-via-rest.md#download-certificate)). <br>Bijvoorbeeld:<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>U kunt het certificaat ook toevoegen aan het certificaat archief van het systeem en deze omgevings variabele vervolgens instellen op het pad van dat archief. <br> Bijvoorbeeld op Ubuntu: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>Algemene fouten

Deze fouten zijn niet specifiek voor elke toepassing.

|Foutbericht  |Aanbevolen actie |
|---------|---------|
|Er is een time-out opgestaan. |Meld u aan bij het Data Box apparaat en controleer of het is ontgrendeld. Telkens wanneer het apparaat opnieuw wordt opgestart, blijft het vergrendeld totdat iemand zich aanmeldt.|

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [vereisten voor het data Box Blob-opslag systeem](data-box-system-requirements-rest.md).
