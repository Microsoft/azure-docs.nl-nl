---
title: Beheerde identiteiten voor Azure-resources met Service Bus
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om toegang te krijgen Azure Service Bus entiteiten (wachtrijen, onderwerpen en abonnementen).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: 0558e00ac7e8ce67d2e5194b02d2de06f2d38ff1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785429"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory toegang tot Azure Service Bus resources
[Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) is een azure-functie waarmee u een beveiligde identiteit kunt maken die is gekoppeld aan de implementatie waaronder uw toepassingscode wordt uitgevoerd. Vervolgens kunt u die identiteit koppelen aan rollen voor toegangsbeheer die aangepaste machtigingen verlenen voor toegang tot specifieke Azure-resources die uw toepassing nodig heeft.

Met beheerde identiteiten beheert het Azure-platform deze runtime-identiteit. U hoeft de toegangssleutels niet op te slaan en te beveiligen in uw toepassingscode of -configuratie, voor de identiteit zelf of voor de resources die u nodig hebt om toegang te krijgen. Een Service Bus-client-app die wordt uitgevoerd in een Azure App Service-toepassing of op een virtuele machine met ingeschakelde beheerde entiteiten voor Azure-resources, hoeft geen SAS-regels en -sleutels of andere toegangstokens te verwerken. De client-app heeft alleen het eindpuntadres van de Service Bus Messaging-naamruimte nodig. Wanneer de app verbinding maakt, Service Bus de context van de beheerde entiteit aan de client in een bewerking die wordt weergegeven in een voorbeeld verder in dit artikel. Zodra de client is gekoppeld aan een beheerde identiteit, Service Bus alle geautoriseerde bewerkingen uitvoeren. Autorisatie wordt verleend door een beheerde entiteit te koppelen aan Service Bus rollen. 

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipaal (een gebruiker, groep of toepassing) toegang probeert te krijgen tot een Service Bus entiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces in twee stappen. 

 1. Eerst wordt de identiteit van de beveiligingsprincipaal geverifieerd en wordt een OAuth 2.0-token geretourneerd. De resourcenaam voor het aanvragen van een token is `https://servicebus.azure.net` .
 1. Vervolgens wordt het token doorgegeven als onderdeel van een aanvraag aan de Service Bus-service om toegang tot de opgegeven resource te autor toestemming te geven.

De verificatiestap vereist dat een toepassingsaanvraag een OAuth 2.0-toegangsteken tijdens runtime bevat. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een virtuele-machineschaalset of een Azure Function-app, kan deze een beheerde identiteit gebruiken voor toegang tot de resources. 

Voor de autorisatiestap moeten een of meer Azure-rollen worden toegewezen aan de beveiligingsprincipaal. Azure Service Bus biedt Azure-rollen die sets machtigingen voor Service Bus resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipaal bepalen de machtigingen die de principal krijgt. Zie Ingebouwde Azure-rollen voor Azure Service Bus voor meer informatie over het toewijzen [van Azure-rollen Azure Service Bus.](#azure-built-in-roles-for-azure-service-bus) 

Native toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook autoreren met Azure AD. In dit artikel wordt beschreven hoe u een toegangs token aanvraagt en dit gebruikt om aanvragen voor Service Bus verlenen. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-rollen toewijzen voor toegangsrechten
Azure Active Directory (Azure AD) autoreert toegangsrechten voor beveiligde resources via op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC).](../role-based-access-control/overview.md) Azure Service Bus definieert een set ingebouwde Azure-rollen die algemene sets machtigingen omvat die worden gebruikt voor toegang tot Service Bus-entiteiten. U kunt ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een Azure-rol wordt toegewezen aan een Azure AD-beveiligingsprincipaal, verleent Azure toegang tot deze resources voor die beveiligingsprincipaal. Toegang kan worden beperkt tot het abonnementsniveau, de resourcegroep of de Service Bus naamruimte. Een Azure AD-beveiligingsprincipaal kan een gebruiker, een groep, een toepassingsservice-principal of een beheerde identiteit voor Azure-resources zijn.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Ingebouwde Azure-rollen voor Azure Service Bus
Voor Azure Service Bus is het beheer van naamruimten en alle gerelateerde resources via de Azure Portal en de Azure Resource Management-API al beveiligd met behulp van het Azure RBAC-model. Azure biedt de onderstaande ingebouwde Azure-rollen voor het autoriseren van toegang tot een Service Bus naamruimte:

