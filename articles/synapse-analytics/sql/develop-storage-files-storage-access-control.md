---
title: Toegang tot opslagaccounts beheren voor SQL on-demand (preview)
description: Lees hier hoe SQL on-demand (preview) toegang krijgt tot Azure Storage en hoe u de toegang tot opslag kunt beheren voor SQL on-demand in Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2d5d508afe81975cbeda448b497a098e8a3bbcf3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589275"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>Toegang tot opslagaccounts beheren voor SQL on-demand (preview)

Bij een SQL on demand-query worden bestanden rechtstreeks gelezen uit Azure Storage. Machtigingen voor toegang tot de bestanden in Azure Storage worden op twee niveaus bepaald:
- **Opslagniveau**: de gebruiker moet toegang hebben tot onderliggende opslagbestanden. Uw opslagbeheerder moet de Azure AD-principal toestemming geven om bestanden te lezen/schrijven, of een SAS-sleutel genereren die wordt gebruikt voor toegang tot de opslag.
- **Niveau van SQL-service**: de gebruiker moet de machtiging `SELECT` hebben om gegevens te lezen uit een [externe tabel](develop-tables-external-tables.md) of de machtiging `ADMINISTER BULK ADMIN` voor het uitvoeren van `OPENROWSET` evenals toestemming om referenties te gebruiken die worden gebruikt voor toegang tot de opslag.

In dit artikel worden de typen referenties beschreven die u kunt gebruiken en hoe het opzoeken van referenties voor SQL- en Azure AD-gebruikers in zijn werk gaat.

## <a name="supported-storage-authorization-types"></a>Ondersteunde autorisatietypen voor opslag

Een gebruiker die zich heeft aangemeld bij een SQL on demand-resource moet gemachtigd zijn om query's uit te voeren op de bestanden in Azure Storage als de bestanden niet openbaar beschikbaar zijn. Er worden drie autorisatietypen ondersteund:

- [Shared Access Signature](?tabs=shared-access-signature)
- [Gebruikersidentiteit](?tabs=user-identity)
- [Beheerde identiteit](?tabs=managed-identity)

