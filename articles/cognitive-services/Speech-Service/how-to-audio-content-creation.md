---
title: Audio content creation-Speech Service
titleSuffix: Azure Cognitive Services
description: Het maken van audio-inhoud is een online hulp programma waarmee u de tekst-naar-spraak-uitvoer van micro soft kunt aanpassen en verfijnen voor uw apps en producten.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 7d4d1acac591bfe6ce40efcf6166357d43718363
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024956"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>De synthese verbeteren met het hulp programma voor het maken van een audio-inhoud

Het [maken van audio-inhoud](https://aka.ms/audiocontentcreation) is een eenvoudig te gebruiken en krachtig hulp programma waarmee u uiterst natuurlijke audio-inhoud kunt bouwen voor verschillende scenario's, zoals audiobooks, nieuws uitzendingen, video-gesp roken tekst en chat bots. Met het maken van audio-inhoud kunt u tekst-naar-spraak stemmen nauw keurig afstemmen en aangepaste audio-ervaringen ontwerpen op een efficiënte en goedkope manier.

Het hulp programma is gebaseerd op de [SSML (Speech synthese Markup Language)](speech-synthesis-markup.md). Hiermee kunt u de uitvoer kenmerken van tekst naar spraak in realtime of batch synthese aanpassen, zoals spraak tekens, gesp roken stijlen, spreek snelheid, uitspraak en prosody.

U kunt eenvoudig toegang hebben tot meer dan 150 vooraf gemaakte stemmen over dicht bij 50 verschillende talen, waaronder de geavanceerde Neural TTS-stemmen en uw aangepaste stem als u er een hebt gemaakt. 

Zie de [video zelf studie](https://www.youtube.com/watch?v=O1wIJ7mts_w) voor het maken van audio-inhoud.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag?

Het maken van audio-inhoud is een gratis hulp programma, maar u betaalt voor de Azure speech-service die u gebruikt. Als u met het hulp programma wilt werken, moet u zich aanmelden met een Azure-account en een spraak resource maken. Voor elk Azure-account hebt u maandelijkse gratis spraak quota's die 500.000 tekens bevatten voor Neural TTS-stemmen (per maand), 5.000.000 tekens voor standaard-en aangepaste stemmen (per maand) en één aangepaste Voice endpoint-hosting service (per maand). De maandelijks toegewezen hoeveelheid is doorgaans voldoende voor een klein inhouds team van ongeveer 3-5 mensen. Hier volgen de stappen voor het maken van een Azure-account en het ophalen van een spraak bron. 

### <a name="step-1---create-an-azure-account"></a>Stap 1: een Azure-account maken

Als u wilt werken met het maken van audio-inhoud, moet u een [Microsoft-account](https://account.microsoft.com/account) en een [Azure-account](https://azure.microsoft.com/free/ai/)hebben. Volg deze instructies om [het account](./overview.md#try-the-speech-service-for-free)in te stellen. 

[Azure Portal](https://portal.azure.com/) is de centrale plaats waar u uw Azure-account kunt beheren. U kunt de spraak resource maken, de product toegang beheren en alles bewaken van eenvoudige web-apps tot complexe Cloud implementaties. 

### <a name="step-2---create-a-speech-resource"></a>Stap 2: een spraak bron maken

Nadat u zich hebt aangemeld voor het Azure-account, moet u een spraak bron maken onder uw Azure-account om toegang te krijgen tot spraak Services. Bekijk de instructies voor het [maken van een spraak resource](./overview.md#create-the-azure-resource). 

Het kan even duren voordat u uw nieuwe spraakresource is geïmplementeerd. Zodra de implementatie is voltooid, kunt u de traject voor het maken van de audio-inhoud starten. 

 >[!NOTE]
   > Als u van plan bent Neural stemmen te gebruiken, moet u ervoor zorgen dat u uw resource maakt in [een regio die Neural stemmen ondersteunt](regions.md#standard-and-neural-voices).
 
### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Stap 3: Meld u aan bij het maken van de audio-inhoud met uw Azure-account en spraak resource

1. Nadat u het Azure-account en de spraak resource hebt opgehaald, kunt u zich aanmelden bij het [maken van audio-inhoud](https://aka.ms/audiocontentcreation) door te klikken op aan de **slag**.
2. De pagina met **spraak bronnen** wordt weer gegeven. Selecteer de spraak resource waarmee u wilt werken. Klik op **Go to Studio** om te beginnen met het maken van audio. U kunt hier ook een nieuwe spraak bron maken door te klikken op **Nieuw maken**. Wanneer u zich voor de volgende keer aanmeldt bij het hulp programma voor het maken van audio-inhoud, wordt u rechtstreeks naar de audio-werk bestanden in de huidige spraak resource gekoppeld. 
3. U kunt uw spraak resource op elk gewenst moment wijzigen met de optie **instellingen** , die zich in de bovenste navigatie bevindt.

## <a name="how-to-use-the-tool"></a>Hoe gebruikt u het hulp programma?

Dit diagram toont de stappen die nodig zijn om tekst-naar-spraak-uitvoer te verfijnen. Gebruik de onderstaande koppelingen voor meer informatie over elke stap.

:::image source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Een diagram van de stappen die nodig zijn om tekst-naar-spraak-uitvoer te verfijnen":::


1. Kies de spraak resource waarmee u wilt werken.
2. [Maak een audio-afstemmings bestand](#create-an-audio-tuning-file) met behulp van onbewerkte tekst of SSML-scripts. Typ of upload uw inhoud in om audio content te maken.
3. Kies de stem en de taal voor de script inhoud. Voor het maken van audio-inhoud [zijn alle micro soft tekst-naar-spraak-stemmen](language-support.md#text-to-speech)opgenomen. U kunt standaard, Neural of uw eigen aangepaste stem gebruiken.
   >[!NOTE]
   > Toegang voor gestuurde Neural is beschikbaar voor aangepaste stemmen op het niveau van een hoge definitie die vergelijkbaar is met spraak herkenning. Zie [beperking process](./text-to-speech.md)(Engelstalig) voor meer informatie.

4. Klik op het **afspeel** pictogram (een drie hoek) om de standaard-synthese uitvoer te bekijken. Verbeter vervolgens de uitvoer door de uitspraak, het afbreek teken, de hoogte, de rente, de intonation, de stem stijl en meer te wijzigen. Zie voor een volledige lijst met opties [spraak synthese Markup Language (Engelstalig](speech-synthesis-markup.md)). Hier volgt een [video](https://www.youtube.com/watch?v=O1wIJ7mts_w) om te laten zien hoe u spraak uitvoer nauw keuriger kunt afstemmen met het maken van audio-inhoud. 
5. [Uw afgestemde audio](#export-tuned-audio)opslaan en exporteren. Wanneer u de afstemmings track opslaat in het systeem, kunt u blijven werken en de uitvoer herhalen. Wanneer u tevreden bent met de uitvoer, kunt u een taak voor het maken van een audio maken met de export functie. U kunt de status van de export taak bekijken en de uitvoer downloaden voor gebruik met uw apps en producten.

## <a name="create-an-audio-tuning-file"></a>Een audio-afstemmings bestand maken

Er zijn twee manieren om uw inhoud te verkrijgen in het hulp programma voor het maken van audio-inhoud.

**Optie 1:**

1. Klik op het pictogram **nieuw bestand** in de rechter bovenhoek om een nieuw audio-afstemmings bestand te maken.
2. Typ of plak de inhoud in het bewerkings venster. De tekens voor elk bestand zijn Maxi maal 20.000. Als uw script langer is dan 20.000 tekens, kunt u optie 2 gebruiken om uw inhoud automatisch te splitsen in meerdere bestanden. 
3. Vergeet niet om op te slaan.

**Optie 2:**

1. Klik op **uploaden** om een of meer tekst bestanden te importeren. Zowel tekst zonder opmaak als SSML worden ondersteund. Als uw script bestand meer dan 20.000 tekens bevat, splitst u het bestand op alinea's, op teken of op reguliere expressies. 
3. Wanneer u uw tekst bestanden uploadt, moet u ervoor zorgen dat het bestand voldoet aan deze vereisten.

   | Eigenschap | Waarde/opmerkingen |
   |----------|---------------|
   | Bestandsindeling | Tekst zonder opmaak (. txt)<br/> SSML tekst (. txt)<br/> Zip-bestanden worden niet ondersteund |
   | Coderings indeling | UTF-8 |
   | Bestandsnaam | Elk bestand moet een unieke naam hebben. Dubbele waarden worden niet ondersteund. |
   | Tekst lengte | Teken beperking van het tekst bestand is 20.000. Als uw bestanden de beperking overschrijden, kunt u de bestanden splitsen met de instructies in het hulp programma. |
   | SSML-beperkingen | Elk SSML-bestand kan slechts één SSML bevatten. |

**Voor beeld van tekst zonder opmaak**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```
**Voor beeld van SSML-tekst**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Afgestemde audio exporteren

Nadat u de audio-uitvoer hebt gecontroleerd en tevreden bent met uw afstemming en aanpassing, kunt u de audio exporteren.

1. Klik op **exporteren** om een taak voor het maken van een audio te maken. **Exporteren naar audio bibliotheek** wordt aanbevolen omdat het de lange audio-uitvoer en de volledige audio-uitvoer ondersteunt. U kunt de audio ook rechtstreeks downloaden naar uw lokale schijf, maar alleen de eerste tien minuten zijn beschikbaar.
2. Kies de uitvoer indeling voor uw afgestemde audio. Hieronder vindt u een lijst met ondersteunde indelingen en voorbeeld tarieven.
3. U kunt de status van de taak weer geven op het tabblad **taak exporteren** . Als de taak mislukt, raadpleegt u de pagina gedetailleerde informatie voor een volledig rapport.
4. Wanneer de taak is voltooid, kunt u de audio downloaden van het tabblad **audio bibliotheek** .
5. Klik op **Downloaden**. U bent nu klaar om uw aangepaste afgestemde audio in uw apps of producten te gebruiken.

**Ondersteunde audio-indelingen**

| Indeling | sample frequentie van 16 kHz | 24 kHz sample frequentie |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16-mono-PCM | riff-24khz-16-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-mp3 | Audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Hoe kan ik gebruikers voor het maken van audio-inhoud toevoegen/verwijderen?

Als meer dan één gebruiker het maken van audio-inhoud wil gebruiken, kunt u uw Azure-account en wacht woord delen met de gebruiker of gebruikers toegang verlenen tot het Azure-abonnement en de spraak resource. Als u een gebruiker aan een Azure-abonnement toevoegt, heeft de gebruiker toegang tot alle resources onder het Azure-abonnement. Maar als u alleen een gebruiker toevoegt aan een spraak bron, heeft de gebruiker alleen toegang tot de spraak bron en heeft deze geen toegang tot andere bronnen onder dit Azure-abonnement. Een gebruiker met toegang tot de spraak resource kan audio-inhoud maken gebruiken.

### <a name="add-users-to-a-speech-resource"></a>Gebruikers toevoegen aan een spraak resource

Volg deze stappen om een gebruiker toe te voegen aan een spraak bron, zodat deze audio-inhoud kan maken.

1. Zoek naar **cognitieve Services** in het [Azure Portal](https://portal.azure.com/), selecteer de spraak resource waaraan u gebruikers wilt toevoegen.
2. Klik op **Toegangsbeheer (IAM)** . Klik op het tabblad **Roltoewijzingen** om alle roltoewijzingen voor dit abonnement weer te geven.
    :::image source="media/audio-content-creation/access-control-roles.png" alt-text="Tabblad roltoewijzing":::
1. Klik op **Toevoegen** > **Roltoewijzing toevoegen** om het deelvenster Roltoewijzing toevoegen te openen. Selecteer in de vervolg keuzelijst rol de gebruikersrol **cognitieve service** . Als u de gebruiker eigenaar van deze spraak bron wilt worden, kunt u de rol **eigenaar** selecteren.
1. Selecteer een gebruiker in de lijst. Als de gebruiker niet in de lijst wordt weer gegeven, kunt u in het selectie vakje typen om in de map te zoeken naar namen en e-mail adressen. Als de gebruiker zich niet in deze map bevindt, kunt u de [Microsoft-account](https://account.microsoft.com/account) van de gebruiker (die wordt vertrouwd door Azure Active Directory) invoeren.
1. Klik op **Opslaan** om de rol toe te wijzen. Na enkele ogen blikken is de gebruiker de rol van de cognitieve service toegewezen aan het spraak bron bereik.

    :::image source="media/audio-content-creation/add-role-first.png" alt-text="Dialoog venster rol toevoegen":::

1. De gebruikers die u toevoegt, ontvangen een uitnodigings-e-mail. Nadat deze zijn geklikt op **uitnodiging accepteren**  >  **accepteren om lid te worden van Azure**, kunnen ze [audio-inhoud maken](https://aka.ms/audiocontentcreation)gebruiken.

Gebruikers die zich in dezelfde spraak resource bevinden, zien elkaars werk in het maken van audio Contents Studio. Als u wilt dat elke afzonderlijke gebruiker een unieke en particuliere werk plek heeft bij het maken van audio-inhoud, moet u voor elke gebruiker [een nieuwe spraak bron maken](#step-2---create-a-speech-resource) en elke gebruiker de unieke toegang geven tot de spraak resource. 

### <a name="remove-users-from-a-speech-resource"></a>Gebruikers uit een spraak resource verwijderen
1. Zoek naar **cognitieve Services** in het Azure Portal, selecteer de spraak bron waarvan u gebruikers wilt verwijderen.
2. Klik op **Toegangsbeheer (IAM)** . Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen voor deze spraak resource weer te geven.
3. Selecteer de gebruikers die u wilt verwijderen en klik op **verwijderen**  >  **OK**.
    :::image source="media/audio-content-creation/remove-user.png" alt-text="Knop verwijderen":::

### <a name="enable-users-to-grant-access"></a>Gebruikers in staat stellen om toegang te verlenen
Als u een van de gebruikers toegang wilt geven tot andere gebruikers, moet u de gebruiker de rol van eigenaar van de spraak resource geven en de gebruiker instellen als de Azure Directory-lezer. 
1. Voeg de gebruiker toe als de eigenaar van de spraak resource. Zie [gebruikers toevoegen aan een spraak bron](#add-users-to-a-speech-resource).
    :::image source="media/audio-content-creation/add-role.png" alt-text="Veld eigenaar van rol":::
1. Selecteer het samengevouwen menu in de linkerbovenhoek. Klik op **Azure Active Directory** en klik vervolgens op **gebruikers**.
1. Zoek de Microsoft-account van de gebruiker en ga naar de detail pagina van de gebruiker. Klik op **toegewezen rollen**.
1. Klik op **toewijzingen**  ->  **Directory-lezers** toevoegen.

## <a name="see-also"></a>Zie ook

* [Lange audio-API](./long-audio-api.md)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
