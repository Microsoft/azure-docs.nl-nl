---
title: Azure Remote Rendering en modelopslag beveiligen
description: Een Remote Rendering-toepassing beperken voor het beveiligen van inhoud
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d8a7bb620b7fcc9c878986d3575e22bb6f0f77bc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724112"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Zelfstudie: Azure Remote Rendering en modelopslag beveiligen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Azure Blob Storage met Azure Remote Rendering-modellen beveiligen
> * Verifiëren met Azure AD om toegang te krijgen tot uw Azure Remote Rendering-exemplaar
> * Azure-referenties gebruiken voor Azure Remote Rendering-verificatie

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie gaat verder op de [zelfstudie: Materialen, belichting en effecten verfijnen](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Waarom extra beveiliging nodig is

De huidige status van de toepassing en de toegang tot uw Azure-resources ziet er als volgt uit:

![Initiële beveiliging](./media/security-one.png)

Zowel met "AccountID + AccountKey" als de "URL + SAS Token" worden een gebruikersnaam en wachtwoord in feite samen opgeslagen. Als bijvoorbeeld "AccountID + AccountKey" zichtbaar is, zou een kwaadwillende gebruiker uw ARR-bronnen kunnen gebruiken zonder uw toestemming.

## <a name="securing-your-content-in-azure-blob-storage"></a>Uw inhoud beveiligen in Azure Blob Storage

Met Azure Remote Rendering kunt u veilig toegang krijgen tot de inhoud van uw Azure Blob Storage met de juiste configuratie. Zie [Instructies: Opslagaccounts koppelen](../../../how-tos/create-an-account.md#link-storage-accounts) om uw Azure Remote Rendering-exemplaar te configureren met uw Blob Storage-accounts.

Wanneer u een gekoppelde blobopslag gebruikt, gebruikt u iets andere methoden voor het laden van modellen:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

In de bovenstaande regels wordt de `FromSAS`-versie van de parameters en sessieactie gebruikt. Deze moeten worden geconverteerd naar de niet-SAS-versies:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

We gaan **RemoteRenderingCoordinator** wijzigen om een aangepast model te laden uit een gekoppeld Blob Storage-account.

1. Als u dit nog niet gedaan hebt, voltooit u de [Instructies: Opslagaccounts koppelen](../../../how-tos/create-an-account.md#link-storage-accounts) om uw ARR-exemplaar toestemming te geven voor toegang tot uw Blob Storage exemplaar.
1. Voeg de volgende gewijzigde **LoadModel**-methode toe aan **RemoteRenderingCoordinator**, net onder de huidige **LoadModel**-methode:

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Deze code is voor het grootste deel hetzelfde als de oorspronkelijke `LoadModel`-methode, maar de SAS-versies van de methode-aanroepen zijn vervangen door de niet-SAS-versies.

    De extra invoer `storageAccountName` en `blobContainerName` zijn ook toegevoegd aan de argumenten. We roepen deze nieuwe **LoadModel**-methode aan via een andere methode die vergelijkbaar is met de eerste **LoadTestModel**-methode die we in de eerste zelfstudie hebben gemaakt.

1. Voeg de volgende methode toe aan **RemoteRenderingCoordinator**, net na **LoadTestModel**

    ```csharp
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Met deze code worden drie extra tekenreeksvariabelen toegevoegd aan het onderdeel **RemoteRenderingCoordinator**.
    ![Schermopname waarin de Storage Account Name, Blob Container Name en het Model Path van het onderdeel RemoteRenderingCoordinator zijn gemarkeerd.](./media/storage-account-linked-model.png)

1. Voeg uw waarden toe aan het onderdeel **RemoteRenderingCoordinator**. Als u de [quickstart voor modelconversie](../../../quickstarts/convert-model.md) hebt uitgevoerd, hebt u nu de volgende waarden:

    * **Naam van opslagaccount**: De naam van uw opslagaccount, de wereldwijd unieke naam die u voor uw opslagaccount hebt gekozen. In de quickstart was dit *arrtutorialstorage*. Uw waarde zal hiervan afwijken.
    * **Naam van de blobcontainer**: arroutput, de Blob Storage-container
    * **Pad naar model**: De combinatie van "outputFolderPath" en "outputAssetFileName", gedefinieerd in het bestand *arrconfig.json*. In de quickstart was dit "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". Dit zou resulteren in een waarde voor het pad naar het model van "converted/robot/robot.arrAsset". Uw waarde zal hiervan afwijken.

    >[!TIP]
    > Als u [het script **Conversion.ps1** uitvoert](../../../quickstarts/convert-model.md#run-the-conversion), zonder het argument "-UseContainerSas", worden alle bovenstaande waarden voor u uitgevoerd in plaats van het SAS-token. ![Gekoppeld model](./media/converted-output.png)
1. U kunt het GameObject **TestModel** voorlopig verwijderen of uitschakelen, om ruimte te maken voor het aangepaste model dat u wilt laden.
1. Speel de scène af en maak verbinding met een externe sessie.
1. Klik met de rechtermuisknop op uw **RemoteRenderingCoordinator** en selecteer **Gekoppeld aangepast model laden**.
    ![Gekoppeld model laden](./media/load-linked-model.png)

Deze stappen hebben de beveiliging van de toepassing verhoogd door het SAS-token uit de lokale toepassing te verwijderen.

De huidige status van de toepassing en de toegang tot uw Azure-resources ziet er nu als volgt uit:

![Betere beveiliging](./media/security-two.png)

We hebben nog een 'wachtwoord', AccountKey (accountsleutel), dat uit de lokale toepassing moet worden verwijderd. Dit kunnen we doen met AAD-verificatie (Azure Active Directory).

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory-verificatie (Azure AD)

Met AAD-verificatie kunt u bepalen welke personen of groepen op een meer bewaakte manier gebruikmaken van ARR. ARR bevat ingebouwde ondersteuning voor het accepteren van [toegangstokens](../../../../active-directory/develop/access-tokens.md) in plaats van een accountsleutel te gebruiken. U kunt toegangstokens beschouwen als een tijdgebonden, gebruikersspecifieke sleutel, waarmee alleen bepaalde onderdelen van de specifieke resource worden ontgrendeld waarvoor deze zijn aangevraagd.

Het script **RemoteRenderingCoordinator** bevat een gemachtigde met de naam **ARRCredentialGetter**. Deze bevat een methode die een **AzureFrontendAccountInfo**-object retourneert. Dit object wordt gebruikt om het externe sessiebeheer te configureren. We kunnen een andere methode toewijzen aan **ARRCredentialGetter**, zodat we een Azure-aanmeldingsstroom kunnen gebruiken om een **AzureFrontendAccountInfo**-object te genereren dat een Azure-toegangstoken bevat. Dit toegangstoken is uniek voor de gebruiker die zich aanmeldt.

1. Volg de [Instructies: Verificatie configureren - Verificatie voor geïmplementeerde toepassingen](../../../how-tos/authentication.md#authentication-for-deployed-applications). Volg de specifieke instructies in de documentatie voor Azure Spatial Anchors voor [gebruikersverificatie van Azure AD](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Dit omvat het registreren van een nieuwe Azure Active Directory-toepassing en het configureren van toegang tot uw ARR-exemplaar.
1. Nadat u de nieuwe AAD-toepassing hebt geconfigureerd, controleert u of uw AAD-toepassing eruitziet zoals in de volgende afbeeldingen:

    **AAD-toepassing -> Verificatie** ![App-verificatie](./media/app-authentication-public.png)

    **AAD-toepassing -> API-machtigingen** ![App-API's](./media/request-api-permissions-step-five.png)

1. Nadat u uw Remote Rendering-account hebt geconfigureerd, controleert u of uw configuratie eruitziet zoals in de volgende afbeelding:

    **AAR -> AccessControl (IAM)** ![ARR-rol](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > Een rol van *Eigenaar* is niet voldoende om sessies te kunnen beheren via de clienttoepassing. Aan elke gebruiker die u de mogelijkheid wilt geven om sessies te beheren, moet u de rol van **Remote Rendering-client** toewijzen. Aan elke gebruiker die u de mogelijkheid wilt geven om sessies te beheren en modellen te converteren, moet u de rol van **Remote Rendering-beheerder** toewijzen.

Aan de Azure-zijde is alles gereed. Nu moet u de code aanpassen om verbinding te maken met de AAR-service. We doen dit door een exemplaar van **BaseARRAuthentication** te implementeren. Hiermee wordt een nieuw **AzureFrontendAccountInfo**-object geretourneerd. In dit geval worden de accountgegevens geconfigureerd met het Azure-toegangstoken.

1. Maak een nieuw script met de naam **AADAuthentication** en vervang de code door het volgende:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;

    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }

        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }

        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountAuthenticationDomain;
        public string AzureRemoteRenderingAccountAuthenticationDomain
        {
            get => azureRemoteRenderingAccountAuthenticationDomain.Trim();
            set => azureRemoteRenderingAccountAuthenticationDomain = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountAuthenticationDomain + "/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AzureRemoteRenderingAccountAuthenticationDomain, AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }

        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });

            return Task.FromResult(0);
        }

        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();

                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();

                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }

            return null;
        }
    }
    ```

>[!NOTE]
> Deze code is verre van compleet en is niet geschikt voor commercieel gebruik. U wilt gebruikers waarschijnlijk ook ten minste de mogelijkheid geven om zich af te melden. U kunt dit doen met behulp van de `Task RemoveAsync(IAccount account)`-methode van de clienttoepassing. Deze code is alleen bedoeld voor gebruik in een zelfstudie. Uw implementatie is specifiek voor uw toepassing.

Met de code wordt eerst geprobeerd het token op de achtergrond op te halen met behulp van **AquireTokenSilent**. Dit lukt als de gebruiker deze toepassing eerder heeft geverifieerd. Als het niet lukt, moet u een methode gebruiken waarbij meer interactie van de gebruiker vereist is.

Voor deze code gebruiken we de [apparaatcodestroom](../../../../active-directory/develop/v2-oauth2-device-code.md) om een toegangstoken te verkrijgen. Met deze stroom kunnen gebruikers zich op een computer of mobiel apparaat aanmelden bij hun Azure-account en moet het resulterende token worden teruggestuurd naar de toepassing HoloLens.

Het belangrijkste deel van deze klasse vanuit het perspectief van ARR is deze regel:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Hier maken we een nieuw **AzureFrontendAccountInfo**-object met behulp van accountdomein, account-id, accountverificatiedomein, en toegangstoken. Dit token wordt vervolgens gebruikt door de ARR-service om Remote Rendering-sessies op te vragen, te maken en samen te voegen zolang de gebruiker is geautoriseerd op basis van de op rollen gebaseerde machtigingen die eerder zijn geconfigureerd.

Na deze wijziging ziet de huidige status van de toepassing en de toegang tot uw Azure-resources er als volgt uit:

![Nog betere beveiliging](./media/security-three.png)

Omdat de gebruikersreferenties niet op het apparaat zijn opgeslagen (of in dit geval zelfs niet op het apparaat zijn ingevoerd), is het risico van blootstelling zeer laag. Het apparaat gebruikt nu een gebruikersspecifiek, tijdgebonden toegangstoken voor toegang tot ARR, dat gebruikmaakt van toegangsbeheer (IAM) om toegang te krijgen tot Blob Storage. Door deze twee stappen zijn de 'wachtwoorden' volledig verwijderd uit de broncode en is de beveiliging aanzienlijk verbeterd. Dit is echter niet de beste beveiliging. De beveiliging kan nog verder worden verbeterd door het model- en sessiebeheer naar een webservice te verplaatsen. Aanvullende beveiligingsoverwegingen worden beschreven in het hoofdstuk [Commerciële gereedheid](../commercial-ready/commercial-ready.md).

### <a name="testing-aad-auth"></a>AAD-verificatie testen

Als AAD-verificatie actief is, moet u zich telkens wanneer u de toepassing start, eerst verifiëren in de Unity-editor. Op het apparaat wordt de verificatiestap de eerste keer uitgevoerd en hoeft deze alleen opnieuw te worden uitgevoerd wanneer het token verloopt of ongeldig is geworden.

1. Voeg het onderdeel **AADAuthentication-** toe aan het GameObject **RemoteRenderingCoordinator**.

    ![AAD-verificatieonderdeel](./media/azure-active-directory-auth-component.png)

1. Vul uw waarden voor de client-id en de tenant-id in. Deze waarden vindt u op de overzichtspagina van de app-registratie:

    * Het **accountdomein** is hetzelfde domein als het accountdomein van **RemoteRenderingCoordinator**.
    * De **client-id van de Active Directory-toepassing** is de *toepassings-id (of client-id)* die te vinden is in de registratie van uw AAD-app (zie de onderstaande afbeelding).
    * De **Azure-tenant-id** is de *directory-id (tenant-id)* die te vinden is in de registratie van uw AAD-app (zie de onderstaande afbeelding).
    * De **account-id van Azure Remote Rendering** is de **account-id** die u hebt gebruikt voor **RemoteRenderingCoordinator**.
    * **Accountverificatiedomein** is hetzelfde **Accountverificatiedomein** dat u hebt gebruikt in **RemoteRenderingCoordinator**.

    ![Schermopname waarin de Application (client) ID en Directory (tenant) ID zijn gemarkeerd.](./media/app-overview-data.png)

1. Druk op Play (Afspelen) in de Unity-editor en verleen toestemming voor het uitvoeren van een sessie.
    Omdat het onderdeel **AADAuthentication** een weergavecontroller heeft, wordt het automatisch gekoppeld om een prompt weer te geven na het scherm voor het sessieverificatiemodel.
1. Volg de instructies in het scherm rechts van **AppMenu**.
    U krijgt iets te zien zoals dit: ![Afbeelding met het instructiepaneel dat rechts van het AppMenu wordt weergegeven.](./media/device-flow-instructions.png)
    Nadat u de opgegeven code op uw secundaire apparaat (of browser op hetzelfde apparaat) hebt ingevoerd en u zich hebt aangemeld met behulp van uw referenties, wordt een toegangstoken geretourneerd naar de aanvragende toepassing, in dit geval de Unity-editor.
1. Hierna moet alles in de toepassing verder normaal worden uitgevoerd. Controleer de Unity-console op fouten als u de fasen niet op de verwachte wijze doorloopt.

## <a name="build-to-device"></a>Bouwen naar apparaat

Als u een toepassing bouwt met behulp van MSAL naar een apparaat, moet u een bestand toevoegen aan de map **Assets** van uw project. Zo kan de toepassing correct worden gecompileerd met behulp van de *Microsoft.Identity.Client.dll* in de **zelfstudie Assets**.

1. Een nieuwe bestand aan **Assets** toevoegen met de naam **link.xml**
1. Voeg het volgende toe aan het bestand:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. De wijzigingen opslaan

Volg de stappen in [Quickstart: Unity-voorbeeld implementeren in HoloLens - Het voorbeeldproject bouwen](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project) om het project te implementeren naar HoloLens.

## <a name="next-steps"></a>Volgende stappen

De rest van deze zelfstudie bevat conceptuele onderwerpen voor het maken van een toepassing die gereed is voor een productieomgeving en die gebruikmaakt van een Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Volgende: Commerciële gereedheid](../commercial-ready/commercial-ready.md)