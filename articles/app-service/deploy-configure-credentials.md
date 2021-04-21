---
title: Implementatiereferenties configureren
description: Meer informatie over de typen implementatiereferenties in Azure App Service en hoe u deze kunt configureren en gebruiken.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 008bfa58c117fc1b43227ba73902d921cec25795
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830571"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Implementatiereferenties configureren voor Azure App Service
Ter beveiliging van app-implementatie vanaf een lokale computer ondersteunt [Azure App Service](./overview.md) twee typen referenties voor lokale [Git-implementatie](deploy-local-git.md) en [FTP/S-implementatie.](deploy-ftp.md) Deze referenties zijn niet hetzelfde als de referenties van uw Azure-abonnement.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> De **pagina Development Center (klassiek)** in de Azure Portal, de oude implementatie-ervaring, wordt in maart 2021 afgeschaft. Deze wijziging heeft geen invloed op bestaande implementatie-instellingen in uw app en u kunt app-implementatie blijven beheren op de **pagina Implementatiecentrum.**

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Referenties voor gebruikersbereik configureren

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de [opdracht az webapp deployment user set](/cli/azure/webapp/deployment/user#az_webapp_deployment_user_set) uit. Vervang \<username> en \<password> door de gebruikersnaam en het wachtwoord van de gebruiker van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool @ niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wachtwoord als `null`.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

U kunt de referenties voor het gebruikersbereik niet configureren met Azure PowerShell. Gebruik een andere methode of overweeg het [gebruik van referenties voor toepassingsbereik.](#appscope) 

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

U kunt uw referenties voor het gebruikersbereik configureren op de resourcepagina van [elke app.](../azure-resource-manager/management/manage-resources-portal.md#manage-resources) Ongeacht in welke app u deze referenties configureert, is deze van toepassing op alle apps voor alle abonnementen in uw Azure-account. 

In de [Azure Portal](https://portal.azure.com)moet u ten minste één app hebben voordat u toegang hebt tot de pagina met implementatiereferenties. Uw referenties voor het gebruikersbereik configureren:

1. Selecteer in het linkermenu van uw app de > FTPS-referenties van het implementatiecentrum of lokale  >   **Git-/FTPS-referenties.**

    ![Laat zien hoe u het FTP-dashboard kunt selecteren in het implementatiecentrum in Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Schuif omlaag naar **Gebruikersbereik,** configureer **de gebruikersnaam** en het **wachtwoord** en selecteer **opslaan.**

Zodra u uw implementatiereferenties hebt ingesteld, kunt u de gebruikersnaam van de *Git-implementatie* vinden op de pagina **Overzicht van uw** app.

![Laat zien hoe u de gebruikersnaam van de Git-implementatie kunt vinden op de pagina Overzicht van uw app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Als De Git-implementatie is geconfigureerd, wordt op de pagina een **gebruikersnaam voor Git/implementatie weergegeven;** anders een **FTP-/implementatie-gebruikersnaam.**

> [!NOTE]
> Azure geeft uw implementatiewachtwoord voor het gebruikersbereik niet weer. Als u het wachtwoord bent vergeten, kunt u uw referenties opnieuw instellen door de stappen in deze sectie te volgen.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Referenties voor gebruikersbereik gebruiken met FTP/FTPS

Voor de authenticatie bij een FTP/FTPS-eindpunt met referenties voor gebruikersbereik is een gebruikersnaam met de volgende indeling vereist: `<app-name>\<user-name>`

Aangezien referenties voor het gebruikersbereik zijn gekoppeld aan de gebruiker en niet aan een specifieke resource, moet de gebruikersnaam deze indeling hebben om de aanmeldingsactie naar het juiste app-eindpunt te sturen.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Referenties voor toepassingsbereik op halen

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Haal de referenties voor het toepassingsbereik op met [de opdracht az webapp deployment list-publishing-profiles.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) Bijvoorbeeld:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Voor [lokale Git-implementatie](deploy-local-git.md)kunt u ook de opdracht [az webapp deployment list-publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) gebruiken om een externe Git-URI voor uw app op te halen, met de referenties voor het toepassingsbereik al ingesloten. Bijvoorbeeld:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Haal de referenties voor het toepassingsbereik op met de [opdracht Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) Bijvoorbeeld:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer ftps-referenties of lokale  >  **Git-/FTPS-referenties** in het linkermenu van uw app.

    ![Laat zien hoe u het FTP-dashboard kunt selecteren in het implementatiecentrum in Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Selecteer in **de sectie** Toepassingsbereik de koppeling **Kopiëren** om de gebruikersnaam of het wachtwoord te kopiëren.

-----

## <a name="reset-application-scope-credentials"></a>Referenties voor toepassingsbereik opnieuw instellen

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Stel de referenties voor het toepassingsbereik opnieuw in met [de opdracht az resource invoke-action:](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Stel de referenties voor het toepassingsbereik opnieuw in met de [opdracht Invoke-AzResourceAction:](/powershell/module/az.resources/invoke-azresourceaction)

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer ftps-referenties of lokale  >  **Git-/FTPS-referenties** in het linkermenu van uw app.

    ![Laat zien hoe u het FTP-dashboard kunt selecteren in het implementatiecentrum in Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Selecteer in **de sectie** Toepassingsbereik de optie **Opnieuw instellen.**

-----

## <a name="disable-basic-authentication"></a>Basisverificatie uitschakelen

Sommige organisaties moeten voldoen aan beveiligingsvereisten en schakelen de toegang liever uit via FTP of WebDeploy. Op deze manier hebben de leden van de organisatie alleen toegang tot de App Services via API's die worden beheerd door Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Voer de volgende CLI-opdracht uit om FTP-toegang tot de site uit te schakelen. Vervang de tijdelijke aanduidingen door uw resourcegroep en sitenaam. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of FTP-toegang is geblokkeerd, kunt u proberen te verifiëren met behulp van een FTP-client, zoals FileZilla. Als u de publicatiereferenties wilt ophalen, gaat u naar de overzichtsblade van uw site en klikt u op Publicatieprofiel downloaden. Gebruik de FTP-hostnaam, gebruikersnaam en het wachtwoord van het bestand om te verifiëren. U krijgt dan een 401-foutbericht dat aangeeft dat u niet bent geautoriseerd.

### <a name="webdeploy-and-scm"></a>WebDeploy en SCM

Voer de volgende CLI-opdracht uit om basistoegang tot de WebDeploy-poort en SCM-site uit te schakelen. Vervang de tijdelijke aanduidingen door uw resourcegroep en sitenaam. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of de referenties voor het publicatieprofiel zijn geblokkeerd op WebDeploy, probeert u een web-app te publiceren met [Visual Studio 2019.](/visualstudio/deployment/quickstart-deploy-to-azure)

### <a name="disable-access-to-the-api"></a>Toegang tot de API uitschakelen

De API in de vorige sectie wordt op rollen gebaseerd toegangsbeheer van [](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) Azure (Azure RBAC) gebruikt. Dit betekent dat u een aangepaste rol kunt maken en gebruikers met lagere machtigingen kunt toewijzen aan de rol, zodat ze geen basisvereeniging op sites kunnen inschakelen. Volg deze instructies om de aangepaste [rol te configureren.](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)

U kunt ook [Azure Monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) om geslaagde verificatieaanvragen te controleren en Azure Policy [om](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) deze configuratie af te dwingen voor alle sites in uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Ontdek hoe u deze referenties gebruikt om uw app te implementeren vanuit lokale [Git](deploy-local-git.md) of [met FTP/S.](deploy-ftp.md)
