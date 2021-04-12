---
title: Een beheer programma voor Windows virtueel bureau blad (klassiek) implementeren met Service-Principal-Azure
description: Het beheer programma voor het virtuele bureau blad van Windows (klassiek) implementeren met behulp van Power shell.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 60eec4580e222123795db9554f56e74cb01c5257
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444442"
---
# <a name="deploy-a-windows-virtual-desktop-classic-management-tool-with-powershell"></a>Een beheer hulpprogramma voor virtueel bureau blad (klassiek) van Windows implementeren met Power shell

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

In dit artikel wordt uitgelegd hoe u het beheer programma implementeert met behulp van Power shell.

## <a name="important-considerations"></a>Belangrijke overwegingen

Elk Azure Active Directory (Azure AD)-abonnement van de Tenant moet een eigen afzonderlijke implementatie van het beheer programma hebben. Dit hulp programma biedt geen ondersteuning voor B2B-scenario's (Business-to-Business) van Azure AD.

Dit beheer programma is een voor beeld. Micro soft zal belang rijke updates voor de beveiliging en kwaliteit bieden. [De bron code is beschikbaar in github](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Of u nu een klant of partner bent, we raden u aan het hulp programma aan te passen aan de behoeften van uw bedrijf.

De volgende browsers zijn compatibel met het beheer programma:

- Google Chrome 68 of hoger
- Micro soft Edge 40,15063 of hoger
- Mozilla Firefox 52,0 of hoger
- Safari 10 of hoger (alleen macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Wat u nodig hebt om het beheer programma te implementeren

Voordat u het beheer programma implementeert, hebt u een Azure Active Directory-gebruiker (Azure AD) nodig voor het maken van een app-registratie en het implementeren van de beheer GEBRUIKERSINTERFACE. Deze gebruiker moet:

- Machtiging voor het maken van resources in uw Azure-abonnement
- Toestemming voor het maken van een Azure AD-toepassing. Volg deze stappen om te controleren of uw gebruiker over de vereiste machtigingen beschikt door de instructies in de [vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)te volgen.

Nadat u het beheer programma hebt geïmplementeerd en geconfigureerd, raden we u aan een gebruiker te vragen de beheer GEBRUIKERSINTERFACE te starten om ervoor te zorgen dat alles werkt. De gebruiker die de beheer GEBRUIKERSINTERFACE start, moet beschikken over een roltoewijzing waarmee ze de Windows Virtual Desktop-Tenant kunnen weer geven of bewerken.

## <a name="set-up-powershell"></a>PowerShell instellen

Ga aan de slag door u aan te melden bij zowel de AZ-als Azure AD Power shell-modules. U kunt als volgt aanmelden:

1. Open Power shell als beheerder en navigeer naar de map waarin u de Power shell-scripts hebt opgeslagen.
2. Meld u aan bij Azure met een account met eigenaar-of Inzender machtigingen voor het Azure-abonnement dat u wilt gebruiken voor het maken van het beheer programma door de volgende cmdlet uit te voeren:

    ```powershell
    Login-AzAccount
    ```

3. Voer de volgende cmdlet uit om u aan te melden bij Azure AD met hetzelfde account dat u hebt gebruikt voor de AZ Power shell-module:

    ```powershell
    Connect-AzureAD
    ```

4. Daarna gaat u naar de map waar u de twee Power shell-scripts van de RDS-Templates GitHub opslag plaats hebt opgeslagen.

Houd het Power shell-venster dat u hebt gebruikt om u aan te melden, om extra Power shell-cmdlets uit te voeren terwijl u bent aangemeld.

## <a name="create-an-azure-active-directory-app-registration"></a>Een Azure Active Directory app-registratie maken

Als u het beheer programma wilt implementeren en configureren, moet u eerst de volgende Power shell-scripts downloaden van de [RDS-sjablonen github opslag plaats](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy/scripts)

```powershell
Set-Location -Path "c:\temp"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/createWvdMgmtUxAppRegistration.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\createWvdMgmtUxAppRegistration.ps1"
$uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/scripts/updateWvdMgmtUxApiUrl.ps1"
Invoke-WebRequest -Uri $uri -OutFile ".\updateWvdMgmtUxApiUrl.ps1"
```

Voer de volgende opdrachten uit om de app-registratie met de vereiste API-machtigingen te maken:

```powershell
$appName = Read-Host -Prompt "Enter a unique name for the management tool's app registration. The name can't contain spaces or special characters."
$azureSubscription = Get-AzSubscription | Out-GridView -PassThru
$subscriptionId = $azureSubscription.Id
Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription

.\createWvdMgmtUxAppRegistration.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de registratie van de Azure AD-app hebt voltooid, kunt u het beheer programma implementeren.

## <a name="deploy-the-management-tool"></a>Hulpprogramma voor beheer implementeren

Voer de volgende Power shell-opdrachten uit om het beheer hulpprogramma te implementeren en dit te koppelen aan de service-principal die u zojuist hebt gemaakt:

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateParameters = @{}
$templateParameters.Add('isServicePrincipal', $true)
$templateParameters.Add('azureAdminUserPrincipalNameOrApplicationId', $ServicePrincipalCredentials.UserName)
$templateParameters.Add('azureAdminPassword', $servicePrincipalCredentials.Password)
$templateParameters.Add('applicationName', $appName)

Get-AzSubscription -SubscriptionId $subscriptionId | Select-AzSubscription
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-management-ux/deploy/mainTemplate.json" `
    -TemplateParameterObject $templateParameters `
    -Verbose
```

Nadat u de web-app hebt gemaakt, moet u een omleidings-URI toevoegen aan de Azure AD-toepassing om gebruikers te kunnen aanmelden.

## <a name="set-the-redirect-uri"></a>De omleidings-URI instellen

Voer de volgende Power shell-opdrachten uit om de web-app-URL op te halen en in te stellen als de omleidings-URI voor verificatie (ook wel een antwoord-URL genoemd):

```powershell
$webApp = Get-AzWebApp -ResourceGroupName $resourceGroupName -Name $appName
$redirectUri = "https://" + $webApp.DefaultHostName + "/"
Get-AzureADApplication -All $true | where { $_.AppId -match $servicePrincipalCredentials.UserName } | Set-AzureADApplication -ReplyUrls $redirectUri
```

Nu u een omleidings-URI hebt toegevoegd, moet u de API-URL bijwerken zodat het beheer programma kan communiceren met de API-back-end-service.

## <a name="update-the-api-url-for-the-web-application"></a>De API-URL voor de webtoepassing bijwerken

Voer het volgende script uit om de API-URL-configuratie bij te werken in de front-end van de webtoepassing:

```powershell
.\updateWvdMgmtUxApiUrl.ps1 -AppName $appName -SubscriptionId $subscriptionId
```

Nu u de web-app voor het beheer programma volledig hebt geconfigureerd, is het tijd om de Azure AD-toepassing te controleren en toestemming te geven.

## <a name="verify-the-azure-ad-application-and-provide-consent"></a>De Azure AD-toepassing controleren en toestemming geven

De configuratie van de Azure AD-toepassing controleren en toestemming geven:

1. Open uw Internet browser en meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw beheerders account.
2. Zoek in de zoek balk aan de bovenkant van de Azure Portal naar **app-registraties** en selecteer het item onder **Services**.
3. Selecteer **alle toepassingen** en zoek de unieke app-naam die u hebt ingevoerd voor het Power shell-script in [een registratie voor een Azure Active Directory-app maken](#create-an-azure-active-directory-app-registration).
4. Selecteer in het deel venster aan de linkerkant van de browser **verificatie** en zorg ervoor dat de omleidings-URI hetzelfde is als de web-app-URL voor het beheer programma, zoals wordt weer gegeven in de volgende afbeelding.

   [![De verificatie pagina met de ingevoerde omleidings-URI ](../media/management-ui-redirect-uri-inline.png)](../media/management-ui-redirect-uri-expanded.png#lightbox)

5. Selecteer in het linkerdeel venster **API-machtigingen** om te bevestigen dat er machtigingen zijn toegevoegd. Als u een globale beheerder bent, selecteert u de knop **toestemming van `tenantname` beheerder verlenen voor** en volgt u de dialoog prompts om toestemming van de beheerder voor uw organisatie op te geven.

    [![De pagina ](../media/management-ui-permissions-inline.png) API-machtigingen](../media/management-ui-permissions-expanded.png#lightbox)

U kunt nu beginnen met het beheer programma.

## <a name="use-the-management-tool"></a>Het beheer programma gebruiken

Nu u het beheer programma op elk gewenst moment hebt ingesteld, kunt u dit altijd en overal starten. U kunt het hulp programma als volgt starten:

1. Open de URL van de web-app in een webbrowser. Als u de URL niet meer weet, kunt u zich aanmelden bij Azure, de app service vinden die u voor het beheer programma hebt geïmplementeerd en vervolgens de URL selecteren.
2. Meld u aan met de referenties van uw Windows-virtueel bureau blad.

   > [!NOTE]
   > Als u geen beheerder toestemming hebt gegeven tijdens het configureren van het beheer programma, moet elke gebruiker die zich aanmeldt een eigen toestemming van de gebruiker opgeven om het hulp programma te kunnen gebruiken.

3. Wanneer u wordt gevraagd om een Tenant groep te kiezen, selecteert u **standaard Tenant groep** in de vervolg keuzelijst.
4. Wanneer u **standaard Tenant groep** selecteert, wordt er een menu aan de linkerkant van het venster weer gegeven. Zoek in dit menu de naam van uw Tenant groep en selecteer deze.

   > [!NOTE]
   > Als u een aangepaste Tenant groep hebt, voert u de naam hand matig in in plaats van te kiezen in de vervolg keuzelijst.

## <a name="report-issues"></a>Problemen melden

Als u problemen ondervindt met het beheer programma of andere virtuele bureau blad-hulpprogram ma's van Windows, volgt u de instructies in [Azure Resource Manager sjablonen voor extern bureaublad-services](https://github.com/Azure/RDS-Templates/blob/master/README.md) om ze te rapporteren op github.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het beheer programma implementeert en er verbinding mee maakt, leert u hoe u Azure Service Health kunt gebruiken om service problemen en status adviezen te bewaken. Zie onze [zelf studie Service waarschuwingen instellen](set-up-service-alerts-2019.md)voor meer informatie.
