---
title: Azure Kubernetes-service uitvoeren-Text Analytics
titleSuffix: Azure Cognitive Services
description: Implementeer de Text Analytics container installatie kopie naar de Azure Kubernetes-service en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877813"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Een Text Analytics-container implementeren in azure Kubernetes service

Meer informatie over het implementeren van de Azure Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) container installatie kopie naar Azure Kubernetes service (AKS). In deze procedure ziet u hoe u een Text Analytics resource maakt, hoe u een gekoppelde sentiment-analyse kopie maakt en hoe u deze indeling van de twee kunt volgen vanuit een browser. Door gebruik te maken van containers kunt u uw aandacht afleiden van het beheer van de infra structuur om te richten op het ontwikkelen van toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor deze procedure zijn verschillende hulpprogram ma's vereist die moeten worden geïnstalleerd en lokaal worden uitgevoerd. Gebruik Azure Cloud Shell niet. U hebt het volgende nodig:

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een tekst editor, bijvoorbeeld [Visual Studio code](https://code.visualstudio.com/download).
* De [Azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) is geïnstalleerd.
* De [Kubernetes-cli](https://kubernetes.io/docs/tasks/tools/install-kubectl/) is geïnstalleerd.
* Een Azure-resource met de juiste prijs categorie. Niet alle prijs categorieën werken met deze container:
    * **Azure Text Analytics** resource met alleen F0 of Standard-prijs categorieën.
    * **Azure Cognitive Services** resource met de prijs categorie s0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Volgende stappen

* Meer [Cognitive Services containers](../../cognitive-services-container-support.md) gebruiken
* De [Text Analytics verbonden service](../vs-text-connected-service.md) gebruiken
