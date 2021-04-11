---
title: Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Media Services (AMS) kunt gebruiken voor het leveren van een stroom die dynamisch wordt versleuteld door AMS met zowel PlayReady als Widevine DRMs. De PlayReady-licentie is afkomstig van Media Services PlayReady-licentie server en de Widevine-licentie wordt geleverd door de Axinom-licentie server.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 9c93fa4e-b4da-4774-ab6d-8b12b371631d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: Mingfeiy;rajputam;Juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 532e982b02bb35074370db45fec8cc42a898c83e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015503"
---
# <a name="using-axinom-to-deliver-widevine-licenses-to-azure-media-services"></a>Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [castLabs](media-services-castlabs-integration.md)
> * [Axinom](media-services-axinom-integration.md)
> 
> 

## <a name="overview"></a>Overzicht
Azure Media Services (AMS) heeft Google Widevine Dynamic Protection toegevoegd (Zie [de blog van Mingfei](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) voor meer informatie). Daarnaast heeft Azure Media Player (AMP) ook ondersteuning toegevoegd voor Widevine (Zie het [amp-document](https://amp.azure.net/libs/amp/latest/docs/) voor meer informatie). Dit is een belang rijke stroom in streaming DASH-inhoud die wordt beveiligd door CENC met multi-native-DRM (PlayReady en Widevine) in moderne browsers die zijn uitgerust met MSE en EME.

Vanaf de Media Services .NET SDK versie 3.5.2 kunt u met Media Services de Widevine-licentie sjabloon configureren en Widevine-licenties ophalen. U kunt ook de volgende AMS-partners gebruiken om Widevine-licenties te leveren: [Axinom](https://www.axinom.com), [EZDRM](https://ezdrm.com/) en [castLabs](https://castlabs.com/company/partners/azure/).

In dit artikel wordt beschreven hoe u Widevine-licentie servers die worden beheerd door Axinom integreert en test. Dit betreft met name:  

* Dynamische Common Encryption configureren met multi-DRM (PlayReady en Widevine) met bijbehorende Url's voor de aanschaf van licenties;
* Genereren van een JWT-token om te voldoen aan de vereisten van de licentie server;
* Het ontwikkelen van Azure Media Player-app, die de aanschaf van licenties met JWT-token verificatie afhandelt;

Het volledige systeem en de stroom van de inhouds sleutel, sleutel-ID, sleutel zaad, JTW-token en de bijbehorende claims kunnen het beste worden beschreven in het volgende diagram:

![STREEPJE en CENC](./media/media-services-axinom-integration/media-services-axinom1.png)

## <a name="content-protection"></a>Inhoudsbeveiliging
Zie blog van Mingfei voor meer informatie over het configureren van dynamische beveiliging en het bezorgings beleid voor sleutels: [het configureren van Widevine-verpakking met Azure Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services).

U kunt dynamische CENC-beveiliging met multi-DRM configureren voor een streepje-streaming met de volgende opties:

1. PlayReady-beveiliging voor micro soft Edge en IE11, die een token autorisatie beperking kan hebben. Het token beperkt beleid moet vergezeld gaan van een token dat is uitgegeven door een Secure token service (STS), zoals Azure Active Directory;
2. Widevine-beveiliging voor Chrome kan token verificatie vereisen met token dat is uitgegeven door een andere STS. 

Zie de sectie [JWT-token generatie](media-services-axinom-integration.md#jwt-token-generation) voor waarom Azure Active Directory niet als STS kan worden gebruikt voor de Widevine-licentie server van Axinom.

### <a name="considerations"></a>Overwegingen
1. U moet de Axinom opgegeven sleutel Seed (8888000000000000000000000000000000000000) en de gegenereerde of geselecteerde sleutel-ID voor het genereren van de inhouds sleutel voor het configureren van de key delivery-service gebruiken. Axinom-licentie server verzendt alle licenties met inhouds sleutels op basis van dezelfde sleutel Seed, die geldig zijn voor testen en productie.
2. De Widevine-licentie voor het verkrijgen van tests: [https://drm-widevine-licensing.axtest.net/AcquireLicense](https://drm-widevine-licensing.axtest.net/AcquireLicense) . Zowel HTTP als HTTS zijn toegestaan.

## <a name="azure-media-player-preparation"></a>Azure Media Player voorbereiding
AMP v 1.4.0 ondersteunt het afspelen van AMS-inhoud die dynamisch wordt verpakt met PlayReady en Widevine DRM.
Als Widevine-licentie server geen token verificatie vereist, hoeft u verder niets te doen om een streepje te testen dat wordt beveiligd door Widevine. Het AMP-team biedt bijvoorbeeld een eenvoudig voor [beeld](https://amp.azure.net/libs/amp/latest/samples/dynamic_multiDRM_PlayReadyWidevineFairPlay_notoken.html), waarin u kunt zien dat het werkt in micro soft Edge en IE11 met PlayReady en Chrome met Widevine.
Voor de Widevine-licentie server van Axinom is JWT-token verificatie vereist. Het JWT-token moet worden verzonden met een licentie aanvraag via een HTTP-header "X-AxDRM-Message". Voor dit doel moet u de volgende Java script toevoegen aan het AMP van de host van de webpagina voordat u de bron instelt:

```html
<script>AzureHtml5JS.KeySystem.WidevineCustomAuthorizationHeader = "X-AxDRM-Message"</script>
```

De rest van de code van het AMP is de Standard AMP-API, zoals [hier](https://amp.azure.net/libs/amp/latest/docs/)in het amp-document wordt beschreven.

Het bovenstaande java script voor het instellen van de aangepaste autorisatie-header is nog steeds een aanpak op korte termijn voordat de officiële benadering van de lange termijn in AMP wordt uitgebracht.

## <a name="jwt-token-generation"></a>JWT-tokens genereren
Voor het testen van de Axinom Widevine-licentie server is JWT-token authenticatie vereist. Daarnaast is een van de claims in de JWT-token van een complex object type in plaats van primitieve gegevens typen.

Helaas kan Azure AD alleen JWT-tokens uitgeven met primitieve typen. Op deze manier kunt u met .NET Framework-API (System. Identity model. tokens. SecurityTokenHandler en JwtPayload) alleen complex object typen invoeren als claims. De claims worden echter nog steeds geserialiseerd als teken reeks. Daarom kunnen we geen van de twee gebruiken voor het genereren van het JWT-token voor Widevine-licentie aanvragen.

Het [JWT NuGet-pakket](https://www.nuget.org/packages/JWT) van John Sheehan voldoet aan de behoeften zodat we dit NuGet-pakket gaan gebruiken.

Hieronder ziet u de code voor het genereren van een JWT-token met de benodigde claims zoals vereist door de Axinom Widevine-licentie server voor het testen:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.IdentityModel.Tokens;
using System.IdentityModel.Protocols.WSTrust;
using System.Security.Claims;

namespace OpenIdConnectWeb.Utils
{
    public class JwtUtils
    {
        //using John Sheehan's NuGet JWT library: https://www.nuget.org/packages/JWT/
        public static string CreateJwtSheehan(string symmetricKeyHex, string key_id)
        {
            byte[] symmetricKey = ConvertHexStringToByteArray(symmetricKeyHex);  //hex string to byte[] Note: Note that the key is a hex string, however it must be treated as a series of bytes not a string when encoding.

            var payload = new Dictionary<string, object>()
            {
                { "version", 1 },
                { "com_key_id", System.Configuration.ConfigurationManager.AppSettings["ax:com_key_id"] },
                { "message", new { type = "entitlement_message", key_ids = new string[] { key_id } }  }
            };

            string token = JWT.JsonWebToken.Encode(payload, symmetricKey, JWT.JwtHashAlgorithm.HS256);

            return token;
        }

        //convert hex string to byte[]
        public static byte[] ConvertHexStringToByteArray(string hexString)
        {
            if (hexString.Length % 2 != 0)
            {
                throw new ArgumentException(String.Format(System.Globalization.CultureInfo.InvariantCulture, "The binary key cannot have an odd number of digits: {0}", hexString));
            }

            byte[] HexAsBytes = new byte[hexString.Length / 2];
            for (int index = 0; index < HexAsBytes.Length; index++)
            {
                string byteValue = hexString.Substring(index * 2, 2);
                HexAsBytes[index] = byte.Parse(byteValue, System.Globalization.NumberStyles.HexNumber, System.Globalization.CultureInfo.InvariantCulture);
            }

            return HexAsBytes;
        }

    }  

}  
```

Axinom Widevine-licentie server

```xml
<add key="ax:laurl" value="https://drm-widevine-licensing.axtest.net/AcquireLicense" />
<add key="ax:com_key_id" value="69e54088-e9e0-4530-8c1a-1eb6dcd0d14e" />
<add key="ax:com_key" value="4861292d027e269791093327e62ceefdbea489a4c7e5a4974cc904b840fd7c0f" />
<add key="ax:keyseed" value="8888000000000000000000000000000000000000" />
```

### <a name="considerations"></a>Overwegingen
1. Hoewel de AMS PlayReady-service voor het leveren van licenties een ' Bearer = ' voor een verificatie token vereist, wordt de Axinom Widevine-licentie server niet gebruikt.
2. De Axinom-communicatie sleutel wordt gebruikt als handtekening sleutel. De sleutel is een hexadecimale teken reeks, maar moet worden beschouwd als een reeks bytes die geen teken reeks is bij het coderen. Dit wordt bereikt met de methode ConvertHexStringToByteArray.

## <a name="retrieving-key-id"></a>Sleutel-ID ophalen
U hebt wellicht gezien dat in de code voor het genereren van een JWT-token een sleutel-ID is vereist. Omdat de JWT-token gereed moet zijn voordat u de AMP-speler kunt laden, moet de sleutel-ID worden opgehaald om een JWT-token te genereren.

Er zijn natuurlijk meerdere manieren om de sleutel-ID op te halen. Een voor beeld: een code-ID kan worden opgeslagen in combi natie met meta gegevens van inhoud in een Data Base. U kunt ook sleutel-ID ophalen uit een streepje MPD (Media Presentation Description)-bestand. De onderstaande code is voor de laatste.

```csharp
//get key_id from DASH MPD
public static string GetKeyID(string dashUrl)
{
    if (!dashUrl.EndsWith("(format=mpd-time-csf)"))
    {
        dashUrl += "(format=mpd-time-csf)";
    }

    XPathDocument objXPathDocument = new XPathDocument(dashUrl);
    XPathNavigator objXPathNavigator = objXPathDocument.CreateNavigator();
    XmlNamespaceManager objXmlNamespaceManager = new XmlNamespaceManager(objXPathNavigator.NameTable);
    objXmlNamespaceManager.AddNamespace("",     "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("ns1",  "urn:mpeg:dash:schema:mpd:2011");
    objXmlNamespaceManager.AddNamespace("cenc", "urn:mpeg:cenc:2013");
    objXmlNamespaceManager.AddNamespace("ms",   "urn:microsoft");
    objXmlNamespaceManager.AddNamespace("mspr", "urn:microsoft:playready");
    objXmlNamespaceManager.AddNamespace("xsi",  "https://www.w3.org/2001/XMLSchema-instance");
    objXmlNamespaceManager.PushScope();

    XPathNodeIterator objXPathNodeIterator;
    objXPathNodeIterator = objXPathNavigator.Select("//ns1:MPD/ns1:Period/ns1:AdaptationSet/ns1:ContentProtection[@value='cenc']", objXmlNamespaceManager);

    string key_id = string.Empty;
    if (objXPathNodeIterator.MoveNext())
    {
        key_id = objXPathNodeIterator.Current.GetAttribute("default_KID", "urn:mpeg:cenc:2013");
    }

    return key_id;
}
```

## <a name="summary"></a>Samenvatting

Met de nieuwste toevoeging van Widevine-ondersteuning in zowel Azure Media Services Content Protection als Azure Media Player kunnen we streaming van DASH + multi-native-DRM (PlayReady + Widevine) implementeren met zowel de PlayReady-licentie service in AMS als Widevine licentie server van Axinom voor de volgende moderne browsers:

* Chrome
* Micro soft Edge in Windows 10
* IE 11 op Windows 8,1 en Windows 10
* Zowel Firefox (Desktop) als Safari op Mac (niet iOS) worden ook ondersteund via Silverlight en met de URL met Azure Media Player

De volgende para meters zijn vereist in de Axinom Widevine-licentie server van de mini-oplossing. Behalve voor sleutel-ID worden de rest van de para meters door Axinom opgegeven op basis van de instellingen van de Widevine-server.

| Parameter | Hoe deze wordt gebruikt |
| --- | --- |
| Communicatie sleutel-ID |Moet worden opgenomen als waarde van de claim "com_key_id" in het JWT-token (Zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |
| Communicatie sleutel |Moet worden gebruikt als de handtekening sleutel van het JWT-token (Zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |
| Sleutel zaad |Moet worden gebruikt voor het genereren van een inhouds sleutel met een bepaalde inhouds sleutel-ID (Zie  [deze](media-services-axinom-integration.md#content-protection) sectie). |
| URL voor Widevine-licentie aanschaf |Moet worden gebruikt bij het configureren van het leverings beleid voor assets voor streepje streaming (Zie  [deze](media-services-axinom-integration.md#content-protection) sectie). |
| Inhouds sleutel-ID |Moet worden opgenomen als onderdeel van de waarde van de claim van de toeslag van de JWT-token (Zie [deze](media-services-axinom-integration.md#jwt-token-generation) sectie). |

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Dankwoord
We willen graag de volgende personen erkennen die hebben bijgedragen aan het maken van dit document: Kristjan Jõgi van Axinom, Mingfei Yan en Amit Rajput.

