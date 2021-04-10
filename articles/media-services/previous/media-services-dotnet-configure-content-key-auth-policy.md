---
title: Een autorisatie beleid voor de inhouds sleutel configureren met behulp van de Media Services .NET SDK | Microsoft Docs
description: Meer informatie over het configureren van een verificatie beleid voor een inhouds sleutel met behulp van de Media Services .NET SDK.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 63cc20a9d7f6878961519aa28ae6478d9145852d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014210"
---
# <a name="configure-a-content-key-authorization-policy-by-using-the-media-services-net-sdk"></a>Een autorisatie beleid voor inhouds sleutels configureren met behulp van de Media Services .NET SDK

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Overzicht
 U kunt Azure Media Services gebruiken voor het leveren van MPEG-DASH-, Smooth Streaming-en HTTP Live Streaming (HLS)-streams die zijn beveiligd met de Advanced Encryption Standard (AES) door gebruik te maken van 128-bits versleutelings sleutels of [PlayReady Digital Rights Management (DRM)](https://www.microsoft.com/playready/overview/). Met Media Services kunt u ook STREEPJES stromen leveren die zijn versleuteld met Widevine DRM. Zowel PlayReady als Widevine zijn versleuteld volgens de specificatie Common Encryption (ISO/IEC 23001-7 CENC).

Media Services biedt ook een service voor het leveren van belang rijke services waarmee clients AES-sleutels of PlayReady/Widevine-licenties kunnen verkrijgen om de versleutelde inhoud af te spelen.

Als u Media Services een Asset wilt versleutelen, moet u een versleutelings sleutel (CommonEncryption of EnvelopeEncryption) koppelen aan de Asset. Zie [Create ContentKeys with .net](media-services-dotnet-create-contentkey.md)(Engelstalig) voor meer informatie. U moet ook autorisatie beleid configureren voor de sleutel (zoals beschreven in dit artikel).

Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van AES of DRM-versleuteling. Voor het ontsleutelen van de stream, wordt door de speler de sleutel van de sleutelleveringsservice aangevraagd. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. Het autorisatie beleid voor inhouds sleutels kan een of meer autorisatie beperkingen hebben. De opties zijn open of Token beperking. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de[SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)-indeling (Simple Web token) en de JSON Web token ([JWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3))-indeling.

Media Services biedt geen STS. U kunt een aangepaste STS maken of Azure Access Control Service gebruiken om tokens te verlenen. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking (zoals beschreven in dit artikel). Als het token geldig is en de claims in het token overeenkomen met die zijn geconfigureerd voor de inhouds sleutel, retourneert de Media Services key delivery service de versleutelings sleutel naar de client.

Raadpleeg voor meer informatie de volgende artikelen:

- [Verificatie JWT-token](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van de JWT-claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

### <a name="some-considerations-apply"></a>Er zijn enkele overwegingen van toepassing
* Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Als u uw inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eind punt de status ' running ' hebben. 
* Uw asset moet een set adaptieve bitsnelheid Mp4's of een adaptieve bitrate Smooth Streaming-bestanden bevatten. Zie [een Asset coderen](media-services-encode-asset.md)voor meer informatie.
* Upload en codeer uw assets met de optie AssetCreationOptions. StorageEncrypted.
* Als u van plan bent meerdere inhouds sleutels te hebben waarvoor dezelfde beleids configuratie is vereist, raden we u aan om één autorisatie beleid te maken en het opnieuw te gebruiken met meerdere inhouds sleutels.
* De key delivery service slaat ContentKeyAuthorizationPolicy en de bijbehorende objecten (beleids opties en-beperkingen) gedurende 15 minuten op. U kunt ContentKeyAuthorizationPolicy maken en opgeven dat u een token beperking wilt gebruiken, deze wilt testen en vervolgens het beleid wilt bijwerken naar de beperking open. Dit proces duurt ongeveer 15 minuten voordat het beleid overschakelt naar de open-versie van het beleid.
* Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
* Op dit moment kunt u geen progressieve down loads versleutelen.
* Een Media Services streaming-eind punt stelt de waarde in van de CORS ' Access-Control-Allow-Origin ' in het Preflight-antwoord als het Joker teken \* . Deze waarde is goed geschikt voor de meeste spelers, waaronder Azure Media Player, Roku en JWPlayer, en andere. Sommige spelers die gebruikmaken van dashjs werken echter niet omdat, waarbij de referenties modus is ingesteld op ' include ', XMLHttpRequest in hun dashjs geen joker teken ' \* ' als waarde van ' toegangs beheer-allow-Origin ' toestaat. Als tijdelijke oplossing voor deze beperking in dashjs, als u uw client vanuit één domein host, kan Media Services dat domein opgeven in de kopregel van het Preflight-antwoord. Open een ondersteunings ticket via de Azure Portal voor hulp.

## <a name="aes-128-dynamic-encryption"></a>AES-128 dynamische versleuteling
### <a name="open-restriction"></a>Beperking openen
Beperking openen betekent dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden.

In het volgende voor beeld wordt een open autorisatie beleid gemaakt en toegevoegd aan de inhouds sleutel:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Token beperking
In deze sectie wordt beschreven hoe u een autorisatie beleid voor de inhouds sleutel maakt en het koppelt aan de inhouds sleutel. In het autorisatie beleid wordt beschreven aan welke autorisatie vereisten moet worden voldaan om te bepalen of de gebruiker gemachtigd is om de sleutel te ontvangen. Bevat de lijst met verificatie sleutels bijvoorbeeld de sleutel waarmee het token is ondertekend?

Als u de optie voor de token beperking wilt configureren, moet u een XML gebruiken om de autorisatie vereisten van het token te beschrijven. De configuratie-XML van de token beperking moet voldoen aan het volgende XML-schema:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doel groep (ook wel bereik genoemd) beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

Wanneer u de Media Services SDK voor .NET gebruikt, kunt u de TokenRestrictionTemplate-klasse gebruiken om het beperkings token te genereren.
In het volgende voor beeld wordt een autorisatie beleid met een token beperking gemaakt. In dit voor beeld moet de client een token aanbieden dat een ondertekeningssleutel (VerificationKey), een token Uitgever en de vereiste claims bevat.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Test token
Ga als volgt te werk om een test token te verkrijgen op basis van de token beperking die is gebruikt voor het sleutel autorisatie beleid:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>PlayReady dynamische versleuteling
U kunt Media Services gebruiken om de rechten en beperkingen te configureren die u wilt dat de PlayReady DRM-runtime wordt afgedwongen wanneer een gebruiker beveiligde inhoud wil afspelen. 

Wanneer u uw inhoud beveiligt met PlayReady, is een van de dingen die u in uw autorisatie beleid moet opgeven, een XML-teken reeks die de [PlayReady-licentie sjabloon](media-services-playready-license-template-overview.md)definieert. In de PlayReadyLicenseResponseTemplate-en PlayReadyLicenseTemplate-klassen in de Media Services SDK voor .NET kunt u de PlayReady-licentie sjabloon definiëren.

Zie voor meer informatie over het versleutelen van uw inhoud met PlayReady en Widevine de [Dynamic common Encryption gebruiken playready en/of Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Beperking openen
Beperking openen betekent dat het systeem de sleutel levert aan iedereen die een belang rijke aanvraag doet. Deze beperking kan handig zijn voor test doeleinden.

In het volgende voor beeld wordt een open autorisatie beleid gemaakt en toegevoegd aan de inhouds sleutel:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Token beperking
Als u de optie voor de token beperking wilt configureren, moet u een XML gebruiken om de autorisatie vereisten van het token te beschrijven. De configuratie-XML van de token beperking moet voldoen aan het XML-schema dat wordt weer gegeven in de sectie ' token restrictie schema '.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Als u een test token wilt ophalen op basis van de token beperking die voor het sleutel autorisatie beleid is gebruikt, raadpleegt u de sectie[test token](#test-token). 

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Typen die worden gebruikt wanneer u ContentKeyAuthorizationPolicy definieert
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a name="tokentype"></a><a id="TokenType"></a>Type

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u het autorisatie beleid voor de inhouds sleutel hebt geconfigureerd, raadpleegt u [een leverings beleid voor assets configureren](media-services-dotnet-configure-asset-delivery-policy.md).
