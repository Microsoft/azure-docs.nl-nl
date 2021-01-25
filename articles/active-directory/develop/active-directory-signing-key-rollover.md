---
title: Rollover van de handtekening sleutel in het micro soft Identity-platform
description: In dit artikel worden de aanbevolen procedures voor het door voeren van de handtekening sleutel voor Azure Active Directory beschreven.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: bd2bd67774eb55051e55e4433984c0fd1fda5240
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98755574"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Rollover van de handtekening sleutel in het micro soft Identity-platform
In dit artikel wordt beschreven wat u moet weten over de open bare sleutels die worden gebruikt door het micro soft Identity-platform voor het ondertekenen van beveiligings tokens. Het is belang rijk te weten dat deze sleutels periodiek worden doorgevoerd en kan in een nood geval direct worden doorgevoerd. Alle toepassingen die gebruikmaken van het micro soft-identiteits platform moeten het proces voor sleutel rollover programmatisch kunnen afhandelen. Ga verder met lezen om te begrijpen hoe de sleutels werken, hoe u de invloed van de rollover op uw toepassing kunt beoordelen en hoe u uw toepassing kunt bijwerken of een periodiek hand matig rollover proces kunt instellen voor het afhandelen van Key rollover, indien nodig.

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Overzicht van ondertekeningssleutels in het micro soft Identity-platform
Het micro soft Identity-platform maakt gebruik van open bare-sleutel cryptografie die is gebaseerd op industrie normen om een vertrouwens relatie tussen zichzelf en de toepassingen die deze gebruiken te maken. In de praktijk werkt dit op de volgende manier: het micro soft Identity-platform gebruikt een handtekening sleutel die bestaat uit een openbaar en een persoonlijk sleutel paar. Wanneer een gebruiker zich aanmeldt bij een toepassing die gebruikmaakt van het micro soft-identiteits platform voor verificatie, maakt het micro soft Identity-platform een beveiligings token dat informatie over de gebruiker bevat. Dit token is ondertekend door het micro soft Identity-platform met behulp van de bijbehorende persoonlijke sleutel voordat deze wordt teruggestuurd naar de toepassing. Om te controleren of het token geldig is en afkomstig is van micro soft Identity platform, moet de toepassing de hand tekening van het token valideren met behulp van de open bare sleutels die zijn opgenomen in het micro soft-identiteits platform dat zich bevindt in het [OpenID Connect Connect-detectie document](https://openid.net/specs/openid-connect-discovery-1_0.html) van de Tenant of het SAML/WS-inrichtings [document met federatieve meta gegevens](../azuread-dev/azure-ad-federation-metadata.md).

Uit veiligheids overwegingen wordt de handtekening sleutel van het micro soft Identity-platform periodiek getotaliseerd en, in het geval van nood, direct kan worden doorgevoerd. Er is geen set of gegarandeerde tijd tussen deze sleutel rollen: elke toepassing die met het micro soft-identiteits platform wordt geïntegreerd, moet worden voor bereid om een sleutel rollover gebeurtenis te verwerken, ongeacht hoe vaak deze kan optreden. Als dat niet het geval is, zal de aanmeldings aanvraag mislukken als uw toepassing een verlopen sleutel probeert te gebruiken om de hand tekening op een token te verifiëren.  Controleren elke 24 uur op updates is een best practice, met een beperking (één keer per vijf minuten), waarmee het sleutel document onmiddellijk wordt vernieuwd als er een token met een onbekende sleutel-id wordt aangetroffen. 

Er is altijd meer dan één geldige sleutel beschikbaar in het OpenID Connect Connect Discovery-document en het federatieve meta gegevens document. Uw toepassing moet worden voor bereid voor het gebruik van alle sleutels die zijn opgegeven in het document, omdat een sleutel binnenkort kan worden samengevouwen, een andere kan worden vervangen, enzovoort.  Het aantal aanwezige sleutels kan in de loop van de tijd worden gewijzigd op basis van de interne architectuur van het micro soft Identity-platform als we nieuwe platforms, nieuwe Clouds of nieuwe verificatie protocollen ondersteunen. De volg orde van de sleutels in het JSON-antwoord en de volg orde waarin ze zijn weer gegeven, moet worden beschouwd als Meaninful voor uw app. 

Toepassingen die slechts één ondertekeningssleutel ondersteunen of die hand matig moeten worden bijgewerkt naar de ondertekeningssleutels, zijn inherent minder veilig en betrouwbaar.  Ze moeten worden bijgewerkt voor het gebruik van [standaard bibliotheken](reference-v2-libraries.md) om ervoor te zorgen dat ze altijd up-to-date-handtekeningen sleutels gebruiken, onder andere aanbevolen procedures. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Controleren of uw toepassing wordt beïnvloed en wat er gebeurt
Hoe uw toepassing de sleutel rollover afhandelt, is afhankelijk van variabelen zoals het type toepassing of het id-protocol en de bibliotheek die zijn gebruikt. In de volgende secties wordt beoordeeld of de meest voorkomende typen toepassingen worden beïnvloed door de sleutel rollover en wordt uitgelegd hoe u de toepassing bijwerkt ter ondersteuning van automatische rollover of de sleutel hand matig bijwerkt.

* [Systeem eigen client toepassingen die bronnen gebruiken](#nativeclient)
* [Webtoepassingen/Api's die bronnen gebruiken](#webclient)
* [Webtoepassingen/Api's die resources beveiligen en gebouwd met behulp van Azure-app Services](#appservices)
* [Webtoepassingen/Api's die resources beveiligen met behulp van .NET OWIN OpenID Connect Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [Webtoepassingen/Api's die resources beveiligen met behulp van .NET core OpenID Connect Connect of JwtBearerAuthentication middleware](#owincore)
* [Webtoepassingen/Api's die resources beveiligen met behulp van Node.js Pass Port-Azure-ad-module](#passport)
* [Webtoepassingen/Api's die resources beveiligen en zijn gemaakt met Visual Studio 2015 of hoger](#vs2015)
* [Webtoepassingen die bronnen beveiligen en maken met Visual Studio 2013](#vs2013)
* Web-Api's die bronnen beveiligen en worden gemaakt met Visual Studio 2013
* [Webtoepassingen die bronnen beveiligen en maken met Visual Studio 2012](#vs2012)
* [Webtoepassingen die bronnen beveiligen en die zijn gemaakt met Visual Studio 2010, 2008 o met behulp van Windows Identity Foundation](#vs2010)
* [Webtoepassingen/Api's die resources beveiligen met behulp van andere bibliotheken of hand matig een van de ondersteunde protocollen implementeren](#other)

Deze richt lijnen zijn **niet** van toepassing op:

* Toepassingen die zijn toegevoegd vanuit de Azure AD-toepassings galerie (met inbegrip van aangepaste), hebben afzonderlijke richt lijnen met betrekking tot het ondertekenen van sleutels. [Meer informatie.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* On-premises toepassingen die zijn gepubliceerd via toepassings proxy, hoeven zich geen zorgen te maken over handtekening sleutels.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Systeem eigen client toepassingen die bronnen gebruiken
Toepassingen die alleen toegang hebben tot resources (dat wil zeggen Microsoft Graph, sleutel kluis, Outlook API en andere micro soft-Api's), wordt in het algemeen alleen een token opgehaald en door gegeven aan de eigenaar van de resource. Gezien het niet beveiligen van resources, inspecteert ze niet het token en hoeven ze niet te controleren of ze correct zijn ondertekend.

Systeem eigen client toepassingen, of desktop of Mobile, vallen in deze categorie en worden dus niet beïnvloed door de rollover.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Webtoepassingen/Api's die bronnen gebruiken
Toepassingen die alleen toegang hebben tot resources (dat wil zeggen Microsoft Graph, sleutel kluis, Outlook API en andere micro soft-Api's), wordt in het algemeen alleen een token opgehaald en door gegeven aan de eigenaar van de resource. Gezien het niet beveiligen van resources, inspecteert ze niet het token en hoeven ze niet te controleren of ze correct zijn ondertekend.

Webtoepassingen en Web-Api's die gebruikmaken van de app-only-stroom (client referenties/client certificaat) om tokens aan te vragen, vallen in deze categorie en worden daarom niet beïnvloed door de rollover.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Webtoepassingen/Api's die resources beveiligen en gebouwd met behulp van Azure-app Services
De functie voor verificatie/autorisatie van Azure-app Services (EasyAuth) heeft al de benodigde logica voor het automatisch verwerken van de sleutel rollover.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Webtoepassingen/Api's die resources beveiligen met behulp van .NET OWIN OpenID Connect Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication middleware
Als uw toepassing gebruikmaakt van de .NET OWIN OpenID Connect Connect, WS-Fed of WindowsAzureActiveDirectoryBearerAuthentication middleware, beschikt deze al over de benodigde logica om de sleutel rollover automatisch te verwerken.

U kunt controleren of uw toepassing een van deze gebruikt door te zoeken naar een van de volgende fragmenten in de Startup.cs-of Startup.Auth.cs-bestanden van uw toepassing.

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Webtoepassingen/Api's die resources beveiligen met behulp van .NET core OpenID Connect Connect of JwtBearerAuthentication middleware
Als uw toepassing gebruikmaakt van .NET core OWIN OpenID Connect Connect of JwtBearerAuthentication middleware, beschikt deze al over de benodigde logica om de sleutel rollover automatisch te verwerken.

U kunt controleren of uw toepassing een van deze gebruikt door te zoeken naar een van de volgende fragmenten in de Startup.cs of Startup.Auth.cs van uw toepassing

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Webtoepassingen/Api's die resources beveiligen met behulp van Node.js Pass Port-Azure-ad-module
Als uw toepassing gebruikmaakt van de Node.js Pass Port-ad-module, beschikt deze al over de benodigde logica voor het automatisch verwerken van de sleutel rollover.

U kunt controleren of uw toepassings-Pass Port-AD naar het volgende code fragment in de app.js van uw toepassing zoeken

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Webtoepassingen/Api's die resources beveiligen en zijn gemaakt met Visual Studio 2015 of hoger
Als uw toepassing is gemaakt met behulp van een sjabloon voor webtoepassingen in Visual Studio 2015 of hoger en u hebt **werk-of school accounts** geselecteerd in het menu **authenticatie wijzigen** , beschikt u al over de benodigde logica om de sleutel rollover automatisch te verwerken. Met deze logica, die is inge sloten in de OWIN OpenID Connect Connect, worden de sleutels opgehaald en opgeslagen in de cache van het OpenID Connect Connect-detectie document en worden ze regel matig vernieuwd.

Als u hand matig verificatie aan uw oplossing hebt toegevoegd, heeft uw toepassing mogelijk niet de benodigde sleutel rollover logica. U moet zelf schrijven of u kunt de stappen in [webtoepassingen/api's gebruiken met andere bibliotheken of hand matig een van de ondersteunde protocollen implementeren](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Webtoepassingen die bronnen beveiligen en maken met Visual Studio 2013
Als uw toepassing is gemaakt met behulp van een sjabloon voor webtoepassingen in Visual Studio 2013 en u **organisatie accounts** hebt geselecteerd in het menu **authenticatie wijzigen** , beschikt deze al over de benodigde logica om de sleutel rollover automatisch te verwerken. Deze logica slaat de unieke id van uw organisatie en de informatie over de handtekening sleutel op in twee database tabellen die zijn gekoppeld aan het project. U kunt de connection string voor de data base vinden in het Web.config-bestand van het project.

Als u hand matig verificatie aan uw oplossing hebt toegevoegd, heeft uw toepassing mogelijk niet de benodigde sleutel rollover logica. U moet zelf schrijven of u kunt de stappen in [Web Applications/api's gebruiken met andere bibliotheken of hand matig een van de ondersteunde protocollen implementeren.](#other)

Met de volgende stappen kunt u controleren of de logica goed werkt in uw toepassing.

1. Open de oplossing in Visual Studio 2013 en klik vervolgens op het tabblad **Server Explorer** in het rechter venster.
2. Vouw **gegevens verbindingen**, **DefaultConnection** en **tabellen** uit. Zoek de tabel **IssuingAuthorityKeys** , klik er met de rechter muisknop op en klik vervolgens op **tabel gegevens weer geven**.
3. In de tabel **IssuingAuthorityKeys** is er ten minste één rij die overeenkomt met de vingerafdruk waarde voor de sleutel. Alle rijen in de tabel verwijderen.
4. Klik met de rechter muisknop op de tabel **tenants** en klik vervolgens op **tabel gegevens weer geven**.
5. In de tabel **tenants** is er ten minste één rij die overeenkomt met een unieke Directory-Tenant-id. Alle rijen in de tabel verwijderen. Als u de rijen in de tabel **tenants** en **IssuingAuthorityKeys** niet verwijdert, krijgt u tijdens runtime een fout melding.
6. Maak de toepassing en voer deze uit. Nadat u bent aangemeld bij uw account, kunt u de toepassing stoppen.
7. Ga terug naar de **Server Explorer** en Bekijk de waarden in de tabel **IssuingAuthorityKeys** en **tenants** . U ziet dat ze automatisch opnieuw zijn ingevuld met de juiste gegevens uit het federatieve meta gegevens document.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Web-Api's die bronnen beveiligen en worden gemaakt met Visual Studio 2013
Als u in Visual Studio 2013 een web API-toepassing hebt gemaakt met behulp van de Web-API-sjabloon en vervolgens **organisatie accounts** hebt geselecteerd in het menu **verificatie wijzigen** , hebt u al de benodigde logica in uw toepassing.

Als u verificatie hand matig hebt geconfigureerd, volgt u de onderstaande instructies om te leren hoe u uw web-API kunt configureren om de bijbehorende sleutel gegevens automatisch bij te werken.

Het volgende code fragment laat zien hoe u de meest recente sleutels van het document met federatieve meta gegevens ophaalt en vervolgens de [JWT-token-handler](/previous-versions/dotnet/framework/security/json-web-token-handler) gebruikt om het token te valideren. In het code fragment wordt ervan uitgegaan dat u uw eigen cache mechanisme gebruikt voor het persistent maken van de sleutel voor het valideren van toekomstige tokens van het micro soft Identity-platform, ongeacht of het zich in een Data Base, configuratie bestand of ergens anders bevinden.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Webtoepassingen die bronnen beveiligen en maken met Visual Studio 2012
Als uw toepassing is gemaakt in Visual Studio 2012, hebt u waarschijnlijk het hulp programma voor identiteits-en toegangs beheer gebruikt voor het configureren van uw toepassing. Het is ook waarschijnlijk dat u gebruikmaakt van het [valideren van de naam register van de verlener (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). De VINR is verantwoordelijk voor het onderhouden van informatie over vertrouwde id-providers (micro soft Identity platform) en de sleutels die worden gebruikt om tokens te valideren die door hen worden uitgegeven. Met de VINR kunt u de belang rijke informatie die is opgeslagen in een Web.config bestand, ook eenvoudig bijwerken door het meest recente document met federatieve meta gegevens te downloaden dat is gekoppeld aan uw directory, te controleren of de configuratie verouderd is met het nieuwste document en de toepassing zo aan te werken dat deze de nieuwe sleutel nodig heeft.

Als u uw toepassing hebt gemaakt met behulp van een van de code voorbeelden of de overzichts documentatie van micro soft, is de logica voor sleutel rollover al opgenomen in uw project. U ziet dat de onderstaande code al in uw project bestaat. Als uw toepassing deze logica nog niet heeft, volgt u de onderstaande stappen om deze toe te voegen en te controleren of deze correct werkt.

1. In **Solution Explorer** voegt u een verwijzing naar de **System. Identity model** -assembly voor het betreffende project toe.
2. Open het **Global.asax.cs** -bestand en voeg het volgende toe met behulp van de instructies:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Voeg de volgende methode toe aan het **Global.asax.cs** -bestand:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Roep de methode **RefreshValidationSettings ()** aan in de methode **Application_Start ()** in **Global.asax.cs** , zoals weer gegeven:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Zodra u deze stappen hebt gevolgd, wordt de Web.config van uw toepassing bijgewerkt met de meest recente gegevens uit het federatieve meta gegevens document, met inbegrip van de meest recente sleutels. Deze update wordt uitgevoerd telkens wanneer de groep van toepassingen wordt gerecycled in IIS. IIS wordt standaard elke 29 uur ingesteld voor het recyclen van toepassingen.

Volg de onderstaande stappen om te controleren of de sleutel rollover logica werkt.

1. Nadat u hebt gecontroleerd of de bovenstaande code door uw toepassing wordt gebruikt, opent u het **Web.config** bestand en navigeert u naar het blok, en zoekt u naar de **\<issuerNameRegistry>** volgende regels:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. Wijzig in de **\<add thumbprint="">** instelling de waarde van de vinger afdruk door een teken te vervangen door een andere. Sla het **Web.config** bestand op.
3. Bouw de toepassing en voer deze uit. Als u het aanmeld proces kunt volt ooien, wordt de sleutel door uw toepassing bijgewerkt door de vereiste informatie te downloaden uit het federatieve meta gegevens document van uw Directory. Als u problemen ondervindt bij het aanmelden, moet u controleren of de wijzigingen in uw toepassing juist zijn door het artikel [Sign-On toevoegen aan uw webtoepassing met micro soft Identity platform te](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) lezen of het volgende code voorbeeld te downloaden en te controleren: [multi tenant-Cloud toepassing voor Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Webtoepassingen die bronnen beveiligen en zijn gemaakt met Visual Studio 2008 of 2010 en Windows Identity Foundation (WIF) v 1.0 voor .NET 3,5
Als u een toepassing op WIF v 1.0 hebt gemaakt, is er geen mechanisme voor het automatisch vernieuwen van de configuratie van uw toepassing om een nieuwe sleutel te gebruiken.

* *Eenvoudigste manier* Gebruik het FedUtil-hulp programma dat is opgenomen in de WIF-SDK, waarmee het meest recente meta gegevens document kan worden opgehaald en uw configuratie kan worden bijgewerkt.
* Werk uw toepassing bij naar .NET 4,5, die de nieuwste versie van WIF bevat die zich in de systeem naam ruimte bevindt. U kunt vervolgens het [validatie programma naam register (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) gebruiken om automatische updates van de configuratie van de toepassing uit te voeren.
* Voer een hand matige rollover uit conform de instructies aan het einde van dit document.

Instructies voor het gebruik van de FedUtil om uw configuratie bij te werken:

1. Controleer of de WIF v 1.0 SDK is geïnstalleerd op uw ontwikkel computer voor Visual Studio 2008 of 2010. U kunt [deze hier downloaden](https://www.microsoft.com/en-us/download/details.aspx?id=4451) als u deze nog niet hebt geïnstalleerd.
2. Open de oplossing in Visual Studio, klik met de rechter muisknop op het betreffende project en selecteer **federatieve meta gegevens bijwerken**. Als deze optie niet beschikbaar is, is FedUtil en/of de WIF v 1.0 SDK niet geïnstalleerd.
3. Selecteer in de prompt **Update** om te beginnen met het bijwerken van uw federatieve meta gegevens. Als u toegang hebt tot de server omgeving waar de toepassing wordt gehost, kunt u eventueel de [automatische update planner](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))van FedUtil gebruiken.
4. Klik op **volt ooien** om het update proces te volt ooien.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Webtoepassingen/Api's die resources beveiligen met behulp van andere bibliotheken of hand matig een van de ondersteunde protocollen implementeren
Als u een andere bibliotheek gebruikt of een van de ondersteunde protocollen hand matig implementeert, moet u de bibliotheek of uw implementatie controleren om ervoor te zorgen dat de sleutel wordt opgehaald uit het OpenID Connect Connect Discovery-document of het federatieve meta gegevens document. U kunt dit ook controleren door een zoek opdracht in uw code of de code van de bibliotheek uit te voeren voor alle aanroepen naar het OpenID Connect Discovery-document of het federatieve meta gegevens document.

Als de sleutel wordt opgeslagen ergens of hardcoded in uw toepassing, kunt u de sleutel hand matig ophalen en deze bijwerken door een hand matige rollover uit te voeren overeenkomstig de instructies aan het einde van dit document. **Het wordt ten zeerste aangeraden om uw toepassing te verbeteren om automatische rollover te ondersteunen** met behulp van een van de benaderingen in dit artikel om toekomstige onderbrekingen en overhead te voor komen als het micro soft Identity-platform de rollover uitgebracht verhoogt of een nood herstel buiten de band heeft.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Uw toepassing testen om te bepalen of deze wordt beïnvloed
U kunt controleren of uw toepassing automatische sleutel rollover ondersteunt door de scripts te downloaden en de instructies in [deze github-opslag plaats](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) te volgen.

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Hand matige rollover uitvoeren als uw toepassing automatische rollover niet ondersteunt
Als uw toepassing **geen** automatische rollover ondersteunt, moet u een proces opzetten dat regel matig de handtekening sleutels van micro soft Identity platform bewaakt en een hand matige rollover uitvoert. [Deze github-opslag plaats](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) bevat scripts en instructies over hoe u dit doet.