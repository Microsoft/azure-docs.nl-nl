---
title: Aangepaste beleids regels implementeren met Azure-pijp lijnen
titleSuffix: Azure AD B2C
description: Meer informatie over het implementeren van Azure AD B2C aangepast beleid in een CI/CD-pijp lijn met behulp van Azure-pijp lijnen in azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca4464f40c0807e51adffca8e3e68c622314de6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256885"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Aangepaste beleids regels implementeren met Azure-pijp lijnen

Door gebruik te maken van een continue integratie-en leverings pijplijn (CI/CD) die u in [Azure-pijp lijnen][devops-pipelines]hebt ingesteld, kunt u uw Azure AD B2C aangepaste beleids regels in uw software-levering en code beheer automatisering toevoegen. Wanneer u implementeert in verschillende Azure AD B2C omgevingen, bijvoorbeeld dev, test en productie, wordt u aangeraden hand matige processen te verwijderen en geautomatiseerd testen uit te voeren met behulp van Azure-pijp lijnen.

Er zijn drie primaire stappen vereist om Azure-pijp lijnen in te scha kelen voor het beheren van aangepaste beleids regels in Azure AD B2C:

1. Een registratie van webtoepassingen maken in uw Azure AD B2C Tenant
1. Een Azure-opslag plaats configureren
1. Een Azure-pijp lijn configureren

> [!IMPORTANT]
> Het beheren van Azure AD B2C aangepaste beleids regels met een Azure-pijp lijn maakt momenteel gebruik van **Preview** -bewerkingen die beschikbaar zijn op het Microsoft Graph API- `/beta` eind punt. Het gebruik van deze API's in productie-apps wordt niet ondersteund. Zie voor meer informatie de [referentie over het Microsoft Graph rest API bèta-eind punt](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Vereisten

