---
title: 'Snelstartgids: een spraak assistent maken met aangepaste opdrachten'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start maakt en test u een basis toepassing voor aangepaste opdrachten met behulp van speech Studio.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: ea32358aa86a6093cd72adf959d5fe731cf1e125
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786873"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Een spraakassistent maken met aangepaste opdrachten

In deze Quick Start maakt en test u een basis toepassing voor aangepaste opdrachten met behulp van speech Studio. U kunt deze toepassing ook openen vanuit een Windows-client-app.

## <a name="region-availability"></a>Beschik baarheid van regio
Op dit moment ondersteunt aangepaste opdrachten spraak abonnementen die in deze regio's worden gemaakt:
* VS - west
* VS - west 2
* VS - oost
* VS - oost 2
* VS - west-centraal
* Europa - noord
* Europa -west
* Azië - oost
* Azië - zuidoost
* India - centraal

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Maak een Azure-spraak bron in een regio die aangepaste opdrachten ondersteunt.<span class="docon docon-navigate-external x-hidden-focus"></span></a> Raadpleeg de sectie **Beschik baarheid van regio's** hierboven voor een lijst met ondersteunde regio's.
> * Down load het voor beeld van een [Smart room Lite](https://aka.ms/speech/cc-quickstart) JSON-bestand.
> * Down load de nieuwste versie van de [Windows Voice Assistant-client](https://aka.ms/speech/va-samples-wvac).

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ga naar de speech Studio voor aangepaste opdrachten

1. Ga in een webbrowser naar [Speech Studio](https://speech.microsoft.com/).
1. Voer uw referenties in om u aan te melden bij de portal.

   De standaard weergave is uw lijst met spraak abonnementen.
   > [!NOTE]
   > Als u de pagina abonnement selecteren niet ziet, kunt u daar navigeren door te klikken op spraak bronnen in het menu instellingen op de bovenste balk.

1. Selecteer uw spraak abonnement en selecteer vervolgens **Go to Studio**.
1. Selecteer **aangepaste opdrachten**.

   De standaard weergave is een lijst met de aangepaste opdrachten voor toepassingen die u onder het geselecteerde abonnement hebt.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Een bestaande toepassing importeren als een nieuw project met aangepaste opdrachten

1. Selecteer **Nieuw project** om een project te maken.

1. Voer in het vak **naam** de project naam in `Smart-Room-Lite` (of iets anders van uw keuze).
1. Selecteer in de lijst **taal** de optie **Engels (Verenigde Staten)**.
1. Selecteer **Bladeren in bestanden** en selecteer in het venster bladeren de **SmartRoomLite.jsin** het bestand.

    > [!div class="mx-imgBorder"]
    > ![Een project maken](media/custom-commands/import-project.png)

1.  Selecteer in de lijst **Luis authoring resource** een authoring resource. Als er geen geldige ontwerp resources zijn, maakt u er een door  **nieuwe Luis maken** te selecteren.

    > [!div class="mx-imgBorder"]
    > ![Een resource maken](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Voer in het vak **resource naam** de naam van de resource in.
    1. Selecteer een resource groep in de lijst **resource groep** .
    1. Selecteer een locatie in de lijst **locatie** .
    1. Selecteer een laag in de lijst **prijs categorie** .
    
    
    > [!NOTE]
    > U kunt resource groepen maken door de naam van de gewenste resource groep in te voeren in het veld Resource groep. De resource groep wordt gemaakt wanneer **maken** is geselecteerd.


1. Selecteer vervolgens **maken** om het project te maken.
1. Nadat het project is gemaakt, selecteert u uw project.
U ziet nu overzicht van de nieuwe toepassing aangepaste opdrachten.

## <a name="try-out-some-voice-commands"></a>Probeer een aantal spraak opdrachten uit
1. Selecteer **trein** bovenaan in het rechterdeel venster.
1. Zodra de training is voltooid, selecteert u **testen** en voert u de volgende uitingen uit:
    - De TV inschakelen
    - De Tempe ratuur instellen op 80 graden
    - Uitschakelen
    - De TV
    - Een alarm instellen voor 5 PM

## <a name="integrate-custom-commands-application-in-an-assistant"></a>De toepassing aangepaste opdrachten integreren in een assistent
Voordat u toegang tot deze toepassing kunt krijgen vanuit een andere locatie dan speech Studio, moet u de toepassing publiceren. Voor het publiceren van een toepassing moet u de voor spelling LUIS-bron configureren.  

### <a name="update-prediction-luis-resource"></a>LUIS-resource voor voor spelling bijwerken


1. Selecteer **instellingen** in het linkerdeel venster en selecteer  **Luis resources** in het middelste deel venster.
1. Selecteer een Voorspellings bron of maak een resource door **nieuwe resource maken** te selecteren.
1. Selecteer **Opslaan**.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-resources instellen](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Omdat de bewerkings resource slechts 1.000 Voorspellings eindpunt aanvragen per maand ondersteunt, moet u een LUIS-Voorspellings bron instellen voordat u uw aangepaste opdrachten mandatorily publiceert.

### <a name="publish-the-application"></a>De toepassing publiceren

Selecteer  **publiceren** boven in het rechterdeel venster. Zodra het publiceren is voltooid, wordt er een nieuw venster weer gegeven. Noteer de waarde van de **toepassings-id** en de **spraak bron sleutel** . U hebt deze twee waarden nodig om toegang te krijgen tot de toepassing vanuit de buitenste speech Studio.

U kunt deze waarden ook ophalen door **instellingen**  >  **Algemeen** te selecteren.

### <a name="access-application-from-client"></a>Toepassing openen vanaf client

In het bereik van dit artikel gebruiken we de Windows Voice Assistant-client die u hebt gedownload als onderdeel van de vereisten. Pak de map uit.
1. Start **VoiceAssistantClient.exe**.
1. Maak een nieuw publicatie profiel en voer een waarde in voor het **verbindings profiel**. Voer in het gedeelte **algemene instellingen** de **abonnements sleutel** values in (dit is hetzelfde als de waarde voor de **spraak bron sleutel** die u hebt opgeslagen bij het publiceren van de toepassing), de **abonnements sleutel regio** en de **App-ID van de aangepaste opdrachten**.
    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding die de sectie Algemene instellingen voor het maken van een WVAC-profiel markeert.](media/custom-commands/create-profile.png)
1. Selecteer **opslaan en profiel Toep assen**.
1. Probeer nu de volgende invoer via spraak/tekst
    > [!div class="mx-imgBorder"]
    > ![WVAC maken](media/custom-commands/conversation.png)


> [!TIP]
> U kunt op vermeldingen in het **activiteiten logboek** klikken om de onbewerkte antwoorden te controleren die worden verzonden vanuit de Custom commands-service.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een bestaande toepassing gebruikt. In de sectie met [procedures](./how-to-develop-custom-commands-application.md)leert u hoe u een aangepaste opdrachten van een nieuwe toepassing kunt ontwerpen, ontwikkelen, opsporen, testen en integreren.