> [!NOTE]
> [Azure AD Pass-Through](#force-azure-ad-pass-through) is het standaardgedrag bij het maken van een werkruimte. Als u die techniek gebruikt, hoeft u geen referenties te maken voor elk opslagaccount dat wordt geopend met behulp van Azure AD-aanmeldingen. U kunt [dit gedrag uitschakelen](#disable-forcing-azure-ad-pass-through).

### <a name="shared-access-signature"></a>[Shared Access Signature](#tab/shared-access-signature)

**SAS (Shared Access Signature; handtekening voor gedeelde toegang)** biedt gedelegeerde toegang tot resources in een opslagaccount. Met behulp van SAS kan een klant clients toegang geven tot resources in een opslagaccount zonder sleutels van het account te delen. SAS geeft u nauwkeurige controle over het type toegang dat u verleent aan clients die een SAS hebben, inclusief het geldigheidsinterval, verleende machtigingen, het acceptabele bereik van IP-adressen en het geaccepteerde protocol (https/http).

U kunt een SAS-token verkrijgen door te navigeren naar de **Azure-portal > Opslagaccount-> Shared Access Signature-> Machtigingen configureren-> SAS en verbindingsreeks genereren**.

> [!IMPORTANT]
> Als er een SAS-token wordt gegenereerd, bevat dit een vraagteken ('?') aan het begin van het token. Als u het token wilt gebruiken in SQL on-demand, moet u het vraag teken ('?') verwijderen bij het maken van een referentie. Bijvoorbeeld:
>
> SAS-token: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

U moet de referenties binnen het database- of serverbereik maken om toegang met behulp van een SAS-token mogelijk te maken.

### <a name="user-identity"></a>[Gebruikersidentiteit](#tab/user-identity)

**Gebruikersidentiteit**, ook wel 'Pass-Through' genoemd, is een type autorisatie waarbij de identiteit van de Azure AD-gebruiker die is aangemeld bij SQL on-demand wordt gebruikt om toegang tot gegevens te autoriseren. Voordat de gegevens worden vrijgegeven, moet de Azure Storage-beheerder machtigingen verlenen aan de Azure AD-gebruiker. Zoals aangegeven in de bovenstaande tabel, wordt dit niet ondersteund voor het SQL-gebruikerstype.

> [!IMPORTANT]
> U moet beschikken over de rol van eigenaar/inzender/lezer van Storage-blobgegevens om via uw identiteit toegang te krijgen tot de gegevens.
> Zelfs als u eigenaar bent van een opslagaccount, moet u nog beschikken over een van deze rollen voor Storage-blobgegevens.
>
> Raadpleeg het artikel [Toegangsbeheer in Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-access-control.md) voor meer informatie over toegangsbeheer in Azure Data Lake Store Gen2.
>

U moet verificatie via Azure AD Pass-Through expliciet inschakelen om Azure AD-gebruikers toegang te geven tot opslag via hun identiteit.

#### <a name="force-azure-ad-pass-through"></a>Azure AD Pass-Through afdwingen

Het afdwingen van Azure AD Pass-Through is een standaard gedrag dat wordt bereikt door een speciale referentienaam, `UserIdentity`, die automatisch wordt gemaakt tijdens het inrichten van de Azure Synapse-werkruimte. Hiermee wordt het gebruik van een Azure AD Pass-Through geforceerd voor elke query van elke Azure AD-aanmelding, die zal plaatsvinden ondanks het bestaan van andere referenties.

> [!NOTE]
> Azure AD Pass-Through is een standaardgedrag. U hoeft geen referenties te maken voor elk opslagaccount dat wordt geopend met behulp van AD-aanmeldingen.

Als u [het afdwingen van Azure AD Pass-Through hebt uitgeschakeld voor elke query](#disable-forcing-azure-ad-pass-through) en u dit weer wilt inschakelen, voert u de volgende code uit:

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

Als u het afdwingen van Azure AD Pass-Through wilt inschakelen voor een specifieke gebruiker, kunt u de machtiging REFERENCE voor die specifieke gebruiker toekennen voor de referentie `UserIdentity`. In het volgende voorbeeld wordt het afdwingen van Azure AD Pass-Through ingeschakeld voor de gebruiker user_name:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO USER [user_name];
```

#### <a name="disable-forcing-azure-ad-pass-through"></a>Afdwingen van Azure AD Pass-Through uitschakelen

U kunt [het afdwingen van Azure AD Pass-Through uitschakelen voor elke query](#force-azure-ad-pass-through). U doet dit door de referentie `Userdentity` te verwijderen met:

```sql
DROP CREDENTIAL [UserIdentity];
```

Als u het gedrag weer wilt inschakelen, raadpleegt u de sectie [Azure AD Pass-Through afdwingen](#force-azure-ad-pass-through).

### <a name="managed-identity"></a>[Beheerde identiteit](#tab/managed-identity)

**Beheerde identiteit** wordt ook wel MSI genoemd. Het is een functie van Azure Active Directory (Azure AD) die Azure-services biedt voor SQL on-demand. Met MSI wordt ook een automatisch beheerde identiteit geïmplementeerd in Azure AD. Deze identiteit kan worden gebruikt om de aanvraag voor toegang tot gegevens in Azure Storage te autoriseren.

Voordat de gegevens worden vrijgegeven, moet de Azure Storage-beheerder machtigingen verlenen aan de beheerde identiteit voor toegang tot de gegevens. Het verlenen van machtigingen aan de beheerde identiteit gebeurt op dezelfde manier als het verlenen van machtigingen aan elke andere gebruiker van Azure AD.

### <a name="anonymous-access"></a>[Anonieme toegang](#tab/public-access)

U kunt toegang krijgen tot openbaar beschikbare bestanden die in Azure-opslagaccounts zijn geplaatst die [anonieme toegang toestaan](/azure/storage/blobs/storage-manage-access-to-resources.md).

---

### <a name="supported-authorization-types-for-databases-users"></a>Ondersteunde autorisatietypen voor databasegebruikers

In de onderstaande tabel vindt u de beschikbare autorisatietypen:

| Autorisatietype                    | *SQL-gebruiker*    | *Azure AD-gebruiker*     |
| ------------------------------------- | ------------- | -----------    |
| [Gebruikersidentiteit](?tabs=user-identity#supported-storage-authorization-types)       | Niet ondersteund | Ondersteund      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | Ondersteund     | Ondersteund      |
| [Beheerde identiteit](?tabs=managed-identity#supported-storage-authorization-types) | Niet ondersteund | Ondersteund      |

### <a name="supported-storages-and-authorization-types"></a>Ondersteunde opslag- en autorisatietypen

U kunt de volgende combinaties van autorisatie- en Azure Storage-typen gebruiken:

|                     | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| *SAS*               | Ondersteund      | Niet ondersteund   | Ondersteund     |
| *Beheerde identiteit* | Ondersteund      | Ondersteund        | Ondersteund     |
| *Gebruikersidentiteit*    | Ondersteund      | Ondersteund        | Ondersteund     |

## <a name="credentials"></a>Referenties

Als u een query wilt uitvoeren op een bestand dat zich in Azure Storage bevindt, heeft het eindpunt van SQL on-demand een referentie nodig die de verificatiegegevens bevat. Er worden twee typen referenties gebruikt:
- Referenties op serverniveau worden gebruikt voor ad-hoc query's die worden uitgevoerd met behulp van de functie `OPENROWSET`. De referentienaam moet overeenkomen met de opslag-URL.
- Referenties binnen het databasebereik worden gebruikt voor externe tabellen. De externe tabel verwijst naar `DATA SOURCE` met de referentie die moet worden gebruikt voor toegang tot de opslag.

Als een gebruiker een referentie mag maken of verwijderen, kan een beheerder de machtiging GRANT/DENY ALTER ANY CREDENTIAL verlenen aan een gebruiker:

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

Databasegebruikers die toegang hebben tot externe opslag, moeten toestemming hebben om referenties te gebruiken.

### <a name="grant-permissions-to-use-credential"></a>Machtigingen verlenen voor het gebruik van referenties

Om de referentie te gebruiken, moet een gebruiker de machtiging `REFERENCES` hebben voor een specifieke referentie. Om een `REFERENCES` machtiging te verlenen aan een storage_credential voor een specific_user, voert u de volgende opdracht uit:

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

Om een soepele Pass-Through Azure AD-ervaring te garanderen, hebben alle gebruikers standaard het om de referentie `UserIdentity` te gebruiken. Dit wordt bereikt door een automatische uitvoering van de volgende instructie bij het inrichten van de Azure Synapse-werkruimte:

```sql
GRANT REFERENCES ON CREDENTIAL::[UserIdentity] TO [public];
```

## <a name="server-scoped-credential"></a>Referentie binnen serverbereik

Referenties binnen het bereik van de server worden gebruikt wanneer de SQL-aanmelding de functie `OPENROWSET` aanroept zonder `DATA_SOURCE` om bestanden te lezen in een opslagaccount. De naam van de referentie binnen serverbereik **moet** overeenkomen met de URL van Azure Storage. U kunt een referentie toevoegen door [CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) uit te voeren. U moet als argument een naam voor de referentie opgeven. Deze moet overeenkomen met een deel van het pad of het volledige pad naar gegevens in Storage (zie hieronder).

> [!NOTE]
> Het argument FOR CRYPTOGRAPHIC PROVIDER wordt niet ondersteund.

De naam van de referentie op serverniveau moet overeenkomen met het volledige pad naar het opslagaccount (en eventueel container) in de volgende indeling: `<prefix>://<storage_account_path>/<storage_path>`. Paden naar opslagaccounts worden beschreven in de volgende tabel:

| Externe gegevensbron       | Voorvoegsel | Pad van opslagaccount                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <opslagaccount>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <opslagaccount>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <opslagaccount>.dfs.core.windows.net              |

> [!NOTE]
> Er is een speciale referentie op serverniveau me de naam `UserIdentity` die [Azure AD Pass-Through](?tabs=user-identity#force-azure-ad-pass-through) afdwingt.

Met de referenties binnen serverbereik wordt toegang tot Azure-opslag verleend met behulp van de volgende verificatietypen:

### <a name="shared-access-signature"></a>[Shared Access Signature](#tab/shared-access-signature)

Met het volgende script maakt u een referentie op serverniveau die kan worden gebruikt door de functie `OPENROWSET` om met behulp van een SAS-token toegang te krijgen tot ieder bestand in Azure-opslag. Maak deze referentie om de SQL-principal in te schakelen die de functie `OPENROWSET` uitvoert om bestanden te lezen die zijn beveiligd met een SAS-sleutel in de Azure-opslag die overeenkomt met de URL in de referentienaam.

U moet <*mystorageaccountname*> vervangen door de werkelijke naam van uw opslagaccount en <*mystorageaccountcontainername*> door de naam van de container:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="user-identity"></a>[Gebruikersidentiteit](#tab/user-identity)

Met het volgende script maakt u een referentie op serverniveau waarmee gebruikers de Azure AD-identiteit kunnen gebruiken om zich als iemand anders voor te doen.

```sql
CREATE CREDENTIAL [UserIdentity]
WITH IDENTITY = 'User Identity';
```

### <a name="managed-identity"></a>[Beheerde identiteit](#tab/managed-identity)

Met het volgende script maakt u een referentie op serverniveau die kan worden gebruikt door de functie `OPENROWSET` om met behulp van een via de werkruimte beheerde identiteit toegang te krijgen tot een bestand in Azure-opslag.

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[Openbare toegang](#tab/public-access)

Met het volgende script maakt u een referentie op serverniveau die kan worden gebruikt door de functie `OPENROWSET` om toegang te krijgen tot bestanden in vrij toegankelijke Azure-opslag. Maak deze referentie om de SQL-principal in te schakelen die de functie `OPENROWSET` uitvoert om vrij toegankelijke bestanden te lezen in de Azure-opslag die overeenkomt met de URL in de referentienaam.

U moet <*mystorageaccountname*> vervangen door de werkelijke naam van uw opslagaccount en <*mystorageaccountcontainername*> door de naam van de container:

```sql
CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = '';
GO
```
---

## <a name="database-scoped-credential"></a>Referenties in databasebereik

Referenties in databasebereik worden gebruikt wanneer een principal de functie `OPENROWSET` aanroept met `DATA_SOURCE` of gegevens selecteert uit een [externe tabel](develop-tables-external-tables.md) die geen toegang heeft tot openbare bestanden. De referentie in het databasebereik hoeft niet overeen te komen met de naam van het opslagaccount, omdat deze expliciet wordt gebruikt in de gegevensbron (DATA SOURCE) die de locatie van de opslag definieert.

Met de referenties binnen databasebereik wordt toegang tot Azure-opslag verleend met behulp van de volgende verificatietypen:

### <a name="shared-access-signature"></a>[Shared Access Signature](#tab/shared-access-signature)

Met het volgende script maakt u een referentie die wordt gebruikt voor toegang tot bestanden in opslag met behulp van een SAS-token dat is opgegeven in de referentie.

```sql
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="azure-ad-identity"></a>[Azure AD-identiteit](#tab/user-identity)

Met het volgende script maakt u een referentie in het databasebereik die wordt gebruikt door de [externe tabel](develop-tables-external-tables.md) en `OPENROWSET`-functies die gebruikmaken van de gegevensbron met referentie om toegang te krijgen tot opslagbestanden met behulp van hun eigen Azure AD-identiteit.

```sql
CREATE DATABASE SCOPED CREDENTIAL [AzureAD]
WITH IDENTITY = 'User Identity';
GO
```

### <a name="managed-identity"></a>[Beheerde identiteit](#tab/managed-identity)

Met het volgende script maakt u een referentie in het databasebereik die kan worden gebruikt om de huidige Azure AD-gebruiker te imiteren als een beheerde identiteit van de service. 

```sql
CREATE DATABASE SCOPED CREDENTIAL [SynapseIdentity]
WITH IDENTITY = 'Managed Identity';
GO
```

De referentie in het databasebereik hoeft niet overeen te komen met de naam van het opslagaccount, omdat deze expliciet wordt gebruikt in de gegevensbron (DATA SOURCE) die de locatie van de opslag definieert.

### <a name="public-access"></a>[Openbare toegang](#tab/public-access)

Referenties in het databasebereik zijn niet vereist om toegang te geven tot openbaar beschikbare bestanden. Maak [een gegevensbron zonder referenties in het databasebereik](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source) om toegang te krijgen tot openbaar beschikbare bestanden in Azure Storage.

---

Referenties in het databasebereik worden gebruikt in externe gegevensbronnen om op te geven welke verificatiemethode er wordt gebruikt voor toegang tot deze opslag:

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>Voorbeelden

**Toegang tot openbaar beschikbare gegevensbron**

Gebruik het volgende script om een tabel te maken die toegang heeft tot een openbaar beschikbare gegevensbron.

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://****.blob.core.windows.net/public-access' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [publicData], FILE_FORMAT = [SynapseParquetFormat] )
```

Een databasegebruiker kan de inhoud van de bestanden uit de gegevensbron lezen met behulp van de externe tabel of de functie [OPENROWSET](develop-openrowset.md) die verwijst naar de gegevensbron:

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

**Toegang tot gegevensbron met behulp van referenties**

Pas het volgende script aan om een externe tabel te maken die toegang heeft tot Azure-storage met behulp van een SAS-token, de Azure AD-identiteit van een gebruiker of de beheerde identiteit van de werkruimte.

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use User Identity, Managed Identity, or SAS. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL MyIdentity WITH IDENTITY = 'User Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://*******.blob.core.windows.net/samples'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = MyIdentity 
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FILE_FORMAT = [SynapseParquetFormat] )

```

Een databasegebruiker kan de inhoud van de bestanden uit de gegevensbron lezen met behulp van [externe tabel](develop-tables-external-tables.md) of de functie [OPENROWSET](develop-openrowset.md) die verwijst naar de gegevensbron:

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT=PARQUET) as rows;
GO
```

## <a name="next-steps"></a>Volgende stappen

In de onderstaande artikelen vindt u informatie over hoe u een query kunt uitvoeren op verschillende soorten mappen en bestandstypen, en hoe u weergaven maakt en gebruikt:

- [Query's uitvoeren op één CSV-bestand](query-single-csv-file.md)
- [Query's uitvoeren op mappen en meerdere CSV-bestanden](query-folders-multiple-csv-files.md)
- [Query's uitvoeren op specifieke bestanden](query-specific-files.md)
- [Query's uitvoeren op Parquet-bestanden](query-parquet-files.md)
- [Weergaven maken en gebruiken](create-use-views.md)
- [Query's uitvoeren op JSON-bestanden](query-json-files.md)
- [Query uitvoeren op met Parquet geneste typen](query-parquet-nested-types.md)
