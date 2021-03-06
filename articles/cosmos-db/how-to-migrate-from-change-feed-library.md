---
title: Migreren van de bibliotheek voor het wijzigen van de feed-processor naar de Azure Cosmos DB .NET v3 SDK
description: Meer informatie over hoe u uw toepassing kunt migreren met behulp van de bibliotheek voor het wijzigen van de feed-processor naar de Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588880"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migreren van de bibliotheek voor het wijzigen van de feed-processor naar de Azure Cosmos DB .NET v3 SDK

In dit artikel worden de vereiste stappen beschreven voor het migreren van de code van een bestaande toepassing die gebruikmaakt van de [wijzigings werk bibliotheek](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) van de feed [in de laatste](change-feed.md) versie van de .NET SDK (ook wel .net v3 SDK genoemd).

## <a name="required-code-changes"></a>Vereiste code wijzigingen

De .NET v3 SDK heeft verschillende belang rijke wijzigingen, de volgende zijn de belangrijkste stappen voor het migreren van uw toepassing:

1. Converteer de `DocumentCollectionInfo` instanties naar `Container` verwijzingen voor de containers bewaakt en leases.
1. Aanpassingen die worden gebruikt `WithProcessorOptions` , moeten worden bijgewerkt voor `WithLeaseConfiguration` gebruik `WithPollInterval` en voor intervallen, `WithStartTime` [voor begin tijd](how-to-configure-change-feed-start-time.md)en `WithMaxItems` het definiëren van het maximum aantal items.
1. Stel de `processorName` op `GetChangeFeedProcessorBuilder` in op overeenkomen met de `ChangeFeedProcessorOptions.LeasePrefix`waarde die is `string.Empty` geconfigureerd op of gebruik anders.
1. De wijzigingen worden niet meer geleverd als een `IReadOnlyList<Document>`in plaats daarvan, maar het `IReadOnlyCollection<T>` `T` is een type dat u moet definiëren. er is geen klasse van het basis item meer.
1. Als u de wijzigingen wilt afhandelen, hebt u geen implementatie meer nodig. in plaats daarvan moet u [een gemachtigde definiëren](change-feed-processor.md#implementing-the-change-feed-processor). De gemachtigde kan een statische functie zijn of, als u de status van de uitvoeringen wilt hand haven, uw eigen klasse maken en een instantie methode door geven als gemachtigde.

Als de oorspronkelijke code voor het bouwen van de wijzigings verwerkings processor er bijvoorbeeld als volgt uitziet:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

De gemigreerde code ziet er als volgt uit:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

En de gemachtigde kan een statische methode zijn:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Provincie en lease-container

Net als bij de wijzigings bibliotheek van de feed-processor gebruikt de functie wijzigings feed in .NET v3 SDK een [lease-container](change-feed-processor.md#components-of-the-change-feed-processor) om de status op te slaan. De schema's zijn echter verschillend.

De SDK v3 Change feed-processor detecteert elke oude status van de bibliotheek en migreert deze automatisch naar het nieuwe schema bij de eerste uitvoering van de gemigreerde toepassings code. 

U kunt de toepassing veilig stoppen met de oude code, de code migreren naar de nieuwe versie, de gemigreerde toepassing starten en eventuele wijzigingen die zijn opgetreden tijdens het stoppen van de toepassing, worden opgehaald en verwerkt door de nieuwe versie.

> [!NOTE]
> Migraties van toepassingen die gebruikmaken van de bibliotheek naar de .NET v3 SDK, zijn in één richting, aangezien de status (leases) wordt gemigreerd naar het nieuwe schema. De migratie is niet achterwaarts compatibel.


## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Voor beelden van gebruik op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voor beelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over het wijzigen van de feed-processor in de volgende artikelen:

* [Overzicht van de processor voor wijzigings invoer](change-feed-processor.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
* [Starttijd van verwerker van wijzigingenfeed](how-to-configure-change-feed-start-time.md)
