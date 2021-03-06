---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77202110"
---
U kunt de volgende parameter typen voor de activerings-BLOB gebruiken:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* Een POCO serialiseerbaar als JSON
* `ICloudBlob`<sup>i</sup>
* `CloudBlockBlob`<sup>i</sup>
* `CloudPageBlob`<sup>i</sup>
* `CloudAppendBlob`<sup>i</sup>

<sup>1</sup> vereist de binding `direction` ' InOut ' in *Function. json* of `FileAccess.ReadWrite` in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen opslag-SDK en er een fout bericht wordt weer gegeven, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)hebt.

Binding met `string`, `Byte[]`, of poco wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een `Stream` of `CloudBlockBlob` -type te gebruiken. Zie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) verderop in dit artikel voor meer informatie.