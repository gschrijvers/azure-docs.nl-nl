---
title: Een doellaag toevoegen aan een kaart | Microsoft Azure kaarten
description: In dit artikel leert u hoe u een tekenlaag aan een kaart kunt toevoegen met behulp van de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 7ae11734eb804715f3eb1b5edcb02fc328dafec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77208553"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Een tekenlaag aan een kaart toevoegen

Dit artikel laat u zien hoe u punt gegevens van een gegevens bron kunt weer geven als een tekenlaag op een kaart. Met bellen lagen worden punten weer gegeven als cirkels op de kaart met een harde pixel RADIUS. 

> [!TIP]
> Met bellen lagen worden standaard de coördinaten van alle geometrieën in een gegevens bron weer gegeven. Als u de laag zo wilt beperken dat alleen de functies van de punt geometrie `filter` worden weer gegeven, stelt `['==', ['geometry-type'], 'Point']` u `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` de eigenschap van de laag in of als u ook multi point-functies wilt toevoegen.

## <a name="add-a-bubble-layer"></a>Een bubbellaag toevoegen

Met de volgende code wordt een matrix met punten geladen in een gegevens bron. Vervolgens worden de gegevens punten verbonden met een [Bubble laag](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest). De laag Bubble geeft de straal van elke bel weer met vijf pixels en een opvul kleur wit. En, een streek kleur blauw en een streek breedte van zes pixels. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Hieronder ziet u het volledige programma voor het uitvoeren van code van de bovenstaande functionaliteit.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer gegevens bron' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de pen <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>BubbleLayer data source</a> by Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Labels weer geven met een Bubble Layer

Deze code laat zien hoe u een Bubble laag kunt gebruiken om een punt op de kaart weer te geven. En hoe u een symbool laag kunt gebruiken om een label weer te geven. Als u het pictogram van de laag Symbol wilt verbergen, `image` stelt u de eigenschap van de `'none'`pictogram opties in op.

<br/>

<iframe height='500' scrolling='no' title='Gegevens bron met meerdere lagen' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>gegevens bron</a> met meerdere lagen Pen door<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Een Bubble laag aanpassen

De Bubble laag heeft slechts enkele opmaak opties. Hier volgt een hulp programma om het uit te proberen.

<br/>

<iframe height='700' scrolling='no' title='Opties voor Bubble Layers' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Zie de <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>laag opties</a> van de Pen op Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

Raadpleeg de volgende artikelen voor meer code voorbeelden om toe te voegen aan uw kaarten:

> [!div class="nextstepaction"]
> [Een gegevensbron maken](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Gegevensgestuurde stijlexpressies gebruiken](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Codevoorbeelden](https://docs.microsoft.com/samples/browse/?products=azure-maps)