* [Azure AD B2C Tenant](tutorial-create-tenant.md)en referenties voor een gebruiker in de directory met de [B2C IEF Policy](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) beheerdersrol
* [Aangepast beleid](tutorial-create-user-flows.md?pivots=b2c-custom-policy) dat is geüpload naar uw Tenant
* De [beheer-app](microsoft-graph-get-started.md) die is geregistreerd in uw Tenant met het Microsoft Graph API-machtigings *beleid. readwrite. TrustFramework*
* [Azure-pijp lijn](https://azure.microsoft.com/services/devops/pipelines/)en toegang tot een [Azure DevOps Services-project][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Stroom van toekenning van client referenties

Het scenario dat hier wordt beschreven, maakt gebruik van service-to-service-aanroepen tussen Azure-pijp lijnen en Azure AD B2C met behulp van de OAuth 2,0- [client referenties toekenning stroom](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Deze toekennings stroom staat een webservice zoals Azure-pijp lijnen (de vertrouwelijke client) toe om eigen referenties te gebruiken in plaats van een gebruiker te imiteren voor verificatie bij het aanroepen van een andere webservice (de Microsoft Graph-API, in dit geval). Azure-pijp lijnen verkrijgen een token niet-interactief en vervolgens worden er aanvragen naar de Microsoft Graph-API.

## <a name="register-an-application-for-management-tasks"></a>Een toepassing registreren voor beheer taken

Zoals vermeld in [vereisten](#prerequisites), hebt u een toepassings registratie nodig die uw Power shell-scripts heeft uitgevoerd door Azure-pijp lijnen. Dit kan worden gebruikt voor toegang tot de resources in uw Tenant.

Als u al een toepassings registratie hebt die u voor Automation-taken gebruikt, zorg er dan voor dat de **Microsoft Graph**  >  **beleid** beleid  >  **. readwrite. TrustFramework** is toegestaan binnen de **API-machtigingen** van de app-registratie.

Zie [Azure AD B2C beheren met Microsoft Graph](microsoft-graph-get-started.md)voor instructies voor het registreren van een beheer toepassing.

## <a name="configure-an-azure-repo"></a>Een Azure-opslag plaats configureren

Als een beheer toepassing is geregistreerd, kunt u een opslag plaats voor uw beleids bestanden configureren.

1. Meld u aan bij uw Azure DevOps Services-organisatie.
1. [Maak een nieuw project][devops-create-project] of selecteer een bestaand project.
1. Ga in uw project naar **opslag plaatsen** en selecteer de pagina **bestanden** . Selecteer een bestaande opslag plaats of maak er een voor deze oefening.
1. Maak een map met de naam *B2CAssets*. Geef het vereiste *README.MD* een naam en **Voer** het bestand uit. U kunt dit bestand later verwijderen, indien gewenst.
1. Voeg uw Azure AD B2C-beleids bestanden toe aan de map *B2CAssets* . Dit omvat de *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* en andere beleids regels die u hebt gemaakt. Noteer de bestands naam van elk Azure AD B2C-beleids bestand dat u in een latere stap kunt gebruiken (ze worden gebruikt als Power shell-script argumenten).
1. Maak een map met de naam *scripts* in de hoofdmap van de opslag plaats, geef een naam op voor het tijdelijke bestand *DeployToB2c.ps1*. Sla het bestand op dit moment niet door. dit doet u in een latere stap.
1. Plak het volgende Power shell-script in *DeployToB2c.ps1* en **Voer het bestand** uit. Het script verkrijgt een token van Azure AD en roept de Microsoft Graph-API om het beleid in de map *B2CAssets* naar uw Azure AD B2C Tenant te uploaden.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Uw Azure-pijp lijn configureren

Als uw opslag plaats is geïnitialiseerd en gevuld met uw aangepaste beleids bestanden, bent u klaar om de release pijplijn in te stellen.

### <a name="create-pipeline"></a>Pijplijn maken

1. Meld u aan bij uw Azure DevOps Services-organisatie en navigeer naar uw project.
1. Selecteer in uw project **pijp lijnen**  >    >  **nieuwe pijp lijn**.
1. Selecteer onder **Selecteer een sjabloon de** optie **lege taak**.
1. Voer een **naam** in voor het stadium, bijvoorbeeld *DeployCustomPolicies*, en sluit vervolgens het deel venster.
1. Selecteer **een artefact toevoegen** en selecteer onder **bron type** Azure- **opslag plaats**.
    1. Kies de bron opslagplaats met de map *scripts* die u hebt ingevuld met het Power shell-script.
    1. Kies een **standaard vertakking**. Als u in de vorige sectie een nieuwe opslag plaats hebt gemaakt, is de standaard vertakking *Master*.
    1. Behoud de **standaard versie** -instelling van *de standaard vertakking*.
    1. Voer een **bron alias** voor de opslag plaats in. Bijvoorbeeld *policyRepo*. Neem geen spaties op in de alias naam.
1. Selecteer **Toevoegen**
1. Wijzig de naam van de pijp lijn zodat deze overeenkomt met de bedoeling. *Implementeer bijvoorbeeld aangepaste beleids pijplijn*.
1. Selecteer **Opslaan** om de pijplijn configuratie op te slaan.

### <a name="configure-pipeline-variables"></a>Pijplijn variabelen configureren

1. Selecteer het tabblad **variabelen** .
1. Voeg de volgende variabelen onder **pijplijn variabelen** toe en stel de waarden in zoals opgegeven:

    | Name | Waarde |
    | ---- | ----- |
    | `clientId` | **Toepassings-id (client)** van de toepassing die u eerder hebt geregistreerd. |
    | `clientSecret` | De waarde van het **client geheim** dat u eerder hebt gemaakt. <br /> Wijzig het variabele type in **geheim** (Selecteer het vergrendelings pictogram). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, waarbij *uw-B2C* de naam is van uw Azure AD B2C-Tenant. |

1. Selecteer **Opslaan** om de variabelen op te slaan.

### <a name="add-pipeline-tasks"></a>Pijplijn taken toevoegen

Voeg vervolgens een taak toe om een beleids bestand te implementeren.

1. Selecteer het tabblad **taken** .
1. Selecteer **Agent taak** en selecteer vervolgens het plus teken ( **+** ) om een taak aan de agent taak toe te voegen.
1. Zoek en selecteer **Power shell**. Selecteer niet Azure PowerShell, Power shell op de doel computers of een andere Power shell-vermelding.
1. Selecteer de zojuist toegevoegde **Power shell-script** taak.
1. Voer de volgende waarden in voor de Power shell-script taak:
    * **Taak versie**: 2. *
    * **Weergave naam**: de naam van het beleid dat met deze taak moet worden geüpload. Bijvoorbeeld *B2C_1A_TrustFrameworkBase*.
    * **Type**: bestandspad
    * **Scriptpad**: Selecteer het weglatings teken (**_..._* _), navigeer naar de map _Scripts * en selecteer vervolgens het *DeployToB2C.ps1* bestand.
    * **Opmerkingen**

        Voer de volgende waarden in voor **argumenten**. Vervang door `{alias-name}` de alias die u in de vorige sectie hebt opgegeven.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Als de alias die u hebt opgegeven bijvoorbeeld *policyRepo* is, moet de argument regel:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecteer **Opslaan** om de agent taak op te slaan.

Met de taak die u zojuist hebt toegevoegd, wordt *een* beleids bestand geüpload naar Azure AD B2C. Voordat u doorgaat, moet u de taak hand matig activeren (**release maken**) om er zeker van te zijn dat deze is voltooid voordat er aanvullende taken worden gemaakt.

Als de taak is voltooid, voegt u implementatie taken toe door de voor gaande stappen voor elk van de aangepaste beleids bestanden uit te voeren. Wijzig de `-PolicyId` en `-PathToFile` argument waarden voor elk beleid.

De `PolicyId` is een waarde die aan het begin van een XML-beleids bestand in het knoop punt TrustFrameworkPolicy is gevonden. De `PolicyId` in de volgende beleids-XML is bijvoorbeeld *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Wanneer de agents worden uitgevoerd en de beleids bestanden worden geüpload, moet u ervoor zorgen dat ze in deze volg orde worden geüpload:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Het Framework voor identiteits ervaring dwingt deze volg orde af als de bestands structuur is gebaseerd op een hiërarchische keten.

## <a name="test-your-pipeline"></a>Test uw pijplijn

Uw release pijplijn testen:

1. Selecteer **pijp lijnen** en vervolgens **releases**.
1. Selecteer de pijp lijn die u eerder hebt gemaakt, bijvoorbeeld *DeployCustomPolicies*.
1. Selecteer **release maken** en selecteer vervolgens **maken** om de release in de wachtrij te plaatsen.

U ziet een melding banner dat aangeeft dat er een release in de wachtrij is geplaatst. Als u de status wilt weer geven, selecteert u de koppeling in het meldings banner of selecteert u deze in de lijst op het tabblad **releases** .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

* [Service-naar-service aanroepen met behulp van client referenties](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
