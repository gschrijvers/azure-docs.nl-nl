---
title: 'Quick Start: een query naar de API verzenden met behulp van Java-Bing lokale zakelijke zoek opdracht'
titleSuffix: Azure Cognitive Services
description: Gebruik deze Quick Start om aanvragen te verzenden naar de Bing lokale Business Search-API, een Azure-cognitieve service.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: c50222c645926a6e48995e6c66d7844adc02699f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873002"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Snelstartgids: een query naar de Bing Local Business Search-API verzenden met behulp van Java

Gebruik deze Quick Start om te leren hoe u aanvragen verzendt naar de Bing lokale Business Search-API, een Azure-cognitieve service. Hoewel deze eenvoudige toepassing wordt geschreven in Java, is de API een betrouw bare webservice die compatibel is met elke programmeer taal die kan leiden tot het maken van HTTP-aanvragen en het parseren van JSON.

In deze voorbeeld toepassing worden lokale antwoord gegevens opgehaald uit de API voor een zoek query.

## <a name="prerequisites"></a>Vereisten

* De [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Een [Cognitive Services-API-account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) met Bing zoeken-API's. Voor deze Quick start is de [gratis proef versie](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) voldoende. Sla de API-sleutel op die wordt weer gegeven wanneer u de gratis proef versie activeert. Zie [Cognitive Services prijzen-Bing Search API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)voor meer informatie.


## <a name="create-the-request"></a>De aanvraag maken 

Met de volgende code maakt u een `WebRequest` , stelt de kop van de toegangs sleutel in en voegt een query reeks voor *Hotel toe in Bellevue*.  Vervolgens wordt de aanvraag verzonden en het antwoord toegewezen aan een tekenreeks die de JSON-tekst moet bevatten.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>De volledige toepassing uitvoeren

De volgende code gebruikt de Bing lokale Business Search-API om Zoek resultaten te retour neren van de Bing zoek machine. Voer deze code uit door de volgende stappen uit te voeren:
1. Download of installeer de gson-bibliotheek.
2. Maak een nieuw Java-project in uw favoriete IDE of editor.
3. Voeg de onderstaande code toe.
4. Vervang de waarde `subscriptionKey` door een geldige toegangssleutel voor uw abonnement.
5. Voer het programma uit.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

            // receive JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();

            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);

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
        }

        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }

        public static void main (String[] args) {
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

                System.out.println("\nRelevant HTTP Headers:\n");
                for (String header : result.relevantHeaders.keySet())
                    System.out.println(header + ": " + result.relevantHeaders.get(header));

                System.out.println("\nJSON Response:\n");
                System.out.println(prettify(result.jsonResponse));
            }
            catch (Exception e) {
                e.printStackTrace(System.out);
                System.exit(1);
            }
        }
    }

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

## <a name="next-steps"></a>Volgende stappen
- [Lokale zakelijke zoek acties C# Quick Start](local-quickstart.md)
- [Snelstartgids voor lokaal zoeken in node. js](local-search-node-quickstart.md)
- [Snelstartgids voor lokale zakelijke Zoek opdrachten python](local-search-python-quickstart.md)
