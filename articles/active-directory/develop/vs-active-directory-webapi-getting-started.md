---
title: Aan de slag met Azure AD in Visual Studio WebApi-projecten
description: Aan de slag met Azure Active Directory in WebApi-projecten na het maken van verbinding met of het maken van een Azure AD met behulp van Visual Studio Connected Services
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 9a9f8aaa9e3f90e78668d2294ea9959b480e3384
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886072"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Aan de slag met Azure Active Directory (WebApi-projecten)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-webapi-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-webapi-what-happened.md)

In dit artikel vindt u aanvullende richt lijnen nadat u Active Directory hebt toegevoegd aan een ASP.NET WebAPI-project via de opdracht **project > Connected Services** van Visual Studio. Als u de service nog niet aan uw project hebt toegevoegd, kunt u dit op elk gewenst moment doen.

Zie [Wat is er gebeurd met mijn WebAPI-project?](vs-active-directory-webapi-what-happened.md) voor de wijzigingen die zijn aangebracht in het project bij het toevoegen van de verbonden service.

## <a name="requiring-authentication-to-access-controllers"></a>Verificatie vereisen voor toegang tot controllers

Alle controllers in uw project zijn voorzien van het `[Authorize]` kenmerk. Voor dit kenmerk moet de gebruiker worden geverifieerd voordat toegang kan worden verkregen tot de Api's die door deze controllers worden gedefinieerd. Als u wilt toestaan dat de controller anoniem wordt geopend, verwijdert u dit kenmerk van de controller. Als u de machtigingen op een meer gedetailleerd niveau wilt instellen, past u het kenmerk toe op elke methode waarvoor autorisatie is vereist in plaats van het toe te passen op de klasse controller.

## <a name="next-steps"></a>Volgende stappen

- [Verificatie scenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
