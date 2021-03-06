---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 770e037641ac8fbf75989dc94b66fd1df0689e50
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81422013"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp" target="_blank">Installeer de Speech-SDK voor uw ontwikkel omgeving en maak een leeg voorbeeld<span class="docon docon-navigate-external x-hidden-focus"></span>project</a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Een LUIS-app maken voor de intentie herkenning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Uw project openen in Visual Studio

Open vervolgens uw project in Visual Studio.

1. Start Visual Studio 2019.
2. Laad uw project en open `helloworld.cpp`het.

## <a name="start-with-some-boilerplate-code"></a>Begin met een van de standaard code

Laten we een code toevoegen die als een skelet voor het project werkt. Houd er rekening mee dat u een asynchrone methode hebt `recognizeIntent()`gemaakt met de naam.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-16,72-80)]

## <a name="create-a-speech-configuration"></a>Een spraak configuratie maken

Voordat u een `IntentRecognizer` object kunt initialiseren, moet u een configuratie maken die de sleutel en locatie voor uw Luis-Voorspellings bron gebruikt.

> [!IMPORTANT]
> Uw start sleutel-en ontwerp sleutels werken niet. U moet uw Voorspellings sleutel en-locatie gebruiken die u eerder hebt gemaakt. Zie voor meer informatie [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

Voeg deze code in de `recognizeIntent()` methode in. Zorg ervoor dat u deze waarden bijwerkt:

* Vervang `"YourLanguageUnderstandingSubscriptionKey"` door uw Luis-Voorspellings sleutel.
* Vervang `"YourLanguageUnderstandingServiceRegion"` door uw Luis-locatie.  Gebruik de **regio-id** uit de [regio](https://aka.ms/speech/sdkregion).

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarden, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=25)]

In dit voor beeld `FromSubscription()` wordt de methode gebruikt `SpeechConfig`om de te bouwen. Zie [SpeechConfig-klasse](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig)voor een volledige lijst met beschik bare methoden.

De spraak-SDK wordt standaard herkend door en-US voor de taal. Zie de [bron taal voor spraak opgeven](../../../../how-to-specify-source-language.md) voor de tekst voor informatie over het kiezen van de bron taal.

## <a name="initialize-an-intentrecognizer"></a>Een IntentRecognizer initialiseren

Nu gaan we een `IntentRecognizer`maken. Voeg deze code toe in `recognizeIntent()` de-methode, rechts onder uw spraak configuratie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=28)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Een LanguageUnderstandingModel en intenties toevoegen

U moet een `LanguageUnderstandingModel` met de intentie herkenning koppelen en de intenties die u wilt herkennen, toevoegen. We gaan de intenties gebruiken van het vooraf ontwikkelde domein voor Start Automation.

Voeg deze code toe onder `IntentRecognizer`uw. Zorg ervoor dat u vervangt `"YourLanguageUnderstandingAppId"` door de id van uw Luis-app.

>[!TIP]
> Als u hulp nodig hebt bij het vinden van deze waarde, raadpleegt u [een Luis-app maken voor de intentie herkenning](#create-a-luis-app-for-intent-recognition).

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=31-33)]

## <a name="recognize-an-intent"></a>Een intentie herkennen

Vanuit het `IntentRecognizer` object roept u de `RecognizeOnceAsync()` -methode aan. Met deze methode kan de speech-service weten dat u één woord groep verstuurt voor herkenning en dat zodra de woord groep is geïdentificeerd om te stoppen met het herkennen van spraak. Voor de eenvoud zullen we wachten op het volt ooien van de toekomst.

Voeg deze code toe onder uw model:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=43)]

## <a name="display-the-recognition-results-or-errors"></a>De herkennings resultaten (of fouten) weer geven

Wanneer het herkennings resultaat wordt geretourneerd door de spraak service, wilt u er iets mee doen. We gaan het eenvoudig opslaan en het resultaat afdrukken naar de console.

Voeg hieronder de volgende `auto result = recognizer->RecognizeOnceAsync().get();`code toe:

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=46-71)]

## <a name="check-your-code"></a>Controleer uw code

Op dit moment moet uw code er als volgt uitzien:

> [!NOTE]
> Er zijn enkele opmerkingen toegevoegd aan deze versie.

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/intent-recognition/helloworld/helloworld.cpp?range=6-79)]

## <a name="build-and-run-your-app"></a>Uw app bouwen en uitvoeren

Nu bent u klaar om uw app te bouwen en de spraak herkenning te testen met behulp van de speech-service.

1. **De code compileren** **: Kies** > build**Build Solution**in de menu balk van Visual Studio.
2. **Start uw app** -vanuit de menu balk, kies **fout** > **opsporing starten** of druk op <kbd>F5</kbd>.
3. **Herkenning starten** : u wordt gevraagd om een woord groep in het Engels te spreken. Uw spraak wordt verzonden naar de spraak service, getranscribeerd als tekst en weer gegeven in de-console.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
