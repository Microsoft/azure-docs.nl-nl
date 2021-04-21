---
title: Inhoud implementeren met FTP/S
description: Meer informatie over het implementeren van uw app Azure App Service ftp of FTPS. De websitebeveiliging verbeteren door niet-versleutelde FTP uit te uitschakelen.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: fd856ee47c1100292f7558bb0fa2a1772951a3cb
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832365"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uw app implementeren in Azure App Service ftp/s

In dit artikel wordt beschreven hoe u FTP of FTPS kunt gebruiken om uw web-app, back-en-back-en-app voor mobiele apps of API-apps te implementeren [Azure App Service.](./overview.md)

Het FTP/S-eindpunt voor uw app is al actief. Er is geen configuratie nodig om FTP/S-implementatie in te kunnenschakelen.

> [!NOTE]
> De **pagina Development Center (klassiek)** in de Azure Portal, de oude implementatie-ervaring, wordt in maart 2021 afgeschaft. Deze wijziging heeft geen invloed op bestaande implementatie-instellingen in uw app en u kunt app-implementatie blijven beheren op de **pagina Implementatiecentrum.**

## <a name="get-deployment-credentials"></a>Implementatiereferenties op halen

1. Volg de instructies in [Implementatiereferenties](deploy-configure-credentials.md) configureren voor Azure App Service om de referenties voor het toepassingsbereik te kopiëren of de referenties voor het gebruikersbereik in te stellen. U kunt met beide referenties verbinding maken met het FTP/S-eindpunt van uw app.

1. Maak de FTP-gebruikersnaam in de volgende indeling, afhankelijk van uw keuze van referentiebereik:

    | Toepassingsbereik | Gebruikersbereik |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    In App Service wordt het FTP/S-eindpunt gedeeld tussen apps. Omdat de referenties voor het gebruikersbereik niet zijn gekoppeld aan een specifieke resource, moet u de gebruikersnaam voor het gebruikersbereik voorbereiden met de app-naam, zoals hierboven wordt weergegeven.

## <a name="get-ftps-endpoint"></a>FTP/S-eindpunt op halen
    
# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Kopieer het FTPS-eindpunt op dezelfde beheerpagina vooruw app waar u de  >  **implementatiereferenties (FTP-referenties** voor het **implementatiecentrum) hebt gekopieerd.**

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de [opdracht az webapp deployment list-publishing-profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) uit. In het volgende voorbeeld wordt een [JMES-pad gebruikt om](https://jmespath.org/) de FTP/S-eindpunten uit de uitvoer te extraheren.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Elke app heeft twee FTP/S-eindpunten, één is lezen/schrijven, terwijl de andere alleen-lezen is ( bevat ) en is voor scenario's voor `profileName` `ReadOnly` gegevensherstel. Als u bestanden wilt implementeren met FTP, kopieert u de URL van het eindpunt voor lezen/schrijven.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de [opdracht Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) uit. In het volgende voorbeeld wordt het FTP/S-eindpunt uit de XML-uitvoer geëxtraheert.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Bestanden implementeren in Azure

1. Gebruik vanuit uw FTP-client (bijvoorbeeld [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberd cybersecurity](https://cyberduck.io/)of [WinSCP) de verbindingsgegevens](https://winscp.net/index.php)die u hebt verzameld om verbinding te maken met uw app.
2. Kopieer uw bestanden en hun respectieve mapstructuur naar de [ **map /site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in Azure (of de map **/site/wwwroot/App_Data/Jobs/** voor WebJobs).
3. Blader naar de URL van uw app om te controleren of de app correct wordt uitgevoerd. 

> [!NOTE] 
> In [tegenstelling tot Implementaties op basis van Git](deploy-local-git.md) en [Zip-implementatie](deploy-zip.md)biedt FTP-implementatie geen ondersteuning voor buildautomatisering, zoals: 
>
> - herstel van afhankelijkheden (zoals NuGet, NPM, PIP en Composer-automatiseringen)
> - compilatie van binaire .NET-bestanden
> - generatie van web.config (hier is een [voorbeeldNode.js )](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Genereer deze benodigde bestanden handmatig op uw lokale computer en implementeer ze vervolgens samen met uw app.
>

## <a name="enforce-ftps"></a>FTPS afdwingen

Voor verbeterde beveiliging moet u FTP alleen via TLS/SSL toestaan. U kunt ftp en FTPS ook uitschakelen als u geen FTP-implementatie gebruikt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer op de resourcepagina van uw app in [Azure Portal](https://portal.azure.com)de optie   >  **Configuratie Algemene instellingen** in het linkernavigatievenster.

2. Als u niet-versleutelde FTP wilt uitschakelen, selecteert **u FTPS alleen** met **de FTP-status**. Als u zowel FTP als FTPS volledig wilt uitschakelen, selecteert u **Uitgeschakeld.** Klik op **Opslaan** als u klaar bent. Als u **alleen FTPS gebruikt,** moet u TLS 1.2 of hoger afdwingen door te navigeren naar de blade **TLS/SSL-instellingen** van uw web-app. TLS 1.0 en 1.1 worden alleen ondersteund met **FTPS.**

    ![FTP/S uitschakelen](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de [opdracht az webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) uit met het argument `--ftps-state` .

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Mogelijke waarden voor `--ftps-state` zijn `AllAllowed` (FTP en FTPS ingeschakeld), `Disabled` (FTP en FTPs uitgeschakeld) en `FtpsOnly` (alleen FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de [opdracht Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) uit met de `-FtpsState` parameter .

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Mogelijke waarden voor `--ftps-state` zijn `AllAllowed` (FTP en FTPS ingeschakeld), `Disabled` (FTP en FTPs uitgeschakeld) en `FtpsOnly` (alleen FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Problemen met FTP-implementatie oplossen

- [Hoe kan ik problemen met FTP-implementatie oplossen?](#how-can-i-troubleshoot-ftp-deployment)
- [Ik kan ftp niet gebruiken en mijn code niet publiceren. Hoe kan ik het probleem oplossen?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hoe kan ik problemen met FTP-implementatie oplossen?

De eerste stap voor het oplossen van problemen met FTP-implementatie is het isoleren van een implementatieprobleem van een runtimetoepassingsprobleem.

Een implementatieprobleem resulteert doorgaans in geen bestanden of verkeerde bestanden die in uw app zijn geïmplementeerd. U kunt problemen oplossen door uw FTP-implementatie te onderzoeken of een alternatief implementatiepad (zoals broncodebeheer) te selecteren.

Een probleem met een runtimetoepassing resulteert doorgaans in de juiste set bestanden die in uw app zijn geïmplementeerd, maar onjuist app-gedrag. U kunt problemen oplossen door u te richten op codegedrag tijdens runtime en specifieke foutpaden te onderzoeken.

Zie Deployment vs. runtime issues (Implementatie- versus [runtimeproblemen)](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)om een probleem met de implementatie of runtime vast te stellen.

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Ik kan FTP niet gebruiken en mijn code niet publiceren. Hoe kan ik het probleem oplossen?
Controleer of u de juiste [hostnaam en](#get-ftps-endpoint) referenties [hebt ingevoerd.](#get-deployment-credentials) Controleer ook of de volgende FTP-poorten op uw computer niet worden geblokkeerd door een firewall:

- Verbindingspoort voor FTP-besturingselement: 21, 990
- FTP-gegevensverbindingspoort: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?
Azure App Service ondersteunt verbinding maken via zowel de modus Actief als passief. De passieve modus heeft de voorkeur omdat uw implementatiemachines zich meestal achter een firewall bevinden (in het besturingssysteem of als onderdeel van een thuisnetwerk of bedrijfsnetwerk). Zie een [voorbeeld uit de WinSCP-documentatie.](https://winscp.net/docs/ui_login_connection) 

## <a name="more-resources"></a>Meer bronnen

* [Lokale Git-implementatie in Azure App Service](deploy-local-git.md)
* [Azure App Service implementatiereferenties](deploy-configure-credentials.md)
* [Voorbeeld: Een web-app maken en bestanden implementeren met FTP (Azure CLI).](./scripts/cli-deploy-ftp.md)
* [Voorbeeld: bestanden uploaden naar een web-app met ftp (PowerShell).](./scripts/powershell-deploy-ftp.md)
