---
title: Overzicht van Media Services v3 Widevine-licentie sjablonen
description: Meer informatie over Azure Media Services met de licentie sjabloon Widevine en hoe deze wordt gebruikt voor het configureren van Widevine-licenties.
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
ms.date: 05/07/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 9ef45e804b593f36171907395c564c8c6058c286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453730"
---
# <a name="media-services-v3-with-widevine-license-template-overview"></a>Overzicht van Media Services V3 met Widevine-licentie sjabloon

Met Azure Media Services kunt u uw inhoud versleutelen met **Google Widevine**. Media Services biedt ook een service voor het leveren van Widevine-licenties. U kunt Azure Media Services-Api's gebruiken om Widevine-licenties te configureren. Wanneer een speler uw met Widevine beveiligde inhoud probeert af te spelen, wordt een aanvraag verzonden naar de service voor levering van licenties om de licentie te verkrijgen. Als de licentie service de aanvraag goedkeurt, wordt de licentie door de service verleend. Het wordt verzonden naar de client en wordt gebruikt om de opgegeven inhoud te ontsleutelen en af te spelen.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

Een Widevine-licentie aanvraag wordt opgemaakt als een JSON-bericht.  



```json
{  
    "payload":"<license challenge>",
    "content_id": "<content id>"
    "provider": "<provider>"
    "allowed_track_types":"<types>",
    "content_key_specs":[  
        {  
            "track_type":"<track type 1>"
        },
        {  
            "track_type":"<track type 2>"
        },
        …
    ],
    "policy_overrides":{  
        "can_play":<can play>,
        "can persist":<can persist>,
        "can_renew":<can renew>,
        "rental_duration_seconds":<rental duration>,
        "playback_duration_seconds":<playback duration>,
        "license_duration_seconds":<license duration>,
        "renewal_recovery_duration_seconds":<renewal recovery duration>,
        "renewal_server_url":"<renewal server url>",
        "renewal_delay_seconds":<renewal delay>,
        "renewal_retry_interval_seconds":<renewal retry interval>,
        "renew_with_usage":<renew with usage>
    }
}
```

>[!NOTE]
> U kunt een leeg bericht zonder waarden maken, alleen " {} ." Vervolgens wordt er een licentiesjabloon gemaakt met de standaardinstellingen. De standaardinstellingen zijn in de meeste gevallen prima. Voor micro soft-scenario's voor het leveren van licenties moet altijd de standaard waarden worden gebruikt. Als u de waarden provider en content_id moet instellen, moet een provider overeenkomen met Widevine-referenties.

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
Als er een bestaand beleid bestaat, hoeft u geen van de waarden op te geven in de specificatie van de inhouds sleutel. Het bestaande beleid dat aan deze inhoud is gekoppeld, wordt gebruikt om de uitvoer beveiliging te bepalen, zoals Digital Content Protection met hoge band breedte (HDCP) en het copy General Management System (CGMS). Als er geen bestaand beleid is geregistreerd bij de Widevine-licentie server, kan de inhouds provider de waarden in de licentie aanvraag injecteren.   

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
| policy_overrides&#46;can_play |Boolean, True of False |Hiermee wordt aangegeven dat het afspelen van de inhoud is toegestaan. De standaardinstelling is onwaar. |
| policy_overrides&#46;can_persist |Boolean, True of False |Geeft aan dat de licentie kan worden bewaard voor niet-vluchtige opslag voor offline gebruik. De standaardinstelling is onwaar. |
| policy_overrides&#46;can_renew |Boolean, True of False |Hiermee wordt aangegeven dat het vernieuwen van deze licentie is toegestaan. Indien waar, kan de duur van de licentie worden verlengd met heartbeat. De standaardinstelling is onwaar. |
| policy_overrides&#46;license_duration_seconds |int64 |Hiermee wordt het tijd venster voor deze specifieke licentie aangegeven. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides&#46;rental_duration_seconds |int64 |Hiermee wordt het tijd venster aangegeven tijdens het afspelen is toegestaan. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides&#46;playback_duration_seconds |int64 |Het weergave venster van de tijd na het afspelen begint binnen de licentie duur. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. De standaard waarde is 0. |
| policy_overrides&#46;renewal_server_url |tekenreeks |Alle heartbeat-aanvragen (verlenging) voor deze licentie worden omgeleid naar de opgegeven URL. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renewal_delay_seconds |int64 |Het aantal seconden na de license_start_time voordat de verlenging voor het eerst wordt geprobeerd. Dit veld wordt alleen gebruikt als can_renew waar is. De standaard waarde is 0. |
| policy_overrides&#46;renewal_retry_interval_seconds |int64 |Hiermee geeft u de vertraging in seconden tussen de volgende aanvragen voor licentie vernieuwing, in geval van een fout. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renewal_recovery_duration_seconds |int64 |Het venster waarin het afspelen kan worden voortgezet terwijl er wordt geprobeerd om te vernieuwen, maar dit is niet gelukt als gevolg van back-end-problemen met de licentie server. Een waarde van 0 geeft aan dat er geen limiet is voor de duur. Dit veld wordt alleen gebruikt als can_renew waar is. |
| policy_overrides&#46;renew_with_usage |Boolean, True of False |Geeft aan dat de licentie wordt verzonden voor verlenging wanneer het gebruik wordt gestart. Dit veld wordt alleen gebruikt als can_renew waar is. |

