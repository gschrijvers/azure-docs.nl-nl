---
title: SSIS-migratie met Azure SQL Managed instance als de data base-werk belasting bestemming
description: SSIS-migratie met Azure SQL Managed instance als de werkbelasting bestemming voor de data base.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: c91731d70d5db59ecf5daa726fd42ee42c58f51e
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84116312"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>SSIS-migratie met Azure SQL Managed instance als de data base-werk belasting bestemming

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bij het migreren van data base-workloads van een SQL Server-exemplaar naar een Azure SQL Managed instance, moet u bekend zijn met de [Azure Data Migration service](https://docs.microsoft.com/azure/dms/dms-overview)(DMS) en de [netwerktopologieën voor migraties van SQL Managed instances met behulp van DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies).

Dit artikel is gericht op de migratie van de SSIS-pakketten (SQL Server Integration service) die zijn opgeslagen in SSIS Catalog (SSISDB) en SQL Server Agent Jobs waarmee SSIS-pakket uitvoeringen worden gepland.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS-catalogus migreren (SSISDB)

SSISDB-migratie kan worden uitgevoerd met behulp van DMS, zoals beschreven in het artikel: [SSIS-pakketten migreren naar SQL Managed instance](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SSIS-taken naar SQL Managed instance agent

SQL Managed instance heeft een systeem eigen, eersteklas scheduler, net als SQL Server Agent on-premises.  Omdat er nog geen migratie hulpprogramma voor SSIS-taken beschikbaar is, moeten deze worden gemigreerd van SQL Server Agent on-premises naar de SQL Managed instance agent via scripts/hand matig kopiëren.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [Netwerk topologieën voor migraties van SQL-beheerde exemplaren met behulp van DMS](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS-pakketten migreren naar een beheerd exemplaar van SQL](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>Volgende stappen

- [Verbinding maken met SSISDB in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [SSIS-pakketten uitvoeren die zijn geïmplementeerd in azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
