---
title: Inhoud implementeren met FTP/S
description: Meer informatie over het implementeren van uw app naar Azure App Service met FTP of FTPS. Verbeter de beveiliging van websites door onversleutelde FTP uit te scha kelen.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045799"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Uw app implementeren op Azure App Service met behulp van FTP/S

In dit artikel leest u hoe u FTP of FTPS gebruikt om uw web-app, back-end voor mobiele apps of API-apps te implementeren in [Azure app service](./overview.md).

Het FTP/S-eind punt voor uw app is al actief. Er is geen configuratie nodig om de implementatie van FTP/S in te scha kelen.

> [!NOTE]
> De pagina **Development Center (klassiek)** in de Azure Portal, die de oude implementatie-ervaring is, wordt in maart 2021 afgeschaft. Deze wijziging is niet van invloed op bestaande implementatie-instellingen in uw app en u kunt de implementatie van apps blijven beheren op de pagina **implementatie centrum** .

## <a name="get-deployment-credentials"></a>Implementatie referenties ophalen

1. Volg de instructies op [implementatie referenties configureren voor Azure app service](deploy-configure-credentials.md) om de referenties voor het toepassings bereik te kopiëren of de referenties voor het gebruikers bereik in te stellen. U kunt verbinding maken met het FTP/S-eind punt van uw app met behulp van de referenties.

1. Maak de FTP-gebruikers naam in de volgende indeling, afhankelijk van uw keuze van het referentie bereik:

    | Toepassings bereik | Gebruikers bereik |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    In App Service wordt het FTP/S-eind punt gedeeld tussen apps. Omdat de referenties van het gebruikers bereik niet zijn gekoppeld aan een specifieke resource, moet u de gebruikers naam van de gebruiker laten voorafgaan door met de app-naam zoals hierboven wordt weer gegeven.

## <a name="get-ftps-endpoint"></a>FTP/S-eind punt ophalen
    
# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Kopieer het FTPS-eind punt op dezelfde beheer pagina voor uw app waar ude implementatie referenties (  >  **FTP-referenties** voor het implementatie centrum) hebt gekopieerd.

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de opdracht [AZ webapp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) uit. In het volgende voor beeld wordt een [JMES-pad](https://jmespath.org/) gebruikt om de FTP/S-eind punten uit de uitvoer op te halen.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Elke app heeft twee FTP/S-eind punten, een is lezen/schrijven, terwijl de andere alleen-lezen ( `profileName` contains `ReadOnly` ) is en is voor scenario's voor gegevens herstel. Als u bestanden wilt implementeren met FTP, kopieert u de URL van het eind punt voor lezen/schrijven.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de opdracht [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) uit. In het volgende voor beeld wordt het FTP/S-eind punt geëxtraheerd uit de XML-uitvoer.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Bestanden implementeren in azure

1. Gebruik op uw FTP-client (bijvoorbeeld [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)of [WinSCP](https://winscp.net/index.php)) de verbindings gegevens die u hebt verzameld om verbinding te maken met uw app.
2. Kopieer uw bestanden en hun bijbehorende mapstructuur naar de [ **/site/wwwroot** -map](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) in azure (of de **/site/wwwroot/App_Data** Directory voor webjobs).
3. Blader naar de URL van uw app om te controleren of de app correct wordt uitgevoerd. 

> [!NOTE] 
> In tegens telling tot [op Git gebaseerde implementaties](deploy-local-git.md) en een [zip-implementatie](deploy-zip.md)biedt FTP-implementatie geen ondersteuning voor het bouwen van automatisering, zoals: 
>
> - afhankelijkheden herstellen (zoals NuGet, NPM, PIP en Composer automatisering)
> - compilatie van .NET binaire bestanden
> - genereren van web.config (dit is een [Node.js voor beeld](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Genereer deze benodigde bestanden hand matig op uw lokale machine en implementeer ze vervolgens samen met uw app.
>

## <a name="enforce-ftps"></a>FTPS afdwingen

Voor een betere beveiliging moet u FTP alleen toestaan via TLS/SSL. U kunt ook FTP-en FTPS uitschakelen als u geen FTP-implementatie gebruikt.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Op de resource pagina van uw app in [Azure Portal](https://portal.azure.com)selecteert u   >  **algemene instellingen** voor configuratie in het linkernavigatievenster.

2. Als u niet-versleutelde FTP wilt uitschakelen, selecteert u **FTPS alleen** in **FTP-status**. Als u zowel FTP als FTPS volledig wilt uitschakelen, selecteert u **uitgeschakeld**. Klik op **Opslaan** als u klaar bent. Als u **alleen FTPS** gebruikt, moet u TLS 1,2 of hoger afdwingen door te navigeren naar de Blade **TLS/SSL-instellingen** van uw web-app. TLS 1,0 en 1,1 worden alleen ondersteund met **FTPS**.

    ![FTP/S uitschakelen](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de opdracht [AZ webapp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) uit met het `--ftps-state` argument.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Mogelijke waarden voor `--ftps-state` zijn `AllAllowed` (FTP en FTPS ingeschakeld), `Disabled` (FTP en FTPS uitgeschakeld) en `FtpsOnly` (alleen FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer de opdracht [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) uit met de `-FtpsState` para meter.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Mogelijke waarden voor `--ftps-state` zijn `AllAllowed` (FTP en FTPS ingeschakeld), `Disabled` (FTP en FTPS uitgeschakeld) en `FtpsOnly` (alleen FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Problemen met FTP-implementatie oplossen

- [Hoe kan ik problemen met FTP-implementatie oplossen?](#how-can-i-troubleshoot-ftp-deployment)
- [Ik kan niet FTP en mijn code publiceren. Hoe kan ik het probleem oplossen?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Hoe kan ik problemen met FTP-implementatie oplossen?

De eerste stap voor het oplossen van problemen met FTP-implementatie is het isoleren van een implementatie probleem van een runtime-toepassings probleem.

Een implementatie probleem leidt doorgaans ertoe dat er geen bestanden of verkeerde bestanden in uw app worden geïmplementeerd. U kunt problemen oplossen door uw FTP-implementatie te onderzoeken of door een alternatief pad voor de implementatie te selecteren (zoals broncode beheer).

Een runtime-toepassings probleem resulteert doorgaans in de juiste set met bestanden die zijn geïmplementeerd in uw app, maar een onjuist gedrag van de app. U kunt problemen oplossen door te focussen op code gedrag tijdens runtime en het onderzoeken van specifieke fout paden.

Zie implementatie-en [runtime problemen](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)als u een probleem met de implementatie of de uitvoering wilt vaststellen.

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Ik kan FTP niet gebruiken en mijn code niet publiceren. Hoe kan ik het probleem oplossen?
Controleer of u de juiste [hostnaam](#get-ftps-endpoint) en [referenties](#get-deployment-credentials)hebt ingevoerd. Controleer ook of de volgende FTP-poorten op uw computer niet zijn geblokkeerd door een firewall:

- Verbindings poort voor FTP-besturings element: 21, 990
- FTP-gegevens verbindings poort: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Hoe kan ik verbinding maken met FTP in Azure App Service via de passieve modus?
Azure App Service ondersteunt verbinding via de actieve en passieve modus. De passieve modus verdient de voor keur omdat uw implementatie computers zich doorgaans achter een firewall bevinden (in het besturings systeem of als onderdeel van een thuis-of bedrijfs netwerk). Bekijk een [voor beeld in de WinSCP-documentatie](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Meer bronnen

* [Lokale Git-implementatie in Azure App Service](deploy-local-git.md)
* [Implementatie referenties Azure App Service](deploy-configure-credentials.md)
* Voor [Beeld: een web-app maken en bestanden implementeren met FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).
* Voor [Beeld: bestanden uploaden naar een web-app met FTP (Power shell)](./scripts/powershell-deploy-ftp.md).
