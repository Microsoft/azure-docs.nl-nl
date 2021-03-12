---
title: Overzicht van Widevine-licentie sjablonen | Microsoft Docs
description: Dit onderwerp bevat een overzicht van een Widevine-licentie sjabloon die wordt gebruikt voor het configureren van Widevine-licenties.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4a3805e50ce48d1439696a3d9015d6ba8571470
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011184"
---
# <a name="widevine-license-template-overview"></a>Overzicht van Widevine-licentiesjablonen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

U kunt Azure Media Services gebruiken om Google Widevine-licenties te configureren en aan te vragen. Wanneer de speler uw met Widevine beveiligde inhoud probeert af te spelen, wordt een aanvraag verzonden naar de service voor levering van licenties om een licentie te verkrijgen. Als de licentie service de aanvraag goedkeurt, wordt de licentie door de service verleend. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te ontsleutelen en af te spelen.

Een Widevine-licentie aanvraag wordt opgemaakt als een JSON-bericht.  

>[!NOTE]
> U kunt een leeg bericht zonder waarden maken, alleen " {} ." Vervolgens wordt er een licentiesjabloon gemaakt met de standaardinstellingen. De standaardinstellingen zijn in de meeste gevallen prima. Voor micro soft-scenario's voor het leveren van licenties moet altijd de standaard waarden worden gebruikt. Als u de waarden provider en content_id moet instellen, moet een provider overeenkomen met Widevine-referenties.

```json
{  
   "payload": "<license challenge>",
   "content_id": "<content id>" 
   "provider": "<provider>"
   "allowed_track_types": "<types>",
   "content_key_specs": [  
      {  
         "track_type": "<track type 1>"
      },
      {  
         "track_type": "<track type 2>"
      },
      …
   ],
   "policy_overrides": {  
      "can_play": <can play>,
      "can persist": <can persist>,
      "can_renew": <can renew>,
      "rental_duration_seconds": <rental duration>,
      "playback_duration_seconds": <playback duration>,
      "license_duration_seconds": <license duration>,
      "renewal_recovery_duration_seconds": <renewal recovery duration>,
      "renewal_server_url": "<renewal server url>",
      "renewal_delay_seconds": <renewal delay>,
      "renewal_retry_interval_seconds": <renewal retry interval>,
      "renew_with_usage": <renew with usage>
   }
}
```

## <a name="json-message"></a>JSON-bericht
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| nettolading |Met base64 gecodeerde teken reeks |De licentie aanvraag die door een client is verzonden. |
| content_id |Met base64 gecodeerde teken reeks |Id die wordt gebruikt voor het afleiden van de sleutel-ID en de inhouds sleutel voor elke content_key_specs. track_type. |
| providers |tekenreeks |Wordt gebruikt om inhouds sleutels en-beleid op te zoeken. Als micro soft key delivery wordt gebruikt voor Widevine-licentie levering, wordt deze para meter genegeerd. |
| policy_name |tekenreeks |Naam van een eerder geregistreerd beleid. Optioneel. |
| allowed_track_types |vaste |SD_ONLY of SD_HD. Hiermee bepaalt u welke inhouds sleutels zijn opgenomen in een licentie. |
| content_key_specs |Matrix van JSON-structuren, zie de sectie "inhouds sleutel specificaties".  |Een nauw keurigere controle over welke inhouds sleutels moeten worden geretourneerd. Zie de sectie "inhouds sleutel specificaties" voor meer informatie. Er kan slechts één van de allowed_track_types en content_key_specs waarden worden opgegeven. |
| use_policy_overrides_exclusively |Boolean, True of False |Gebruik beleids kenmerken die zijn opgegeven door policy_overrides en laat alle eerder opgeslagen beleid weg. |
| policy_overrides |JSON-structuur, zie de sectie ' beleids onderdrukkingen '. |Beleids instellingen voor deze licentie.  In het geval dat deze asset een vooraf gedefinieerd beleid heeft, worden deze opgegeven waarden gebruikt. |
| session_init |JSON-structuur, zie de sectie sessie-initialisatie. |Optionele gegevens worden door gegeven aan de licentie. |
| parse_only |Boolean, True of False |De licentie aanvraag wordt geparseerd, maar er wordt geen licentie verleend. Waarden van de licentie aanvraag worden echter geretourneerd in het antwoord. |

## <a name="content-key-specs"></a>Specificaties van inhouds sleutel
Als er al een bestaand beleid bestaat, hoeft u geen van de waarden in de specificatie van de inhouds sleutel op te geven. Het bestaande beleid dat aan deze inhoud is gekoppeld, wordt gebruikt om de uitvoer beveiliging te bepalen, zoals Digital Content Protection met hoge band breedte (HDCP) en het copy General Management System (CGMS). Als er geen bestaand beleid is geregistreerd bij de Widevine-licentie server, kan de inhouds provider de waarden in de licentie aanvraag injecteren.   

Elke content_key_specs waarde moet worden opgegeven voor alle sporen, ongeacht de use_policy_overrides_exclusively optie. 

