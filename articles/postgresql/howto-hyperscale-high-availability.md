---
title: Hoge Beschik baarheid configureren-grootschalige (Citus)-Azure Database for PostgreSQL
description: Hoge Beschik baarheid in-of uitschakelen
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 78e6e8c18e67ce2dff0de94d298f06693a40135f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584048"
---
# <a name="configure-hyperscale-citus-high-availability"></a>Hoge Beschik baarheid van grootschalige (Citus) configureren

Azure Database for PostgreSQL-grootschalige (Citus) biedt hoge Beschik baarheid (HA) om uitval tijd van de data base te voor komen. Als HA is ingeschakeld, ontvangt elk knoop punt in een server groep een stand-by. Als het oorspronkelijke knoop punt een slechte status krijgt, wordt de stand-by wordt gepromoveerd om het te vervangen.

> [!IMPORTANT]
> Omdat HA het aantal servers in de groep verdubbelt, worden ook de kosten verdubbeld.

Het inschakelen van HA kan tijdens het maken van de Server groep, of later op het tabblad **configureren** voor uw server groep in de Azure Portal. De gebruikers interface ziet er in beide gevallen ongeveer als volgt uit. Sleep de schuif regelaar voor **hoge Beschik baarheid** naar Ja:

![ha-schuif regelaar](./media/howto-hyperscale-high-availability/01-ha-slider.png)

Klik op de knop **Opslaan** om uw selectie toe te passen. Het inschakelen van HA kan enige tijd in beslag nemen, omdat de Server groep stand-by staat en gegevens streamt naar deze.

Op het tabblad Overzicht voor de Server groep worden alle knoop punten en de bijbehorende stand-by weer **gegeven** , samen met een kolom met **hoge Beschik baarheid** , die aangeeft of ha voor elk knoop punt is ingeschakeld.

![de kolom ha in het overzicht van de Server groep](./media/howto-hyperscale-high-availability/02-ha-column.png)

### <a name="next-steps"></a>Volgende stappen

Meer informatie over [hoge Beschik baarheid](concepts-hyperscale-high-availability.md).
