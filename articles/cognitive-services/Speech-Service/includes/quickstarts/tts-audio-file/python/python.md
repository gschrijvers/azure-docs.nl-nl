---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 4429a03709d0f565d5fcf98f6999c812c0d43561
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673081"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](~/articles/cognitive-services/Speech-Service/get-started.md).
* [Python 3,5 tot 3,8](https://www.python.org/downloads/).
* Het Python Speech-SDK-pakket is beschikbaar voor deze besturingssystemen:
    * Windows: x64 en x86.
    * Mac: Mac OS X-versie 10.12 of hoger.
    * Linux: Ubuntu 16.04/18.04, Debian 9, RHEL 7/8, CentOS 7/8 op x64.
* Voer op Linux de volgende opdrachten uit om de vereiste pakketten te installeren:

# <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl python3
```

> [!NOTE]
> - Volg op RHEL/CentOS 7 de instructies voor het [configureren van RHEL/CentOS 7 voor Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Volg in RHEL/CentOS 8 de instructies voor het [configureren van openssl voor Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

* In Windows hebt u [micro soft Visual C++ Redistributable voor Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) voor uw platform nodig.

## <a name="install-the-speech-sdk"></a>De Speech-SDK installeren

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

Met deze opdracht installeert u het Python-pakket van [PyPI](https://pypi.org/) voor de Speech-SDK:

```Bash
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Ondersteuning en updates

Updates voor het Python Speech-SDK-pakket worden gedistribueerd via PyPI en aangekondigd in de [Releaseopmerkingen](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Als een nieuwe versie beschikbaar is, kunt u een update naar deze versie uitvoeren met behulp van de opdracht `pip install --upgrade azure-cognitiveservices-speech`.
U kunt controleren welke versie momenteel is geïnstalleerd door de variabele `azure.cognitiveservices.speech.__version__` te bekijken.

Zie [opties voor ondersteuning en hulp](~/articles/cognitive-services/Speech-Service/support.md) als er een probleem is of als er een functie ontbreekt.

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Een Python-toepassing maken dat de Speech-SDK gebruikt

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U kunt de [voorbeeldcode](#sample-code) uit deze quickstart kopiëren naar een `quickstart.py`-bronbestand en de voorbeeldcode uitvoeren in uw IDE of in de console:

```Bash
python quickstart.py
```

Of u kunt deze quickstart-zelfstudie downloaden als een [Jupyter](https://jupyter.org)-notebook uit de [opslagplaats met Speech-SDK-voorbeelden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) en de quickstart uitvoeren als een notitieblok.

### <a name="sample-code"></a>Voorbeeldcode

````python
import azure.cognitiveservices.speech as speechsdk

# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.audio.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>De Speech-SDK installeren en gebruiken met Visual Studio Code

1. Down load en installeer een 64-bits versie van [python](https://www.python.org/downloads/), 3,5 tot 3,8 op uw computer.
1. Download en installeer [Visual Studio Code](https://code.visualstudio.com/Download).
1. Open Visual Studio Code en installeer de Python-extensie. Selecteer **File**  >  **extensies voor bestands voorkeuren**  >  **Extensions** in het menu. Zoek naar **Python**.

   ![De Python-extensie installeren](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Maak een map om daar het project in op te slaan. Dat kunt u bijvoorbeeld doen met Windows Verkenner.
1. Selecteer in Visual Studio Code het pictogram **Bestand**. Open vervolgens de map die u hebt gemaakt.

   ![Een map openen](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Maak een nieuw Python-bronbestand, `speechsdk.py`, door het pictogram Nieuw bestand te selecteren.

   ![Een bestand maken](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Kopieer en plak de [Python-code](#sample-code) en sla deze op in het zojuist gemaakte bestand.
1. Voeg de gegevens van uw Speech Service-abonnement in.
1. Als er een Python-interpreter is geselecteerd, wordt deze onder aan het venster weergegeven, links van de statusbalk.
   Anders wordt er een lijst met beschikbare Python-interpreters geopend. Open het opdracht palet (<kbd>CTRL + SHIFT + P</kbd>) en voer **python in: Select interpreter**. Kies de juiste versie.
1. U kunt het Python Speech-SDK-pakket installeren vanuit Visual Studio Code. Doe dat als het pakket nog niet is geïnstalleerd voor de Python-interpreter die u hebt geselecteerd.
   Open een terminal voor het installeren van het Speech-SDK-pakket. Breng het opdracht palet opnieuw aan (<kbd>CTRL + SHIFT + P</kbd>) en voer de **volgende Terminal in: nieuwe geïntegreerde terminal maken**.
   Voer in de terminal die wordt geopend, de opdracht `python -m pip install azure-cognitiveservices-speech` in, of de juiste opdracht voor uw systeem.
1. Als u de voorbeeldcode wilt uitvoeren, klikt u met de rechtermuisknop ergens in de editor. Selecteer **Python-bestand uitvoeren in de Terminal**.
   Uw tekst wordt geconverteerd naar spraak en opgeslagen in de opgegeven audio gegevens.

   ```console
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

Als er zich problemen voordoen met deze instructies, raadpleegt u de uitgebreidere [Python-zelfstudie voor Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]

## <a name="see-also"></a>Zie ook

- [Een aangepaste stem maken](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Aangepaste spraak voorbeelden vastleggen](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
