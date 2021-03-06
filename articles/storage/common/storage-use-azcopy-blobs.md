---
title: Gegevens overdragen van of naar Azure Blob-opslag met behulp van AzCopy V10 toevoegen | Microsoft Docs
description: Dit artikel bevat een verzameling AzCopy-voorbeeld opdrachten die u helpen bij het maken van containers, het kopiëren van bestanden en het synchroniseren van mappen tussen lokale bestands systemen en containers.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: b676c2647fbf7c93d271e1d7f68653452125e39b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137192"
---
# <a name="transfer-data-with-azcopy-and-blob-storage"></a>Gegevens overdragen met AzCopy en Blob Storage

AzCopy is een opdracht regel programma dat u kunt gebruiken om gegevens naar, van of tussen opslag accounts te kopiëren. Dit artikel bevat voorbeeld opdrachten die werken met Blob Storage.

> [!TIP]
> In de voor beelden in dit artikel worden Path-argumenten met enkele aanhalings tekens (' '). Gebruik enkele aanhalings tekens in alle opdracht shells, met uitzonde ring van de Windows-opdracht shell (cmd. exe). Als u een Windows-opdracht shell (cmd. exe) gebruikt, plaatst u padvariabelen tussen dubbele aanhalings tekens ("") in plaats van enkele aanhalings tekens (' ').

## <a name="get-started"></a>Aan de slag

Zie het artikel aan de [slag met AzCopy](storage-use-azcopy-v10.md) om AzCopy te downloaden en meer te weten te komen over de manieren waarop u autorisatie referenties kunt opgeven voor de opslag service.

> [!NOTE]
> In de voor beelden in dit artikel wordt ervan uitgegaan dat u uw identiteit `AzCopy login` hebt geverifieerd met behulp van de opdracht. AzCopy gebruikt vervolgens uw Azure AD-account om toegang te verlenen tot gegevens in Blob Storage.
>
> Als u liever een SAS-token gebruikt om toegang te verlenen tot BLOB-gegevens, kunt u dat token toevoegen aan de bron-URL in elke AzCopy-opdracht.
>
> Bijvoorbeeld: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`.

## <a name="create-a-container"></a>Een container maken

U kunt de opdracht [azcopy](storage-ref-azcopy-make.md) maken gebruiken om een container te creëren. In de voor beelden in deze sectie wordt een `mycontainer`container met de naam gemaakt.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy make 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy make 'https://mystorageaccount.blob.core.windows.net/mycontainer'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy make 'https://mystorageaccount.dfs.core.windows.net/mycontainer'` |

Zie [azcopy maken](storage-ref-azcopy-make.md)voor gedetailleerde naslag documentatie.

## <a name="upload-files"></a>Bestanden uploaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om bestanden en mappen te uploaden van uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Bestand uploaden
> * Een map uploaden
> * De inhoud van een map uploaden 
> * Specifieke bestanden uploaden

> [!TIP]
> U kunt de upload bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Markering|
> |---|---|
> |Upload bestanden als toevoeg-blobs of pagina-blobs.|**--BLOB-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Upload naar een specifieke Access-laag (zoals de laag archief).|**--blok-BLOB-laag**=\[geen\|Hot\|-\|koud archief\]|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

### <a name="upload-a-file"></a>Bestand uploaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt'` |

U kunt ook een bestand uploaden met behulp van een Joker teken (*) ergens in het bestandspad of de bestands naam. Bijvoorbeeld: `'C:\myDirectory\*.txt'`, of `C:\my*\*.txt`.

### <a name="upload-a-directory"></a>Een map uploaden

In dit voor beeld wordt een map (en alle bestanden in die map) naar een BLOB-container gekopieerd. Het resultaat is een map in de container met dezelfde naam.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --recursive` |

Als u wilt kopiëren naar een map in de container, geeft u alleen de naam van die map op in de opdracht reeks.

|    |     |
|--------|-----------|
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' --recursive` |

Als u de naam opgeeft van een map die niet bestaat in de container, maakt AzCopy een nieuwe map met die naam.

### <a name="upload-the-contents-of-a-directory"></a>De inhoud van een map uploaden

U kunt de inhoud van een map uploaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>\*' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>'` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory\*' 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory'` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden te uploaden in alle submappen.

### <a name="upload-specific-files"></a>Specifieke bestanden uploaden

