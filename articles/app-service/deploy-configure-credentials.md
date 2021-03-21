---
title: Implementatiereferenties configureren
description: Meer informatie over de typen implementatie referenties in Azure App Service en hoe u deze kunt configureren en gebruiken.
ms.topic: article
ms.date: 02/11/2021
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: c7d3c7c8b5da40a4e9ccd9085af5a850b9ebc3dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052344"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Implementatie referenties voor Azure App Service configureren
Als u de implementatie van een app vanaf een lokale computer wilt beveiligen, ondersteunt [Azure app service](./overview.md) twee typen referenties voor [lokale Git-implementatie](deploy-local-git.md) en [FTP/S-implementatie](deploy-ftp.md). Deze referenties zijn niet hetzelfde als de referenties van uw Azure-abonnement.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

> [!NOTE]
> De pagina **Development Center (klassiek)** in de Azure Portal, die de oude implementatie-ervaring is, wordt in maart 2021 afgeschaft. Deze wijziging is niet van invloed op bestaande implementatie-instellingen in uw app en u kunt de implementatie van apps blijven beheren op de pagina **implementatie centrum** .

## <a name="configure-user-scope-credentials"></a><a name="userscope"></a>Referenties voor gebruikers bereik configureren

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Voer de opdracht [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) uit. Vervang \<username> en \<password> door de gebruikersnaam en het wachtwoord van de gebruiker van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool @ niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wachtwoord als `null`.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

