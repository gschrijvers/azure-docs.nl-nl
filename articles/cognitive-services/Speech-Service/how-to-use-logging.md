---
title: Speech SDK logging-Speech Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het inschakelen van logboek registratie in de Speech SDK (C++, C#, Python, objectief-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 707a0f801a739a7a91cee19635e609305cd8f021
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74805787"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Logboek registratie inschakelen in de Speech SDK

Logboek registratie in bestand is een optionele functie voor de spraak-SDK. Tijdens het vastleggen van de ontwikkeling biedt aanvullende informatie en diagnostische gegevens van de kern onderdelen van de Speech SDK. U kunt deze inschakelen door de eigenschap `Speech_LogFilename` in te stellen voor een spraak configuratie object op de locatie en de naam van het logboek bestand. Logboek registratie wordt globaal geactiveerd zodra een herkenner is gemaakt op basis van die configuratie en kan later niet worden uitgeschakeld. U kunt de naam van een logboek bestand niet wijzigen tijdens een actieve logboek registratie sessie.

> [!NOTE]
> Logboek registratie is beschikbaar sinds Speech SDK versie 1.4.0 in alle ondersteunde spraak SDK-programmeer talen, met uitzonde ring van Java script.

## <a name="sample"></a>Voorbeeld

De naam van het logboek bestand is opgegeven voor een configuratie object. Als voor `SpeechConfig` beeld wordt aangenomen dat u een instantie hebt gemaakt met de naam `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

U kunt een herkenner maken op basis van het configuratie object. Hiermee wordt logboek registratie ingeschakeld voor alle kenmerken.

> [!NOTE]
> Als u een `SpeechSynthesizer` van het configuratie object maakt, wordt logboek registratie niet ingeschakeld. Als logboek registratie is ingeschakeld, ontvangt u ook diagnostische gegevens van de `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Een logboek bestand maken op verschillende platformen

Voor Windows of Linux kan het logboek bestand zich in elk pad bevinden waarvoor de gebruiker schrijf machtigingen heeft. Schrijf machtigingen voor bestandssysteem locaties in andere besturings systemen kunnen standaard worden beperkt of beperkt.

### <a name="universal-windows-platform-uwp"></a>Universal Windows Platform (UWP)

UWP-toepassingen moeten logboek bestanden op een van de toepassings gegevens locaties (lokaal, roaming of tijdelijk) worden geplaatst. Een logboek bestand kan worden gemaakt in de map van de lokale toepassing:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Meer informatie over bestands toegangs machtigingen voor UWP-toepassingen is [hier](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)beschikbaar.

### <a name="android"></a>Android

U kunt een logboek bestand opslaan in interne opslag, externe opslag of de cachemap. Bestanden die zijn gemaakt in de interne opslag of de cache directory zijn privé voor de toepassing. Het is raadzaam om een logboek bestand te maken in de externe opslag.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Met de bovenstaande code wordt een logboek bestand opgeslagen in de externe opslag in de hoofdmap van een toepassingsspecifiek Directory. Een gebruiker heeft toegang tot het bestand met de bestands beheerder (meestal `Android/data/ApplicationName/logfile.txt`in). Het bestand wordt verwijderd wanneer de installatie van de toepassing ongedaan wordt gemaakt.

U moet ook een machtiging `WRITE_EXTERNAL_STORAGE` aanvragen in het manifest bestand:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Meer informatie over gegevens en bestands opslag voor Android-toepassingen is [hier](https://developer.android.com/guide/topics/data/data-storage.html)beschikbaar.

#### <a name="ios"></a>iOS

Alleen mappen in de sandbox van de toepassing zijn toegankelijk. Bestanden kunnen worden gemaakt in de mappen documenten, bibliotheek en temp. Bestanden in de map documenten kunnen beschikbaar worden gesteld aan een gebruiker. Het volgende code fragment toont het maken van een logboek bestand in de map Application document:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Als u toegang wilt krijgen tot een gemaakt bestand, voegt u `Info.plist` de onderstaande eigenschappen toe aan de eigenschappen lijst van de toepassing:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Meer informatie over het iOS-bestands systeem is [hier](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html)beschikbaar.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
