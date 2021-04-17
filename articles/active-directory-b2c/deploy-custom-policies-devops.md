---
title: Aangepast beleid implementeren met Azure Pipelines
titleSuffix: Azure AD B2C
description: Leer hoe u aangepaste Azure AD B2C implementeert in een CI/CD-pijplijn met behulp van Azure Pipelines in Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529431"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Aangepast beleid implementeren met Azure Pipelines

Met behulp van een CI/CD-pijplijn (Continue integratie en levering) die u in [Azure Pipelines][devops-pipelines]instelt, kunt u uw aangepaste Azure AD B2C opnemen in uw softwarelevering en automatisering van codebeheer. Wanneer u implementeert in verschillende Azure AD B2C-omgevingen, zoals ontwikkelen, testen en productie, wordt u aangeraden handmatige processen te verwijderen en automatische tests uit te voeren met behulp van Azure Pipelines.

Er zijn drie primaire stappen vereist voor het inschakelen van Azure Pipelines voor het beheren van aangepaste beleidsregels binnen Azure AD B2C:

1. Een webtoepassingsregistratie maken in uw Azure AD B2C tenant
1. Een Azure-repo configureren
1. Een Azure-pijplijn configureren

> [!IMPORTANT]
> Voor Azure AD B2C aangepaste beleidsregels met een Azure-pijplijn worden momenteel **preview-bewerkingen** gebruikt die beschikbaar zijn op Microsoft Graph `/beta` API-eindpunt. Het gebruik van deze API's in productie-apps wordt niet ondersteund. Zie de naslaginformatie over het [bèta Microsoft Graph REST API-eindpunt voor meer informatie.](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)

## <a name="prerequisites"></a>Vereisten

