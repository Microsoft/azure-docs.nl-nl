---
title: Service-naar-service-verificatie voor het Azure Key Vault met behulp van .NET
description: Gebruik de bibliotheek micro soft. Azure. Services. AppAuthentication om u te verifiëren bij het Azure Key Vault met behulp van .NET.
keywords: lokale referenties voor Azure Key-kluis verificatie
author: msmbaldwin
services: key-vault
ms.author: mbaldwin
ms.date: 09/04/2020
ms.topic: how-to
ms.service: key-vault
ms.openlocfilehash: ac3ee108fc63441b2a9381b9e7624631bdca4e5b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998103"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Service-naar-service-verificatie voor het Azure Key Vault met behulp van .NET

> [!NOTE]
> **Micro soft. Azure. Services. AppAuthentication** wordt niet meer aanbevolen voor gebruik met een nieuwe Key Vault-SDK. Het wordt vervangen door een nieuwe **Azure Identity client-bibliotheek** die beschikbaar is voor .net, Java, type script en python en moet worden gebruikt voor alle nieuwe ontwikkeling. Meer informatie vindt u hier: [verificatie voor het Key Vault van code](./developers-guide.md#azure-identity-client-libraries).

Als u zich wilt verifiëren bij Azure Key Vault, hebt u een Azure Active Directory (Azure AD)-referentie nodig, ofwel een gedeeld geheim of een certificaat.

Het beheren van dergelijke referenties kan lastig zijn. Het is geneigd om referenties in een app te bundelen door deze op te nemen in de bron-of configuratie bestanden. De `Microsoft.Azure.Services.AppAuthentication` voor .net-bibliotheek vereenvoudigt dit probleem. Het gebruikt de referenties van de ontwikkelaar voor verificatie tijdens de lokale ontwikkeling. Wanneer de oplossing later naar Azure wordt geïmplementeerd, schakelt de bibliotheek automatisch over naar toepassings referenties. Het gebruik van ontwikkelaars referenties tijdens de lokale ontwikkeling is veiliger omdat u geen Azure AD-referenties hoeft te maken of referenties tussen ontwikkel aars moet delen.

De `Microsoft.Azure.Services.AppAuthentication` -bibliotheek beheert de verificatie automatisch, zodat u zich kunt concentreren op uw oplossing in plaats van uw referenties. Het ondersteunt lokale ontwikkeling met geïntegreerde micro soft Visual Studio, Azure CLI of Azure AD Integrated-verificatie. Wanneer het wordt geïmplementeerd in een Azure-resource die een beheerde identiteit ondersteunt, gebruikt de bibliotheek automatisch [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md). Er zijn geen code-of configuratie wijzigingen vereist. De bibliotheek biedt ook ondersteuning voor direct gebruik van Azure AD- [client referenties](../../active-directory/develop/howto-authenticate-service-principal-powershell.md) wanneer een beheerde identiteit niet beschikbaar is of wanneer de beveiligings context van de ontwikkelaar tijdens de lokale ontwikkeling niet kan worden bepaald.

## <a name="prerequisites"></a>Vereisten

- [Visual studio 2019](https://www.visualstudio.com/downloads/) of [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- De app-verificatie-extensie voor Visual Studio, beschikbaar als een afzonderlijke uitbrei ding voor Visual Studio 2017 Update 5 en gebundeld met het product in update 6 en hoger. Met Update 6 of hoger kunt u de installatie van de extensie voor app-verificatie controleren door Azure-hulpprogram ma's voor ontwikkel aars te selecteren in het installatie programma van Visual Studio.

## <a name="using-the-library"></a>De bibliotheek gebruiken

Voor .NET-toepassingen is de eenvoudigste manier om te werken met een beheerde identiteit via het `Microsoft.Azure.Services.AppAuthentication` pakket. Stappen om aan de slag te gaan:

1. Selecteer **extra**  >  **NuGet package manager**  >  **NuGet-pakketten beheren voor oplossing** om verwijzingen toe te voegen aan de [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) -en [micro soft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-pakketten voor uw project.

1. Voeg de volgende code toe:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

De thread-safe- `AzureServiceTokenProvider` klasse slaat het token in de cache op in het geheugen en haalt dit net vóór de verval datum op uit Azure AD. Dit betekent dat u nooit de verval datum van het token hoeft te controleren voordat u de methode aanroept `GetAccessTokenAsync` . 

De `GetAccessTokenAsync` methode vereist een resource-id. Zie [Wat is beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over Microsoft Azure Services.

## <a name="local-development-authentication"></a>Lokale ontwikkel verificatie

Voor lokale ontwikkeling zijn er twee primaire verificatie scenario's: [verificatie voor Azure-Services](#authenticating-to-azure-services)en [verificatie bij aangepaste services](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Verifiëren bij Azure-Services

Lokale computers bieden geen ondersteuning voor beheerde identiteiten voor Azure-resources. Als gevolg hiervan gebruikt de `Microsoft.Azure.Services.AppAuthentication` bibliotheek uw ontwikkelaars referenties om uit te voeren in uw lokale ontwikkel omgeving. Wanneer de oplossing is geïmplementeerd in azure, gebruikt de bibliotheek een beheerde identiteit om over te scha kelen naar een OAuth 2,0-client referentie toewijzings stroom. Deze aanpak houdt in dat u dezelfde code lokaal en extern kunt testen zonder dat u zich zorgen hoeft te maken.

Voor lokale ontwikkeling worden `AzureServiceTokenProvider` tokens opgehaald met behulp van **Visual Studio**, **Azure Command-Line Interface** (CLI) of **geïntegreerde Azure AD-verificatie**. Elke optie wordt opeenvolgend geprobeerd en de bibliotheek gebruikt de eerste optie die slaagt. Als er geen optie werkt, `AzureServiceTokenProviderException` wordt er een uitzonde ring gegenereerd met gedetailleerde informatie.

#### <a name="authenticating-with-visual-studio"></a>Verifiëren met Visual Studio

Verificatie met behulp van Visual Studio:

1. Meld u aan bij Visual Studio en gebruik **hulpprogram ma's** &nbsp; > &nbsp; **Options** om **Opties** te openen.

1. Selecteer **Azure-service verificatie**, kies een account voor lokale ontwikkeling en selecteer **OK**.

Als u problemen ondervindt met het gebruik van Visual Studio, zoals fouten met betrekking tot het token provider bestand, moet u de voor gaande stappen zorgvuldig door nemen.

Mogelijk moet u uw ontwikkelaars token opnieuw verifiëren. Hiertoe selecteert u **extra** &nbsp; > &nbsp; **Opties** en selecteert u vervolgens **Azure- &nbsp; service &nbsp; verificatie**. Zoek naar een koppeling **opnieuw verifiëren** onder het geselecteerde account. Selecteer deze om te verifiëren.

#### <a name="authenticating-with-azure-cli"></a>Verifiëren met Azure CLI

Als u Azure CLI voor lokale ontwikkeling wilt gebruiken, zorg er dan voor dat u versie [Azure cli v 2.0.12](/cli/azure/install-azure-cli) of hoger hebt.

Azure CLI gebruiken:

1. Zoek in de Windows-taak balk naar Azure CLI om de **Microsoft Azure opdracht prompt** te openen.

1. Meld u aan bij de Azure Portal: *AZ login* om u aan te melden bij Azure.

1. Controleer de toegang door *AZ account Get-access-token--resource https: \/ /Vault.Azure.net* in te voeren. Als er een fout bericht wordt weer gegeven, controleert u of de juiste versie van Azure CLI correct is geïnstalleerd.

   Als Azure CLI niet in de standaard directory is geïnstalleerd, kan er een fout melding worden weer gegeven dat `AzureServiceTokenProvider` het pad voor Azure CLI niet kan worden gevonden. Gebruik de omgevings variabele **AzureCLIPath** om de map voor de Azure cli-installatie te definiëren. `AzureServiceTokenProvider` voegt de map die is opgegeven in de omgevings variabele **AzureCLIPath** toe aan de omgevings variabele **Path** als dat nodig is.

1. Als u bent aangemeld bij Azure CLI met behulp van meerdere accounts of als uw account toegang heeft tot meerdere abonnementen, moet u het te gebruiken abonnement opgeven. Voer de opdracht *AZ account set--subscription <Subscription-id>* in.

Met deze opdracht wordt alleen uitvoer gegenereerd bij een fout. Als u de huidige account instellingen wilt controleren, voert u de opdracht in `az account list` .

#### <a name="authenticating-with-azure-ad-authentication"></a>Verifiëren met Azure AD-verificatie

Als u Azure AD-verificatie wilt gebruiken, controleert u het volgende:

- Uw on-premises Active Directory worden gesynchroniseerd met Azure AD. Zie [Wat is hybride identiteit met Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md)voor meer informatie.

- Uw code wordt uitgevoerd op een computer die lid is van een domein.

### <a name="authenticating-to-custom-services"></a>Verifiëren op aangepaste services

Wanneer een service Azure-Services aanroept, werken de vorige stappen omdat Azure-Services toegang tot zowel gebruikers als toepassingen toestaan.

Bij het maken van een service die een aangepaste service aanroept, gebruikt u Azure AD-client referenties voor lokale ontwikkelings verificatie. Er zijn twee opties:

- Een Service-Principal gebruiken om u aan te melden bij Azure:

    1. Een service-principal maken. Zie [een Azure-service-principal maken met Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

    1. Gebruik Azure CLI om u aan te melden met de volgende opdracht:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Omdat de Service-Principal mogelijk geen toegang tot een abonnement heeft, gebruikt u het `--allow-no-subscriptions` argument.

- Gebruik omgevings variabelen om details van de Service-Principal op te geven. Zie [de toepassing uitvoeren met een Service-Principal](#running-the-application-using-a-service-principal)voor meer informatie.

Nadat u zich hebt aangemeld bij Azure, `AzureServiceTokenProvider` wordt de Service-Principal gebruikt voor het ophalen van een token voor lokale ontwikkeling.

Deze aanpak geldt alleen voor lokale ontwikkeling. Wanneer uw oplossing is geïmplementeerd naar Azure, wordt de bibliotheek overgeschakeld naar een beheerde identiteit voor verificatie.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>De toepassing uitvoeren met beheerde identiteit of door de gebruiker toegewezen identiteit

Wanneer u uw code uitvoert op een Azure App Service of een virtuele machine van Azure waarvoor een beheerde identiteit is ingeschakeld, gebruikt de bibliotheek automatisch de beheerde identiteit. Er zijn geen code wijzigingen vereist, maar de beheerde identiteit moet machtigingen voor de sleutel kluis *krijgen* . U kunt de beheerde identiteit *Get* -machtigingen geven via het *toegangs beleid* van de sleutel kluis.

U kunt ook verifiëren met een door de gebruiker toegewezen identiteit. Zie [informatie over beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)voor meer informatie over door de gebruiker toegewezen identiteiten. Als u wilt verifiëren met een door de gebruiker toegewezen identiteit, moet u de client-ID van de door de gebruiker toegewezen identiteit opgeven in de connection string. De connection string is opgegeven in [ondersteuning voor de verbindings reeks](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>De toepassing uitvoeren met een Service-Principal

Het kan nodig zijn om een Azure AD-client referentie te maken om te verifiëren. Deze situatie kan zich voordoen in de volgende voor beelden:

- Uw code wordt uitgevoerd op een lokale ontwikkel omgeving, maar niet onder de identiteit van de ontwikkelaar. Service Fabric bijvoorbeeld gebruikt het [account Network Service](../../service-fabric/service-fabric-application-secret-management.md) voor lokale ontwikkeling.

- Uw code wordt uitgevoerd in een lokale ontwikkel omgeving en u verifieert bij een aangepaste service, zodat u uw ontwikkelaars identiteit niet kunt gebruiken.

- Uw code wordt uitgevoerd op een Azure Compute-resource die nog geen beheerde identiteiten voor Azure-resources ondersteunt, zoals Azure Batch.

Er zijn drie primaire methoden voor het gebruik van een Service-Principal om uw toepassing uit te voeren. Als u een van deze wilt gebruiken, moet u eerst een service-principal maken. Zie [een Azure-service-principal maken met Azure cli](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Een certificaat in de lokale-opslag plaats gebruiken om u aan te melden bij Azure AD

1. Maak een Service-Principal-certificaat met behulp van de opdracht Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Met deze opdracht maakt u een. pem-bestand (persoonlijke sleutel) dat is opgeslagen in uw basismap. Implementeer dit certificaat in het *LocalMachine* -of het *CurrentUser* -archief.

    > [!Important]
    > De CLI-opdracht genereert een. pem-bestand, maar Windows biedt alleen systeem eigen ondersteuning voor PFX-certificaten. Als u in plaats daarvan een PFX-certificaat wilt genereren, gebruikt u de Power shell-opdrachten die hier worden weer gegeven: een [Service-Principal maken met een zelfondertekend certificaat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Met deze opdrachten wordt het certificaat automatisch geïmplementeerd.

1. Stel een omgevings variabele met de naam **AzureServicesAuthConnectionString** in op de volgende waarde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Vervang *{AppId}*, *{TenantId}* en *{vinger afdruk}* door de waarden die u in stap 1 hebt gegenereerd. Vervang *{CertificateStore}* door *LocalMachine* of *CurrentUser*, op basis van uw implementatie plan.

1. Voer de toepassing uit.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Een gedeelde geheime referentie gebruiken om u aan te melden bij Azure AD

1. Maak een Service-Principal-certificaat met een wacht woord met behulp van de Azure CLI [AZ AD SP create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) opdracht met de para meter--SDK-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Stel een omgevings variabele met de naam **AzureServicesAuthConnectionString** in op de volgende waarde:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Vervang _{AppId}_, _{TenantId}_ en _{ClientSecret}_ door de waarden die u in stap 1 hebt gegenereerd.

1. Voer de toepassing uit.

Als alles goed is ingesteld, zijn er geen verdere code wijzigingen nodig. `AzureServiceTokenProvider` maakt gebruik van de omgevings variabele en het certificaat om te verifiëren bij Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Een certificaat in Key Vault gebruiken om u aan te melden bij Azure AD

Met deze optie kunt u het client certificaat van een Service-Principal opslaan in Key Vault en dit gebruiken voor Service-Principal-verificatie. U kunt deze optie gebruiken voor de volgende scenario's:

- Lokale verificatie, waar u wilt verifiëren met behulp van een expliciete Service-Principal en u de Service-Principal-referenties veilig wilt laten in een sleutel kluis. Het ontwikkelaars account moet toegang hebben tot de sleutel kluis.

- Verificatie van Azure waarbij u een expliciete referentie wilt gebruiken en de Service-Principal-referenties veilig wilt laten in een sleutel kluis. U kunt deze optie gebruiken voor een scenario met meerdere tenants. De beheerde identiteit moet toegang hebben tot de sleutel kluis.

De beheerde identiteit of uw ontwikkelaars identiteit moet gemachtigd zijn om het client certificaat op te halen uit de Key Vault. De AppAuthentication-bibliotheek gebruikt het opgehaalde certificaat als de client referentie van de Service-Principal.

Een client certificaat voor Service-Principal-verificatie gebruiken:

1. Maak een Service-Principal-certificaat en sla dit automatisch op in uw Key Vault. Gebruik de Azure CLI [AZ AD SP create-for-RBAC--sleutel kluis \<keyvaultname> --CERT \<certificatename> --Create-CERT--Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) opdracht:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    De certificaat-id is een URL in de indeling `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Vervang `{KeyVaultCertificateSecretIdentifier}` in dit connection string door de certificaat-id:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Als uw sleutel kluis bijvoorbeeld *myKeyVault* heet en u een certificaat hebt gemaakt met de naam *myCert*, zou de certificaat-id er als volgt uitziet:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Ondersteuning voor verbindings reeks

`AzureServiceTokenProvider`De volgende verificatie methoden worden standaard in de aangegeven volg orde geprobeerd om een token op te halen:

- [Een beheerde identiteit voor Azure-resources](../..//active-directory/managed-identities-azure-resources/overview.md)
- Visual Studio-verificatie
- [Azure CLI-verificatie](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)
- [Geïntegreerde Windows-verificatie](/aspnet/web-api/overview/security/integrated-windows-authentication)

Als u het proces wilt beheren, gebruikt u een connection string dat is door gegeven aan de `AzureServiceTokenProvider` constructor of die is opgegeven in de omgevings variabele *AzureServicesAuthConnectionString* .  De volgende opties worden ondersteund:

| Verbindings reeks optie | Scenario | Opmerkingen|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokale ontwikkeling | `AzureServiceTokenProvider` maakt gebruik van AzureCli om token op te halen. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokale ontwikkeling | `AzureServiceTokenProvider` maakt gebruik van Visual Studio om token op te halen. |
| `RunAs=CurrentUser` | Lokale ontwikkeling | Niet ondersteund in .NET core. `AzureServiceTokenProvider` maakt gebruik van Azure AD Integrated-verificatie om token op te halen. |
| `RunAs=App` | [Beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider` maakt gebruik van een beheerde identiteit voor het ophalen van token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Door de gebruiker toegewezen identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) | `AzureServiceTokenProvider` maakt gebruik van een door de gebruiker toegewezen identiteit voor het ophalen van token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Verificatie van aangepaste services | `KeyVaultCertificateSecretIdentifier` is de geheime id van het certificaat. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Service-principal | `AzureServiceTokenProvider` maakt gebruik van een certificaat om een token van Azure AD op te halen. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Service-principal | `AzureServiceTokenProvider` gebruikt certificaat om token op te halen van Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Service-principal |`AzureServiceTokenProvider` maakt gebruik van een geheim om token van Azure AD op te halen. |

## <a name="samples"></a>Voorbeelden

`Microsoft.Azure.Services.AppAuthentication`Raadpleeg de volgende code voorbeelden voor een overzicht van de tape wisselaar in actie.

- [Een beheerde identiteit gebruiken om tijdens runtime een geheim op te halen uit Azure Key Vault](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Een Azure Resource Manager-sjabloon implementeren vanuit een Azure-VM met een beheerde identiteit](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Gebruik .net core-voor beelden en een beheerde identiteit voor het aanroepen van Azure-Services vanaf een Azure Linux-VM](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>AppAuthentication problemen oplossen

### <a name="common-issues-during-local-development"></a>Veelvoorkomende problemen tijdens de lokale ontwikkeling

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI is niet geïnstalleerd, u bent niet aangemeld of u hebt niet de nieuwste versie

Voer *AZ account Get-access-token* uit om te zien of er voor Azure cli een token voor u wordt weer gegeven. Als **er geen dergelijk programma wordt gevonden**, installeert u de [nieuwste versie van de Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest). U wordt mogelijk gevraagd u aan te melden.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider kan het pad voor de Azure CLI niet vinden

AzureServiceTokenProvider zoekt naar Azure CLI op de standaard installatie locaties. Als Azure CLI niet kan worden gevonden, stelt u de omgevings variabele **AzureCLIPath** in op de installatiemap van Azure cli. Met AzureServiceTokenProvider wordt de omgevings variabele toegevoegd aan de omgevings variabele PATH.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>U bent aangemeld bij Azure CLI met behulp van meerdere accounts, hetzelfde account heeft toegang tot abonnementen in meerdere tenants, of u krijgt een fout bericht over geweigerde toegang wanneer u aanroepen tijdens de lokale ontwikkeling probeert te doen

Stel met behulp van Azure CLI het standaard abonnement in op een met het account dat u wilt gebruiken. Het abonnement moet zich in dezelfde Tenant bevallen als de resource die u wilt openen: **AZ account set--Subscription [abonnement-id]**. Als er geen uitvoer wordt weer gegeven, is deze geslaagd. Controleer of het juiste account is ingesteld op de standaard waarde met **AZ account list**.

### <a name="common-issues-across-environments"></a>Veelvoorkomende problemen in omgevingen

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Onbevoegde toegang, toegang geweigerd, verboden of soort gelijke fout

De gebruikte principal heeft geen toegang tot de resource waartoe deze toegang probeert te krijgen. Verleen uw gebruikers account of het MSI-bestand van de App Service de toegang tot een bron. Welk exemplaar is afhankelijk van het feit of u het voor beeld uitvoert op uw lokale computer of in azure hebt geïmplementeerd op uw App Service. Sommige resources, zoals sleutel kluizen, hebben ook een eigen [toegangs beleid](./secure-your-key-vault.md#data-plane-and-access-policies) dat u kunt gebruiken om toegang te verlenen tot principals, zoals gebruikers, apps en groepen.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Veelvoorkomende problemen bij de implementatie van Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>De beheerde identiteit is niet ingesteld op de App Service

Controleer de omgevings variabelen MSI_ENDPOINT en MSI_SECRET bestaan met behulp van de [kudu-console voor fout opsporing](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Als deze omgevings variabelen niet bestaan, is de beheerde identiteit niet ingeschakeld op de App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Veelvoorkomende problemen bij het lokaal implementeren met IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Kan geen tokens ophalen wanneer de app voor fout opsporing in IIS

AppAuth wordt standaard uitgevoerd in een andere gebruikers context in IIS. Daarom heeft het geen toegang tot het gebruik van uw ontwikkelaars identiteit voor het ophalen van toegangs tokens. U kunt IIS zo configureren dat deze wordt uitgevoerd met uw gebruikers context, met de volgende twee stappen:
- Configureer de groep van toepassingen voor de web-app die moet worden uitgevoerd als uw huidige gebruikers account. Meer informatie vindt u [hier](/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Stel ' setProfileEnvironment ' in op ' True '. Meer informatie vindt u [hier](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Ga naar% windir% \System32\inetsrv\config\applicationHost.config
    - Zoek naar ' setProfileEnvironment '. Als de eigenschap is ingesteld op ' false ', wijzigt u deze in ' True '. Als deze niet aanwezig is, voegt u deze als een kenmerk toe aan het processModel-element ( /configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment ) en stelt u deze in op ' True '.

- Meer informatie over [beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/index.yml).
- Meer informatie over [Azure AD-verificatie scenario's](../../active-directory/develop/authentication-vs-authorization.md).