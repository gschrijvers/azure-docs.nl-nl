---
title: Notification Hubs gebruiken met python
description: Meer informatie over het gebruik van Azure Notification Hubs van een python-toepassing.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 1ff8c382813654b1dee38a99bf2cc0ca67afbedd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76313824"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Notification Hubs van python gebruiken

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

U hebt toegang tot alle Notification Hubs functies van een Java/PHP/python/ruby-back-end met behulp van de REST-interface van de notification hub, zoals beschreven in het MSDN-artikel [Notification hubs rest-api's](https://msdn.microsoft.com/library/dn223264.aspx).

> [!NOTE]
> Dit is een voor beeld van een implementatie van een referentie voor het implementeren van de melding verzenden in Python en is niet de door de officiële ondersteunde meldingen hub-SDK. Het voor beeld is gemaakt met behulp van python 3,4.

In dit artikel leest u informatie over:

- Bouw een REST-client voor Notification Hubs functies in python.
- Verzend meldingen via de Python-interface naar de notification hub-REST Api's.
- Ontvang een dump van de HTTP REST-aanvraag/respons voor fout opsporing/educatief gebruik.

U kunt de [zelf studie aan de slag](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) volgen voor het mobiele platform van uw keuze, waarbij u het back-end-gedeelte in python implementeert.

> [!NOTE]
> Het bereik van het voor beeld is alleen beperkt tot het verzenden van meldingen en voert geen registratie beheer uit.

## <a name="client-interface"></a>Client interface

De hoofd client interface kan dezelfde methoden bieden die beschikbaar zijn in de [.net notification hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx). Met deze interface kunt u rechtstreeks alle zelf studies en voor beelden die op deze site beschikbaar zijn, vertalen en door de community op internet bijgedragen.

U kunt alle beschik bare code vinden in het [PYTHON rest wrapper]-voor beeld.

Als u bijvoorbeeld een client wilt maken:

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Een Windows-pop-upmelding verzenden:

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>Implementatie

Als u dit nog niet hebt gedaan, volgt u de [zelf studie] aan de slag tot aan de laatste sectie waarin u de back-end moet implementeren.

Alle Details voor het implementeren van een volledige REST wrapper vindt u op [MSDN](https://msdn.microsoft.com/library/dn530746.aspx). In deze sectie wordt de python-implementatie beschreven van de belangrijkste stappen die nodig zijn om toegang te krijgen tot Notification Hubs REST-eind punten en meldingen te verzenden

1. De verbindingsreeks parseren
2. Het autorisatie token genereren
3. Een melding verzenden via HTTP REST API

### <a name="parse-the-connection-string"></a>De verbindingsreeks parseren

Hier is de hoofd klasse die de client implementeert, waarvan de constructor de connection string parseert:

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>Beveiligings token maken

De Details voor het maken van het beveiligings token zijn [hier](https://msdn.microsoft.com/library/dn495627.aspx)beschikbaar.
Voeg de volgende methoden toe `NotificationHub` aan de klasse om het token te maken op basis van de URI van de huidige aanvraag en de referenties die zijn geëxtraheerd uit de Connection String.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))


@staticmethod
def encode_base64(data):
    return base64.b64encode(data)


def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest


def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>Een melding verzenden via HTTP REST API

U kunt eerst een klasse definiëren die een melding weergeeft.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm',
                         'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

Deze klasse is een container voor een systeem eigen meldings hoofdtekst of een set eigenschappen van een sjabloon melding, een set kopteksten, die een indeling (systeem eigen platform of sjabloon) en platformspecifieke eigenschappen (zoals Apple Expires-eigenschappen en WNS-headers) bevat.

Raadpleeg de [documentatie van Notification hubs rest-api's](https://msdn.microsoft.com/library/dn495827.aspx) en de specifieke indelingen voor het meldings platform voor alle beschik bare opties.

Nu met deze klasse, schrijft u de methoden voor het verzenden van `NotificationHub` meldingen binnen de klasse.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(
        parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True,
                                       indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()


def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)


def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)


def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)


def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)


def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)


def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast',
                      'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile',
                      'X-NotificationClass': '1'}

    self.send_notification(nh, tags)


