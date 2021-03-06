---
title: Uitgebreide geojson-geometrie | Microsoft Azure kaarten
description: In dit artikel leert u hoe Microsoft Azure Maps de geojson spec uitbreidt om bepaalde geometrieën weer te geven.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 98db10f0fc7a417f39d4bb00e77af6bdea034a03
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79276398"
---
# <a name="extended-geojson-geometries"></a>Uitgebreide geojson-geometrie

Azure Maps biedt een lijst met krachtige Api's voor het zoeken in en langs geografische functies. Deze Api's voldoen aan de standaard [GEOjson-specificatie][1] van de geografische functies.  

De [geojson spec][1] ondersteunt alleen de volgende geometrieën:

* GeometryCollection
* Lines Tring
* Multi line String
* Point
* Multi Polygon
* Spreek
* Polygoon

Sommige Azure Maps-Api's accepteren geoelementen die geen deel uitmaken van de [GEOjson-specificatie][1]. De [Zoek opdracht in Geometry](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) API accepteert bijvoorbeeld cirkel en veelhoeken.

Dit artikel bevat een gedetailleerde uitleg over hoe Azure Maps de [GEOjson-specificatie][1] uitbreidt om bepaalde geometrieën weer te geven.

## <a name="circle"></a>Cirkel

De `Circle` geometrie wordt niet ondersteund door de [geojson-specificatie][1]. We gebruiken een `GeoJSON Point Feature` object om een cirkel aan te duiden.

Een `Circle` geometrie die wordt weer `GeoJSON Feature` gegeven met behulp van het object __moet__ de volgende coördinaten en eigenschappen bevatten:

- Midden

    Het midden van de cirkel wordt weer gegeven `GeoJSON Point` met behulp van een-object.

- Radius

    De cirkel `radius` wordt weer gegeven met `GeoJSON Feature`behulp van de eigenschappen. De RADIUS-waarde is in _meters_ en moet van het `double`type zijn.

- SubType

    De geometrie van de cirkel moet ook `subType` de eigenschap bevatten. Deze eigenschap moet een deel van de `GeoJSON Feature`eigenschappen zijn en de waarde moet een _cirkel_ zijn

#### <a name="example"></a>Voorbeeld

Hier ziet u hoe u een cirkel maakt met behulp van een `GeoJSON Feature` -object. Laten we de cirkel in de breedte graad: 47,639754 en de lengte graad:-122,126986 en deze toewijzen aan een straal gelijk aan 100 meters:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rechthoek

De `Rectangle` geometrie wordt niet ondersteund door de [geojson-specificatie][1]. We gebruiken een `GeoJSON Polygon Feature` object om een rechthoek weer te geven. De uitbrei ding van de rechthoek wordt voornamelijk gebruikt door de module teken hulpprogramma's van de Web-SDK.

Een `Rectangle` geometrie die wordt weer `GeoJSON Polygon Feature` gegeven met behulp van het object __moet__ de volgende coördinaten en eigenschappen bevatten:

- Hoeken

    De hoeken van de rechthoek worden weer gegeven met behulp `GeoJSON Polygon` van de coördinaten van een object. Er moeten vijf coördinaten zijn: één voor elke hoek. En, een vijfde coördinaat die gelijk is aan de eerste coördinaat, om de polygoon ring te sluiten. Er wordt van uitgegaan dat deze coördinaten worden uitgelijnd en de ontwikkelaar kan deze naar wens draaien.

- SubType

    De geometrie van de rechthoek moet ook `subType` de eigenschap bevatten. Deze eigenschap moet onderdeel zijn van de `GeoJSON Feature`eigenschappen en de waarde moet _rechthoekig_ zijn

### <a name="example"></a>Voorbeeld

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Volgende stappen

Meer informatie over geojson-gegevens in Azure Maps:

> [!div class="nextstepaction"]
> [Geojson-indeling geofence](geofence-geojson.md)

Bekijk de woorden lijst met algemene technische termen die zijn gekoppeld aan Azure Maps en locatie intelligence-toepassingen:

> [!div class="nextstepaction"]
> [Azure Maps-woordenlijst](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946
