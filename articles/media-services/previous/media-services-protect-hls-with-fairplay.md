---
title: HLS-inhoud beveiligen met micro soft PlayReady of Apple FairPlay-Azure | Microsoft Docs
description: Dit onderwerp bevat een overzicht en laat zien hoe u Azure Media Services kunt gebruiken om uw HTTP Live Streaming-inhoud (HLS) dynamisch te versleutelen met Apple FairPlay. U ziet ook hoe u de Media Services License delivery service kunt gebruiken om FairPlay-licenties te leveren aan clients.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 15aab28b7dfbaf305412f1080346b54cc6827437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103009637"
---
# <a name="protect-your-hls-content-with-apple-fairplay-or-microsoft-playready"></a>Uw HLS-inhoud beschermen met Apple FairPlay of micro soft PlayReady

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.   > er geen nieuwe functies of functionaliteit aan Media Services v2 worden toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Met Azure Media Services kunt u uw HTTP Live Streaming-inhoud (HLS) dynamisch versleutelen met behulp van de volgende indelingen:  

* **AES-128-envelop lege sleutel**

    De volledige chunk wordt versleuteld met behulp van de modus **AES-128 CBC** . De ontsleuteling van de stroom wordt ondersteund door iOS en OS X Player systeem eigen. Zie [using AES-128 Dynamic Encryption and key delivery service](media-services-protect-with-aes128.md)(Engelstalig) voor meer informatie.
* **Apple FairPlay**

    De afzonderlijke video-en audio voorbeelden worden versleuteld met behulp van de modus **AES-128 CBC** . **Fairplay streaming** (fps) is geïntegreerd in de besturings systemen van het apparaat, met systeem eigen ondersteuning voor IOS en Apple TV. Safari op OS X maakt FPS mogelijk met behulp van de EME-interface ondersteuning (Encrypted media Extensions).
* **Micro soft PlayReady**

De volgende afbeelding toont de **dynamische versleutelings werk stroom HLS + Fairplay of PlayReady** .

![Diagram van de werk stroom voor dynamische versleuteling](./media/media-services-content-protection-overview/media-services-content-protection-with-FairPlay.png)

In dit artikel wordt beschreven hoe u Media Services kunt gebruiken om uw HLS-inhoud dynamisch te versleutelen met Apple FairPlay. U ziet ook hoe u de Media Services License delivery service kunt gebruiken om FairPlay-licenties te leveren aan clients.

> [!NOTE]
> Als u uw HLS-inhoud ook wilt versleutelen met PlayReady, moet u een gemeen schappelijke inhouds sleutel maken en deze koppelen aan uw asset. U moet ook het autorisatie beleid voor de inhouds sleutel configureren, zoals beschreven in [using Dynamic common Encryption](media-services-protect-with-playready-widevine.md).
>
>

## <a name="requirements-and-considerations"></a>Vereisten en overwegingen

