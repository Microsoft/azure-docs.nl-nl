---
title: Implementatiereferenties configureren
description: Meer informatie over de typen implementatie referenties in Azure App Service en hoe u deze kunt configureren en gebruiken.
ms.topic: article
ms.date: 08/14/2019
ms.reviewer: byvinyal
ms.custom: seodec18
ms.openlocfilehash: e5793d21f27128162095e2d86e13006c5b6e7b7c
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007990"
---
# <a name="configure-deployment-credentials-for-azure-app-service"></a>Implementatie referenties voor Azure App Service configureren
[Azure app service](./overview.md) ondersteunt twee typen referenties voor [lokale Git-implementatie](deploy-local-git.md) en [FTP/S-implementatie](deploy-ftp.md). Deze referenties zijn niet hetzelfde als de referenties van uw Azure-abonnement.

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

## <a name="configure-user-level-credentials"></a><a name="userscope"></a>Referenties op gebruikers niveau configureren

U kunt uw referenties op gebruikers niveau configureren op de [resource pagina](../azure-resource-manager/management/manage-resources-portal.md#manage-resources)van elke app. Ongeacht in welke app u deze referenties configureert, is dit van toepassing op alle apps en voor alle abonnementen in uw Azure-account. 

### <a name="in-the-cloud-shell"></a>In de Cloud Shell

Als u de implementatie gebruiker wilt configureren in de [Cloud shell](https://shell.azure.com), voert u de opdracht [AZ webapp Deployment User set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) uit. Vervang \<username> en \<password> door de gebruikersnaam en het wachtwoord van de gebruiker van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool @ niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wachtwoord als `null`. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

### <a name="in-the-portal"></a>In de portal

In de Azure Portal moet u ten minste één app hebben voordat u toegang kunt krijgen tot de pagina implementatie referenties. Uw referenties op gebruikers niveau configureren:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **app Services**  >  **\<any_app>**  >  **Deployment Center**  >  **-FTP-**  >  **dash board**.

    ![Laat zien hoe u het FTP-dash board kunt selecteren in het implementatie centrum van Azure-app Services.](./media/app-service-deployment-credentials/access-no-git.png)

    Als u de Git-implementatie al hebt geconfigureerd, selecteert u **app Services**  >  **&lt; any_app>**  >  **Deployment Center**  >  **FTP/credentials**.

    ![Laat zien hoe u het FTP-dash board kunt selecteren in het implementatie centrum in Azure-app Services voor uw geconfigureerde Git-implementatie.](./media/app-service-deployment-credentials/access-with-git.png)

2. Selecteer **gebruikers referenties**, Configureer de gebruikers naam en het wacht woord en selecteer vervolgens **referenties opslaan**.

Wanneer u de referenties voor de implementatie hebt ingesteld, kunt u de gebruikers naam van de *Git* -implementatie vinden op de pagina **overzicht** van uw app.

![Laat zien hoe u de gebruikers naam voor git-implementatie kunt vinden op de overzichts pagina van uw app.](./media/app-service-deployment-credentials/deployment_credentials_overview.png)

Als de Git-implementatie is geconfigureerd, wordt op de pagina een **Git/implementatie-gebruikers naam** weer gegeven. anders een **FTP/implementatie-gebruikers naam**.

> [!NOTE]
> Het wacht woord voor implementatie op gebruikers niveau wordt niet weer gegeven in Azure. Als u het wacht woord vergeet, kunt u uw referenties opnieuw instellen door de stappen in deze sectie te volgen.
>
> 

## <a name="use-user-level-credentials-with-ftpftps"></a>Referenties op gebruikers niveau gebruiken met FTP-FTPS

Verificatie bij een FTP-FTPS-eind punt met referenties op gebruikers niveau vereist een gebruikers naam in de volgende indeling: `<app-name>\<user-name>`

Omdat referenties op gebruikers niveau zijn gekoppeld aan de gebruiker en niet aan een specifieke resource, moet de gebruikers naam de volgende indeling hebben om de aanmeldings actie door te sturen naar het juiste app-eind punt.

## <a name="get-and-reset-app-level-credentials"></a><a name="appscope"></a>Referenties op app-niveau ophalen en opnieuw instellen
De referenties op app-niveau ophalen:

1. Selecteer in het menu links in het [Azure Portal](https://portal.azure.com) **app Services**  >  **&lt; any_app>**  >  **Deployment Center**  >  **FTP/credentials**.

2. Selecteer **app-referenties** en selecteer de koppeling **kopiëren** om de gebruikers naam of het wacht woord te kopiëren.

Als u de referenties op app-niveau opnieuw wilt instellen, selecteert u **referenties opnieuw instellen** in hetzelfde dialoog venster.

## <a name="disable-basic-authentication"></a>Basis verificatie uitschakelen

Sommige organisaties moeten voldoen aan de beveiligings vereisten en voor komen dat de toegang via FTP of Web Deploy wordt uitgeschakeld. Op deze manier hebben de leden van de organisatie alleen toegang tot de App Services via Api's die worden beheerd door Azure Active Directory (Azure AD).

### <a name="ftp"></a>FTP

Voer de volgende CLI-opdracht uit om FTP-toegang tot de site uit te scha kelen. Vervang de tijdelijke aanduidingen door de resource groep en de naam van de site. 

```bash
az resource update --resource-group <resource-group> --name ftp --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of FTP-toegang is geblokkeerd, kunt u proberen te verifiëren met behulp van een FTP-client, zoals FileZilla. Als u de referenties voor publiceren wilt ophalen, gaat u naar de Blade overzicht van uw site en klikt u op publicatie profiel downloaden. Gebruik de FTP-hostnaam, gebruikers naam en het wacht woord van het bestand om te verifiëren. u ontvangt een 401-fout melding die aangeeft dat u niet bent gemachtigd.

### <a name="webdeploy-and-scm"></a>Webdeploy en SCM

Voer de volgende CLI-opdracht uit om basis verificatie toegang tot de Web Deploy-poort en de SCM-site uit te scha kelen. Vervang de tijdelijke aanduidingen door de resource groep en de naam van de site. 

```bash
az resource update --resource-group <resource-group> --name scm --namespace Microsoft.Web --resource-type basicPublishingCredentialsPolicies --parent sites/<site-name> --set properties.allow=false
```

Als u wilt controleren of de referenties van het publicatie profiel zijn geblokkeerd voor webimplementatie, kunt u [een web-app publiceren met Visual Studio 2019](/visualstudio/deployment/quickstart-deploy-to-azure).

### <a name="disable-access-to-the-api"></a>Toegang tot de API uitschakelen

De API in de vorige sectie is een back-up van Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Dit betekent dat u [een aangepaste rol kunt maken](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role) en gebruikers met een lagere priveldged aan de rol kan toewijzen, zodat de basis verificatie op geen enkele site kan worden ingeschakeld. [Volg deze instructies](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#create-a-custom-rbac-role)voor het configureren van de aangepaste rol.

U kunt [Azure monitor](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#audit-with-azure-monitor) ook gebruiken om de geslaagde verificatie aanvragen te controleren en [Azure Policy](https://azure.github.io/AppService/2020/08/10/securing-data-plane-access.html#enforce-compliance-with-azure-policy) te gebruiken om deze configuratie af te dwingen voor alle sites in uw abonnement.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van deze referenties voor het implementeren van uw app vanuit [lokale Git](deploy-local-git.md) of het gebruik van [FTP/S](deploy-ftp.md).
