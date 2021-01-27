---
title: Beheerde identiteiten voor Azure-resources met Service Bus
description: In dit artikel wordt beschreven hoe u beheerde identiteiten gebruikt om toegang te krijgen tot Azure Service Bus entiteiten (wacht rijen, onderwerpen en abonnementen).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: dac12820adf863a7e51457d34f9180543089fc7b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881564"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Azure Service Bus bronnen
[Beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) is een functie van meerdere Azure waarmee u een beveiligde identiteit kunt maken die is gekoppeld aan de implementatie waaronder uw toepassings code wordt uitgevoerd. U kunt deze identiteit vervolgens koppelen aan de toegangs beheer rollen die aangepaste machtigingen verlenen om toegang te krijgen tot specifieke Azure-resources die uw toepassing nodig heeft.

Met beheerde identiteiten beheert het Azure-platform deze runtime-identiteit. U hoeft geen toegangs sleutels op te slaan en te beveiligen in uw toepassings code of configuratie, hetzij voor de identiteit zelf, hetzij voor de resources die u nodig hebt. Een Service Bus client-app die wordt uitgevoerd in een Azure App Service toepassing of op een virtuele machine met ingeschakelde beheerde entiteiten voor Azure-bronnen ondersteuning, hoeft geen SAS-regels en sleutels of andere toegangs tokens te verwerken. De client-app heeft alleen het eindpunt adres van de Service Bus Messa ging-naam ruimte nodig. Wanneer de app verbinding maakt, Service Bus de context van de beheerde entiteit koppelen aan de client in een bewerking die verderop in dit artikel wordt weer gegeven. Zodra deze is gekoppeld aan een beheerde identiteit, kan uw Service Bus-client alle geautoriseerde bewerkingen uitvoeren. Autorisatie wordt verleend door een beheerde entiteit te koppelen aan Service Bus rollen. 

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een Service Bus entiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. De resource naam voor het aanvragen van een token is `https://servicebus.azure.net` .
 1. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de Service Bus-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure function-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources. 

De autorisatie stap vereist dat er een of meer Azure-rollen aan de beveiligingsprincipal worden toegewezen. Azure Service Bus biedt Azure-rollen die sets machtigingen voor Service Bus resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie voor meer informatie over het toewijzen van Azure-rollen aan Azure Service Bus [Azure ingebouwde rollen voor Azure service bus](#azure-built-in-roles-for-azure-service-bus). 

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook worden geautoriseerd met Azure AD. In dit artikel leest u hoe u een toegangs token aanvraagt en hoe u het kunt gebruiken om aanvragen voor Service Bus-resources te autoriseren. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/overview.md). Azure Service Bus definieert een set ingebouwde rollen van Azure die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot Service Bus entiteiten, en u kunt ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep of de Service Bus naam ruimte. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een beheerde identiteit voor Azure-resources zijn.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Ingebouwde rollen van Azure voor Azure Service Bus
Voor Azure Service Bus is het beheer van naam ruimten en alle gerelateerde resources via de Azure Portal en de Azure Resource Management-API al beveiligd met behulp van het Azure RBAC-model. Azure biedt de onderstaande ingebouwde Azure-rollen voor het verlenen van toegang tot een Service Bus naam ruimte:

