---
title: DTU-resource beperkt enkele data bases
description: Op deze pagina worden enkele veelvoorkomende DTU-resource limieten voor afzonderlijke data bases in Azure SQL Database beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 5adb09389e1d5be13f766240d9fac5a644190d6d
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051008"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model---azure-sql-database"></a>Resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model-Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel vindt u gedetailleerde resource limieten voor Azure SQL Database afzonderlijke data bases met behulp van het DTU-aankoop model.

Zie [DTU-resource limieten-elastische Pools](resource-limits-dtu-elastic-pools.md)voor meer informatie over de resource limieten voor DTU-aankoop modellen voor elastische Pools. Zie voor vCore resource limieten [vCore resource limieten: individuele data bases](resource-limits-vcore-single-databases.md) en [vCore resource limieten: elastische Pools](resource-limits-vcore-elastic-pools.md). Zie [Inkoop modellen en service lagen](purchasing-models.md)voor meer informatie over de verschillende aankoop modellen.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Eén Data Base: opslag grootten en berekenings grootten

In de volgende tabellen ziet u de beschik bare resources voor één data base in elke servicelaag en de reken grootte. U kunt de servicelaag, de reken grootte en de opslag hoeveelheid voor één data base instellen met behulp van de [Azure Portal](single-database-manage.md#azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [Power shell](single-database-manage.md#powershell), de [Azure cli](single-database-manage.md#azure-cli)of de [rest API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Zie [Eén data base schalen](single-database-scale.md) voor meer informatie over schaling en overwegingen

### <a name="basic-service-tier"></a>Servicelaag Basic

| **Reken grootte** | **Standaard** |
| :--- | --: |
| Maximaal aantal DTU's | 5 |
| Inbegrepen opslag (GB) | 2 |
| Maximum aantal opslag opties (GB) | 2 |
| Maximale OLTP-opslag in het geheugen (GB) |N.v.t. |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen) | 30 |
| Maximaal aantal gelijktijdige sessies | 300 |
|||

> [!IMPORTANT]
> De Basic-servicelaag biedt minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Met betrekking tot gegevens opslag wordt de Basic-servicelaag op standaard pagina-blobs geplaatst. Standaard pagina-blobs maken gebruik van schijven op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkelings-, test-en andere weinig frequent gebruikte workloads die minder gevoelig zijn voor prestatie variaties.
>

### <a name="standard-service-tier"></a>Servicelaag Standard

| **Reken grootte** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximaal aantal DTU's | 10 | 20 | 50 | 100 |
| Inbegrepen opslag (GB) | 250 | 250 | 250 | 250 |
| Maximum aantal opslag opties (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximale OLTP-opslag in het geheugen (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen)| 60 | 90 | 120 | 200 |
| Maximaal aantal gelijktijdige sessies |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> De standaard S0-, S1-en S2-lagen bieden minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Met betrekking tot gegevens opslag worden de standaard S0-en S1-service lagen geplaatst op standaard pagina-blobs. Standaard pagina-blobs maken gebruik van schijven op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkelings-, test-en andere weinig frequent gebruikte workloads die minder gevoelig zijn voor prestatie variaties.
>

### <a name="standard-service-tier-continued"></a>Standard-servicelaag (vervolg)

| **Reken grootte** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximaal aantal DTU's | 200 | 400 | 800 | 1600 | 3000 |
| Inbegrepen opslag (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximum aantal opslag opties (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximale OLTP-opslag in het geheugen (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. |N.v.t. |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen)| 400 | 800 | 1600 | 3200 |6000 |
| Maximaal aantal gelijktijdige sessies |4800 | 9600 | 19200 | 30.000 |30.000 |
|||||||

### <a name="premium-service-tier"></a>Premium servicelaag

| **Reken grootte** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximaal aantal DTU's | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inbegrepen opslag (GB) | 500 | 500 | 500 | 500 | 4096 * | 4096 * |
| Maximum aantal opslag opties (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 * | 4096 * |
| Maximale OLTP-opslag in het geheugen (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maxi maal aantal gelijktijdige werk nemers (aanvragen)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximaal aantal gelijktijdige sessies | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
|||||||

\*Van 1024 GB tot 4096 GB in stappen van 256 GB

> [!IMPORTANT]
> Meer dan 1 TB aan opslag ruimte in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China-oost, China-noord, Duitsland-centraal, Duitsland-noordoost, VS-West-Centraal, US DoD regio's en Amerikaanse overheid centraal. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.  
> [!NOTE]
> `tempdb`Zie voor limieten [tempdb-limieten](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model](resource-limits-vcore-single-databases.md) voor vCore resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](resource-limits-vcore-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Voor DTU-resource limieten voor elastische Pools raadpleegt [u resource limieten voor elastische Pools met behulp van het DTU-aankoop model](resource-limits-dtu-elastic-pools.md)
- Zie [SQL Managed instance resource limits](../managed-instance/resource-limits.md)voor resource limieten voor beheerde exemplaren in een Azure SQL Managed instance.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een logische SQL-Server](resource-limits-logical-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een logische SQL-Server.
