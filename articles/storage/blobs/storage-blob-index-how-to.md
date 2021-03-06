---
title: BLOB-index gebruiken om gegevens te beheren en te zoeken in Azure Blob-opslag
description: Zie voor beelden van het gebruik van BLOB-index Tags om blob-objecten te categoriseren, te beheren en te doorzoeken.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: cc82b6578b06323d8cf9a09644d50043dba8e554
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774318"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>BLOB-index Tags (preview) gebruiken om gegevens te beheren en te zoeken in Azure Blob-opslag

Met Blob-index Tags worden gegevens in uw opslag account gecategoriseerd met de kenmerken van de sleutel waarde-Tags. Deze tags worden automatisch geïndexeerd en weer gegeven als een query bare multi-dimensionale index om eenvoudig gegevens te vinden. Dit artikel laat u zien hoe u gegevens kunt instellen, ophalen en zoeken met behulp van BLOB-index Tags.

Zie voor meer informatie over de BLOB-index [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

> [!NOTE]
> BLOB-index bevindt zich in de open bare preview en is beschikbaar in de regio's **Frankrijk-centraal** en **Frankrijk-Zuid** . Zie voor meer informatie over deze functie, samen met bekende problemen en beperkingen, [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Vereisten
# <a name="portal"></a>[Portal](#tab/azure-portal)
- Het abonnement is geregistreerd en goedgekeurd voor toegang tot de BLOB-index preview
- Toegang tot [Azure Portal](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
Als blob-index bevindt zich in de open bare preview-versie, wordt het .NET-opslag pakket vrijgegeven in de preview-NuGet-feed. Deze bibliotheek is onderhevig aan wijzigingen tussen nu en wanneer deze officieel wordt. 

1. Voeg in Visual Studio de URL toe `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` aan uw NuGet-pakket bronnen. 

   Zie [pakket bronnen](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)voor meer informatie.

2. Zoek in de NuGet package manager het pakket **Azure. storage. blobs** en Installeer versie **12.5.0-dev. 20200422.2** naar uw project. U kunt ook de opdracht uitvoeren```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2```

   Zie [een pakket zoeken en installeren](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)voor meer informatie.

3. Voeg de volgende using-instructies toe aan de bovenkant van het code bestand.
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>Een nieuwe BLOB uploaden met index Tags
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/) 

2. Navigeer naar de optie **containers** onder **BLOB-service**, selecteer uw container

3. Selecteer de knop **uploaden** om de Blade uploaden te openen en blader door het lokale bestands systeem om een bestand te vinden dat u wilt uploaden als een blok-blob.

4. Vouw de vervolg keuzelijst **Geavanceerd** uit en ga naar de sectie **index Tags voor BLOB**

5. Voer de tags voor de BLOB-index van de sleutel/waarde in die u wilt Toep assen op uw gegevens

6. Selecteer de knop **uploaden** om de BLOB te uploaden

![Gegevens uploaden met Blob-index Tags](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)
In het volgende voor beeld ziet u hoe u een toevoeg-BLOB maakt met tags die zijn ingesteld tijdens het maken.
```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Labels voor BLOB-indexen ophalen, instellen en bijwerken
# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/) 

2. Navigeer naar de optie **containers** onder **BLOB-service**, selecteer uw container

3. Selecteer de gewenste Blob in de lijst met blobs in de geselecteerde container

4. Op het tabblad Overzicht van BLOB worden de eigenschappen van uw BLOB weer gegeven, inclusief alle **BLOB-index Tags**

5. U kunt de index Tags voor sleutel/waarde voor uw BLOB ophalen, instellen, wijzigen of verwijderen

6. Selecteer de knop **Opslaan** om de updates voor uw BLOB te bevestigen

![BLOB-index Tags voor objecten ophalen, instellen, bijwerken en verwijderen](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Gegevens filteren en vinden met Blob-index Tags

# <a name="portal"></a>[Portal](#tab/azure-portal)

Binnen het Azure Portal past het filter index Tags van blob automatisch de `@container` para meter toe om de geselecteerde container te bereiken. Als u gelabelde gegevens wilt filteren en zoeken in uw hele opslag account, gebruikt u onze REST API, Sdk's of hulpprogram ma's.

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/). 

2. Navigeer naar de optie **containers** onder **BLOB-service**, selecteer uw container

3. Selecteer de knop voor het **filteren van BLOB-index Tags** om binnen de geselecteerde container te filteren

4. Voer een BLOB-index code sleutel en label waarde in

5. Selecteer de knop voor het **filteren van BLOB-index Tags** om extra label filters toe te voegen (Maxi maal 10)

![Gelabelde objecten filteren en zoeken met Blob-index Tags](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Levenscyclus beheer met filters voor BLOB-index codes

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecteer uw opslag account in de [Azure Portal](https://portal.azure.com/). 

2. Ga naar de optie **levenscyclus beheer** onder **BLOB-service**

3. Selecteer *regel toevoegen* en vul vervolgens de velden voor de actie sets in

4. Filterset selecteren om een optioneel filter toe te voegen voor voorvoegsel overeenkomst en overeenkomende BLOB-index ![ Label filters voor levenscyclus beheer toevoegen](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. Selecteer **controleren + toevoegen** om het ![ voor beeld van de regel instellingen levenscyclus beheer regel met filter voor BLOB index Tags te bekijken](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. Selecteer **toevoegen** om de nieuwe regel toe te passen op het levenscyclus beheer beleid

# <a name="net"></a>[.NET](#tab/net)
Het [levenscyclus beheer](storage-lifecycle-management-concepts.md) beleid wordt voor elk opslag account toegepast op het niveau van het controle vlak. Voor .NET installeert u de [Microsoft Azure Management-opslag bibliotheek versie 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) of hoger om te profiteren van het filter van de BLOB-index in een levenscyclus beheer regel.

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over blob-index. Zie [gegevens beheren en zoeken op Azure Blob Storage met Blob-index (preview)](storage-manage-find-blobs.md )

Meer informatie over levenscyclus beheer. Zie [de levens cyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
