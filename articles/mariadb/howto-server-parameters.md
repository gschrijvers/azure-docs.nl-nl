---
title: Server parameters configureren-Azure Portal-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u MariaDB-server parameters in Azure Database for MariaDB kunt configureren met behulp van de Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/16/2020
ms.openlocfilehash: f39e9450fb922e5b93d7b4b809df73cf5ab007c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81602394"
---
# <a name="how-to-configure-server-parameters-in-azure-database-for-mariadb-by-using-the-azure-portal"></a>Server parameters configureren in Azure Database for MariaDB met behulp van de Azure Portal

Azure Database for MariaDB ondersteunt de configuratie van sommige server parameters. In dit artikel wordt beschreven hoe u deze para meters configureert met behulp van de Azure Portal. Niet alle server parameters kunnen worden aangepast.

## <a name="navigate-to-server-parameters-on-azure-portal"></a>Navigeer naar server parameters op Azure Portal

1. Meld u aan bij de Azure Portal en zoek vervolgens de Azure Database for MariaDB-server.
2. Klik onder de sectie **instellingen** op **server parameters** om de pagina server parameters voor de Azure database for MariaDB-server te openen.
![Pagina Azure Portal Server parameters](./media/howto-server-parameters/azure-portal-server-parameters.png)
3. Zoek alle instellingen die u moet aanpassen. Bekijk de kolom **Beschrijving** om inzicht te krijgen in het doel en de toegestane waarden.
![Vervolg keuzelijst opsommen](./media/howto-server-parameters/3-toggle_parameter.png)
4. Klik op **Opslaan** om uw wijzigingen op te slaan.
![Wijzigingen opslaan of negeren](./media/howto-server-parameters/4-save_parameters.png)
5. Als u nieuwe waarden voor de para meters hebt opgeslagen, kunt u altijd terugkeren naar de standaard waarden door **alles opnieuw instellen op de standaard**waarde te selecteren.
![Alles opnieuw instellen op standaard waarden](./media/howto-server-parameters/5-reset_parameters.png)

## <a name="list-of-configurable-server-parameters"></a>Lijst met Configureer bare server parameters

De lijst met ondersteunde server parameters groeit voortdurend. Gebruik het tabblad Server parameters in Azure Portal om de definitie op te halen en server parameters te configureren op basis van uw toepassings vereisten.

## <a name="non-configurable-server-parameters"></a>Niet-Configureer bare server parameters

De InnoDB-buffer groep en het maximum aantal verbindingen kunnen niet worden geconfigureerd en zijn gekoppeld aan uw [prijs categorie](concepts-pricing-tiers.md).

|**Prijs categorie**| **vCore (s)**|**InnoDB buffer pool (MB)**|
|---|---|---|
|Basic| 1| 1024|
|Basic| 2| 2560|
|Algemeen gebruik| 2| 3584|
|Algemeen gebruik| 4| 7680|
|Algemeen gebruik| 8| 15360|
|Algemeen gebruik| 16| 31232|
|Algemeen gebruik| 32| 62976|
|Algemeen gebruik| 64| 125952|
|Geoptimaliseerd geheugen| 2| 7168|
|Geoptimaliseerd geheugen| 4| 15360|
|Geoptimaliseerd geheugen| 8| 30720|
|Geoptimaliseerd geheugen| 16| 62464|
|Geoptimaliseerd geheugen| 32| 125952|

Deze aanvullende server parameters kunnen niet worden geconfigureerd in het systeem:

|**Bepaalde**|**Vaste waarde**|
| :------------------------ | :-------- |
|innodb_file_per_table in de Basic-laag|UIT|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256 MB|
|innodb_log_files_in_group|2|

Andere server parameters die hier niet worden vermeld, worden ingesteld op hun MariaDB-out-of-Box-standaard waarden voor [MariaDB](https://mariadb.com/kb/en/library/xtradbinnodb-server-system-variables/).

## <a name="working-with-the-time-zone-parameter"></a>Werken met de para meter tijd zone

### <a name="populating-the-time-zone-tables"></a>De tijd zone tabellen worden gevuld

De tijdzone tabellen op uw server kunnen worden gevuld door de `mysql.az_load_timezone` opgeslagen procedure aan te roepen vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench.

> [!NOTE]
> Als u de opdracht uitvoert `mysql.az_load_timezone` vanuit MySQL Workbench, moet u de veilige update modus mogelijk eerst uitschakelen met `SET SQL_SAFE_UPDATES=0;`.

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
> U moet de server opnieuw opstarten om te controleren of de tabellen van de tijd zone juist zijn ingevuld. Gebruik de [Azure Portal](howto-restart-server-portal.md) of [cli](howto-restart-server-cli.md)om de server opnieuw op te starten.
Voer de volgende opdracht uit om de beschik bare tijd zone waarden weer te geven:

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>De tijd zone van het globale niveau instellen

De tijd zone op het globale niveau kan worden ingesteld op de pagina **server parameters** in de Azure Portal. In het onderstaande voor de globale tijd zone wordt de waarde ' VS/Pacific ' ingesteld.

![De para meter voor de tijd zone instellen](./media/howto-server-parameters/timezone.png)

### <a name="setting-the-session-level-time-zone"></a>De tijd zone op sessie niveau instellen

De tijd zone op sessie niveau kan worden ingesteld door de `SET time_zone` opdracht uit te voeren vanuit een hulp programma zoals de MySQL-opdracht regel of MySQL Workbench. In het volgende voor beeld wordt de tijd zone ingesteld op de **Amerikaanse/Pacific-** tijd zone.

```sql
SET time_zone = 'US/Pacific';
```

Raadpleeg de MariaDB-documentatie voor [datum-en tijd functies](https://mariadb.com/kb/en/library/convert_tz/).

<!--
## Next steps

- [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md).
-->
