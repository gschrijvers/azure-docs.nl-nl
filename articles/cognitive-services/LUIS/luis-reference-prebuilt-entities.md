---
title: Alle vooraf gemaakte entiteiten-LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat een lijst van de vooraf gemaakte entiteiten die zijn opgenomen in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219718"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteiten per cultuur in uw LUIS-model

Language Understanding (LUIS) biedt vooraf gemaakte entiteiten. Wanneer een vooraf samengestelde entiteit is opgenomen in uw toepassing, bevat LUIS de overeenkomstige entiteit voorspellingen in het eindpunt antwoord. Alle voor beelden van uitingen worden ook aangeduid met de entiteit. Het gedrag van vooraf gemaakte entiteiten **kan niet** worden gewijzigd. Tenzij anders vermeld, zijn vooraf gebouwde entiteiten beschikbaar in alle land instellingen van de LUIS-toepassing (cultures). In de volgende tabel ziet u de vooraf gemaakte entiteiten die voor elke cultuur worden ondersteund.

|Cultuur|Subcultuur|Opmerkingen|
|--|--|--|
|Chinees|[zh-CN](#chinese-entity-support)||
|Nederlands|[nl-NL](#dutch-entity-support)||
|Engels|[en-US (Amerikaans)](#english-american-entity-support)||
|Frans|[fr-ca (Canada)](#french-canadian-entity-support), [fr-fr (Frank rijk)](#french-france-entity-support), ||
|Duits|[de-DE](#german-entity-support)||
|Italiaans|[it-IT](#italian-entity-support)||
|Japans|[ja-JP](#japanese-entity-support)||
|Koreaans|[ko-KR](#korean-entity-support)||
|Portugees|[pt-BR (Brazilië)](#portuguese-brazil-entity-support)||
|Spaans|[es-es (Spanje)](#spanish-spain-entity-support), [es-MX (Mexico)](#spanish-mexico-entity-support)||
|Turks|[Turks](#turkish-entity-support)|Geen vooraf gemaakte entiteiten die in Turks worden ondersteund|

## <a name="prediction-endpoint-runtime"></a>Voorspellings eindpunt runtime

De beschik baarheid van een vooraf samengestelde entiteit in een specifieke taal wordt bepaald door de runtime-versie van de Voorspellings eindpunt.

## <a name="chinese-entity-support"></a>Ondersteuning voor Chinese entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```zh-CN``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Ondersteuning voor Nederlandstalige entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```nl-NL``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Ondersteuning voor Engelse (American)-entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```en-US``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    V2, V3   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Ondersteuning voor Frans (Frank rijk)

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```fr-FR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |   -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Ondersteuning voor Frans (Canada)

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```fr-CA``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Duitse entiteits ondersteuning

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```de-DE``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Ondersteuning voor Italiaanse entiteiten

Italiaanse vooraf ontwikkelde leeftijd, valuta, dimensie, getal, percentage _resolutie_ gewijzigd van v2 en V3 preview.

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```it-IT``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Ondersteuning voor Japanse entiteit

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```ja-JP``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2,-   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2,-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2,-   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2,-   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2,-   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Koreaanse entiteits ondersteuning

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```ko-KR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    -   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    -   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Ondersteuning voor Portugees (Brazilië)-entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```pt-BR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Ondersteuning voor Spaanse (Spanje)-entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```es-ES``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    V2, V3   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Ondersteuning voor Spaanse (Mexico)-entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```es-MX``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>gevormd<br>day   |    -   |
[Valuta (Money)](luis-reference-prebuilt-currency.md):<br>Gulden<br>Fractionele eenheid (ex: afronding)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>time Range   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>dikte<br>informatie (bijvoorbeeld: bit/byte)<br>lengte (bijvoorbeeld: meter)<br>snelheid (bijvoorbeeld: mijl per uur)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Woordgroep](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Telwoord](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Rang telwoord](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Persoonnaam](luis-reference-prebuilt-person.md)   |    -   |
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Tempe ratuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>graden<br>rankine<br>delisle<br>Fahrenheit   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Opmerkingen over [afgeschafte vooraf samengestelde entiteiten](luis-reference-prebuilt-deprecated.md) weer geven

De woordgroep is niet beschikbaar in alle subcultuur van Portugees (Brazilië) ```pt-BR```.

## <a name="turkish-entity-support"></a>Turkse entiteit ondersteuning

**Er zijn geen vooraf gemaakte entiteiten die in Turks worden ondersteund.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bijdragen aan vooraf ontwikkelde entity culturen
De vooraf gemaakte entiteiten worden ontwikkeld in het open-source-tekst project van recognizers. [Bijdragen](https://github.com/Microsoft/Recognizers-Text) aan het project. Dit project bevat voor beelden van valuta per cultuur.

GeographyV2 en Persoonnaam worden niet opgenomen in het tekst project recognizers. Voor problemen met deze vooraf gemaakte entiteiten opent u een [ondersteunings aanvraag](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de entiteiten [aantal](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)en [valuta](luis-reference-prebuilt-currency.md) .
