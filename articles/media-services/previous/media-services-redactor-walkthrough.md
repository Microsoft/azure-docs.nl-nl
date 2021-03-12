---
title: Gezichten met Azure Media Analytics-Overzicht redigeren | Microsoft Docs
description: In dit onderwerp vindt u stapsgewijze instructies voor het uitvoeren van een volledige redactie werk stroom met behulp van Azure Media Services Explorer (AMSE) en Azure Media Redactor Visualer (open source-hulp programma).
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: 1a7e20681dfa7da7ce30f46a7c4b0b6df6f78916
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009552"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Gezichten met Azure Media Analytics-Overzicht redigeren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

**Azure media Redactor** is een [Azure Media Analytics](./legacy-components.md) media processor (MP) dat schaal bare gezichts redactie in de Cloud biedt. Met gezichts redactie kunt u uw video aanpassen zodat u de gezichten van geselecteerde personen kunt vervagen. U kunt de gezichts redactie service gebruiken in de scenario's voor open bare veiligheid en nieuws media. Een paar minuten van beeld materiaal dat meerdere gezichten bevat, kan uren duren om hand matig te worden geredigeerd, maar bij deze service zijn slechts enkele eenvoudige stappen vereist voor het redactie proces van het gezicht. Zie [deze](https://azure.microsoft.com/blog/azure-media-redactor/) blog voor meer informatie.

Zie het onderwerp [overzicht van gezichts redactie](media-services-face-redaction.md) voor meer informatie over **Azure media Redactor**.

In dit onderwerp vindt u stapsgewijze instructies voor het uitvoeren van een volledige redactie werk stroom met behulp van Azure Media Services Explorer (AMSE) en Azure Media Redactor Visualer (open source-hulp programma).

Zie [deze](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog voor meer informatie.

## <a name="azure-media-services-explorer-workflow"></a>Werk stroom van Azure Media Services Explorer

De eenvoudigste manier om aan de slag te gaan met Redactor is het gebruik van het open source AMSE-hulp programma op GitHub. U kunt een vereenvoudigde werk stroom uitvoeren via de **gecombineerde** modus als u geen toegang nodig hebt tot de JSON van de annotatie of het gezichts-jpg-afbeeldings bestand.

### <a name="download-and-setup"></a>Downloaden en instellen

1. Down load [hier](https://aka.ms/amseforv2)het AMSE for AMS v2-hulp programma.
1. Meld u aan bij uw Media Services-account met behulp van uw service sleutel.

    Voor het verkrijgen van de accountnaam en sleutelinformatie gaat u naar de [Azure Portal](https://portal.azure.com/) en selecteert u uw AMS-account. Selecteer vervolgens instellingen > sleutels. In het venster Sleutels beheren worden de accountnaam en de primaire en secundaire sleutel weergegeven. Kopieer de waarden van de accountnaam en de primaire sleutel.

![Scherm afbeelding toont Microsoft Azure Media Services waar u de naam en sleutel van uw account kunt invoeren.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Eerste fase – analyse modus

1. Upload uw media bestand via Asset-> upload of via slepen en neerzetten. 
1. Klik met de rechter muisknop en werk uw media bestand met Media Analytics – > Azure Media Redactor – > analyse modus. 


![Scherm afbeelding toont een menu met een werkend item (s) met Azure Media Redactor.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Scherm opname toont Azure Media Redactor met de eerste fase: de analyse modus is geselecteerd.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

De uitvoer bevat een consistentie bestand met de locatie gegevens van het Opper vlak en een jpg van elk gedetecteerd gezicht. 

![Scherm afbeelding toont de uitvoer van de analyse.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Tweede fase – redactie modus

1. Upload uw oorspronkelijke video-activum naar de uitvoer van de eerste fase en stel deze in als een primaire Asset. 

    ![Scherm afbeelding toont de knoppen uploaden en instellen als primaire.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. Beschrijving Upload een Dance_idlist.txt-bestand met een door een nieuwe regel gescheiden lijst met de Id's die u wilt redigeren. 

    ![Scherm afbeelding toont de optie voor het uploaden van het tekst bestand.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. Beschrijving Breng wijzigingen aan in het annotations.jsop bestand, zoals het verg Roten van de grenzen van het selectie kader. 
4. Klik met de rechter muisknop op het uitvoer activum van de eerste stap, selecteer de Redactor en voer uit met de modus **redactie** . 

    ![Scherm opname toont Azure Media Redactor met de tweede fase: redactie modus geselecteerd.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Down load of deel de laatste geredigeerde uitvoer activa. 

    ![Scherm afbeelding toont de knop downloaden.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Hulp programma voor Azure Media Redactor visualiseren open source

Een open source [visualer-hulp programma](https://github.com/Microsoft/azure-media-redactor-visualizer) is ontworpen om ontwikkel aars te helpen bij het starten van de aantekeningen-indeling met het parseren en gebruiken van de uitvoer.

Nadat u de opslag plaats hebt gekloond, moet u FFMPEG van de [officiële site](https://ffmpeg.org/download.html)downloaden om het project uit te voeren.

Als u een ontwikkelaar bent die de gegevens van de JSON-aantekening probeert te parseren, kijkt u in modellen. meta gegevens voor voor beelden van voorbeeld code.

### <a name="set-up-the-tool"></a>Het hulp programma instellen

1.  Down load en bouw de volledige oplossing. 

    ![Scherm opname toont de bouw oplossing die u hebt geselecteerd in het menu.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Down load FFMPEG van [hier](https://ffmpeg.org/download.html). Dit project is oorspronkelijk ontwikkeld met versie be1d324 (2016-10-04) met statische koppeling. 
3.  Kopieer ffmpeg.exe en ffprobe.exe naar dezelfde uitvoermap als AzureMediaRedactor.exe. 

    ![Scherm afbeelding toont de inhoud van de map, waaronder ffmpeg en ffprobe.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Voer AzureMediaRedactor.exe uit. 

### <a name="use-the-tool"></a>Het hulp programma gebruiken

1. Verwerk uw video in uw Azure Media Services-account met het MP Redactor in de analyse modus. 
2. Down load zowel het oorspronkelijke video bestand als de uitvoer van de taak redactie-analyseren. 
3. Voer de toepassing visualer uit en kies de bovenstaande bestanden. 

    ![Scherm afbeelding toont Azure Media Redactor het uploaden van bestanden.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Bekijk een voor beeld van het bestand. Selecteer de gezichten die u wilt vervagen via de zijbalk aan de rechter kant. 
    
    ![Scherm afbeelding toont Azure Media Redactor waar u kunt bekijken en gezichten selecteert om te vervagen.](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Het onderste tekst veld wordt bijgewerkt met de face-Id's. Maak een bestand met de naam ' idlist.txt ' met deze Id's als een lijst met door een nieuwe regel gescheiden waarden. 

    >[!NOTE]
    > Het idlist.txt moet worden opgeslagen in ANSI. U kunt Klad blok gebruiken om op te slaan in ANSI.
    
6.  Upload dit bestand uit stap 1 naar het uitvoer activum. Upload de oorspronkelijke video naar dit activum en stel deze in als primair activum. 
7.  Een redactie taak uitvoeren op deze asset met de modus ' redigeren ' om de uiteindelijke, geredigeerde video te verkrijgen. 

## <a name="next-steps"></a>Volgende stappen 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwante koppelingen
[Overzicht van Azure Media Services Analytics](./legacy-components.md)

[Demo's Azure Media Analytics](http://amslabs.azurewebsites.net/demos/Analytics.html)

[Aangekondigd gezichts redactie voor Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)