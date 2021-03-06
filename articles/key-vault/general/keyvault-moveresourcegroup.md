---
title: Azure Key Vault een kluis naar een andere resource groep verplaatsen | Microsoft Docs
description: Richt lijnen voor het verplaatsen van een sleutel kluis naar een andere resource groep.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to move my vault to another resource group.
ms.openlocfilehash: bbc27af9eb448911093473d6ab20fde8004c7b88
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783719"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Een Azure Key Vault verplaatsen tussen resource groepen

## <a name="overview"></a>Overzicht

Het verplaatsen van een sleutel kluis over resource groepen is een ondersteunde functie voor sleutel kluis. Het verplaatsen van een sleutel kluis tussen resource groepen heeft geen invloed op de configuratie van de sleutel kluis firewall of het toegangs beleid. Verbonden toepassingen en service-principals moeten blijven werken zoals bedoeld.

## <a name="design-considerations"></a>Overwegingen bij het ontwerp

Uw organisatie heeft mogelijk Azure Policy geïmplementeerd met afdwinging of uitsluitingen op het niveau van de resource groep. Er is mogelijk een andere set beleids toewijzingen in de resource groep waar uw sleutel kluis momenteel bestaat en de resource groep waar u de sleutel kluis naartoe wilt verplaatsen. Een conflict in beleids vereisten is het mogelijk om uw toepassingen te onderbreken.

### <a name="example"></a>Voorbeeld

U hebt een toepassing die is verbonden met een sleutel kluis die certificaten maakt die twee jaar geldig zijn. De resource groep waar u de sleutel kluis wilt verplaatsen, heeft een beleids toewijzing die het maken van certificaten blokkeert die langer dan één jaar geldig zijn. Nadat u de sleutel kluis naar de nieuwe resource groep hebt verplaatst, wordt de bewerking voor het maken van een certificaat dat twee jaar geldig is, geblokkeerd door een Azure-beleids toewijzing.

### <a name="solution"></a>Oplossing

Zorg ervoor dat u naar de pagina Azure Policy gaat in de Azure Portal en Bekijk de beleids toewijzingen voor uw huidige resource groep, evenals de resource groep die u wilt verplaatsen en zorg ervoor dat er geen verschillen zijn.

## <a name="procedure"></a>Procedure

1. Aanmelden bij Azure Portal
2. Navigeer naar uw sleutel kluis
3. Klik op het tabblad Overzicht
4. Selecteer de knop "verplaatsen"
5. Selecteer verplaatsen naar een andere resource groep in de vervolg keuzelijst opties
6. Selecteer de resource groep waarnaar u de sleutel kluis wilt verplaatsen
7. De waarschuwing bevestigen over het verplaatsen van resources
8. Selecteer OK

Key Vault evalueert nu de geldigheid van de resource verplaatsing en waarschuwt u eventuele fouten. Als er geen fouten worden gevonden, wordt het verplaatsen van de resource voltooid. 