U kunt de referenties van het gebruikers bereik niet configureren met Azure PowerShell. Gebruik een andere methode of overweeg de [referenties voor het toepassings bereik](#appscope)te gebruiken. 

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

U kunt uw referenties voor gebruikers bereik configureren op de [resource pagina](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)van elke app. Ongeacht in welke app u deze referenties configureert, is dit van toepassing op alle apps voor alle abonnementen in uw Azure-account. 

In de [Azure Portal](https://portal.azure.com)moet u ten minste één app hebben voordat u toegang kunt krijgen tot de pagina implementatie referenties. Uw referenties voor gebruikers bereik configureren:

1. Selecteer in het menu links van uw app >-referenties voor het **implementatie centrum**  >  **FTPS** of **lokale Git/FTPS-referenties**.

    ![Laat zien hoe u het FTP-dash board kunt selecteren in het implementatie centrum van Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Schuif omlaag naar **gebruikers bereik**, Configureer de **gebruikers naam** en het **wacht woord** en selecteer vervolgens **Opslaan**.

Wanneer u de referenties voor de implementatie hebt ingesteld, kunt u de gebruikers naam van de *Git* -implementatie vinden op de pagina **overzicht** van uw app.

![Laat zien hoe u de gebruikers naam voor git-implementatie kunt vinden op de overzichts pagina van uw app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Als de Git-implementatie is geconfigureerd, wordt op de pagina een **Git/implementatie-gebruikers naam** weer gegeven. anders een **FTP/implementatie-gebruikers naam**.

> [!NOTE]
> Het implementatie wachtwoord voor uw gebruikers bereik wordt niet weer gegeven in Azure. Als u het wacht woord vergeet, kunt u uw referenties opnieuw instellen door de stappen in deze sectie te volgen.
>
> 

-----

## <a name="use-user-scope-credentials-with-ftpftps"></a>Referenties voor gebruikers bereik gebruiken met FTP-FTPS

Verificatie bij een FTP-FTPS-eind punt met referenties voor gebruikers bereik vereist een gebruikers naam in de volgende indeling: `<app-name>\<user-name>`

Omdat referenties voor gebruikers bereik zijn gekoppeld aan de gebruiker en niet aan een specifieke resource, moet de gebruikers naam deze indeling hebben om de aanmeldings actie naar het juiste app-eind punt te sturen.

## <a name="get-application-scope-credentials"></a><a name="appscope"></a>Referenties voor toepassings bereik ophalen

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Haal de referenties voor het toepassings bereik op met de opdracht [AZ webapp Deployment List-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Bijvoorbeeld:

```azurecli-interactive
az webapp deployment list-publishing-profiles --resource-group <group-name> --name <app-name>
```

Voor [lokale Git-implementatie](deploy-local-git.md)kunt u ook de opdracht [AZ webapp Deployment List-Publishing-credentials](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_credentials) gebruiken om een externe Git-URI voor uw app op te halen, waarbij de referenties voor het toepassings bereik al zijn Inge sloten. Bijvoorbeeld:

```azurecli-interactive
az webapp deployment list-publishing-credentials --resource-group <group-name> --name <app-name> --query scmUri
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Gebruik de opdracht [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) om de referenties voor het toepassings bereik op te halen. Bijvoorbeeld:

```azurepowershell-interactive
Get-AzWebAppPublishingProfile -ResourceGroupName <group-name> -Name <app-name>
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer in het menu links van uw app de FTPS-referenties voor het **implementatie centrum**  >   of **lokale Git/FTPS-referenties**.

    ![Laat zien hoe u het FTP-dash board kunt selecteren in het implementatie centrum van Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Selecteer in de sectie **toepassings bereik** de **Kopieer** koppeling om de gebruikers naam of het wacht woord te kopiëren.

-----

## <a name="reset-application-scope-credentials"></a>Referenties voor toepassings bereik opnieuw instellen

# <a name="azure-cli"></a>[Azure-CLI](#tab/cli)

Stel de referenties voor het toepassings bereik opnieuw in met behulp van de opdracht [AZ resource invoke-Action](/cli/azure/resource#az_resource_invoke_action) :

```azurecli-interactive
az resource invoke-action --action newpassword --resource-group <group-name> --name <app-name> --resource-type Microsoft.Web/sites
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Stel de referenties voor het toepassings bereik opnieuw in met de opdracht [invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) :

```azurepowershell-interactive
Invoke-AzResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action newpassword
```

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer in het menu links van uw app de FTPS-referenties voor het **implementatie centrum**  >   of **lokale Git/FTPS-referenties**.

    ![Laat zien hoe u het FTP-dash board kunt selecteren in het implementatie centrum van Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

2. Selecteer **opnieuw instellen** in de sectie **toepassings bereik** .

-----

## <a name="disable-basic-authentication"></a>Basis verificatie uitschakelen

Sommige organisaties moeten voldoen aan de beveiligings vereisten en voor komen dat de toegang via FTP of Web Deploy wordt uitgeschakeld. Op deze manier hebben de leden van de organisatie alleen toegang tot de App Services via Api's die worden beheerd door Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Voer de volgende CLI-opdracht uit om FTP-toegang tot de site uit te scha kelen. Vervang de tijdelijke aanduidingen door de resource groep en de naam van de site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of FTP-toegang is geblokkeerd, kunt u proberen te verifiëren met behulp van een FTP-client, zoals FileZilla. Als u de referenties voor publiceren wilt ophalen, gaat u naar de Blade overzicht van uw site en klikt u op publicatie profiel downloaden. Gebruik de FTP-hostnaam, gebruikers naam en het wacht woord van het bestand om te verifiëren. u ontvangt een 401-fout melding die aangeeft dat u niet bent gemachtigd.

### <a name="webdeploy-and-scm"></a>Webdeploy en SCM

Voer de volgende CLI-opdracht uit om basis verificatie toegang tot de Web Deploy-poort en de SCM-site uit te scha kelen. Vervang de tijdelijke aanduidingen door de resource groep en de naam van de site. 

```azurecli-interactive
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of de referenties van het publicatie profiel zijn geblokkeerd voor webimplementatie, kunt u [een web-app publiceren met Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Toegang tot de API uitschakelen

De API in de vorige sectie is een back-up van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Dit betekent dat u [een aangepaste rol kunt maken](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) en gebruikers met een lagere priveldged aan de rol kan toewijzen, zodat de basis verificatie op geen enkele site kan worden ingeschakeld. [Volg deze instructies](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)voor het configureren van de aangepaste rol.

U kunt [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) ook gebruiken om de geslaagde verificatie aanvragen te controleren en [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) te gebruiken om deze configuratie af te dwingen voor alle sites in uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van deze referenties voor het implementeren van uw app vanuit [lokale Git](deploy-local-git.md) of het gebruik van [FTP/S](deploy-ftp.md).