def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)


def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

Met deze methoden wordt een HTTP POST-aanvraag verzonden naar het/messages-eind punt van uw notification hub, met de juiste hoofd tekst en headers voor het verzenden van de melding.

### <a name="using-debug-property-to-enable-detailed-logging"></a>De eigenschap debug gebruiken om gedetailleerde logboek registratie in te scha kelen

Als u de eigenschap debug inschakelt tijdens het initialiseren van de notification hub, worden gedetailleerde logboek gegevens over de HTTP-aanvraag en de reactie dump opgeslagen, evenals een gedetailleerd waarschuwings bericht over verzen ding.
De [eigenschap TestSend](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100)) van de notification hubs retourneert gedetailleerde informatie over de melding verzenden resultaat.
Als u de initialisatie wilt gebruiken, gebruikt u de volgende code:

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

De HTTP-URL voor de verzend aanvraag van de notification hub wordt toegevoegd als resultaat van een test query teken reeks.

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>De zelf studie volt ooien

U kunt nu de zelf studie aan de slag volt ooien door de melding van een back-end van python te verzenden.

Initialiseer uw Notification Hubs-client (Vervang de connection string en de naam van de hub volgens de instructies in de [zelf studie aan de slag]):

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

Voeg vervolgens de verzend code toe, afhankelijk van uw mobiele doel platform. Dit voor beeld voegt ook methoden op een hoger niveau toe om het verzenden van meldingen op basis van het platform mogelijk te maken, bijvoorbeeld send_windows_notification voor Windows. send_apple_notification (voor Apple) etc.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store en Windows Phone 8,1 (niet-Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8,0 en 8,1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Als u de python-code uitvoert, moet er een melding op uw doel apparaat worden weer gegeven.

## <a name="examples"></a>Voorbeelden

### <a name="enabling-the-debug-property"></a>De `debug` eigenschap inschakelen

Wanneer u de vlag debug tijdens het initialiseren van de NotificationHub inschakelt, ziet u gedetailleerde HTTP-aanvraag-en reactie dump en NotificationOutcome zoals het volgende, waar u kunt zien welke HTTP-headers in de aanvraag worden door gegeven en welk HTTP-antwoord is ontvangen van de notification hub:

![][1]

U ziet bijvoorbeeld een gedetailleerd resultaat van de notification hub.

- Wanneer het bericht is verzonden naar de service voor push meldingen.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- Als er geen doelen voor een push melding zijn gevonden, wordt waarschijnlijk de volgende uitvoer weer gegeven als het antwoord (wat aangeeft dat er geen registraties zijn gevonden om de melding te leveren waarschijnlijk omdat de registraties niet-overeenkomende labels bevatten)
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Pop-upmelding aan Windows verzenden

Let op de kopteksten die worden verzonden wanneer u een melding voor een broadcast-pop naar Windows-client verzendt.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>Melding verzenden met een tag (of een label expressie)

Let op de Tags HTTP-header die wordt toegevoegd aan de HTTP-aanvraag (in het volgende voor beeld wordt de melding alleen verzonden naar registraties met de nettolading ' sporten ')

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>Melding verzenden met meerdere labels

U ziet hoe de HTTP-header van Tags verandert wanneer er meerdere labels worden verzonden.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>Sjabloon melding

U ziet dat de notatie van de HTTP-header wordt gewijzigd en de lading-hoofd tekst wordt verzonden als onderdeel van de HTTP-aanvraag tekst:

**Geregistreerde sjabloon aan de client zijde:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**Server zijde: verzenden van de lading**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een python REST-client maakt voor Notification Hubs. Hier kunt u het volgende doen:

- Down load het volledige [PYTHON rest-wrapper]-voor beeld, dat alle code in dit artikel bevat.
- Meer informatie over de functie voor het markeren van Notification Hubs in het [laatste nieuws zelf studie]
- Blijf leren over Notification Hubs-sjablonen in de [zelf studie over het lokaliseren van nieuws]

<!-- URLs -->
[Python REST wrapper-voor beeld]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[Zelfstudie Aan de slag]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Zelf studie over laatste nieuws]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Nieuws zelf studie voor lokalisatie]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
