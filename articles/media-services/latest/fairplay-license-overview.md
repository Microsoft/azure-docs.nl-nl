---
title: Media Services Apple FairPlay-licentie ondersteuning
description: In dit onderwerp vindt u een overzicht van de vereisten en configuratie van Apple FairPlay-licenties.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 187c1e60d97e0bebb3b6216b0055ddffe6e6cb4c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102454378"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Vereisten voor en configuratie van Apple FairPlay-licenties

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u uw HLS-inhoud met **Apple FairPlay** (AES-128 CBC) versleutelen. Media Services biedt ook een service voor het leveren van FairPlay-licenties. Wanneer een speler uw met FairPlay beveiligde inhoud probeert af te spelen, wordt een aanvraag verzonden naar de service voor levering van licenties om een licentie te verkrijgen. Als de licentie service de aanvraag goedkeurt, wordt de licentie die naar de client wordt verzonden, uitgegeven en wordt de opgegeven inhoud gedecodeerd en afgespeeld.

Media Services biedt ook Api's die u kunt gebruiken om uw FairPlay-licenties te configureren. In dit onderwerp worden FairPlay-licentie vereisten beschreven en wordt uitgelegd hoe u een **Fairplay** -licentie kunt configureren met behulp van Media Services-api's. 

## <a name="requirements"></a>Vereisten

Het volgende is vereist wanneer u Media Services gebruikt voor het versleutelen van uw HLS-inhoud met **Apple FairPlay** en Media Services gebruikt om Fairplay-licenties te leveren:

* Meld u aan met het [Apple-ontwikkel programma](https://developer.apple.com/).
* Apple vereist dat de eigenaar van de inhoud het [implementatie pakket](https://developer.apple.com/contact/fps/)kan verkrijgen. Status die u al hebt geïmplementeerd met Media Services en dat u het uiteindelijke FPS-pakket aanvraagt. Er zijn instructies in het laatste FPS-pakket voor het genereren van certificering en het verkrijgen van de geheime sleutel van de toepassing (ASK). U gebruikt vragen om FairPlay te configureren.
* De volgende zaken moeten worden ingesteld op Media Services de bezorgings zijde van de sleutel/licentie:

    * **App-certificaat (AC)**: dit is een pfx-bestand dat de persoonlijke sleutel bevat. U maakt dit bestand en versleutelt het met een wacht woord. Het pfx-bestand moet de indeling Base64 hebben.

        In de volgende stappen wordt beschreven hoe u een pfx-certificaat bestand genereert voor FairPlay:

        1. Installeer OpenSSL van https://slproweb.com/products/Win32OpenSSL.html .

            Ga naar de map waarin het FairPlay-certificaat en andere bestanden die door Apple worden geleverd, zijn.
        2. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het CER-bestand geconverteerd naar een. pem-bestand.

            "C:\OpenSSL-Win32\bin\openssl.exe" x509: Informeer de FairPlay. CER-out FairPlay-out. pem
        3. Voer de volgende opdracht uit via de opdrachtregel. Hiermee converteert u het. pem-bestand naar een. pfx-bestand met de persoonlijke sleutel. Het wacht woord voor het pfx-bestand wordt vervolgens gevraagd door OpenSSL.

            "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12/pfx-Profiel-export FairPlay-out. pfx-INKEY privatekey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
            
    * **Wacht woord voor app-certificaat**: het wacht woord voor het maken van het pfx-bestand.
    * **Vraag**: deze sleutel wordt ontvangen wanneer u het certificaat genereert met behulp van de Apple-ontwikkelaars Portal. Elk ontwikkel team ontvangt een unieke vraag. Sla een kopie van de vragen op en bewaar deze op een veilige plaats. U moet vragen als FairPlayAsk configureren met Media Services.
    
* De volgende zaken moeten worden ingesteld door de FPS-client:

  * **App-certificaat (AC)**: dit is een CER/. der-bestand dat de open bare sleutel bevat die het besturings systeem gebruikt voor het versleutelen van een nettolading. Media Services moet weten wat het is. Dit is vereist voor de speler. De key delivery-service ontsleutelt deze met behulp van de bijbehorende persoonlijke sleutel.

* Als u een FairPlay versleutelde stroom wilt afspelen, moet u eerst een echte vraag ontvangen en vervolgens een echt certificaat genereren. Dit proces maakt alle drie de volgende onderdelen:

  * . der-bestanden
  * pfx-bestand
  * wacht woord voor het pfx-
  
> [!NOTE]
> Azure Media Services controleert de verval datum van het certificaat niet tijdens de verpakking of de levering van sleutels. Het blijft werken nadat het certificaat is verlopen.

## <a name="fairplay-and-player-apps"></a>FairPlay-en Player-apps

Wanneer uw inhoud is versleuteld met **Apple FairPlay**, worden de afzonderlijke video-en audio voorbeelden versleuteld met behulp van de modus **AES-128 CBC** . **Fairplay streaming** (fps) is geïntegreerd in de besturings systemen van het apparaat, met systeem eigen ondersteuning voor IOS en Apple TV. Safari op OS X maakt FPS mogelijk met behulp van de EME-interface ondersteuning (Encrypted media Extensions).

Azure Media Player biedt ook ondersteuning voor het afspelen van FairPlay. Zie [Azure Media Player-documentatie](https://amp.azure.net/libs/amp/latest/docs/index.html)voor meer informatie.

U kunt uw eigen apps voor de speler ontwikkelen met behulp van de iOS SDK. Als u FairPlay-inhoud wilt afspelen, moet u het License Exchange-protocol implementeren. Dit protocol is niet opgegeven door Apple. Het is aan elke app voor het verzenden van aanvragen voor sleutel levering. De Media Services FairPlay key delivery service verwacht dat SPC als een www-form-URL gecodeerd post bericht wordt gegeven in de volgende vorm:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-configuratie .NET-voor beeld

U kunt Media Services API gebruiken om FairPlay-licenties te configureren. Wanneer de speler uw met FairPlay beveiligde inhoud probeert af te spelen, wordt een aanvraag verzonden naar de service voor levering van licenties om de licentie te verkrijgen. Als de licentie service de aanvraag goedkeurt, wordt de licentie door de service verleend. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te ontsleutelen en af te spelen.

> [!NOTE]
> Normaal gesp roken wilt u FairPlay-beleids opties slechts één keer configureren, omdat u slechts één set van een certificering en een vraag hebt.

In het volgende voor beeld wordt [Media Services .NET SDK](/dotnet/api/microsoft.azure.management.media.models) gebruikt voor het configureren van de licentie.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Volgende stappen

Lees hoe u kunt [beveiligen met DRM](protect-with-drm.md)