## <a name="session-initialization"></a>Sessie-initialisatie
| Name | Waarde | Beschrijving |
| --- | --- | --- |
| provider_session_token |Met base64 gecodeerde teken reeks |Dit sessie token wordt weer gegeven in de licentie en komt voor in latere vernieuwingen. Het sessie token wordt niet persistent gemaakt buiten sessies. |
| provider_client_token |Met base64 gecodeerde teken reeks |Client token om terug te sturen naar de reactie van de licentie. Als de licentie aanvraag een client token bevat, wordt deze waarde genegeerd. Het client token blijft in de voorbije licentie sessies. |
| override_provider_client_token |Boolean, True of False |Als deze eigenschap onwaar is en de licentie aanvraag een client token bevat, gebruikt u het token van de aanvraag, zelfs als er een client token is opgegeven in deze structuur. Indien waar, altijd het token gebruiken dat is opgegeven in deze structuur. |

## <a name="configure-your-widevine-license-with-net"></a>Uw Widevine-licentie configureren met .NET 

Media Services biedt een klasse waarmee u een Widevine-licentie kunt configureren. Als u de licentie wilt maken, geeft u JSON door aan [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate).

Als u de sjabloon wilt configureren, kunt u het volgende doen:

### <a name="directly-construct-a-json-string"></a>Een JSON-teken reeks rechtstreeks samen stellen