U kunt volledige bestands namen opgeven of gedeeltelijke namen gebruiken met Joker tekens (*).

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met `--include-path` de optie. Scheid afzonderlijke bestands namen met een punt komma (`;`).

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-path 'photos;documents\myFile.txt' --recursive` |

In dit voor beeld worden de `C:\myDirectory\photos` map en het `C:\myDirectory\documents\myFile.txt` bestand door AzCopy overgedragen. U moet de optie voor `--recursive` het overdragen van alle bestanden in de `C:\myDirectory\photos` map toevoegen.

U kunt ook bestanden uitsluiten met behulp `--exclude-path` van de optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met `--include-pattern` de optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een`;`semicolin (). 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy '<local-directory-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'C:\myDirectory' 'https://mystorageaccount.dfs.core.windows.net/mycontainer' --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp `--exclude-pattern` van de optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` opties `--exclude-pattern` en zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de `–recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `–include-pattern` en geeft `*.txt` u op om alle tekst bestanden op te halen.

## <a name="download-files"></a>Bestanden downloaden

U kunt de azcopy-opdracht [kopiëren](storage-ref-azcopy-copy.md) gebruiken om blobs, directory's en containers te downloaden naar uw lokale computer.

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Bestand downloaden
> * Een directory downloaden
> * De inhoud van een map downloaden
> * Specifieke bestanden downloaden

> [!TIP]
> U kunt uw download bewerking aanpassen door optionele vlaggen te gebruiken. Hier volgen enkele voor beelden.
>
> |Scenario|Markering|
> |---|---|
> |Bestanden automatisch decomprimeren.|**--decomprimeren**|
> |Geef op hoe gedetailleerd de logboek vermeldingen voor het kopiëren moeten zijn.|**--**=\[waarschuwings\|fout\|info\|op logboek niveau geen\]|
> |Geef op of en hoe de conflicterende bestanden en blobs op de bestemming moeten worden overschreven.|**--**=\[ifSourceNewer\|-\|prompt\|voor onwaar overschrijven\]|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

> [!NOTE]
> Als de `Content-md5` eigenschaps waarde van een BLOB een hash bevat, wordt in AZCOPY een MD5-hash voor gedownloade gegevens berekend en wordt gecontroleerd of de MD5 `Content-md5` -hash die is opgeslagen in de eigenschap van de BLOB overeenkomt met de berekende hash. Als deze waarden niet overeenkomen, mislukt de down load tenzij u dit gedrag overschrijft `--check-md5=NoCheck` door `--check-md5=LogOnly` toe te voegen of aan de Kopieer opdracht.

### <a name="download-a-file"></a>Bestand downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myTextFile.txt' 'C:\myDirectory\myTextFile.txt'` |

### <a name="download-a-directory"></a>Een directory downloaden

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<directory-path>' '<local-directory-path>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/myBlobDirectory' 'C:\myDirectory'  --recursive` |

Dit voor beeld resulteert in een map `C:\myDirectory\myBlobDirectory` met de naam die alle gedownloade bestanden bevat.

### <a name="download-the-contents-of-a-directory"></a>De inhoud van een map downloaden

U kunt de inhoud van een map downloaden zonder de bovenliggende map zelf te kopiëren met behulp van het Joker teken (*).

