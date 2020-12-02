---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187553"
---
1. Start Android Studio en selecteer **Een nieuw Android Studio-project starten** in het **welkomstscherm**.

    ![Schermafbeelding van het welkomstscherm van Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. De wizard **Uw project kiezen** wordt weergegeven. Selecteer **Telefoon en tablet** en **Lege activiteit** in het selectievak voor de activiteit. Selecteer **Next**.

   ![Schermopname van de wizard Uw project kiezen](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Voer op het scherm **Uw project configureren** *Quickstart* in als de **Naam** en voer *samples.speech.cognitiveservices.microsoft.com* in als **Pakketnaam**. Selecteer vervolgens een projectmap. Selecteer als **Minimaal API-niveau** de optie **API 23: Android 6.0 (Marshmallow)** . Laat alle andere selectievakjes leeg en selecteer **Voltooien**.

   ![Schermopname van de wizard Uw project configureren](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio heeft even de tijd nodig om het nieuwe Android-project voor te bereiden. Configureer het project vervolgens voor gebruik van de Azure Cognitive Services Speech SDK en Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

De huidige versie van de Speech SDK van Cognitive Services is 1.13.0.

De Speech SDK voor Android wordt aangeboden als een [AAR (Android Library)](https://developer.android.com/studio/projects/android-library) die de vereiste bibliotheken en Android-machtigingen bevat.
Deze wordt gehost in een Maven-opslagplaats op https:\//csspeechstorage.blob.core.vensters.net/maven/.

Stel uw project in voor gebruik van de Speech SDK. Open het venster **Projectstructuur** door **Bestand** > **Projectstructuur** te kiezen in de menubalk van Android Studio. Breng in het venster **Projectstructuur** de volgende wijzigingen aan:

1. Selecteer **Project** in de lijst aan de linkerkant van het venster. Bewerk de instellingen voor **Default Library Repository** door een komma toe te voegen en vervolgens de URL van onze Maven-opslagplaats tussen enkele aanhalingstekens te plaatsen: 'https:\//csspeechstorage.blob.core.vensters.net/maven/'

   ![Schermafbeelding van het venster Project Structure](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Selecteer in hetzelfde scherm aan de linkerkant **app**. Selecteer vervolgens bovenaan het venster het tabblad **Dependencies**. Selecteer het groene plusteken ( **+** ) en selecteer **Afhankelijkheid van bibliotheek** in de vervolgkeuzelijst.

   ![Schermopname van Afhankelijkheid van bibliotheek](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Voer in het venster dat wordt weergegeven de naam en versie van de Speech SDK voor Android in: *com.microsoft.cognitiveservices.speech:client-sdk:1.13.0*. Selecteer vervolgens **OK**.
   De Speech SDK moet nu worden toegevoegd aan de lijst met afhankelijkheden, zoals wordt weergegeven:

   ![Schermopname van Speech SDK in de lijst met afhankelijkheden](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecteer het tabblad **Properties**. Selecteer voor zowel **Broncompatibiliteit** als **Doelcompatibiliteit** versie **1.9**.

   ![Schermopname van Broncompatibiliteit en Doelcompatibiliteit](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecteer **OK** om het venster **Projectstructuur** te sluiten en wijzigingen toe te passen op het project.
