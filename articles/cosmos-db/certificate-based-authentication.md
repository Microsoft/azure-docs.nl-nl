---
title: Verificatie op basis van certificaten met Azure Cosmos DB en Active Directory
description: Meer informatie over het configureren van een Azure AD-identiteit voor verificatie op basis van certificaten voor toegang tot sleutels van Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 84cbc681d0974e91561daf8918dff389226fa7aa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553966"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Verificatie op basis van certificaten voor een Azure AD-identiteit om toegang te krijgen tot sleutels van een Azure Cosmos DB-account
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met verificatie op basis van certificaten kan de clienttoepassing worden geverifieerd door Azure AD (Azure Active Directory) te gebruiken met een clientcertificaat. U kunt verificatie op basis van certificaten uitvoeren op een computer waarvoor u een id nodig hebt, zoals een on-premises computer of virtuele machine in Azure. Uw toepassing kan vervolgens Azure Cosmos DB sleutels lezen zonder de sleutels rechtstreeks in de toepassing te hebben. In dit artikel wordt beschreven hoe u een voor beeld van een Azure AD-toepassing maakt, hoe u deze configureert voor verificatie op basis van certificaten, zich aanmeldt bij Azure met de nieuwe toepassings-id en vervolgens de sleutels uit uw Azure Cosmos-account haalt. In dit artikel worden Azure PowerShell gebruikt voor het instellen van de identiteiten en biedt een C#-voor beeld-app waarmee sleutels worden geverifieerd en geopend vanuit uw Azure Cosmos-account.  

## <a name="prerequisites"></a>Vereisten

* Installeer de [meest recente versie](/powershell/azure/install-az-ps) van Azure PowerShell.

* Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.

## <a name="register-an-app-in-azure-ad"></a>Een app bij Azure AD registreren

In deze stap maakt u een voor beeld van een webtoepassing in uw Azure AD-account. Deze toepassing wordt later gebruikt voor het lezen van de sleutels uit uw Azure Cosmos DB-account. Gebruik de volgende stappen om een toepassing te registreren: 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Open het deel venster **Active Directory** van Azure, ga naar het deel venster **app-registraties** en selecteer **nieuwe registratie**. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Nieuwe toepassings registratie in Active Directory":::

1. Vul **een toepassings formulier registreren** in met de volgende details:  

   * **Naam** : Geef een naam op voor uw toepassing. Dit kan een wille keurige naam zijn, zoals ' sampleApp '.
   * **Ondersteunde account typen** : Kies **accounts in deze organisatie-Directory alleen (standaard directory),** zodat resources in uw huidige map toegang hebben tot deze toepassing. 
   * **Omleidings-URL** : Kies toepassing van het type **Web** en geef een URL op waar uw toepassing wordt gehost. Dit kan een wille keurige URL zijn. Voor dit voor beeld kunt u een test-URL opgeven, zoals in `https://sampleApp.com` de richting van de app, zelfs als deze niet bestaat.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Een voor beeld-webtoepassing registreren":::

1. Selecteer **registreren** nadat u het formulier hebt ingevuld.

1. Nadat de app is geregistreerd, noteert u de **toepassings-id** en de **object-id**. u gebruikt deze gegevens in de volgende stappen. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="De toepassings-en object-Id's ophalen":::

## <a name="install-the-azuread-module"></a>De AzureAD-module installeren

In deze stap installeert u de Azure AD Power shell-module. Deze module is vereist om de ID op te halen van de toepassing die u in de vorige stap hebt geregistreerd en om een zelfondertekend certificaat aan die toepassing te koppelen. 

1. Open Windows PowerShell ISE met beheerders rechten. Als u dit nog niet hebt gedaan, installeert u de AZ Power shell-module en maakt u verbinding met uw abonnement. Als u meerdere abonnementen hebt, kunt u de context van het huidige abonnement instellen, zoals wordt weer gegeven in de volgende opdrachten:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. De [AzureAD](/powershell/module/azuread/) -module installeren en importeren

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Meld u aan bij uw Azure AD

Meld u aan bij uw Azure AD waar u de toepassing hebt geregistreerd. Gebruik de Connect-AzureAD opdracht om u aan te melden bij uw account, voer uw referenties voor uw Azure-account in in het pop-upvenster. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

