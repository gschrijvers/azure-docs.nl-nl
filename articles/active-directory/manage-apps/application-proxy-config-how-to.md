---
title: Een toepassings proxy toepassing configureren | Microsoft Docs
description: Meer informatie over het maken en configureren van een toepassings proxy toepassing in een paar eenvoudige stappen
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67807860"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Een toepassings proxy toepassing configureren

In dit artikel leert u hoe u een toepassings proxy toepassing kunt configureren in azure AD om uw on-premises toepassingen beschikbaar te maken voor de Cloud.

## <a name="recommended-documents"></a>Aanbevolen documenten

Als u meer wilt weten over de initiële configuraties en het maken van een toepassings proxy toepassing via de beheer Portal, volgt u de [publicerende toepassingen met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md).

Zie [toepassings proxy inschakelen in de Azure Portal](application-proxy-add-on-premises-application.md)voor meer informatie over het configureren van connectors.

Zie [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie over het uploaden van certificaten en het gebruik van aangepaste domeinen.

## <a name="create-the-applicationsetting-the-urls"></a>De toepassing maken/de Url's instellen

Raadpleeg de fout Details voor informatie en suggesties voor het oplossen van de toepassing als u de stappen in de documentatie voor het [publiceren van toepassingen met Azure AD-toepassingsproxy gebruikt](application-proxy-add-on-premises-application.md) en er een fout optreedt bij het maken van de toepassing. De meeste fout berichten bevatten een voorgestelde oplossing. Als u veelvoorkomende fouten wilt voor komen, controleert u het volgende:

- U bent een beheerder met machtigingen voor het maken van een toepassings proxy toepassing
- De interne URL is uniek
- De externe URL is uniek
- De Url's beginnen met http of HTTPS, en eindigen met een "/"
- De URL moet een domein naam zijn, geen IP-adres

Het fout bericht wordt weer gegeven in de rechter bovenhoek wanneer u de toepassing maakt. U kunt ook het meldings pictogram selecteren om de fout berichten weer te geven.

![Hiermee wordt aangegeven waar u de meldings prompt vindt in de Azure Portal](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Connectors/connector groepen configureren

Als u problemen ondervindt bij het configureren van uw toepassing vanwege een waarschuwing over de connectors en connector groepen, raadpleegt u de instructies over het inschakelen van de toepassings proxy voor meer informatie over het downloaden van connectors. Raadpleeg de [documentatie van connectors](application-proxy-connectors.md)als u meer wilt weten over connectors.

Als uw connectors inactief zijn, betekent dit dat ze de service niet kunnen bereiken. Dit komt vaak doordat alle vereiste poorten niet zijn geopend. Zie de sectie vereisten van de documentatie van de toepassings proxy inschakelen voor een lijst met vereiste poorten.

## <a name="upload-certificates-for-custom-domains"></a>Certificaten voor aangepaste domeinen uploaden

Met aangepaste domeinen kunt u het domein van uw externe Url's opgeven. Als u aangepaste domeinen wilt gebruiken, moet u het certificaat voor dat domein uploaden. Zie [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie over het gebruik van aangepaste domeinen en certificaten.

Als u problemen ondervindt met het uploaden van uw certificaat, zoekt u naar de fout berichten in de portal voor aanvullende informatie over het probleem met het certificaat. Veelvoorkomende certificaat problemen zijn onder andere:

- Verlopen certificaat
- Het certificaat is zelfondertekend
- De persoonlijke sleutel ontbreekt in het certificaat

Het fout bericht wordt in de rechter bovenhoek weer gegeven wanneer u het certificaat wilt uploaden. U kunt ook het meldings pictogram selecteren om de fout berichten weer te geven.

## <a name="next-steps"></a>Volgende stappen

[Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md)
