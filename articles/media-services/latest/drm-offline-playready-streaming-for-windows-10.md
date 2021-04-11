---
title: Offline PlayReady streaming configureren
description: In dit artikel wordt uitgelegd hoe u uw Azure Media Services v3-account voor streaming PlayReady voor Windows 10 offline kunt configureren.
services: media-services
keywords: DASH, DRM, Widevine offline modus, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: f720549d0e9edba0865254d6427c19995d70fd8f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106069027"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady streaming voor Windows 10 met Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services offline downloaden/afspelen met DRM-beveiliging ondersteunen. In dit artikel wordt Inge gaan op offline ondersteuning van Azure Media Services voor Windows 10/PlayReady-clients. Raadpleeg de volgende artikelen voor meer informatie over de ondersteuning voor offline modus voor iOS-FairPlay en Android-Widevine-apparaten:

- [Offline FairPlay-streaming voor iOS](drm-offline-fairplay-for-ios-concept.md)
- [Offline Widevine streaming voor Android](drm-offline-widevine-for-android.md)

> [!NOTE]
> Offline DRM wordt alleen gefactureerd voor het maken van één aanvraag voor een licentie wanneer u de inhoud downloadt. Er worden geen fouten in rekening gebracht.

## <a name="background-on-offline-mode-playback"></a>Achtergrond bij afspelen in offline modus

Deze sectie bevat een achtergrond voor het afspelen van offline modus, met name waarom:

* In sommige landen/regio's is Internet Beschik baarheid en/of band breedte nog steeds beperkt. Gebruikers kunnen ervoor kiezen om eerst te downloaden om inhoud te kunnen bekijken met een hoge resolutie voor een goede weergave ervaring. In dit geval is het probleem vaak niet het netwerk beschikbaar, maar is de netwerk bandbreedte beperkt. OTT/OVP-providers vragen om ondersteuning voor de offline modus.
* Als vermeld bij de Netflix 2016 Q3-aandeel houder, is het downloaden van inhoud een ' oft-aangevraagde functie ', en ' We zijn er open mee ' zei door Reed Hastings, Netflix CEO.
* Sommige inhouds providers kunnen geen DRM-licentie levering toestaan buiten de rand van een land/regio. Als een gebruiker in het buiten land moet reizen en nog steeds inhoud wil bekijken, is offline downloaden vereist.
 
De uitdaging voor het implementeren van de offline modus is het volgende:

* MP4 wordt ondersteund door veel spelers, coderings Programma's, maar er is geen binding tussen MP4-container en DRM;
* Op de lange termijn is CFF met CENC de manier om te gaan. Momenteel is het ecosysteem van de hulpprogram ma's/speler echter nog niet aanwezig. We hebben vandaag een oplossing nodig.
 
Het idee is: smooth streaming-bestands indeling ([piff](/iis/media/smooth-streaming/protected-interoperable-file-format)) met H264/AAC heeft een binding met PLAYREADY (AES-128-afdeling). Het afzonderlijke smooth streaming. ismv-bestand (ervan uitgaande dat audio Muxed in video is), is zelf een fMP4 en kan worden gebruikt voor het afspelen. Als een smooth streaming inhoud via PlayReady-versleuteling, wordt elk. ismv-bestand een met PlayReady beschermde gefragmenteerde MP4. We kunnen een. ismv-bestand kiezen met de voorkeurs bitrate en de naam wijzigen als. MP4 voor downloaden.

Er zijn twee opties voor het hosten van de PlayReady beveiligde MP4 voor progressief downloaden:

* Eén kan deze MP4 in hetzelfde container/medium-service-activum plaatsen en Azure Media Services streaming-eind punt gebruiken voor progressieve down loads.
* Eén kan gebruikmaken van de SAS-Locator voor progressief downloaden rechtstreeks vanuit Azure Storage, waarbij Azure Media Services worden omzeild.
 
U kunt twee typen PlayReady-licentie levering gebruiken:

* PlayReady-service voor het leveren van licenties in Azure Media Services;
* PlayReady-licentie servers die overal worden gehost.

Hieronder vindt u twee sets test assets, de eerste met PlayReady-licentie levering in AMS, terwijl de tweede een mijn PlayReady-licentie server gebruikt die wordt gehost op een virtuele machine van Azure:

## <a name="asset-1"></a>Activa #1

* URL voor progressieve down load: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>Activa #2

* URL voor progressieve down load: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (on-premises): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

Voor het testen van afspelen hebben we een universele Windows-toepassing in Windows 10 gebruikt. In [Windows 10 Universal](https://github.com/Microsoft/Windows-universal-samples)-voor beelden is er een basis speler-voor beeld met de naam [adaptief stream](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming)-voor beeld. U hoeft alleen maar de code toe te voegen om de gedownloade video te selecteren en deze als bron te gebruiken in plaats van een adaptieve streaming-bron. De wijzigingen bevinden zich in de knop gebeurtenis-handler klikken:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Offline modus voor het afspelen van PlayReady beveiligde fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)

Omdat de video zich onder PlayReady-beveiliging bevindt, kan de scherm opname niet worden toegevoegd aan de video.

In samen vatting hebben we de offline modus op Azure Media Services bereikt:

* Versleuteling van inhoud en PlayReady kan worden uitgevoerd in Azure Media Services of andere hulpprogram ma's.
* Inhoud kan worden gehost in Azure Media Services of Azure Storage voor progressief downloaden.
* De levering van PlayReady-licenties kan van Azure Media Services of elders zijn.
* De voor bereide smooth streaming inhoud kan nog steeds worden gebruikt voor online streamen met behulp van een streepje of Smooth met PlayReady als DRM.