Open nog een exemplaar van Windows PowerShell ISE en voer de volgende opdrachten uit om een zelfondertekend certificaat te maken en lees de sleutel die aan het certificaat is gekoppeld:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>De op certificaten gebaseerde referentie maken 

Voer vervolgens de volgende opdrachten uit om de object-ID van uw toepassing op te halen en de op certificaten gebaseerde referentie te maken. In dit voor beeld stellen we het certificaat in op verlopen na een jaar. u kunt dit instellen op elke vereiste eind datum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

De bovenstaande opdracht resulteert in de uitvoer die lijkt op de onderstaande scherm afbeelding:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Uitvoer van referenties voor het maken van een certificaat":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Uw Azure Cosmos-account configureren voor het gebruik van de nieuwe identiteit

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Ga naar uw Azure Cosmos-account en open de Blade **toegangs beheer (IAM)** .

1. Selecteer **functie toewijzing** **toevoegen** en toevoegen. Voeg de sampleApp die u in de vorige stap hebt gemaakt, toe aan de rol **Inzender** , zoals weer gegeven in de volgende scherm afbeelding:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Azure Cosmos-account configureren voor het gebruik van de nieuwe identiteit":::

1. Selecteer **Opslaan** nadat u het formulier hebt ingevuld

## <a name="register-your-certificate-with-azure-ad"></a>Uw certificaat registreren bij Azure AD

U kunt de op certificaten gebaseerde referentie koppelen aan de client toepassing in azure AD vanuit het Azure Portal. Als u de referentie wilt koppelen, moet u het certificaat bestand uploaden met de volgende stappen:

In de registratie van de Azure-app voor de client toepassing:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).

1. Open het deel venster **Active Directory** van Azure, ga naar het deel venster **app-registraties** en open de voor beeld-app die u in de vorige stap hebt gemaakt. 

1. Selecteer **certificaten & geheimen** en upload vervolgens het **certificaat**. Blader door het certificaat bestand dat u in de vorige stap hebt gemaakt om dit te uploaden.

1. Selecteer **Toevoegen**. Nadat het certificaat is geüpload, worden de vinger afdruk, de start datum en de verval waarden weer gegeven.

## <a name="access-the-keys-from-powershell"></a>Toegang tot de sleutels in Power shell

In deze stap meldt u zich aan bij Azure met behulp van de toepassing en het certificaat dat u hebt gemaakt en opent u de sleutels van uw Azure Cosmos-account. 

1. Wis eerst de referenties van het Azure-account dat u hebt gebruikt om u aan te melden bij uw account. U kunt referenties wissen met behulp van de volgende opdracht:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Vervolgens controleert u of u zich kunt aanmelden bij Azure Portal met behulp van de referenties van de toepassing en toegang tot de Azure Cosmos DB sleutels:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Met de vorige opdracht worden de primaire en secundaire primaire sleutels van uw Azure Cosmos-account weer gegeven. U kunt het activiteiten logboek van uw Azure Cosmos-account weer geven om te valideren dat de aanvraag voor het ophalen van sleutels is geslaagd en dat de gebeurtenis wordt geïnitieerd door de toepassing ' sampleApp '.

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="De aanroep Get Keys in azure AD valideren":::

## <a name="access-the-keys-from-a-c-application"></a>Toegang tot de sleutels vanuit een C#-toepassing 

U kunt dit scenario ook valideren door sleutels van een C#-toepassing te openen. De volgende C#-console toepassing die toegang heeft tot Azure Cosmos DB sleutels met behulp van de app die is geregistreerd in Active Directory. Zorg ervoor dat u de tenantId, clientID, certnaam, naam van de resource groep, de abonnements-ID, de naam van de Azure Cosmos-accountnaam bijwerkt voordat u de code uitvoert. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Met dit script worden de primaire en secundaire primaire sleutels uitgevoerd, zoals wordt weer gegeven in de volgende scherm afbeelding:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="uitvoer van csharp-toepassing":::

Net als bij de vorige sectie kunt u het activiteiten logboek van uw Azure Cosmos-account bekijken om te valideren dat de aanvraag voor het ophalen van sleutels wordt gestart door de toepassing ' sampleApp '. 


## <a name="next-steps"></a>Volgende stappen

* [Azure Cosmos-sleutels beveiligen met Azure Key Vault](access-secrets-from-keyvault.md)

* [Beveiligings basislijn voor Azure Cosmos DB](security-baseline.md)