- [Azure Service Bus gegevens eigenaar](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Hiermee wordt gegevens toegang tot Service Bus naam ruimte en de entiteiten (wacht rijen, onderwerpen, abonnementen en filters) ingeschakeld
- [Azure Service Bus gegevens afzender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): gebruik deze rol om toegang te geven tot Service Bus naam ruimte en de entiteiten.
- [Azure Service Bus gegevens ontvanger](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): gebruik deze rol om toegang te krijgen tot Service Bus naam ruimte en de bijbehorende entiteiten. 

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een Azure-rol toewijst een beveiligingsprincipal, moet u het toegangsbereik bepalen dat de beveiligingsprincipal moet hebben. Uit best practices blijkt dat het het beste is om het nauwst mogelijke bereik toe te wijzen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Service Bus resources kunt bereiken, te beginnen met het smalle bereik:

- **Wachtrij**, **onderwerp** of **abonnement**: roltoewijzing is van toepassing op de specifieke service bus entiteit. Op dit moment biedt de Azure Portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan Service Bus Azure-rollen op abonnements niveau. Hier volgt een voor beeld van het gebruik van de Azure CLI-opdracht: [AZ-Role-Assignment-Create](/cli/azure/role/assignment?#az-role-assignment-create) om een identiteit toe te wijzen aan een service bus Azure-rol: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Service Bus naam ruimte**: roltoewijzing omvat de volledige topologie van service bus onder de naam ruimte en aan de Consumer groep die eraan is gekoppeld.
- **Resource groep**: roltoewijzing is van toepassing op alle service bus resources onder de resource groep.
- **Abonnement**: roltoewijzing is van toepassing op alle service bus resources in alle resource groepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat Azure-roltoewijzingen het Maxi maal vijf minuten kan duren voordat deze wordt door gegeven. 

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd [begrijpen functie definities](../role-based-access-control/role-definitions.md#management-and-data-operations). Zie [aangepaste rollen in azure](../role-based-access-control/custom-roles.md)voor meer informatie over het maken van aangepaste Azure-rollen.

## <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen
Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om Service Bus-resources van uw virtuele machine te autoriseren, moet u eerst beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie een van de volgende artikelen voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure-portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjabloon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Client bibliotheken Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Machtigingen verlenen aan een beheerde identiteit in azure AD
Als u een aanvraag voor de Service Bus-service wilt machtigen vanuit een beheerde identiteit in uw toepassing, moet u eerst Azure RBAC-instellingen (op rollen gebaseerd toegangs beheer) voor die beheerde identiteit configureren. Azure Service Bus definieert Azure-rollen met machtigingen voor het verzenden en lezen van Service Bus. Wanneer de Azure-rol is toegewezen aan een beheerde identiteit, wordt aan de beheerde identiteit toegang verleend tot Service Bus entiteiten op het juiste bereik.

Zie [verifiëren en autoriseren met Azure Active Directory voor toegang tot Service Bus resources](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)voor meer informatie over het toewijzen van Azure-rollen.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Service Bus met beheerde identiteiten gebruiken voor Azure-resources
Als u Service Bus met beheerde identiteiten wilt gebruiken, moet u de identiteit van de rol en het juiste bereik toewijzen. De procedure in deze sectie maakt gebruik van een eenvoudige toepassing die wordt uitgevoerd onder een beheerde identiteit en die toegang heeft tot Service Bus resources.

Hier gebruiken we een voor beeld-webtoepassing die wordt gehost in [Azure app service](https://azure.microsoft.com/services/app-service/). Zie [een ASP.net core web-app maken in azure](../app-service/quickstart-dotnetcore.md) voor stapsgewijze instructies voor het maken van een webtoepassing

Wanneer de toepassing is gemaakt, voert u de volgende stappen uit: 

1. Ga naar **instellingen** en selecteer **identiteit**. 
1. Selecteer de **status** die moet worden **ingeschakeld**. 
1. Selecteer **Opslaan** om de instelling op te slaan. 

    ![Beheerde identiteit voor een web-app](./media/service-bus-managed-service-identity/identity-web-app.png)

Zodra u deze instelling hebt ingeschakeld, wordt er een nieuwe service-identiteit gemaakt in uw Azure Active Directory (Azure AD) en geconfigureerd in de App Service host.

> [!NOTE]
> Wanneer u een beheerde identiteit gebruikt, moet de connection string de volgende indeling hebben: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=Managed Identity` .

Wijs deze service-identiteit nu toe aan een rol in het vereiste bereik in uw Service Bus resources.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal
Als u een rol aan een Service Bus naam ruimte wilt toewijzen, gaat u naar de naam ruimte in de Azure Portal. Geef de Access Control (IAM)-instellingen voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

> [!NOTE]
> Met de volgende stappen wordt een service-identiteits functie toegewezen aan uw Service Bus-naam ruimten. U kunt dezelfde stappen volgen om een rol toe te wijzen aan andere ondersteunde bereiken (resource groep en-abonnement). 
> 
> [Maak een service bus Messa ging-naam ruimte](service-bus-create-namespace-portal.md) als u er geen hebt. 

1. Ga in het Azure Portal naar uw Service Bus naam ruimte en geef het **overzicht** voor de naam ruimte weer. 
1. Selecteer **Access Control (IAM)** in het menu links om instellingen voor toegangs beheer voor de naam ruimte service bus weer te geven.
1.  Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien.
3.  Selecteer **toevoegen** en selecteer vervolgens **functie toewijzing toevoegen**.
4.  Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer bij **rol** de service bus rol die u wilt toewijzen. In dit voor beeld is het **Azure service bus eigenaar** van de gegevens.
    1. Selecteer **app service** onder door het **systeem toegewezen beheerde identiteit** voor het veld **toegang toewijzen aan** . 
    1. Selecteer het **abonnement** waarin de beheerde identiteit voor de web-app is gemaakt.
    1. Selecteer de **beheerde identiteit** voor de web-app die u hebt gemaakt. De standaard naam voor de identiteit is hetzelfde als de naam van de web-app. 
    1. Selecteer vervolgens **Opslaan**.
        
        ![Pagina roltoewijzing toevoegen](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Zodra u de rol hebt toegewezen, heeft de webtoepassing toegang tot de Service Bus entiteiten onder het gedefinieerde bereik. 

    > [!NOTE]
    > Zie [Services die beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)voor een lijst met services die beheerde identiteiten ondersteunen.

### <a name="run-the-app"></a>De app uitvoeren
Wijzig nu de standaard pagina van de ASP.NET-toepassing die u hebt gemaakt. U kunt de code van de webtoepassing van [deze github-opslag plaats](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)gebruiken.  

De pagina default. aspx is uw landings pagina. De code vindt u in het Default.aspx.cs-bestand. Het resultaat is een minimale webtoepassing met enkele invoer velden en met de knoppen **verzenden** en **ontvangen** die verbinding maken met Service Bus om berichten te verzenden of te ontvangen.

U ziet hoe het [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) -object wordt geïnitialiseerd. In plaats van de SAS-token provider (Shared Access token) te gebruiken, maakt de code een token provider voor de beheerde identiteit met de `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();` aanroep. Er zijn dus geen geheimen om te behouden en te gebruiken. De stroom van de beheerde identiteits context naar Service Bus en de autorisatie-Handshake worden automatisch verwerkt door de token provider. Het is een eenvoudiger model dan het gebruik van SAS.

Nadat u deze wijzigingen hebt aangebracht, publiceert u de toepassing en voert u deze uit. U kunt eenvoudig de juiste publicatie gegevens verkrijgen door een publicatie profiel te downloaden en vervolgens te importeren in Visual Studio:

![Publicatie profiel ophalen](./media/service-bus-managed-service-identity/msi3.png)
 
Als u berichten wilt verzenden of ontvangen, voert u de naam van de naam ruimte en de naam van de entiteit die u hebt gemaakt in. Klik vervolgens op **verzenden** of **ontvangen**.


> [!NOTE]
> - De beheerde identiteit werkt alleen in de Azure-omgeving, op app-Services, virtuele Azure-machines en schaal sets. Voor .NET-toepassingen is de bibliotheek micro soft. Azure. Services. AppAuthentication, die wordt gebruikt door het Service Bus NuGet-pakket, een abstractie van dit protocol en wordt een lokale ontwikkel ervaring ondersteund. Met deze bibliotheek kunt u uw code ook lokaal op uw ontwikkel computer testen met behulp van uw gebruikers account uit Visual Studio, Azure CLI 2,0 of Active Directory geïntegreerde verificatie. Zie [service-to-service-verificatie voor Azure Key Vault met .net](/dotnet/api/overview/azure/service-to-service-authentication)voor meer informatie over de lokale ontwikkelings opties voor deze bibliotheek.  
> 
> - Beheerde identiteiten werken momenteel niet met App Service implementatie sleuven.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)