| Name | Waarde | Beschrijving |
| --- | --- | --- |
| content_key_specs. track_type |tekenreeks |De naam van het tracerings type. Als content_key_specs is opgegeven in de licentie aanvraag, moet u alle typen spoor expliciet opgeven. Als u dit niet doet, kan het afspelen van de afgelopen 10 seconden mislukken. |
| content_key_specs  <br/> security_level |uint32 |Hiermee worden de vereisten voor het afspelen van clients gedefinieerd. <br/> -Op software gebaseerde, witte box crypto grafie is vereist. <br/> -Software-crypto grafie en een verborgen decoder zijn vereist. <br/> -Het sleutel materiaal en cryptografische bewerkingen moeten worden uitgevoerd binnen een vertrouwde, door hardware ondersteunde omgeving. <br/> -De crypto grafie en de code ring van inhoud moet worden uitgevoerd binnen een vertrouwde, door hardware ondersteunde omgeving.  <br/> -De crypto grafie, het decoderen en alle verwerking van de media (gecomprimeerd en niet-gecomprimeerd) moeten worden afgehandeld binnen een vertrouwde, door hardware ondersteunde omgeving. |
| content_key_specs <br/> required_output_protection. HDC |teken reeks, een van HDCP_NONE, HDCP_V1, HDCP_V2 |Hiermee wordt aangegeven of HDCP is vereist. |
| content_key_specs <br/>sleutel |Base64<br/>versleutelde teken reeks |De inhouds sleutel die moet worden gebruikt voor dit nummer. Indien opgegeven, is de track_type of key_id vereist. De inhouds provider kan deze optie gebruiken om de inhouds sleutel voor dit spoor te injecteren in plaats van de Widevine-licentie server een sleutel te laten genereren of op te zoeken. |
| content_key_specs content_key_specs.key_id |Base64-gecodeerde teken reeks binair, 16 bytes |De unieke id voor de sleutel. |

## <a name="policy-overrides"></a>Beleids onderdrukkingen
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| policy_overrides. can_play |Boolean, True of False |Hiermee wordt aangegeven dat het afspelen van de inhoud is toegestaan. De standaardinstelling is onwaar. |
| policy_overrides. can_persist |Boolean, True of False |Geeft aan dat de licentie kan worden bewaard voor niet-vluchtige opslag voor offline gebruik. De standaardinstelling is onwaar. |
| policy_overrides. can_renew |Boolean, True of False |Hiermee wordt aangegeven dat het vernieuwen van deze licentie is toegestaan. Indien waar, kan de duur van de licentie worden verlengd met heartbeat. De standaardinstelling is onwaar. |
| policy_overrides. license_duration_seconds |int64 |Hiermee wordt het tijd venster voor deze specifieke licentie aangegeven. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides. rental_duration_seconds |int64 |Hiermee wordt het tijd venster aangegeven tijdens het afspelen is toegestaan. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides. playback_duration_seconds |int64 |Het weergave venster van de tijd na het afspelen begint binnen de licentie duur. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides. renewal_server_url |tekenreeks |Alle heartbeat-aanvragen (verlenging) voor deze licentie worden omgeleid naar de opgegeven URL. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renewal_delay_seconds |int64 |Het aantal seconden na de license_start_time voordat de verlenging voor het eerst wordt geprobeerd. Dit veld wordt alleen gebruikt als can_renew waar is. De standaard waarde is 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Hiermee geeft u de vertraging in seconden tussen de volgende aanvragen voor licentie vernieuwing, in geval van een fout. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |Het venster waarin het afspelen kan worden voortgezet terwijl er wordt geprobeerd om te vernieuwen, maar dit is niet gelukt als gevolg van back-end-problemen met de licentie server. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides. renew_with_usage |Boolean, True of False |Geeft aan dat de licentie wordt verzonden voor verlenging wanneer het gebruik wordt gestart. Dit veld wordt alleen gebruikt als can_renew waar is. |

## <a name="session-initialization"></a>Sessie-initialisatie
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| provider_session_token |Met base64 gecodeerde teken reeks |Dit sessie token wordt weer gegeven in de licentie en komt voor in latere vernieuwingen. Het sessie token wordt niet persistent gemaakt buiten sessies. |
| provider_client_token |Met base64 gecodeerde teken reeks |Client token om terug te sturen naar de reactie van de licentie. Als de licentie aanvraag een client token bevat, wordt deze waarde genegeerd. Het client token blijft in de voorbije licentie sessies. |
| override_provider_client_token |Boolean, True of False |Als deze eigenschap onwaar is en de licentie aanvraag een client token bevat, gebruikt u het token van de aanvraag, zelfs als er een client token is opgegeven in deze structuur. Indien waar, altijd het token gebruiken dat is opgegeven in deze structuur. |

## <a name="configure-your-widevine-licenses-by-using-net-types"></a>Uw Widevine-licenties configureren met behulp van .NET-typen
Media Services biedt .NET-Api's die u kunt gebruiken om uw Widevine-licenties te configureren. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Klassen zoals gedefinieerd in de Media Services .NET SDK
De volgende klassen zijn de definities van deze typen:

```dotnetcli
public class WidevineMessage
{
    public WidevineMessage();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public AllowedTrackTypes? allowed_track_types { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public ContentKeySpecs[] content_key_specs { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public object policy_overrides { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum AllowedTrackTypes
{
    SD_ONLY = 0,
    SD_HD = 1
}
public class ContentKeySpecs
{
    public ContentKeySpecs();

    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string key_id { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public RequiredOutputProtection required_output_protection { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public int? security_level { get; set; }
    [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
    public string track_type { get; set; }
}

public class RequiredOutputProtection
{
    public RequiredOutputProtection();

    public Hdcp hdcp { get; set; }
}

[JsonConverter(typeof(StringEnumConverter))]
public enum Hdcp
{
    HDCP_NONE = 0,
    HDCP_V1 = 1,
    HDCP_V2 = 2
}
```

### <a name="example"></a>Voorbeeld
In het volgende voor beeld ziet u hoe u .NET-Api's gebruikt om een eenvoudige Widevine-licentie te configureren:

```dotnetcli
private static string ConfigureWidevineLicenseTemplate()
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            can_renew = false
        }
    };

    string configuration = JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Dynamische algemene versleuteling met PlayReady en/of Widevine gebruiken](media-services-protect-with-playready-widevine.md)

