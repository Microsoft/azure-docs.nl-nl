---
title: Overzicht van Azure Media Services | Microsoft Docs
description: Microsoft Azure Media Services is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Dit artikel geeft een overzicht van Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008227"
---
# <a name="azure-media-services-overview"></a>Overzicht van Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 3](../latest/media-services-overview.md)
> * [Versie 2](media-services-overview.md)

> [!NOTE]
> Er worden geen nieuwe functies meer aan Media Services v2 toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) is een uitbreidbaar cloudplatform waarmee ontwikkelaars schaalbare toepassingen voor mediabeheer en -levering kunnen ontwikkelen. Media Services is gebaseerd op de REST API's waarmee u veilig video- of audio-inhoud kunt uploaden, opslaan, coderen en verpakken, zowel voor levering on demand als levering via livestreaming aan verschillende clients (bijvoorbeeld tv, pc en mobiele apparaten).

U kunt end-to-end-werkstromen volledig met Media Services bouwen. U kunt er ook voor kiezen om onderdelen van derde partijen voor sommige onderdelen van uw werkstroom te gebruiken. U kunt bijvoorbeeld coderen met een coderingsprogramma van een derde partij. Vervolgens kunt u uploaden, beveiligen, verpakken en leveren met Media Services. U kunt uw inhoud live streamen of on-demand leveren. 


## <a name="compliance-privacy-and-security"></a>Compliance, privacy en beveiliging

Het is heel belangrijk dat u zich realiseert dat u zich bij het gebruik van Azure Media Services moet houden aan alle toepasselijke wetgeving en dat het niet is toegestaan Media Services of een Azure-service te gebruiken op een manier die de rechten van anderen schendt of anderen schade berokkent.

Voordat u een video/afbeelding naar Media Services uploadt, moet u over alle juiste rechten beschikken voor het gebruik van de video/afbeelding, inclusief, indien vereist door de wet, alle vereiste toestemmingen van personen (indien van toepassing) in de video/afbeelding, voor het gebruik, de verwerking en de opslag van hun gegevens in Media Services en Azure. Sommige jurisdicties kunnen speciale wettelijke vereisten opleggen voor het verzamelen, online verwerken en opslaan van bepaalde typen gegevens, zoals biometrische gegevens. Voordat u Media Services en Azure gebruikt voor het verwerken en opslaan van gegevens die onder bijzondere wettelijke vereisten vallen, moet u ervoor zorgen dat u voldoet aan de wettelijke vereisten die voor u van toepassing kunnen zijn.

Meer informatie over compliance, privacy en beveiliging in Media Services vindt u op [Vertrouwenscentrum](https://www.microsoft.com/trust-center/?rtc=1) van Microsoft. Raadpleeg de privacyverklaring van micro soft voor de privacy van micro soft, het verwerken en bewaren van gegevens, inclusief de manier waarop u uw gegevens kunt verwijderen, de [Privacy verklaring](https://privacy.microsoft.com/PrivacyStatement), de [voor waarden voor Online Services](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") en de [gegevens verwerking](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Door Media Services te gebruiken, gaat u akkoord met de voorwaarden van de OST, DPA en de Privacyverklaring.
 
## <a name="prerequisites"></a>Vereisten

Als u Azure Media Services wilt gaan gebruiken, moet u over het volgende beschikken:

* Een Azure-account. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com) voor meer informatie.
* Een Azure Media Services-account. Zie [Een account maken](media-services-portal-create-account.md) voor meer informatie.
* (Optioneel) Instellen van de ontwikkelomgeving. Kies .NET of REST API voor uw ontwikkelomgeving. Zie [De omgeving instellen](media-services-dotnet-how-to-use.md) voor meer informatie.

    Leer ook hoe u via [programmacode verbinding kunt maken met de AMS-API](media-services-use-aad-auth-to-access-ams-api.md).
* Een streaming-eindpunt (Standard of Premium) in de status Gestart.  Zie [Managing streaming endpoints](media-services-portal-manage-streaming-endpoints.md) (Streaming-eindpunten beheren) voor meer informatie.

## <a name="sdks-and-tools"></a>SDK's en hulpprogramma's

Als u Media Services-oplossingen wilt maken, kunt u het volgende gebruiken:

* [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Een van de beschikbare client-SDK's:
    * Azure Media Services SDK voor .NET
    
        * [NuGet-pakket](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub-bron code](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services voor Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dit is een niet-Microsoft-versie van een Node.js SDK. Deze wordt onderhouden door een community en biedt nog geen 100% dekking voor AMS API's).
* Bestaande hulpprogramma's:
    * [Azure-portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer (AMSE) is een Winforms-/C#-toepassing voor Windows)

> [!NOTE]
> Zie [Aan de slag met de SDK voor Java-clients voor Azure Media Services](./media-services-java-how-to-use.md) om de nieuwste versie van de Java-SDK op te halen en te ontwikkelen met Java. <br/>
> Als u de nieuwste PHP-SDK voor Media Services wilt downloaden, zoekt u versie 0.5.7 van het Microsoft/WindowAzure-pakket in de [Packagist-opslagplaats](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Codevoorbeelden

Zoeken naar meerdere codevoorbeelden in de galerie **Azure-codevoorbeelden**: [Azure Media Services-codevoorbeelden](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Concepten

Zie [Concepten](media-services-concepts.md) voor Azure Media Services-concepten.

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Ondersteunde scenario's en de beschikbaarheid van Media Services in datacenters

Zie [AMS-scenario's](scenarios-and-availability.md)voor meer informatie over algemene Scenario's van Azure.
Zie [Beschik baarheid van media service](availability-regions-v-2.md)voor meer informatie over regionale Beschik baarheid.

## <a name="service-level-agreement-sla"></a>Service Level Agreement (SLA)

Zie [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/) voor meer informatie.

## <a name="support"></a>Ondersteuning

[Ondersteuning van Azure](https://azure.microsoft.com/support/options/) biedt ondersteuningsopties voor Azure, met inbegrip van Media Services.

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
