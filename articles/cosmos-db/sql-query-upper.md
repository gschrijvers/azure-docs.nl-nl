---
title: BOVENSTE in Azure Cosmos DB query taal
description: Meer informatie over de functie van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303967"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in kleine letters naar hoofdletters.  

De bovenste systeem functie maakt geen gebruik van de index. Als u regel matig niet-hoofdletter gevoelige vergelijkingen wilt maken, kan de functie van het bovenste systeem een aanzienlijke hoeveelheid van RU verbruiken. Als dit het geval is, kunt u, in plaats van de functie voor het bovenste systeem, om gegevens telkens te normaliseren voor vergelijkingen, het hoofdletter gebruik tijdens het invoegen normaliseren. Vervolgens wordt een query zoals SELECT * FROM c waarbij UPPER (c. name) = ' BOB ' gewoon geselecteerd * van c waarbij c.name = ' BOB '.

## <a name="syntax"></a>Syntaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u `UPPER` hoe u kunt gebruiken in een query  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Dit is de resultatenset.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
