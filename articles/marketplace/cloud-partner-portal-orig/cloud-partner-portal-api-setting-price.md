---
title: Prijzen voor aanbiedingen van virtuele machines | Azure Marketplace
description: Hierin worden de drie methoden beschreven voor het opgeven van de prijs van aanbiedingen van virtuele machines.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255886"
---
<a name="pricing-for-virtual-machine-offers"></a>Prijzen voor virtuele machine-aanbiedingen
==================================

> [!NOTE]
> De Cloud Partner-portal-Api's zijn geïntegreerd met partner centrum en blijven werken nadat uw aanbiedingen zijn gemigreerd naar het partner centrum. De integratie introduceert kleine wijzigingen. Bekijk de wijzigingen die worden vermeld in [Cloud Partner-Portal API-referentie](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar het partner centrum.

Er zijn drie manieren om prijzen voor aanbiedingen van virtuele machines op te geven: aangepaste prijzen voor kernen, tarieven per kern en prijs informatie voor spread sheets.


<a name="customized-core-pricing"></a>Aangepaste prijzen voor kernen
-----------------------

Prijzen zijn specifiek voor elke regio en kern combinatie. Elke regio in de lijst verkopen moet worden opgegeven in de sectie **virtualMachinePricing**/**regionPrices** van de definitie.  Gebruik de juiste valuta codes voor elke [regio](#regions) in uw aanvraag.  In het volgende voor beeld ziet u deze vereisten:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Prijzen per kern
----------------

In dit geval geven de uitgevers één prijs op in USD voor de SKU en worden alle overige prijzen automatisch gegenereerd. De prijs per kern is opgegeven in de **para meter van de aanvraag** .

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Prijzen van spread sheet
-------------------

De uitgever kan ook hun prijs werk blad uploaden naar een tijdelijke opslag locatie en vervolgens de URI in de aanvraag, zoals andere bestands artefacten, toevoegen. Het werk blad wordt vervolgens geüpload, vertaald om het opgegeven prijs schema te evalueren en de aanbieding wordt met de prijs informatie bijgewerkt. Bij volgende GET-aanvragen voor de aanbieding worden de spread sheet-URI en de geëvalueerde prijzen voor de regio geretourneerd.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Nieuwe kern grootten toegevoegd op 7/2/2019
---------------------------

VM-uitgevers zijn op 2 juli 2019 op de hoogte gebracht van de toevoeging van nieuwe prijzen voor nieuwe Azure virtual machine-grootten (op basis van het aantal kern geheugens).  De nieuwe prijzen gelden voor de belangrijkste grootten 10, 44, 48, 60, 120, 208 en 416.  Voor bestaande virtuele machines zijn de nieuwe prijzen voor deze kern geheugens automatisch berekend op basis van de huidige prijzen.  Uitgevers moeten tot 1 augustus 2019 de extra prijzen bekijken en de gewenste wijzigingen aanbrengen.  Na deze datum worden de automatisch berekende prijzen voor deze nieuwe kern groottes van kracht als u de uitgever nog niet opnieuw hebt gepubliceerd.


<a name="regions"></a>Regio's
-------

In de volgende tabel ziet u de verschillende regio's die u kunt opgeven voor aangepaste kern prijzen en de bijbehorende valuta codes.

| **Regio** | **Naam**             | **Valutacode** |
|------------|----------------------|-------------------|
| DZ         | Algerije              | DZD               |
| AR         | Argentinië            | ARS               |
| AU         | Australië            | AUD               |
| AT         | Oostenrijk              | EUR               |
| BH         | Bahrein              | BHD               |
| BY         | Belarus              | RUB               |
| BE         | België              | EUR               |
| BR         | Brazilië               | USD               |
| BG         | Bulgarije             | BGN               |
| CA         | Canada               | CAD               |
| CL         | Chili                | CLP               |
| CO         | Colombia             | BLEK               |
| CR         | Costa Rica           | CRC               |
| HR         | Kroatië              | HRK               |
| CY         | Cyprus               | EUR               |
| CZ         | Tsjechië       | CZK               |
| DK         | Denemarken              | DKK               |
| DO         | Dominicaanse Republiek   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egypte                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finland              | EUR               |
| FR         | Frankrijk               | EUR               |
| DE         | Duitsland              | EUR               |
| GR         | Griekenland               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | Hongkong SAR        | HKD               |
| HU         | Hongarije              | HUF               |
| IS         | IJsland              | SCHIJFTOEWIJZING               |
| IN         | India                | INR               |
| Id         | Indonesië            | IDR               |
| IE         | Ierland              | EUR               |
| IL         | Israël               | ILS               |
| IT         | Italië                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordanië               | JOD               |
| KZ         | Kazachstan           | KZT               |
| KE         | Kenia                | KES               |
| KR         | Korea                | KRW               |
| KW         | Koeweit               | KWD               |
| LV         | Letland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litouwen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Noord-Macedonië      | MKD               |
| MY         | Maleisië             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexico               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marokko              | MAD               |
| NL         | Nederland          | EUR               |
| NZ         | Nieuw-Zeeland          | NZD               |
| NG         | Nigeria              | NGN               |
| NO         | Noorwegen               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Peru                 | Tik               |
| PH         | Filipijnen          | PHP               |
| PL         | Polen               | PLN               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Roemenië              | LOEK               |
| RU         | Rusland               | RUB               |
| SA         | Saoedi-Arabië         | SAR               |
| RS         | Servië               | RSD               |
| SG         | Singapore            | SGA               |
| SK         | Slowakije             | EUR               |
| SI         | Slovenië             | EUR               |
| ZA         | Zuid-Afrika         | ZAR               |
| ES         | Spanje                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Zweden               | SEK               |
| CH         | Zwitserland          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | THB               |
| TT         | Trinidad en Tobago  | TTD               |
| TN         | Tunesië              | TND               |
| TR         | Turkije               | TRY               |
| UA         | Oekraïne              | UAH               |
| AE         | Verenigde Arabische Emiraten | EUR               |
| GB         | Verenigd Koninkrijk       | GBP               |
| VS         | Verenigde Staten        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
