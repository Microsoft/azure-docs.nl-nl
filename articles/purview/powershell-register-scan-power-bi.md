---
title: Power shell gebruiken om Power BI te registreren en te scannen (preview-versie)
description: Meer informatie over hoe u Power shell gebruikt om een Power BI-Tenant in azure controle sfeer liggen te registreren en te scannen.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552430"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Power shell gebruiken voor het registreren en scannen van Power BI in azure controle sfeer liggen (preview) 

In dit artikel wordt beschreven hoe u Power shell gebruikt om een scan in te stellen van een Power BI-Tenant in een Azure controle sfeer liggen-catalogus.

## <a name="power-bi-authentication-background"></a>Achtergrond van Power BI-verificatie

De controle sfeer liggen-catalogus moet verbinding maken met de Power BI-beheer-API om artefacten in een Power BI-Tenant te scannen. De Power BI-beheer-API ondersteunt momenteel twee typen verificatie:

- Beheerde identiteit (MSI).
- Gedelegeerde gebruikers authenticatie.

> [!Note]
> MSI wordt aanbevolen, tenzij gedelegeerde gebruikers verificatie is vereist.

## <a name="create-a-security-group"></a>Een beveiligings groep maken

Elke controle sfeer liggen-catalogus heeft een eigen door het systeem toegewezen beheerde identiteit die toegang moet krijgen tot de Power BI Tenant om het scannen mogelijk te maken. De catalogus naam kan worden gebruikt om de identiteit te vinden op Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com)naar Azure Active Directory.
1. Maak een nieuwe beveiligings groep in uw Azure Active Directory door te volgen [een basis groep te maken en leden toe te voegen met behulp van Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > U kunt deze stap overs Laan als u al een beveiligings groep hebt om te gebruiken.

1. Zorg ervoor dat u beveiliging selecteert als het **groeps type**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Type beveiligings groep":::

1. Voeg de beheerde identiteit van uw catalogus toe aan deze beveiligings groep door leden en **leden toevoegen** te selecteren.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Het beheerde exemplaar van de catalogus toevoegen aan de groep":::

1. Zoek de catalogus en selecteer deze.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Catalogus toevoegen door ernaar te zoeken":::

1. Er wordt een melding weer gegeven dat u ziet dat deze is toegevoegd.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="MSI-bestand voor catalogus toevoegen":::

## <a name="associate-the-security-group-with-power-bi"></a>De beveiligings groep koppelen aan Power BI

1. Meld u aan bij de [Beheer Portal van Power bi](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Deze functie vlag toevoegen:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Met deze vlag wordt de functie ingeschakeld waarmee u de beveiligings groep kunt koppelen. Bijvoorbeeld:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > U moet een Power BI beheerder zijn om de pagina met Tenant instellingen weer te geven.

1. Met **instellingen voor ontwikkel aars**  >  **kunt u met Service-principals alleen-lezen Power bi-api's (preview-versie) gebruiken**.
1. Selecteer **specifieke beveiligings groepen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Afbeelding die laat zien hoe service-principals machtigingen voor alleen-lezen Power BI-beheer-API kunnen krijgen ":::

    > [!Caution]
    > Wanneer u de beveiligings groep die u hebt gemaakt (met de door de Data Catalog beheerde identiteit als lid) toestaat om alleen-lezen Power BI-beheer-Api's te gebruiken, kunt u ook toegang krijgen tot de meta gegevens (bijvoorbeeld dash board-en rapport namen, eigen aren, beschrijvingen, enzovoort) voor al uw Power BI artefacten in deze Tenant. Nadat de meta gegevens zijn opgehaald naar de Azure-controle sfeer liggen, worden de machtigingen van controle sfeer liggen, niet Power BI machtigingen, bepalen wie de meta gegevens kunnen zien.

    > [!Note]
    > U kunt de beveiligings groep verwijderen uit uw instellingen voor ontwikkel aars, maar de eerder uitgepakte meta gegevens worden niet verwijderd uit het controle sfeer liggen-account. U kunt deze afzonderlijk verwijderen.

## <a name="register-power-bi-and-set-up-a-scan"></a>Power BI registreren en een scan instellen

Nu u de catalogus machtigingen hebt gegeven om verbinding te maken met de beheer-API van uw Power BI-Tenant, moet u uw Scan instellen in de catalogus. U kunt dit doen door een Power shell-script te configureren en uit te voeren.

1. Down load en pak de ADC Power shell-cmdlets uit.
1. Configureer uw script door waarden op te geven voor de toewijzingen boven aan het script.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > U moet een bijdrager of eigenaar zijn van het abonnement waarmee u de opdrachten uitvoert.

1. Start de scan door het volgende script uit te voeren:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Power BI registreren en scannen

De aanbevolen verificatie methode is MSI. Als u echter een Power BI-Tenant in een andere Azure-Tenant wilt scannen dan uw catalogus, gebruikt u gedelegeerde authenticatie.

Als u gedelegeerde verificatie wilt uitvoeren, moet u referenties voor de gebruikers beheerder en de beheerders referenties van Power BI hebben. U moet ook een Azure-app maken en deze verlenen Power BI Tenant. ReadAll-machtigingen.

1. Navigeer naar [Azure Portal](https://portal.azure.com) en zoek naar **app-registraties**.

1. Selecteer bij **app-registraties** **+ nieuwe registratie**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Afbeelding die laat zien hoe u een nieuwe Azure app-registratie maakt":::

1. Voer een naam in voor uw app.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="nieuwe app registreren":::

1. Nadat de app is gemaakt, selecteert u **API-machtigingen** en vervolgens **+ een machtiging toevoegen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Afbeelding die laat zien hoe u een machtiging toevoegt aan de app":::

1. Selecteer **Power bi service** on **API-machtigingen aanvragen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Afbeelding waarin wordt getoond hoe de aan pbi-service moet worden geselecteerd":::

1. Selecteer **gedelegeerde machtigingen** en **Tenant. Read. all**. Selecteer vervolgens **machtigingen toevoegen**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Afbeelding die laat zien hoe u API-machtigingen aanvraagt":::

1. **Toestemming van beheerder verlenen** selecteren

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Afbeelding die laat zien hoe beheerders toestemming geven":::

1. Kopieer de toepassings-id van de **toepassing (client)** en de **Directory-id (Tenant)** .  U gebruikt deze waarden bij het instellen van de scan.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Afbeelding van het kopiëren van de client-en Tenant-Id's":::

1. Configureer uw scan in Power shell. Het script wordt gevraagd om referenties. U moet ten minste de rol Inzender en controle sfeer liggen hebben voor het Azure-abonnement dat u wilt gebruiken.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Voer uw scan uit.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Volgende stappen

Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)om aan de slag te gaan met Azure controle sfeer liggen.