Deze methode kan fout gevoelig zijn. U kunt het beste een andere methode gebruiken, die wordt beschreven in [vereiste klassen definiëren en serialiseren naar JSON](#classes).

```csharp
ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
{
    WidevineTemplate = @"{""allowed_track_types"":""SD_HD"",""content_key_specs"":[{""track_type"":""SD"",""security_level"":1,""required_output_protection"":{""hdcp"":""HDCP_V2""}}],""policy_overrides"":{""can_play"":true,""can_persist"":true,""can_renew"":false}}"
};
```

### <a name="define-needed-classes-and-serialize-to-json"></a><a id="classes"></a> Benodigde klassen definiëren en serialiseren naar JSON

#### <a name="define-classes"></a>Klassen definiëren

In het volgende voor beeld ziet u een voor beeld van definities van klassen die zijn toegewezen aan het JSON-schema Widevine. U kunt de klassen instantiëren voordat u ze naar JSON-teken reeks serialiseren.  

```csharp
/// <summary>
/// Widevine PolicyOverrides class.
/// </summary>
public class PolicyOverrides
{
    /// <summary>
    /// Gets or sets a value indicating whether playback of the content is allowed. Default is false.
    /// </summary>
    [JsonProperty("can_play")]
    public bool CanPlay { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether the license might be persisted to nonvolatile storage for offline use. Default is false.
    /// </summary>
    [JsonProperty("can_persist")]
    public bool CanPersist { get; set; }

    /// <summary>
    /// Gets or sets a value indicating whether renewal of this license is allowed. If true, the duration of the license can be extended by heartbeat. Default is false.
    /// </summary>
    [JsonProperty("can_renew")]
    public bool CanRenew { get; set; }

    /// <summary>
    /// Gets or sets the time window while playback is permitted. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("rental_duration_seconds")]
    public int RentalDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the viewing window of time after playback starts within the license duration. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("playback_duration_seconds")]
    public int PlaybackDurationSeconds { get; set; }

    /// <summary>
    /// Gets or sets the time window for this specific license. A value of 0 indicates that there is no limit to the duration. Default is 0.
    /// </summary>
    [JsonProperty("license_duration_seconds")]
    public int LicenseDurationSeconds { get; set; }
}

/// <summary>
/// Widevine ContentKeySpec class.
/// </summary>
public class ContentKeySpec
{
    /// <summary>
    /// Gets or sets track type.
    /// If content_key_specs is specified in the license request, make sure to specify all track types explicitly.
    /// Failure to do so results in failure to play back past 10 seconds.
    /// </summary>
    [JsonProperty("track_type")]
    public string TrackType { get; set; }

    /// <summary>
    /// Gets or sets client robustness requirements for playback.
    /// Software-based white-box cryptography is required.
    /// Software cryptography and an obfuscated decoder are required.
    /// The key material and cryptography operations must be performed within a hardware-backed trusted execution environment.
    /// The cryptography and decoding of content must be performed within a hardware-backed trusted execution environment.
    /// The cryptography, decoding, and all handling of the media (compressed and uncompressed) must be handled within a hardware-backed trusted execution environment.
    /// </summary>
    [JsonProperty("security_level")]
    public int SecurityLevel { get; set; }

    /// <summary>
    /// Gets or sets the OutputProtection.
    /// </summary>
    [JsonProperty("required_output_protection")]
    public OutputProtection RequiredOutputProtection { get; set; }
}

/// <summary>
/// OutputProtection Widevine class.
/// </summary>
public class OutputProtection
{
    /// <summary>
    /// Gets or sets HDCP protection.
    /// Supported values : HDCP_NONE, HDCP_V1, HDCP_V2
    /// </summary>
    [JsonProperty("hdcp")]
    public string HDCP { get; set; }

    /// <summary>
    /// Gets or sets CGMS.
    /// </summary>
    [JsonProperty("cgms_flags")]
    public string CgmsFlags { get; set; }
}

/// <summary>
/// Widevine template.
/// </summary>
public class WidevineTemplate
{
    /// <summary>
    /// Gets or sets the allowed track types.
    /// SD_ONLY or SD_HD.
    /// Controls which content keys are included in a license.
    /// </summary>
    [JsonProperty("allowed_track_types")]
    public string AllowedTrackTypes { get; set; }

    /// <summary>
    /// Gets or sets a finer-grained control on which content keys to return.
    /// For more information, see the section "Content key specs."
    /// Only one of the allowed_track_types and content_key_specs values can be specified.
    /// </summary>
    [JsonProperty("content_key_specs")]
    public ContentKeySpec[] ContentKeySpecs { get; set; }

    /// <summary>
    /// Gets or sets policy settings for the license.
    /// In the event this asset has a predefined policy, these specified values are used.
    /// </summary>
    [JsonProperty("policy_overrides")]
    public PolicyOverrides PolicyOverrides { get; set; }
}
```

#### <a name="configure-the-license"></a>De licentie configureren

Gebruik klassen die in de vorige sectie zijn gedefinieerd om JSON te maken die wordt gebruikt om [WidevineTemplate](/dotnet/api/microsoft.azure.management.media.models.contentkeypolicywidevineconfiguration.widevinetemplate#Microsoft_Azure_Management_Media_Models_ContentKeyPolicyWidevineConfiguration_WidevineTemplate)te configureren:

```csharp
private static ContentKeyPolicyWidevineConfiguration ConfigureWidevineLicenseTempate()
{
    WidevineTemplate template = new WidevineTemplate()
    {
        AllowedTrackTypes = "SD_HD",
        ContentKeySpecs = new ContentKeySpec[]
        {
            new ContentKeySpec()
            {
                TrackType = "SD",
                SecurityLevel = 1,
                RequiredOutputProtection = new OutputProtection()
                {
                    HDCP = "HDCP_V2"
                }
            }
        },
        PolicyOverrides = new PolicyOverrides()
        {
            CanPlay = true,
            CanPersist = true,
            CanRenew = false,
            RentalDurationSeconds = 2592000,
            PlaybackDurationSeconds = 10800,
            LicenseDurationSeconds = 604800,
        }
    };

    ContentKeyPolicyWidevineConfiguration objContentKeyPolicyWidevineConfiguration = new ContentKeyPolicyWidevineConfiguration
    {
        WidevineTemplate = Newtonsoft.Json.JsonConvert.SerializeObject(template)
    };
    return objContentKeyPolicyWidevineConfiguration;
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

Lees hoe u kunt [beveiligen met DRM](protect-with-drm.md)
