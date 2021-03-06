---
title: Ontwikkelhulpprogramma’s
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's en geïntegreerde ontwikkel omgevingen die beschikbaar zijn op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: c48cf6a7a82e90d3c9d8dc4c35e37dfb944af99f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282678"
---
# <a name="development-tools-on-the-azure-data-science-virtual-machine"></a>Ontwikkel hulpprogramma's op de Azure Data Science Virtual Machine

De Data Science Virtual Machine (DSVM) bundelt verschillende populaire hulpprogram ma's in een zeer productieve Integrated Development Environment (IDE). Hier volgen enkele hulp middelen die beschikbaar zijn op de DSVM.

## <a name="visual-studio-community-edition"></a>Visual Studio Community Edition

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows: Visual Studio 2017, Windows 2019: Visual Studio 2019      |
| Typische toepassingen      | Software ontwikkeling    |
| Hoe wordt het geconfigureerd en geïnstalleerd op de DSVM?      | Data Science workload (python en R-hulpprogram ma's), Azure workload (Hadoop, Data Lake), node. js, SQL Server-hulpprogram ma's, [Azure machine learning voor Visual Studio code](https://github.com/Microsoft/vs-tools-for-ai)    |
| Hoe gebruiken en uitvoeren      | Snelkoppeling bureau blad`C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\Common7\IDE\devenv.exe`(). Open Visual Studio met behulp van het bureaublad pictogram of het menu **Start** . Zoek naar Program ma's (Windows-logo toets + S), gevolgd door **Visual Studio**. Van daaruit kunt u projecten maken in talen als C#, Python, R en node. js.   |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio code, RStudio, Juno  |

> [!NOTE]
> Mogelijk wordt er een bericht weer gegeven dat de evaluatie periode is verlopen. Voer uw Microsoft-account-referenties in. Of maak een nieuw gratis account om toegang te krijgen tot de Visual Studio-community.

## <a name="visual-studio-code"></a>Visual Studio Code 

|    |           |
| ------------- | ------------- |
| Wat is het?   | IDE voor algemeen gebruik      |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Typische toepassingen      | Code-editor en git-integratie   |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files (x86)\Microsoft VS Code\Code.exe`) in Windows, bureaublad snelkoppeling of Terminal (`code`) in Linux    |
| Gerelateerde hulpprogram ma's op de DSVM      |     Visual Studio, RStudio, Juno  |

## <a name="rstudio-desktop"></a>RStudio Desktop

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Typische toepassingen      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureau blad (`C:\Program Files\RStudio\bin\rstudio.exe`) in Windows, snelkoppeling op Bureau`/usr/bin/rstudio`blad () op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, Juno      |

## <a name="rstudio-server"></a>RStudio Server

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor R-taal   |
| Wat is het?   | IDE op basis van het web voor R    |
| Ondersteunde DSVM-versies      | Linux      |
| Typische toepassingen      |  R-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Schakel de service in met _systemctl rstudio-server inschakelen_en start de service vervolgens met _systemctl start rstudio-server_. Meld u vervolgens aan bij de RStudio-server\/op http:/Your-VM-IP: 8787.       |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio Desktop      |

## <a name="juno"></a>Juno 

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor Julia-taal   |
| Ondersteunde DSVM-versies      | Windows, Linux      |
| Typische toepassingen      |  Julia-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureau blad (`C:\JuliaPro-0.5.1.1\Juno.bat`) in Windows, snelkoppeling op Bureau`/opt/JuliaPro-VERSION/Juno`blad () op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio      |

## <a name="pycharm"></a>Pycharm

|    |           |
| ------------- | ------------- |
| Wat is het?   | Client-IDE voor python-taal    |
| Ondersteunde DSVM-versies      | Windows 2019, Linux      |
| Typische toepassingen      |  Python-ontwikkeling     |
| Hoe gebruiken en uitvoeren      | Bureaublad snelkoppeling (`C:\Program Files\tk`) in Windows. Bureaublad snelkoppeling (`/usr/bin/pycharm`) op Linux      |
| Gerelateerde hulpprogram ma's op de DSVM      |   Visual Studio, Visual Studio code, RStudio      |
