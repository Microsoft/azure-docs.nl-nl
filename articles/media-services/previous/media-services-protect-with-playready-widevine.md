---
title: Dynamische algemene versleuteling met PlayReady en/of Widevine gebruiken | Microsoft Docs
description: U kunt Azure Media Services gebruiken om MPEG-DASH-, Smooth Streaming- en HLS-streams (HTTP Live Streaming) te leveren die zijn beveiligd met Microsoft PlayReady DRM. U kunt hiermee ook DASH-streams leveren die zijn versleuteld met Widevine DRM. Dit onderwerp bevat informatie over het dynamisch versleutelen met PlayReady en Widevine DRM.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 548d1a12-e2cb-45fe-9307-4ec0320567a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: bd544b9eec0c1839ad94ede65e9d4ccde6df81dc
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063350"
---
# <a name="use-playready-andor-widevine-dynamic-common-encryption"></a>Dynamische algemene versleuteling met PlayReady en/of Widevine gebruiken

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > er geen nieuwe functies of functionaliteit aan Media Services v2 worden toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>   

## <a name="overview"></a>Overzicht

 U kunt Azure Media Services gebruiken om MPEG-DASH-, Smooth Streaming- en HLS-streams (HTTP Live Streaming) te leveren die zijn beveiligd met [PlayReady DRM (Digital Rights Management)](https://www.microsoft.com/playready/overview/). Ook met Widevine DRM-licenties kunt u DASH-streams leveren. Zowel PlayReady als Widevine zijn versleuteld volgens de specificatie Common Encryption (ISO/IEC 23001-7 CENC). U kunt de [.NET SDK van Media Services](https://www.nuget.org/packages/windowsazure.mediaservices/) (vanaf versie 3.5.1) of de REST API gebruiken om AssetDeliveryConfiguration voor het gebruik van Widevine te configureren.

Media Services voorziet in een service voor het leveren van PlayReady- en Widevine DRM-licenties. Media Services biedt ook API's waarmee u de rechten en beperkingen kunt configureren die tijdens de uitvoering van PlayReady of Widevine DRM moeten worden afgedwongen wanneer een gebruiker beveiligde inhoud afspeelt. Wanneer een gebruiker door DRM beveiligde inhoud aanvraagt, wordt met de spelertoepassing een licentie aangevraagd bij de licentieservice van Media Services. Als de spelertoepassing is geautoriseerd, ontvangt de speler een licentie van de licentieservice van Media Services. Een PlayReady- of Widevine-licentie bevat de ontsleutelingssleutel die kan worden gebruikt door de clientspeler om de inhoud te ontsleutelen en te streamen.

U kunt ook de volgende Media Services-partners gebruiken om Widevine-licenties te leveren: 

* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Zie Integratie met [Axinom](media-services-axinom-integration.md) en [castLabs](media-services-castlabs-integration.md) voor meer informatie.

Media Services ondersteunt meerdere manieren om gebruikers te autoriseren die sleutels aanvragen. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door een beveiligingstokenservice (STS). Media Services ondersteunt tokens in de indelingen [Simple Web Tokens](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2) (SWT) en [JSON Web Token](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_3) (JWT). 

Zie [Het autorisatiebeleid voor inhoudssleutels configureren](media-services-portal-configure-content-key-auth-policy.md) voor meer informatie.

Als u gebruik wilt maken van dynamische versleuteling, maakt u een Asset die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming bron bestanden bevat. Ook moet u het leveringsbeleid voor de asset configureren (dit wordt verderop in dit onderwerp beschreven). Vervolgens zorgt de server voor streaming on demand er op basis van de indeling die is opgegeven in de streaming-URL voor dat de stream wordt geleverd in het protocol dat u hebt geselecteerd. Als gevolg hiervan slaat u de bestanden maar in één opslagindeling op en betaalt u ook alleen daarvoor. Media Services bouwt en verwerkt het juiste HTTP-antwoord op basis van elke aanvraag van een client.

Dit artikel is nuttig voor ontwikkelaars die werken aan toepassingen die media leveren die worden beveiligd met meerdere DRM's, zoals PlayReady en Widevine. In het artikel wordt beschreven hoe u de PlayReady-service voor het leveren van licenties zo met een autorisatiebeleid configureert dat alleen geautoriseerde clients PlayReady- of Widevine-licenties kunnen ontvangen. Ook wordt uitgelegd hoe u dynamische versleuteling met PlayReady of Widevine DRM via DASH gebruikt.

>[!NOTE]
>Wanneer uw Media Services-account is gemaakt, wordt er een standaard streaming-eindpunt met de status Gestopt aan uw account toegevoegd. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status Wordt uitgevoerd hebben. 

## <a name="download-the-sample"></a>Het voorbeeld downloaden
U kunt in [Azure-voorbeelden op GitHub](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) het voorbeeld downloaden dat in dit artikel wordt beschreven.

## <a name="configure-dynamic-common-encryption-and-drm-license-delivery-services"></a>Dynamic Common Encryption en DRM-services voor het leveren van licenties configureren

Voer de volgende algemene stappen uit wanneer u uw assets met PlayReady beveiligt door middel van de Media Services-service voor het leveren van licenties en dynamische versleuteling:

1. Maak een asset en upload bestanden in de asset.

2. Codeer de asset die het bestand bevat naar de Adaptive Bitrate MP4-set.

3. Maak een inhoudssleutel en koppel deze aan de gecodeerde asset. De inhoudssleutel bevat in Media Services de versleutelingssleutel van de asset.

4. Configureer het autorisatiebeleid voor de inhoudssleutel. U moet het autorisatiebeleid voor de inhoudssleutel configureren. De client moet voldoen aan het beleid voordat de inhoudssleutel aan de client wordt geleverd.

    Wanneer u het autorisatiebeleid voor inhoudssleutel maakt, moet u de leveringsmethode (PlayReady of Widevine) en de beperkingen (openen of token) opgeven. Ook moet u specifieke informatie opgeven voor het type sleutellevering. Deze informatie bepaalt hoe de sleutel aan de client wordt geleverd ([PlayReady](media-services-playready-license-template-overview.md)- of [Widevine](media-services-widevine-license-template-overview.md)-licentiesjabloon).

5. Configureer het leveringsbeleid voor een asset. De configuratie van het leveringsbeleid omvat het leveringsprotocol (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alle). De configuratie omvat ook het type dynamische versleuteling (bijvoorbeeld algemene versleuteling) en de PlayReady- of Widevine-URL voor het verkrijgen van licentie.

    U kunt voor dezelfde asset een ander beleid voor elk protocol toepassen. U kunt bijvoorbeeld PlayReady-versleuteling toepassen op Smooth/DASH en een AES Envelope op HLS. Alle protocollen die niet zijn gedefinieerd in een leveringsbeleid (bijvoorbeeld als u één beleid toevoegt waarmee alleen HLS als protocol wordt opgegeven), worden voor streaming geblokkeerd. De uitzondering is als u helemaal geen leveringsbeleid voor assets hebt gedefinieerd. In dat geval is streaming voor alle protocollen toegestaan.

6. Maak een OnDemand-locator om een streaming-URL te verkrijgen.

Aan het einde van het artikel vindt u een volledig .NET-voorbeeld.

De volgende afbeelding geeft een illustratie van de eerder beschreven werkstroom. Hier wordt de token voor verificatie gebruikt.

![Beveiligen met PlayReady](media/media-services-content-protection-overview/media-services-content-protection-with-drm.png)

De rest van dit artikel bevat gedetailleerde uitleg, codevoorbeelden en koppelingen naar onderwerpen waarin u ziet hoe u de eerder beschreven taken kunt uitvoeren.

## <a name="current-limitations"></a>Huidige beperkingen
Als u een leveringsbeleid voor assets toevoegt of bijwerkt, moet u een eventuele gekoppelde locator verwijderen en een nieuwe locator maken.

Op dit moment is er geen ondersteuning voor meerdere inhoudssleutels wanneer u versleutelt met behulp van Widevine met Media Services. 

## <a name="create-an-asset-and-upload-files-into-the-asset"></a>Een asset maken en bestanden in de asset uploaden
Als u uw video's wilt beheren, coderen en streamen, moet u eerst uw inhoud uploaden naar Media Services. Wanneer uw inhoud is geüpload, wordt deze veilig opgeslagen in de cloud voor verdere verwerking en streaming.

Zie [Upload Files into a Media Services account](media-services-dotnet-upload-files.md) (Bestanden uploaden naar een Media Services-account) voor gedetailleerde informatie.

## <a name="encode-the-asset-that-contains-the-file-to-the-adaptive-bitrate-mp4-set"></a>De asset die het bestand bevat coderen naar de Adaptive Bitrate MP4-set
Bij dynamische versleuteling maakt u een asset die een set multi-bitrate MP4-bestanden of multi-bitrate Smooth Streaming-bronbestanden bevat. Vervolgens zorgt de server voor streaming on demand er op basis van de indeling die is opgegeven in het manifest en de fragmentaanvraag voor dat u de stream ontvangt in het protocol dat u hebt geselecteerd. Daarna slaat u de bestanden maar in één opslagindeling op en betaalt u ook alleen daarvoor. Media Services bouwt en verwerkt het juiste antwoord op basis van aanvragen van een client. Zie [Dynamic packaging overview](media-services-dynamic-packaging-overview.md) (Overzicht van dynamische pakketten).

Zie [Encode an asset by using Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) (Een asset coderen met Media Encoder Standard) voor instructies voor het coderen.

## <a name="create-a-content-key-and-associate-it-with-the-encoded-asset"></a><a id="create_contentkey"></a>Een inhouds sleutel maken en deze koppelen aan de gecodeerde Asset
In Media Services bevat de inhoudssleutel de sleutel waarmee u een asset wilt coderen.

Zie [Create content key](media-services-dotnet-create-contentkey.md) (Inhoudssleutels maken) voor meer informatie.

## <a name="configure-the-content-keys-authorization-policy"></a><a id="configure_key_auth_policy"></a>Het autorisatie beleid voor de inhouds sleutel configureren
Media Services ondersteunt meerdere manieren om gebruikers te verifiëren die sleutels aanvragen. U moet het autorisatiebeleid voor de inhoudssleutel configureren. De client (speler) moet voldoen aan het beleid voordat de sleutel aan de client wordt geleverd. Het autorisatiebeleid voor inhoudssleutels kan een of meer autorisatiebeperkingen hebben: beperking voor openen of tokenbeperkingen.

Zie [Een autorisatiebeleid voor inhoudssleutels configureren](media-services-dotnet-configure-content-key-auth-policy.md#playready-dynamic-encryption) voor meer informatie.

## <a name="configure-an-asset-delivery-policy"></a><a id="configure_asset_delivery_policy"></a>Een leveringsbeleid voor assets configureren
Configureer het leveringsbeleid voor uw asset. De configuratie van het leveringsbeleid voor assets omvat onder andere het volgende:

* De URL voor het ophalen van DRM-licenties.
* Het protocol voor het leveren van assets (bijvoorbeeld MPEG-DASH, HLS, Smooth Streaming of alle).
* Het type dynamische versleuteling (in dit geval algemene versleuteling).

Zie [Een leveringsbeleid voor assets configureren](media-services-dotnet-configure-asset-delivery-policy.md) voor meer informatie.

## <a name="create-an-ondemand-streaming-locator-to-get-a-streaming-url"></a><a id="create_locator"></a>Een OnDemand-streaminglocator maken om een streaming-URL te verkrijgen
U moet de gebruiker voorzien van de streaming-URL voor Smooth Streaming, DASH of HLS.

> [!NOTE]
> Als u het leveringsbeleid voor uw asset toevoegt of bijwerkt, moet u een eventuele locator verwijderen en een nieuwe locator maken.
>
>

Zie [Build a streaming URL](media-services-deliver-streaming-content.md) (Een streaming-URL bouwen) voor instructies over het publiceren van een asset en bouwen van een streaming-URL.

## <a name="get-a-test-token"></a>Een test-token ophalen
Haal op basis van de tokenbeperking een test-token op die is gebruikt voor het sleutelautorisatiebeleid.

```csharp
// Deserializes a string containing an XML representation of a TokenRestrictionTemplate
// back into a TokenRestrictionTemplate class instance.
TokenRestrictionTemplate tokenTemplate =
TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

// Generate a test token based on the data in the given TokenRestrictionTemplate.
//The GenerateTestToken method returns the token without the word "Bearer" in front,
//so you have to add it in front of the token string.
string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
```

U kunt de [Azure Media Services Player](https://aka.ms/azuremediaplayer) gebruiken om uw stream te testen.

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkelomgeving in en vul in het bestand app.config de verbindingsinformatie in, zoals beschreven in [Media Services ontwikkelen met .NET](media-services-dotnet-how-to-use.md).

2. Voeg de volgende elementen toe aan **appSettings** dat in het bestand app.config is gedefinieerd:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Voorbeeld

Het volgende voorbeeld demonstreert de functionaliteit die is geïntroduceerd in de Media Services-SDK voor .NET versie 3.5.2. (Deze bevat met name de mogelijkheid om een Widevine-licentiesjabloon te definiëren en een Widevine-licentie aan te vragen bij Media Services.)

Overschrijf de code in uw Program.cs-bestand met de code die wordt weergegeven in deze sectie.

>[!NOTE]
>Er geldt een limiet van 1 miljoen beleidsregels voor verschillende Media Services-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-id als u altijd dezelfde dagen/toegangsmachtigingen gebruikt. Een voorbeeld is een beleid voor locators die zijn bedoeld om aanwezig te blijven gedurende een lange periode (niet-uploadbeleidsregels). 

Zie [Manage assets and related entities with the Media Services .NET SDK](media-services-dotnet-manage-entities.md#limit-access-policies) (Assets en gerelateerde entiteiten beheren met de Media Services .NET-SDK).

Zorg ervoor dat variabelen zo worden bijgewerkt dat ze verwijzen naar de mappen waar uw invoerbestanden zich bevinden.

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.IO;
using System.Linq;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
using Microsoft.WindowsAzure.MediaServices.Client.Widevine;
using Newtonsoft.Json;

namespace DynamicEncryptionWithDRM
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly Uri _sampleIssuer =
            new Uri(ConfigurationManager.AppSettings["Issuer"]);
        private static readonly Uri _sampleAudience =
            new Uri(ConfigurationManager.AppSettings["Audience"]);

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            bool tokenRestriction = false;
            string tokenTemplateString = null;

            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
            Console.WriteLine("Uploaded asset: {0}", asset.Id);

            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);

            IContentKey key = CreateCommonTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("PlayReady License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense));
            Console.WriteLine();

            if (tokenRestriction)
                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
            else
                AddOpenAuthorizationPolicy(key);

            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
            Console.WriteLine();

            CreateAssetDeliveryPolicy(encodedAsset, key);
            Console.WriteLine("Created asset delivery policy. \n");
            Console.WriteLine();

            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
            {
                // Deserializes a string containing an XML representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note that you need to pass the key ID GUID because 
                // TokenClaim.ContentKeyIdentifierClaim was specified during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            // You can use the https://amsplayer.azurewebsites.net/azuremediaplayer.html player to test streams.
            // Note that DASH works on Internet Explorer 11 (via PlayReady), Microsoft Edge (via PlayReady), and Chrome (via Widevine).

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted DASH URL: {0}/manifest(format=mpd-time-csf)", url);

            Console.ReadLine();
        }

        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
        {
            if (!File.Exists(singleFilePath))
            {
                Console.WriteLine("File does not exist.");
                return null;
            }

            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return inputAsset;
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
        {
            var encodingPreset = "Adaptive Streaming";

            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
                        inputAsset.Name,
                        encodingPreset));

            var mediaProcessors =
            _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();

            var latestMediaProcessor =
            mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();

            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
            encodeTask.InputAssets.Add(inputAsset);
            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), AssetCreationOptions.StorageEncrypted);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }


        static public IContentKey CreateCommonTypeContentKey(IAsset asset)
        {

            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryption);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }

        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
        {

            // Create ContentKeyAuthorizationPolicy with open restrictions
            // and create an authorization policy.         

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Open",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                    Requirements = null
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with no restrictions").
                Result;


            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);
            // Associate the content key authorization policy with the content key.
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;
        }

        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
        {
            string tokenTemplateString = GenerateTokenRequirements();

            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
                {
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString,
                }
                };

            // Configure PlayReady and Widevine license templates.
            string PlayReadyLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

            string WidevineLicenseTemplate = ConfigureWidevineLicenseTemplate();

            IContentKeyAuthorizationPolicyOption PlayReadyPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                restrictions, PlayReadyLicenseTemplate);

            IContentKeyAuthorizationPolicyOption WidevinePolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.Widevine,
                restrictions, WidevineLicenseTemplate);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(PlayReadyPolicy);
            contentKeyAuthorizationPolicy.Options.Add(WidevinePolicy);

            // Associate the content key authorization policy with the content key.
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
            // The following code configures the PlayReady license template by using .NET classes
            // and returns the XML string.

            //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user.
            //It contains a field for a custom data string between the license server and the application
            //(which might be useful for custom app logic) as well as a list of one or more license templates.
            PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

            // The PlayReadyLicenseTemplate class represents a license template you can use to create PlayReady licenses
            // to be returned to end users.
            //It contains the data on the content key in the license and any rights or restrictions to be
            //enforced by the PlayReady DRM runtime when you use the content key.
            PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
            //Configure whether the license is persistent (saved in persistent storage on the client)
            //or nonpersistent (held in memory only while the player uses the license).  
            licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

            // AllowTestDevices controls whether test devices can use the license or not.  
            // If true, the MinimumSecurityLevel property of the license
            // is set to 150. If false (the default), the MinimumSecurityLevel property of the license is set to 2,000.
            licenseTemplate.AllowTestDevices = true;

            // You also can configure the PlayRight in the PlayReady license by using the PlayReadyPlayRight class.
            // It grants the user the ability to play back the content subject to the zero or more restrictions
            // configured in the license and on the PlayRight itself (for playback-specific policy).
            // Much of the policy on the PlayRight has to do with output restrictions,
            // which control the types of outputs that the content can be played over and
            // any restrictions that must be put in place when you use a given output.
            // For example, if DigitalVideoOnlyContentRestriction is enabled,
            // the DRM runtime allows the video to be displayed only over digital outputs
            //(analog video outputs aren't allowed to pass the content).

            //IMPORTANT: These types of restrictions can be very powerful but also can affect the consumer experience.
            // If output protections are too restrictive, 
            // content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

            // For example:
            //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

            responseTemplate.LicenseTemplates.Add(licenseTemplate);

            return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
        }

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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            // Get the PlayReady license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

            // GetKeyDeliveryUrl for Widevine attaches the KID to the URL.
            // For example: https://amsaccount1.keydelivery.mediaservices.windows.net/Widevine/?KID=268a6dcb-18c8-4648-8c95-f46429e4927c.  
            // WidevineBaseLicenseAcquisitionUrl (used in the following) also tells dynamic encryption
            // to append /? KID =< keyId > to the end of the URL when you create the manifest.
            // As a result, the Widevine license acquisition URL has the KID appended twice,
            // so you need to remove the KID in the URL when you call GetKeyDeliveryUrl.

            Uri widevineUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);
            UriBuilder uriBuilder = new UriBuilder(widevineUrl);
            uriBuilder.Query = String.Empty;
            widevineUrl = uriBuilder.Uri;

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
                    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl, widevineUrl.ToString()}

            };

            // In this case, we specify only the DASH streaming protocol in the delivery policy.
            // All other protocols are blocked from streaming.
            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


            // Add AssetDelivery Policy to the asset.
            asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        }

        /// <summary>
        /// Gets the streaming origin locator.
        /// </summary>
        /// <param name="assets"></param>
        /// <returns></returns>
        static public string GetStreamingOriginLocator(IAsset asset)
        {

            // Get a reference to the streaming manifest file from the 
            // collection of files in the asset.

            var assetFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day read-only access policy.
            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

            // Create a locator to the streaming content on an origin.
            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

            // Create a URL to the manifest file.
            return originLocator.Path + assetFile.Name;
        }

        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
            ((IJob)sender).Name,
            e.CurrentState,
            DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
        }

        static private byte[] GetRandomBuffer(int length)
        {
            var returnValue = new byte[length];

            using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
            {
                rng.GetBytes(returnValue);
            }

            return returnValue;
        }
    }
}
```

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook

* [De CENC gebruiken met Multi-DRM en toegangsbeheer](media-services-cenc-with-multidrm-access-control.md)
* [Widevine-verpakking configureren met Media Services](https://mingfeiy.com/how-to-configure-widevine-packaging-with-azure-media-services)
* [Aan de slag met de SDK voor Java-clients voor Azure Media Services](./media-services-java-how-to-use.md)
* Als u de nieuwste PHP-SDK voor Media Services wilt downloaden, zoekt u versie 0.5.7 van het Microsoft/WindowsAzure-pakket in de [Packagist-opslagplaats](https://packagist.org/packages/microsoft/windowsazure#v0.5.7). 
