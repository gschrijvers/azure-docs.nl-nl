---
title: "Snelstartgids: zoeken naar Video's met behulp van de REST API en Java-Bing Video Search"
titleSuffix: Azure Cognitive Services
description: Gebruik deze quickstart om zoekaanvragen voor video's naar de Bing Video's zoeken-REST API te verzenden via Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.openlocfilehash: 0728aa84447573bd8d335daf84c01138c627ecb5
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848655"
---
# <a name="quickstart-search-for-videos-using-the-bing-video-search-rest-api-and-java"></a>Snelstartgids: zoeken naar Video's met behulp van de Bing Video Search REST API en Java

Gebruik deze Quick Start om uw eerste oproep naar de Bing Video's zoeken-API te maken. Met deze eenvoudige Java-toepassing wordt een HTTP-Zoek query naar de API verzonden en wordt het JSON-antwoord weer gegeven. Hoewel deze toepassing wordt geschreven in Java, is de API een REST-webservice die compatibel is met de meeste programmeer talen. 

De broncode voor dit voorbeeld is beschikbaar [op GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingVideoSearchv7.java) met extra foutafhandeling, functies en codeaantekeningen.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* De [Gson-bibliotheek](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Een project maken en initialiseren

1. Maak een nieuw Java-project in uw favoriete IDE of editor en importeer de volgende bibliotheken:

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Maak een nieuwe klasse met de naam `SearchResults` voor het opslaan van de headers en het JSON-antwoord vanuit de API.

    ```java
    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

3. Maak een nieuwe methode `SearchVideos()` met de naam met variabelen voor uw API-eindpunt-host en-pad, uw abonnements sleutel en zoek term. Deze methode retourneert een- `SearchResults` object. Voor de `host` waarde kunt u het globale eind punt in de volgende code gebruiken of het [aangepaste subdomein](../../../cognitive-services/cognitive-services-custom-subdomains.md) eindpunt gebruiken dat wordt weer gegeven in de Azure portal voor uw resource.

    ```java
    public static SearchResults SearchVideos (String searchQuery) throws Exception {
        static String subscriptionKey = "enter your key here";
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bing/v7.0/videos/search";
        static String searchTerm = "kittens";
    }
    ```

## <a name="construct-and-send-the-search-request"></a>De zoekaanvraag samenstellen en verzenden

`SearchVideos()`Voer de volgende stappen uit in de-methode:

1. Maak de URL voor uw aanvraag door uw API-host, pad en gecodeerde Zoek query te combi neren. Gebruik `openConnection()` om een verbinding te maken en voeg vervolgens uw abonnements sleutel toe aan de `Ocp-Apim-Subscription-Key` koptekst.

     ```java
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
     HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
     connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
     ```

2. Haal het antwoord op uit de API en sla de JSON-tekenreeks op.

     ```java
     InputStream stream = connection.getInputStream();
     String response = new Scanner(stream).useDelimiter("\\A").next();
     ```

 3. Gebruik `getHeaderFields()` om de HTTP-headers uit het antwoord te extraheren en sla degene die met Bing zijn gerelateerd in het `results`-object op. Sluit vervolgens de stroom en retour neer het resultaat.

     ```java
     // extract Bing-related HTTP headers
     Map<String, List<String>> headers = connection.getHeaderFields();
     for (String header : headers.keySet()) {
         if (header == null) continue;      // may have null key
         if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
             results.relevantHeaders.put(header, headers.get(header).get(0));
         }
     }
     stream.close();
     return results;
     ```

## <a name="format-the-response"></a>Het antwoord opmaken

Maak een methode met de naam `prettify()` om het antwoord op te maken dat vanuit de Bing Video-API is geretourneerd. Gebruik de bibliotheek van de Gson `JsonParser` om een JSON-teken reeks te converteren naar een-object. Gebruik vervolgens `GsonBuilder()` en `toJson()` om de opgemaakte teken reeks te maken.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="send-the-request-and-print-the-response"></a>De aanvraag verzenden en het antwoord afdrukken

Roep in de hoofdmethode van uw toepassing `SearchVideos` aan met uw zoekterm. Druk vervolgens de HTTP-headers af die zijn opgeslagen in het antwoord en de JSON-teken reeks die wordt geretourneerd door de API.

 ```java
 public static void main (String[] args) {

     SearchResults result = SearchVideos(searchTerm);
     //print the Relevant HTTP Headers
     for (String header : result.relevantHeaders.keySet())
         System.out.println(header + ": " + result.relevantHeaders.get(header));
     System.out.println(prettify(result.jsonResponse));
 }
 ```

## <a name="json-response"></a>JSON-antwoord

Een geslaagd antwoord wordt geretourneerd in de JSON-indeling, zoals u kunt zien in het volgende voorbeeld:

```json
{
    "_type": "Videos",
    "instrumentation": {},
    "readLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?q=kittens",
    "webSearchUrl": "https://www.bing.com/videos/search?q=kittens",
    "totalEstimatedMatches": 1000,
    "value": [
        {
            "webSearchUrl": "https://www.bing.com/videos/search?q=kittens&view=...",
            "name": "Top 10 cute kitten videos compilation",
            "description": "HELP HOMELESS ANIMALS AND WIN A PRIZE BY CHOOSING...",
            "thumbnailUrl": "https://tse4.mm.bing.net/th?id=OVP.n1aE_Oikl4MtzBb...",
            "datePublished": "2014-11-12T22:47:36.0000000",
            "publisher": [
                {
                    "name": "Fabrikam"
                }
            ],
            "creator": {
                "name": "Marcus Appel"
            },
            "isAccessibleForFree": true,
            "contentUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "hostPageUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "encodingFormat": "h264",
            "hostPageDisplayUrl": "https://www.fabrikam.com/watch?v=8HVWitAW-Qg",
            "width": 480,
            "height": 360,
            "duration": "PT3M52S",
            "motionThumbnailUrl": "https://tse4.mm.bing.net/th?id=OM.j4QyJAENJphdZQ_1501386166&pid=Api",
            "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"https://www.fabrikam.com/embed/8HVWitAW-Qg?autoplay=1\" frameborder=\"0\" allowfullscreen></iframe>",
            "allowHttpsEmbed": true,
            "viewCount": 7513633,
            "thumbnail": {
                "width": 300,
                "height": 168
            },
            "videoId": "655D98260D012432848F6558260D012432848F",
            "allowMobileEmbed": true,
            "isSuperfresh": false
        },
        . . .
    ],
    "nextOffset": 36,
    "queryExpansions": [
        {
            "text": "Kittens Meowing",
            "displayText": "Meowing",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Meowing...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Kittens+Meowing&pid..."
            }
        },
        {
            "text": "Funny Kittens",
            "displayText": "Funny",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Funny+Kittens...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
            "thumbnail": {
                "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Funny+Kittens&..."
            }
        },
        . . .
    ],
    "pivotSuggestions": [
        {
            "pivot": "kittens",
            "suggestions": [
                {
                    "text": "Cat",
                    "displayText": "Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Cat&pid=Api..."
                    }
                },
                {
                    "text": "Feral Cat",
                    "displayText": "Feral Cat",
                    "webSearchUrl": "https://www.bing.com/videos/search?q=Feral+Cat...",
                    "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search...",
                    "thumbnail": {
                        "thumbnailUrl": "https://tse3.mm.bing.net/th?q=Feral+Cat&pid=Api&..."
                    }
                }
            ]
        }
    ],
    "relatedSearches": [
        {
            "text": "Kittens Being Born",
            "displayText": "Kittens Being Born",
            "webSearchUrl": "https://www.bing.com/videos/search?q=Kittens+Being+Born...",
            "searchLink": "https://api.cognitive.microsoft.com/api/v7/videos/search?...",
            "thumbnail": {
                "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Kittens+Being+Born&pid=..."
            }
        },
        . . .
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zie ook 

 [Wat is de Bing Video's zoeken-API?](../overview.md)