- [Azure Service Bus gegevenseigenaar:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)hiermee kunt u gegevenstoegang tot Service Bus naamruimte en de entiteiten ervan (wachtrijen, onderwerpen, abonnementen en filters)
- [Azure Service Bus: gebruik deze](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)rol om verzendtoegang te geven tot Service Bus naamruimte en de entiteiten ervan.
- [Azure Service Bus: gebruik deze](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)rol om toegang te verlenen tot Service Bus-naamruimte en de entiteiten ervan. 

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een Azure-rol toewijst een beveiligingsprincipal, moet u het toegangsbereik bepalen dat de beveiligingsprincipal moet hebben. Uit best practices blijkt dat het het beste is om het nauwst mogelijke bereik toe te wijzen.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot Service Bus resources kunt beperken, te beginnen met het kleinste bereik:

- **Wachtrij,** **onderwerp** of **abonnement:** roltoewijzing is van toepassing op de specifieke Service Bus entiteit. Momenteel biedt de Azure Portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan Service Bus Azure-rollen op abonnementsniveau. Hier is een voorbeeld van het gebruik van de Azure [CLI-opdracht: az-role-assignment-create](/cli/azure/role/assignment?#az_role_assignment_create) om een identiteit toe te wijzen aan een Service Bus Azure-rol: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus:** Roltoewijzing omvat de volledige topologie van Service Bus onder de naamruimte en de consumentengroep die ermee is gekoppeld.
- **Resourcegroep:** Roltoewijzing is van toepassing op alle Service Bus resources onder de resourcegroep.
- **Abonnement:** Roltoewijzing is van toepassing op Service Bus resources in alle resourcegroepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat het maximaal vijf minuten kan duren voordat Azure-roltoewijzingen zijn doorgegeven. 

Zie Roldefinities begrijpen voor meer informatie over hoe ingebouwde [rollen worden gedefinieerd.](../role-based-access-control/role-definitions.md#management-and-data-operations) Zie Aangepaste Azure-rollen voor meer informatie over het maken [van aangepaste Azure-rollen.](../role-based-access-control/custom-roles.md)

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een VM inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Service Bus-resources van uw VM te machtigen, moet u eerst beheerde identiteiten inschakelen voor Azure-resources op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in Azure AD
Als u vanuit een beheerde identiteit in uw toepassing een aanvraag bij de Service Bus-service wilt toestaan, configureert u eerst azure RBAC-instellingen (op rollen gebaseerd toegangsbeheer) voor die beheerde identiteit. Azure Service Bus definieert Azure-rollen die machtigingen omvatten voor het verzenden en lezen van Service Bus. Wanneer de Azure-rol wordt toegewezen aan een beheerde identiteit, krijgt de beheerde identiteit toegang tot Service Bus entiteiten binnen het juiste bereik.

Zie Verifiëren en autoriseren met Azure Active Directory voor toegang tot Service Bus resources voor meer informatie over het toewijzen [Service Bus azure-rollen.](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Gebruik Service Bus met beheerde identiteiten voor Azure-resources
Als u Service Bus beheerde identiteiten wilt gebruiken, moet u de rol en het juiste bereik toewijzen aan de identiteit. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en toegang heeft tot Service Bus resources.

Hier gebruiken we een voorbeeldwebtoepassing die wordt gehost in [Azure App Service](https://azure.microsoft.com/services/app-service/). Zie Een ASP.NET [Core-web-app](../app-service/quickstart-dotnetcore.md) maken in Azure voor stapsgewijs instructies voor het maken van een webtoepassing

Nadat de toepassing is gemaakt, volgt u deze stappen: 

1. Ga naar **Instellingen** en selecteer **Identiteit.** 
1. Selecteer de **Status** op **Aan.** 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/service-bus-managed-service-identity/identity-web-app.png)

Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-id gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

> [!NOTE]
> Wanneer u een beheerde identiteit gebruikt, moet connection string de volgende indeling hebben: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=ManagedIdentity` .

Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in Service Bus resources.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal
Als u een rol wilt toewijzen aan Service Bus naamruimte, gaat u naar de naamruimte in de Azure Portal. Geef de Access Control (IAM) voor de resource weer en volg deze instructies om roltoewijzingen te beheren:

> [!NOTE]
> Met de volgende stappen wordt een service-identiteitsrol toegewezen aan Service Bus naamruimten. U kunt dezelfde stappen volgen om een rol toe te wijzen in andere ondersteunde scopes (resourcegroep en abonnement). 
> 
> [Maak een Service Bus Messaging-naamruimte](service-bus-create-namespace-portal.md) als u er nog geen hebt. 

1. Navigeer Azure Portal naar uw Service Bus en geef **het** Overzicht voor de naamruimte weer. 
1. Selecteer **Access Control (IAM)** in het linkermenu om de instellingen voor toegangsbeheer voor de Service Bus weer te geven.
1.  Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien.
3.  Selecteer **Toevoegen** en selecteer vervolgens **Roltoewijzing toevoegen.**
4.  Volg deze **stappen op de** pagina Roltoewijzing toevoegen:
    1. Selecteer **bij** Rol de Service Bus die u wilt toewijzen. In dit voorbeeld is dit Azure Service Bus **eigenaar van gegevens.**
    1. Selecteer voor **het veld Toegang toewijzen** aan **App Service** onder Door het systeem **toegewezen beheerde identiteit.** 
    1. Selecteer het **abonnement** waarin de beheerde identiteit voor de web-app is gemaakt.
    1. Selecteer de **beheerde identiteit voor de** web-app die u hebt gemaakt. De standaardnaam voor de identiteit is hetzelfde als de naam van de web-app. 
    1. Selecteer vervolgens **Opslaan**.
        
        ![Pagina Roltoewijzing toevoegen](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de Service Bus onder het gedefinieerde bereik. 

    > [!NOTE]
    > Zie Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources voor een lijst met services die beheerde [identiteiten ondersteunen.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="run-the-app"></a>De app uitvoeren
Wijzig nu de standaardpagina van de ASP.NET toepassing die u hebt gemaakt. U kunt de webtoepassingscode uit deze [GitHub-opslagplaats gebruiken.](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)  

De pagina Default.aspx is uw landingspagina. De code vindt u in het bestand Default.aspx.cs. Het resultaat is een minimale webtoepassing met  enkele  invoervelden en met knoppen voor verzenden en ontvangen die verbinding maken met Service Bus verzenden of ontvangen van berichten.

U kunt zien hoe [het MessagingFactory-object](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) wordt initialiseren. In plaats van de SAS-tokenprovider (Shared Access Token) te gebruiken, maakt de code een tokenprovider voor de beheerde identiteit met de `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` aanroep. Er zijn dus geen geheimen die moeten worden bewaard en gebruikt. De stroom van de beheerde identiteitscontext naar Service Bus en de autorisatiehandhake worden automatisch verwerkt door de tokenprovider. Het is een eenvoudiger model dan het gebruik van SAS.

Nadat u deze wijzigingen hebt aangebracht, publiceert en voer u de toepassing uit. U kunt eenvoudig de juiste publicatiegegevens verkrijgen door een publicatieprofiel te downloaden en vervolgens te importeren in Visual Studio:

![Publicatieprofiel opmaken](./media/service-bus-managed-service-identity/msi3.png)
 
Als u berichten wilt verzenden of ontvangen, voert u de naam in van de naamruimte en de naam van de entiteit die u hebt gemaakt. Klik vervolgens op **verzenden** of **ontvangen.**


> [!NOTE]
> - De beheerde identiteit werkt alleen binnen de Azure-omgeving, in App Services, Azure-VM's en schaalsets. Voor .NET-toepassingen biedt de bibliotheek Microsoft.Azure.Services.AppAuthentication, die wordt gebruikt door het nuGet-pakket Service Bus, een abstractie van dit protocol en ondersteunt een lokale ontwikkelervaring. Met deze bibliotheek kunt u uw code ook lokaal testen op uw ontwikkelmachine, met behulp van uw gebruikersaccount van Visual Studio, Azure CLI 2.0 of geïntegreerde Active Directory-verificatie. Zie [Service-to-service authentication to Azure Key Vault using .NET (Service-naar-service-verificatie](/dotnet/api/overview/azure/service-to-service-authentication)met behulp van .NET) voor meer informatie over lokale ontwikkelopties met deze bibliotheek.  

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen Service Bus meer informatie over Service Bus messaging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
