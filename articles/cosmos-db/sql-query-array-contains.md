---
title: ARRAY_CONTAINS in Azure Cosmos DB query taal
description: Meer informatie over de manier waarop de matrix SQL-systeem functie in Azure Cosmos DB retourneert een Booleaanse waarde die aangeeft of de matrix de opgegeven waarden bevat
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 47fe20888aa546e414b268b30c2e03580750a040
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78303474"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
Retourneert een Booleaanse waarde die aangeeft of de matrix de opgegeven waarde bevat. U kunt controleren op een gedeeltelijke of volledige overeenkomst van een object met behulp van een Boole-expressie in de opdracht. 

## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is de matrix expressie die moet worden doorzocht.  
  
*expressie*  
   Is de expressie die u wilt vinden.  

*bool_expr*  
   Is een booleaanse expressie. Als het resulteert in ' waar ' en als de opgegeven zoek waarde een object is, wordt met de opdracht gecontroleerd op een gedeeltelijke overeenkomst (het zoek object is een subset van een van de objecten). Als de waarde wordt geëvalueerd als ' false ', wordt met de opdracht gecontroleerd of alle objecten in de matrix volledig overeenkomen. Als niet wordt opgegeven, is de standaard waarde false. 
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt beschreven hoe u kunt controleren op `ARRAY_CONTAINS`lidmaatschap van een matrix met behulp van.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 Dit is de resultatenset.  
  
```json
[{"b1": true, "b2": false}]  
```  

In het volgende voor beeld wordt uitgelegd hoe u een gedeeltelijke overeenkomst van een JSON in een matrix kunt controleren met behulp van ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 Dit is de resultatenset.  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Matrix functies Azure Cosmos DB](sql-query-array-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