* [Azure AD B2C tenant](tutorial-create-tenant.md)en referenties voor een gebruiker in de directory met de [rol B2C IEF-beleidsbeheerder](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Aangepast beleid geüpload](tutorial-create-user-flows.md?pivots=b2c-custom-policy) naar uw tenant
* [Beheer-app](microsoft-graph-get-started.md) die is geregistreerd in uw tenant met Microsoft Graph API-machtiging *Policy.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)en toegang tot een [Azure DevOps Services-project][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Stroom voor het verlenen van clientreferenties

In het scenario dat hier wordt beschreven, wordt gebruik gemaakt van service-naar-service-aanroepen tussen Azure Pipelines en Azure AD B2C met behulp van de OAuth [2.0-clientreferenties-toekenningsstroom](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). Met deze toekenningsstroom kan een webservice zoals Azure Pipelines (de vertrouwelijke client) zijn eigen referenties gebruiken in plaats van een gebruiker te imiteren voor verificatie bij het aanroepen van een andere webservice (in dit geval de Microsoft Graph-API). Azure Pipelines verkrijgt niet-interactief een token en doet vervolgens aanvragen naar Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Een toepassing registreren voor beheertaken

Zoals vermeld in Vereisten, hebt u een [toepassingsregistratie](#prerequisites)nodig die door Azure Pipelines door uw PowerShell-scripts kan worden uitgevoerd voor toegang tot de resources in uw tenant.

Als u al een toepassingsregistratie hebt die u gebruikt voor automatiseringstaken, controleert u of aan deze toepassing de machtiging **Microsoft Graph**  >    >  **Policy.ReadWrite.TrustFramework** is verleend binnen de **API-machtigingen** van de app-registratie.

Zie Manage Azure AD B2C with Microsoft Graph voor instructies over het registreren van [een beheertoepassing.](microsoft-graph-get-started.md)

## <a name="configure-an-azure-repo"></a>Een Azure-repo configureren

Wanneer een beheertoepassing is geregistreerd, kunt u een opslagplaats voor uw beleidsbestanden configureren.

1. Meld u aan bij uw Azure DevOps Services-organisatie.
1. [Maak een nieuw project][devops-create-project] of selecteer een bestaand project.
1. Navigeer in uw project naar **Repos** en selecteer de **pagina** Bestanden. Selecteer een bestaande opslagplaats of maak er een voor deze oefening.
1. Maak een map met de *naam B2CAssets.* Noem het vereiste tijdelijke *aanduidingsbestand README.md* **het bestand door** te geven. U kunt dit bestand later verwijderen, als u wilt.
1. Voeg uw Azure AD B2C toe aan de *map B2CAssets.* Dit omvat *deTrustFrameworkBase.xml,* *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml* en andere beleidsregels die u hebt gemaakt. Noter de bestandsnaam van elk Azure AD B2C voor gebruik in een latere stap (ze worden gebruikt als PowerShell-scriptargumenten).
1. Maak een map met de *naam Scripts* in de hoofdmap van de opslagplaats en noem het tijdelijke bestand *DeployToB2c.ps1*. U kunt het bestand op dit moment niet commiten. Dit doet u in een latere stap.
1. Plak het volgende PowerShell-script in *DeployToB2c.ps1* en vervolgens **het bestand** commit. Het script verkrijgt een token van Azure AD en roept de Microsoft Graph-API aan om het beleid in de *map B2CAssets* te uploaden naar uw Azure AD B2C tenant.

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

## <a name="configure-your-azure-pipeline"></a>Uw Azure-pijplijn configureren

Nu uw opslagplaats is initialiseerd en gevuld met uw aangepaste beleidsbestanden, bent u klaar om de release-pijplijn in te stellen.

### <a name="create-pipeline"></a>Pijplijn maken

1. Meld u aan bij uw Azure DevOps Services-organisatie en navigeer naar uw project.
1. Selecteer in uw project **Pipelines**  >  **Releases**  >  **New pipeline**.
1. Selecteer **onder Een sjabloon selecteren** de optie Lege **taak.**
1. Voer een **fasenaam** in, *bijvoorbeeld DeployCustomPolicies* en sluit vervolgens het deelvenster.
1. Selecteer **Een artefact toevoegen** en selecteer onder **Brontype** **de optie Azure-opslagplaats.**
    1. Kies de bronopslagplaats met de *map Scripts* die u hebt gevuld met het PowerShell-script.
    1. Kies een **standaard branch**. Als u in de vorige sectie een nieuwe opslagplaats hebt gemaakt, is de standaard branch *master*.
    1. Laat de **standaardversie-instelling** op *Meest recent staan in de standaardvertakking*.
    1. Voer een **bronalias in** voor de opslagplaats. Bijvoorbeeld *policyRepo.* Neem geen spaties op in de aliasnaam.
1. Selecteer **Toevoegen**
1. Wijzig de naam van de pijplijn in de intentie. Bijvoorbeeld Deploy *Custom Policy Pipeline*.
1. Selecteer **Opslaan om** de pijplijnconfiguratie op te slaan.

### <a name="configure-pipeline-variables"></a>Pijplijnvariabelen configureren

1. Selecteer het **tabblad** Variabelen.
1. Voeg de volgende variabelen toe onder **Pijplijnvariabelen** en stel de waarden in zoals opgegeven:

    | Name | Waarde |
    | ---- | ----- |
    | `clientId` | **Toepassings-id (client)** van de toepassing die u eerder hebt geregistreerd. |
    | `clientSecret` | De waarde van het **clientgeheim** dat u eerder hebt gemaakt. <br /> Wijzig het type variabele in **geheim** (selecteer het vergrendelingspictogram). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, waarbij *your-b2c-tenant* de naam is van uw Azure AD B2C tenant. |

1. Selecteer **Opslaan om** de variabelen op te slaan.

### <a name="add-pipeline-tasks"></a>Pijplijntaken toevoegen

Voeg vervolgens een taak toe om een beleidsbestand te implementeren.

1. Selecteer het **tabblad** Taken.
1. Selecteer **Agenttaak** en selecteer vervolgens het plusteken ( **+** ) om een taak toe te voegen aan de agenttaak.
1. Zoek en selecteer **PowerShell.** Selecteer niet 'Azure PowerShell', 'PowerShell op doelmachines' of een andere PowerShell-vermelding.
1. Selecteer de zojuist toegevoegde **PowerShell-scripttaak.**
1. Voer de volgende waarden in voor de PowerShell-scripttaak:
    * **Taakversie:** 2.*
    * **Weergavenaam:** de naam van het beleid dat met deze taak moet worden geüpload. U kunt *bijvoorbeeld B2C_1A_TrustFrameworkBase.*
    * **Type:** Bestandspad
    * **Scriptpad:** selecteer het beletselteken (**_..._* _), navigeer naar de map _Scripts* en selecteer vervolgens *hetDeployToB2C.ps1* bestand.
    * **Argumenten:**

        Voer de volgende waarden in voor **Arguments**. Vervang `{alias-name}` door de alias die u in de vorige sectie hebt opgegeven.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Als de opgegeven alias bijvoorbeeld *policyRepo* is, moet de argumentregel zijn:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecteer **Opslaan om** de agent-taak op te slaan.

Met de taak die u zojuist hebt toegevoegd, *wordt één* beleidsbestand geüpload naar Azure AD B2C. Voordat u doorgaat, activeert u de job handmatig (**Release maken**) om ervoor te zorgen dat deze wordt voltooid voordat u extra taken maakt.

Als de taak is voltooid, voegt u implementatietaken toe door de voorgaande stappen uit te voeren voor elk van de aangepaste beleidsbestanden. Wijzig de `-PolicyId` `-PathToFile` argumentwaarden en voor elk beleid.

De `PolicyId` is een waarde die wordt gevonden aan het begin van een XML-beleidsbestand in het knooppunt TrustFrameworkPolicy. De in de `PolicyId` volgende beleids-XML is *bijvoorbeeld B2C_1A_TrustFrameworkBase*:

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

Wanneer u de agents gaat uitvoeren en de beleidsbestanden uploadt, moet u ervoor zorgen dat ze in deze volgorde worden geüpload:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

De Identity Experience Framework dwingt deze volgorde af omdat de bestandsstructuur is gebouwd op een hiërarchische keten.

## <a name="test-your-pipeline"></a>Test uw pijplijn

De release-pijplijn testen:

1. Selecteer **Pijplijnen** en vervolgens **Releases.**
1. Selecteer de pijplijn die u eerder hebt gemaakt, bijvoorbeeld *DeployCustomPolicies.*
1. Selecteer **Release maken en** selecteer vervolgens Maken om **de** release in de wachtrij te krijgen.

U ziet een meldingsbanner met de melding dat er een release in de wachtrij is geplaatst. Als u de status wilt weergeven, selecteert u de koppeling in de meldingsbanner of selecteert u deze in de lijst op het **tabblad Releases.**

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

* [Service-naar-service-aanroepen met clientreferenties](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