> [!NOTE]
> Dit scenario wordt momenteel alleen ondersteund voor accounts die geen hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.blob.core.windows.net/<container-name>/*' '<local-directory-path>/'` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/*' 'C:\myDirectory'` |

> [!NOTE]
> Voeg de `--recursive` vlag toe om bestanden in alle submappen te downloaden.

### <a name="download-specific-files"></a>Specifieke bestanden downloaden

U kunt volledige bestands namen opgeven of gedeeltelijke namen gebruiken met Joker tekens (*).

#### <a name="specify-multiple-complete-file-names"></a>Meerdere volledige bestands namen opgeven

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met `--include-path` de optie. Scheid afzonderlijke bestands namen met behulp van een`;`semicolin ().

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>'  --include-path <semicolon-separated-file-list>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-path 'photos;documents\myFile.txt'--recursive` |

In dit voor beeld worden de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` map en het `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/documents/myFile.txt` bestand door AzCopy overgedragen. U moet de optie voor `--recursive` het overdragen van alle bestanden in de `https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory/photos` map toevoegen.

U kunt ook bestanden uitsluiten met behulp `--exclude-path` van de optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

#### <a name="use-wildcard-characters"></a>Joker tekens gebruiken

Gebruik de [azcopy](storage-ref-azcopy-copy.md) -opdracht copy met `--include-pattern` de optie. Geef gedeeltelijke namen op die de joker tekens bevatten. Scheid namen met behulp van een`;`semicolin ().

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-or-directory-name>' '<local-directory-path>' --include-pattern <semicolon-separated-file-list-with-wildcard-characters>` |
| **Voorbeeld** | `azcopy copy 'https://mystorageaccount.blob.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mystorageaccount.dfs.core.windows.net/mycontainer/FileDirectory' 'C:\myDirectory'  --include-pattern 'myFile*.txt;*.pdf*'` |

U kunt ook bestanden uitsluiten met behulp `--exclude-pattern` van de optie. Zie voor meer informatie [azcopy Copy](storage-ref-azcopy-copy.md) Reference docs.

De `--include-pattern` opties `--exclude-pattern` en zijn alleen van toepassing op bestands namen en niet op het pad.  Als u alle tekst bestanden in een mapstructuur wilt kopiëren, gebruikt u de `–recursive` optie om de volledige mapstructuur op te halen en gebruikt u vervolgens de `–include-pattern` en geeft `*.txt` u op om alle tekst bestanden op te halen.

## <a name="copy-blobs-between-storage-accounts"></a>Blobs kopiëren tussen opslagaccounts

U kunt AzCopy gebruiken om blobs naar andere opslag accounts te kopiëren. De Kopieer bewerking is synchroon, dus wanneer de opdracht wordt geretourneerd. Dit geeft aan dat alle bestanden zijn gekopieerd. 

AzCopy maakt gebruik van [server-naar-server-](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) [api's](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url), zodat gegevens rechtstreeks tussen opslag servers worden gekopieerd. Deze Kopieer bewerkingen gebruiken de netwerk bandbreedte van uw computer niet. U kunt de door Voer van deze bewerkingen verhogen door de waarde van de `AZCOPY_CONCURRENCY_VALUE` omgevings variabele in te stellen. Zie de [door Voer optimaliseren](storage-use-azcopy-configure.md#optimize-throughput)voor meer informatie.

> [!NOTE]
> In dit scenario gelden de volgende beperkingen in de huidige release.
>
> - U moet een SAS-token toevoegen aan elke bron-URL. Als u autorisatie referenties opgeeft met behulp van Azure Active Directory (AD), kunt u de SAS-token alleen weglaten van de doel-URL.
>-  Premium Block Blob Storage-accounts bieden geen ondersteuning voor toegangs lagen. Laat de Access-laag van een BLOB weg van de Kopieer bewerking door `s2s-preserve-access-tier` de `false` in te stellen op `--s2s-preserve-access-tier=false`(bijvoorbeeld:).

Deze sectie bevat de volgende voor beelden:

> [!div class="checklist"]
> * Een BLOB kopiëren naar een ander opslag account
> * Een map kopiëren naar een ander opslag account
> * Een container kopiëren naar een ander opslag account
> * Alle containers, directory's en bestanden kopiëren naar een ander opslag account

Deze voor beelden werken ook met accounts die een hiërarchische naam ruimte hebben. Met [toegang via meerdere protocollen op Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) kunt u dezelfde URL-syntaxis (`blob.core.windows.net`) voor deze accounts gebruiken.

> [!TIP]
> U kunt uw Kopieer bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Markering|
> |---|---|
> |Kopieer bestanden als toevoeg-blobs of pagina-blobs.|**--BLOB-type**=\[BlockBlob\|PageBlob\|AppendBlob\]|
> |Kopiëren naar een specifieke toegangs laag (zoals de laag archief).|**--blok-BLOB-laag**=\[geen\|Hot\|-\|koud archief\]|
> |Bestanden automatisch decomprimeren.|**--**=\[gzip\|-deflater decomprimeren\]|
> 
> Zie [Opties](storage-ref-azcopy-copy.md#options)voor een volledige lijst.

### <a name="copy-a-blob-to-another-storage-account"></a>Een BLOB kopiëren naar een ander opslag account

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<blob-path>'` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |
| **Voor beeld** (hiërarchische naam ruimte) | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myTextFile.txt?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myTextFile.txt'` |

### <a name="copy-a-directory-to-another-storage-account"></a>Een map kopiëren naar een ander opslag account

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-path><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-a-container-to-another-storage-account"></a>Een container kopiëren naar een ander opslag account

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<container-name><SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="copy-all-containers-directories-and-blobs-to-another-storage-account"></a>Alle containers, directory's en blobs kopiëren naar een ander opslag account

Gebruik dezelfde URL-syntaxis (`blob.core.windows.net`) voor accounts die een hiërarchische naam ruimte hebben.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy copy 'https://<source-storage-account-name>.blob.core.windows.net/<SAS-token>' 'https://<destination-storage-account-name>.blob.core.windows.net/' --recursive` |
| **Voorbeeld** | `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |
| **Voor beeld** (hiërarchische naam ruimte)| `azcopy copy 'https://mysourceaccount.blob.core.windows.net/?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-07-04T05:30:08Z&st=2019-07-03T21:30:08Z&spr=https&sig=CAfhgnc9gdGktvB=ska7bAiqIddM845yiyFwdMH481QA8%3D' 'https://mydestinationaccount.blob.core.windows.net' --recursive` |

## <a name="synchronize-files"></a>Bestanden synchroniseren

U kunt de inhoud van een lokaal bestands systeem synchroniseren met een BLOB-container. U kunt ook containers en virtuele mappen synchroniseren met elkaar. Synchronisatie is in één richting. Met andere woorden, u kunt kiezen welke van deze twee eind punten de bron is en welke het doel is. Synchronisatie gebruikt ook server-naar-server-Api's. De voor beelden die in deze sectie worden weer gegeven, werken ook met accounts die een hiërarchische naam ruimte hebben. 

> [!NOTE]
> De huidige release van AzCopy synchroniseert niet tussen andere bronnen en doelen (bijvoorbeeld: File Storage of Amazon Web Services (AWS) S3-buckets).

Met de opdracht [Sync](storage-ref-azcopy-sync.md) worden bestands namen en tijds tempels met de laatste wijziging vergeleken. Stel de `--delete-destination` optionele vlag in op een waarde `true` van `prompt` of om bestanden in de doelmap te verwijderen als deze bestanden niet meer in de bron directory aanwezig zijn.

Als u de `--delete-destination` vlag instelt op `true` AzCopy, worden bestanden verwijderd zonder dat u een prompt hoeft op te geven. Als u wilt dat er een prompt wordt weer gegeven voordat AzCopy een bestand verwijdert `--delete-destination` , stelt `prompt`u de vlag in op.

> [!NOTE]
> Als u onbedoeld verwijderen wilt voor komen, moet u ervoor zorgen dat u de functie voor [voorlopig verwijderen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) inschakelt voordat u de `--delete-destination=prompt|true` vlag gebruikt.

> [!TIP]
> U kunt de synchronisatie bewerking aanpassen met behulp van optionele vlaggen. Hier volgen enkele voor beelden.
>
> |Scenario|Markering|
> |---|---|
> |Opgeven hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden.|**--check-MD5**=\[-controle\|op de\|aanmeldings\|FailIfDifferent FailIfDifferentOrMissing\]|
> |Bestanden uitsluiten op basis van een patroon.|**--exclude-pad**|
> |Geef op hoe gedetailleerd u de logboek vermeldingen voor synchronisatie wilt maken.|**--**=\[waarschuwings\|fout\|info\|op logboek niveau geen\]|
> 
> Zie [Opties](storage-ref-azcopy-sync.md#options)voor een volledige lijst.

### <a name="update-a-container-with-changes-to-a-local-file-system"></a>Een container met wijzigingen in een lokaal bestands systeem bijwerken

In dit geval is de container het doel en is het lokale bestands systeem de bron. 

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-local-file-system-with-changes-to-a-container"></a>Een lokaal bestands systeem bijwerken met wijzigingen in een container

In dit geval is het lokale bestands systeem het doel en de container de bron.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

### <a name="update-a-container-with-changes-in-another-container"></a>Een container bijwerken met wijzigingen in een andere container

De eerste container die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

### <a name="update-a-directory-with-changes-to-a-directory-in-another-file-share"></a>Een Directory bijwerken met wijzigingen in een map in een andere bestands share

De eerste map die wordt weer gegeven in deze opdracht is de bron. De tweede is de bestemming.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **Voorbeeld** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="next-steps"></a>Volgende stappen

Meer voor beelden vindt u in een van deze artikelen:

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)

- [Zelfstudie: on-premises gegevens migreren naar cloudopslag met behulp van AzCopy](storage-use-azcopy-migrate-on-premises-data.md)

- [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)

- [Gegevens overdragen met AzCopy en Amazon S3-buckets](storage-use-azcopy-s3.md)

- [AzCopy configureren, optimaliseren en problemen oplossen](storage-use-azcopy-configure.md)