Het volgende is vereist wanneer u Media Services gebruikt om HLS te leveren die zijn versleuteld met FairPlay en om FairPlay-licenties te leveren:

  * Een Azure-account. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)voor meer informatie.
  * Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](media-services-portal-create-account.md)om er een te maken.
  * Meld u aan met het [Apple-ontwikkel programma](https://developer.apple.com/).
  * Apple vereist dat de eigenaar van de inhoud het [implementatie pakket](https://developer.apple.com/contact/fps/)kan verkrijgen. Status die u al hebt geïmplementeerd met Media Services en dat u het uiteindelijke FPS-pakket aanvraagt. Er zijn instructies in het laatste FPS-pakket voor het genereren van certificering en het verkrijgen van de geheime sleutel van de toepassing (ASK). U gebruikt vragen om FairPlay te configureren.
  * Azure Media Services .NET SDK-versie **3.6.0** of hoger.

De volgende zaken moeten worden ingesteld op Media Services de belangrijkste bezorgings zijde:

  * **App-certificaat (AC)**: dit is een pfx-bestand dat de persoonlijke sleutel bevat. U maakt dit bestand en versleutelt het met een wacht woord.

       Wanneer u een sleutel leverings beleid configureert, moet u dat wacht woord en het pfx-bestand opgeven in Base64-indeling.

      In de volgende stappen wordt beschreven hoe u een pfx-certificaat bestand genereert voor FairPlay:

    1. Installeer OpenSSL van https://slproweb.com/products/Win32OpenSSL.html .

        Ga naar de map waarin het FairPlay-certificaat en andere bestanden die door Apple worden geleverd, zijn.
    2. Voer de volgende opdracht uit via de opdrachtregel. Hiermee wordt het CER-bestand geconverteerd naar een. pem-bestand.

        "C:\OpenSSL-Win32\bin\openssl.exe" x509: Informeer de FairPlay. CER-out FairPlay-out. pem
    3. Voer de volgende opdracht uit via de opdrachtregel. Hiermee converteert u het. pem-bestand naar een. pfx-bestand met de persoonlijke sleutel. Het wacht woord voor het pfx-bestand wordt vervolgens gevraagd door OpenSSL.

        "C:\OpenSSL-Win32\bin\openssl.exe" pkcs12/pfx-Profiel-export FairPlay-out. pfx-INKEY privatekey. pem-in FairPlay-out. pem-Passin file:privatekey-pem-pass.txt
  * **Wacht woord voor app-certificaat**: het wacht woord voor het maken van het pfx-bestand.
  * **Wacht woord-id van het app-certificaat**: u moet het wacht woord uploaden, vergelijkbaar met het uploaden van andere Media Services sleutels. Gebruik de Enum-waarde **ContentKeyType. FairPlayPfxPassword** om de Media Services-id op te halen. Dit is wat ze nodig hebben in de optie voor de sleutel leverings beleid.
  * **IV**: dit is een wille keurige waarde van 16 bytes. De waarde moet overeenkomen met de IV in het beleid voor de levering van activa. U genereert de IV en plaatst deze op beide locaties: het leverings beleid voor assets en de optie voor de sleutel leverings beleid.
  * **Vraag**: deze sleutel wordt ontvangen wanneer u het certificaat genereert met behulp van de Apple-ontwikkelaars Portal. Elk ontwikkel team ontvangt een unieke vraag. Sla een kopie van de vragen op en bewaar deze op een veilige plaats. U moet vragen als FairPlayAsk configureren om later te Media Services.
  * **Ask-id**: deze id wordt opgehaald wanneer u een verzoek uploadt naar Media Services. U moet een vraag uploaden met behulp van de Enum-waarde **ContentKeyType. FairPlayAsk** . Als gevolg hiervan wordt de Media Services-ID geretourneerd. Dit is wat er moet worden gebruikt bij het instellen van de optie voor sleutel leverings beleid.

De volgende zaken moeten worden ingesteld door de FPS-client:

  * **App-certificaat (AC)**: dit is een CER/. der-bestand dat de open bare sleutel bevat die het besturings systeem gebruikt voor het versleutelen van een nettolading. Media Services moet weten wat het is. Dit is vereist voor de speler. De key delivery-service ontsleutelt deze met behulp van de bijbehorende persoonlijke sleutel.

Als u een FairPlay versleutelde stroom wilt afspelen, moet u eerst een echte vraag ontvangen en vervolgens een echt certificaat genereren. Dit proces maakt alle drie de volgende onderdelen:

  * . der-bestanden
  * pfx-bestand
  * wacht woord voor het pfx-

De volgende clients ondersteunen HLS met **AES-128 CBC-** versleuteling: Safari op OS X, Apple TV, Ios.

## <a name="configure-fairplay-dynamic-encryption-and-license-delivery-services"></a>Dynamische versleuteling van FairPlay en services voor het leveren van licenties configureren
Hieronder vindt u algemene stappen voor het beveiligen van uw assets met FairPlay door gebruik te maken van de Media Services License delivery service, en ook door dynamische versleuteling te gebruiken.

1. Maak een asset en upload bestanden in de asset.
2. Codeer de asset die het bestand bevat naar de Adaptive Bitrate MP4-set.
3. Maak een inhoudssleutel en koppel deze aan de gecodeerde asset.  
4. Configureer het autorisatiebeleid voor de inhoudssleutel. Geef het volgende op:

   * De leverings methode (in dit geval FairPlay).
   * Configuratie van FairPlay-beleids opties. Zie de methode **ConfigureFairPlayPolicyOptions ()** in het onderstaande voor beeld voor meer informatie over het configureren van Fairplay.

     > [!NOTE]
     > Normaal gesp roken wilt u FairPlay-beleids opties slechts één keer configureren, omdat u slechts één set van een certificering en een vraag hebt.
     >
     >
   * Beperkingen (openen of Token).
   * Informatie die specifiek is voor het type sleutel levering dat definieert hoe de sleutel aan de client wordt geleverd.
5. Configureer het leverings beleid voor assets. De configuratie van het leverings beleid omvat:

   * Het Delivery Protocol (HLS).
   * Het type dynamische versleuteling (algemene CBC-versleuteling).
   * De licentie verwervings-URL.

     > [!NOTE]
     > Als u een stroom wilt leveren die is versleuteld met FairPlay en een ander Digital Rights Management (DRM)-systeem, moet u een afzonderlijk leverings beleid configureren:
     >
     > * Eén IAssetDeliveryPolicy voor het configureren van dynamisch adaptief streamen via HTTP (DASH) met Common Encryption (CENC) (PlayReady + Widevine) en Smooth met PlayReady
     > * Een andere IAssetDeliveryPolicy voor het configureren van FairPlay voor HLS
     >
     >
6. Maak een OnDemand-locator om een streaming-URL te verkrijgen.

## <a name="use-fairplay-key-delivery-by-player-apps"></a>FairPlay-sleutel levering gebruiken met apps van de speler
U kunt speler-apps ontwikkelen met behulp van de iOS-SDK. Als u FairPlay-inhoud wilt afspelen, moet u het License Exchange-protocol implementeren. Dit protocol is niet opgegeven door Apple. Het is aan elke app voor het verzenden van aanvragen voor sleutel levering. De Media Services FairPlay key delivery service verwacht dat SPC als een www-form-URL gecodeerd post bericht wordt gegeven in de volgende vorm:

`spc=<Base64 encoded SPC>`

> [!NOTE]
> Azure Media Player ondersteunt het afspelen van FairPlay. Raadpleeg de [documentatie van Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) voor meer informatie.
>
>

## <a name="streaming-urls"></a>Streaming-Url's
Als uw asset is versleuteld met meer dan één DRM-bestand, gebruikt u een coderings code in de streaming-URL: (Format = ' 3u8-AAPL ', Encryption = ' xxx ').

De volgende overwegingen zijn van toepassing:

* Er kan slechts nul of één versleutelings type worden opgegeven.
* Het versleutelings type hoeft niet te worden opgegeven in de URL als er slechts één versleuteling is toegepast op de Asset.
* Het versleutelings type is niet hoofdletter gevoelig.
* De volgende versleutelings typen kunnen worden opgegeven:  
  * **Cenc**: common Encryption (PlayReady of Widevine)
  * **cbcs-AAPL**: Fairplay
  * **CBC**: AES-envelop versleuteling

## <a name="create-and-configure-a-visual-studio-project"></a>Maak en configureer een Visual Studio-project.

1. Stel uw ontwikkel omgeving in en vul het app.config bestand in met verbindings informatie, zoals beschreven in [Media Services ontwikkeling met .net](media-services-dotnet-how-to-use.md). 
2. Voeg de volgende elementen toe aan **appSettings** dat in het bestand app.config is gedefinieerd:

    ```xml
    <add key="Issuer" value="http://testissuer.com"/>
    <add key="Audience" value="urn:test"/>
    ```

## <a name="example"></a>Voorbeeld

In het volgende voor beeld ziet u de mogelijkheid om Media Services te gebruiken voor het leveren van uw inhoud die is versleuteld met FairPlay. Deze functionaliteit is geïntroduceerd in de Azure Media Services SDK voor .NET versie 3.6.0. 

Overschrijf de code in uw Program.cs-bestand met de code die wordt weergegeven in deze sectie.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

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
using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
using Newtonsoft.Json;
using System.Security.Cryptography.X509Certificates;

namespace DynamicEncryptionWithFairPlay
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

            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
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
                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
                // back into a TokenRestrictionTemplate class instance.
                TokenRestrictionTemplate tokenTemplate =
                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

                // Generate a test token based on the data in the given TokenRestrictionTemplate.
                // Note, you need to pass the key id Guid because we specified
                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
                                            DateTime.UtcNow.AddDays(365));
                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
                Console.WriteLine();
            }

            string url = GetStreamingOriginLocator(encodedAsset);
            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);

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

            IJob job = _context.Jobs.Create(String.Format("Encoding {0}", inputAsset.Name));

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

        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
        {
            // Create HLS SAMPLE AES encryption content key
            Guid keyId = Guid.NewGuid();
            byte[] contentKey = GetRandomBuffer(16);

            IContentKey key = _context.ContentKeys.Create(
                        keyId,
                        contentKey,
                        "ContentKey",
                        ContentKeyType.CommonEncryptionCbcs);

            // Associate the key with the asset.
            asset.ContentKeys.Add(key);

            return key;
        }


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


            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();

            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
            ContentKeyDeliveryType.FairPlay,
            restrictions,
            FairPlayConfiguration);


            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with no restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

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

            // Configure FairPlay policy option.
            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();


            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                   ContentKeyDeliveryType.FairPlay,
                   restrictions,
                   FairPlayConfiguration);

            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                ContentKeyAuthorizationPolicies.
                CreateAsync("Deliver Common CBC Content Key with token restrictions").
                Result;

            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);

            // Associate the content key authorization policy with the content key
            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
            contentKey = contentKey.UpdateAsync().Result;

            return tokenTemplateString;
        }

        private static string ConfigureFairPlayPolicyOptions()
        {
            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK.
            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
            byte[] askBytes = Guid.NewGuid().ToByteArray();
            var askId = Guid.NewGuid();
            // Key delivery retrieves askKey by askId and uses this key to generate the response.
            IContentKey askKey = _context.ContentKeys.Create(
                        askId,
                        askBytes,
                        "askKey",
                        ContentKeyType.FairPlayASk);

            //Customer password for creating the .pfx file.
            string pfxPassword = "<customer password for creating the .pfx file>";
            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
            var pfxPasswordId = Guid.NewGuid();
            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
                        pfxPasswordId,
                        pfxPasswordBytes,
                        "pfxPasswordKey",
                        ContentKeyType.FairPlayPfxPassword);

            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
            byte[] iv = Guid.NewGuid().ToByteArray();

            //Specify the .pfx file created by the customer.
            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);

            string FairPlayConfiguration =
            Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
                appCert,
                pfxPassword,
                pfxPasswordId,
                askId,
                iv);

            return FairPlayConfiguration;
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

        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
        {
            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();

            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);

            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);

            // Get the FairPlay license service URL.
            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);

            // The reason the below code replaces "https://" with "skd://" is because
            // in the IOS player sample code which you obtained in Apple developer account,
            // the player only recognizes a Key URL that starts with skd://.
            // However, if you are using a customized player,
            // you can choose whatever protocol you want.
            // For example, "https".

            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
            {
                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
            };

            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
            AssetDeliveryProtocol.HLS,
            assetDeliveryPolicyConfiguration);

            // Add AssetDelivery Policy to the asset
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

            var assetFile = asset.AssetFiles.LoList().Where(f => f.Name.ToLower().
                         EndsWith(".ism")).
                         FirstOrDefault();

            // Create a 30-day readonly access policy.
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

## <a name="next-steps-media-services-learning-paths"></a>Volgende stappen: Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
