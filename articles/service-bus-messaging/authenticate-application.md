---
title: Een toepassing verifiëren voor toegang tot Azure Service Bus entiteiten
description: Dit artikel bevat informatie over het verifiëren van een toepassing met Azure Active Directory om toegang te krijgen tot Azure Service Bus entiteiten (wacht rijen, onderwerpen, enzovoort)
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: c4e19c0ab26d491ba0b95159e274383431aefaee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92518225"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-service-bus-entities"></a>Een toepassing met Azure Active Directory voor toegang tot Azure Service Bus entiteiten verifiëren en autoriseren
Azure Service Bus ondersteunt het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor het Service Bus van entiteiten (wacht rijen, onderwerpen, abonnementen of filters). Met Azure AD kunt u Azure RBAC (op rollen gebaseerd toegangs beheer) gebruiken om machtigingen te verlenen aan een beveiligingsprincipal, wat een gebruiker, groep of toepassings service-principal kan zijn. Zie [informatie over de verschillende rollen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Wanneer een beveiligingsprincipal (een gebruiker, groep of toepassing) probeert toegang te krijgen tot een Service Bus entiteit, moet de aanvraag worden geautoriseerd. Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat. 

 1. Eerst wordt de identiteit van de beveiligingsprincipal geverifieerd en wordt een OAuth 2,0-token geretourneerd. De resource naam voor het aanvragen van een token is `https://servicebus.azure.net` .
 1. Vervolgens wordt het token door gegeven als onderdeel van een aanvraag aan de Service Bus-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime. Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure-VM, een schaalset voor virtuele machines of een Azure function-app, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources. Zie [toegang verifiëren voor Azure service bus resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](service-bus-managed-service-identity.md)voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden door gegeven aan service bus service. 

De autorisatie stap vereist dat er een of meer Azure-rollen aan de beveiligingsprincipal worden toegewezen. Azure Service Bus biedt Azure-rollen die sets machtigingen voor Service Bus resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal, bepalen de machtigingen die de principal heeft. Zie voor meer informatie over het toewijzen van Azure-rollen aan Azure Service Bus [Azure ingebouwde rollen voor Azure service bus](#azure-built-in-roles-for-azure-service-bus). 

Systeem eigen toepassingen en webtoepassingen die aanvragen indienen bij Service Bus kunnen ook worden geautoriseerd met Azure AD. In dit artikel leest u hoe u een toegangs token aanvraagt en hoe u het kunt gebruiken om aanvragen voor Service Bus-resources te autoriseren. 


## <a name="assigning-azure-roles-for-access-rights"></a>Azure-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [Azure RBAC](../role-based-access-control/overview.md). Azure Service Bus definieert een set ingebouwde rollen van Azure die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot Service Bus entiteiten, en u kunt ook aangepaste rollen definiëren voor toegang tot de gegevens.

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep of de Service Bus naam ruimte. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Ingebouwde rollen van Azure voor Azure Service Bus
Voor Azure Service Bus is het beheer van naam ruimten en alle gerelateerde resources via de Azure Portal en de Azure Resource Management-API al beveiligd met behulp van het Azure RBAC-model. Azure biedt de onderstaande ingebouwde Azure-rollen voor het verlenen van toegang tot een Service Bus naam ruimte:

- [Azure Service Bus gegevens eigenaar](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner): Hiermee wordt gegevens toegang tot Service Bus naam ruimte en de entiteiten (wacht rijen, onderwerpen, abonnementen en filters) ingeschakeld
- [Azure Service Bus gegevens afzender](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender): gebruik deze rol om toegang te geven tot Service Bus naam ruimte en de entiteiten.
- [Azure Service Bus gegevens ontvanger](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver): gebruik deze rol om toegang te krijgen tot Service Bus naam ruimte en de bijbehorende entiteiten. 

## <a name="resource-scope"></a>Resourcebereik 
Voordat u een Azure-rol toewijst een beveiligingsprincipal, moet u het toegangsbereik bepalen dat de beveiligingsprincipal moet hebben. Uit best practices blijkt dat het het beste is om het nauwst mogelijke bereik toe te wijzen.

In de volgende lijst worden de niveaus beschreven waarmee u toegang tot Service Bus resources kunt bereiken, te beginnen met het smalle bereik:

- **Wachtrij**, **onderwerp** of **abonnement**: roltoewijzing is van toepassing op de specifieke service bus entiteit. Op dit moment biedt de Azure Portal geen ondersteuning voor het toewijzen van gebruikers/groepen/beheerde identiteiten aan Service Bus Azure-rollen op abonnements niveau. 
- **Service Bus naam ruimte**: roltoewijzing omvat de volledige topologie van service bus onder de naam ruimte en aan de Consumer groep die eraan is gekoppeld.
- **Resource groep**: roltoewijzing is van toepassing op alle service bus resources onder de resource groep.
- **Abonnement**: roltoewijzing is van toepassing op alle service bus resources in alle resource groepen in het abonnement.

> [!NOTE]
> Houd er rekening mee dat Azure-roltoewijzingen het Maxi maal vijf minuten kan duren voordat deze wordt door gegeven. 

Zie voor meer informatie over hoe ingebouwde rollen worden gedefinieerd [begrijpen functie definities](../role-based-access-control/role-definitions.md#management-and-data-operations). Zie [aangepaste rollen in azure](../role-based-access-control/custom-roles.md)voor meer informatie over het maken van aangepaste Azure-rollen.


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal  
Raadpleeg [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van de toegang tot Azure-resources met behulp van Azure RBAC en de Azure Portal. 

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die resource in de Azure Portal. Geef de instellingen voor toegangs beheer (IAM) voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

> [!NOTE]
> De stappen die hieronder worden beschreven, wijzen een rol toe aan uw Service Bus-naam ruimte. U kunt dezelfde stappen volgen om een rol toe te wijzen aan andere ondersteunde bereiken (resource groep, abonnement, enz.).

1. Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw service bus naam ruimte. Selecteer **Access Control (IAM)** in het menu links om de instellingen voor toegangs beheer voor de naam ruimte weer te geven. Als u een Service Bus naam ruimte moet maken, volgt u de instructies in dit artikel: [een service bus Messa ging-naam ruimte maken](service-bus-create-namespace-portal.md).

    ![Selecteer Access Control in het menu links](./media/authenticate-application/select-access-control-menu.png)
1. Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien. Selecteer de knop **toevoegen** op de werk balk en selecteer vervolgens **functie toewijzing toevoegen**. 

    ![Knop toevoegen op de werk balk](./media/authenticate-application/role-assignments-add-button.png)
1. Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **Service Bus rol** die u wilt toewijzen. 
    1. Zoek naar de beveiligingsprincipal **(gebruiker** , groep, Service-Principal) waaraan u de rol wilt toewijzen.
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een gebruiker](./media/authenticate-application/assign-role-to-user.png)
    4. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. In de volgende afbeelding ziet u bijvoorbeeld dat Azure-gebruikers zich in de rol Azure Service Bus gegevens eigenaar bevindt. 
        
        ![Gebruiker in de lijst](./media/authenticate-application/user-in-list.png)

U kunt vergelijk bare stappen volgen om een rol toe te wijzen aan een resource groep of een abonnement. Wanneer u de rol en het bereik ervan hebt gedefinieerd, kunt u dit gedrag testen met de [voor beelden op github](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).


## <a name="authenticate-from-an-application"></a>Verifiëren vanuit een toepassing
Een belang rijk voor deel van het gebruik van Azure AD met Service Bus is dat uw referenties niet meer in uw code hoeven te worden opgeslagen. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. Azure AD verifieert de beveiligingsprincipal (een gebruiker, een groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD het toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om aanvragen voor Azure Service Bus te autoriseren.

In de volgende secties ziet u hoe u uw systeem eigen toepassing of webtoepassing kunt configureren voor verificatie met het micro soft Identity-platform 2,0. Zie [overzicht van micro soft Identity platform (v 2.0)](../active-directory/develop/v2-overview.md)voor meer informatie over micro soft identity platform 2,0.

Zie [toegang tot Azure Active Directory webtoepassingen verlenen met behulp van de oauth 2,0 code Grant flow](../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de OAuth 2,0-code subsidie stroom.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-Tenant
De eerste stap bij het gebruik van Azure AD om Service Bus entiteiten te autoriseren, registreert uw client toepassing met een Azure AD-Tenant vanuit de [Azure Portal](https://portal.azure.com/). Wanneer u uw client toepassing registreert, geeft u informatie over de toepassing op AD. Azure AD biedt vervolgens een client-ID (ook wel een toepassings-ID genoemd) die u kunt gebruiken om uw toepassing te koppelen aan Azure AD runtime. Zie voor meer informatie over de client-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

De volgende afbeeldingen tonen stappen voor het registreren van een webtoepassing:

![Een toepassing registreren](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de omleidings-URI opgeven. Voor systeem eigen toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidings-URI een geldige URI zijn, omdat deze de URL specificeert waarnaar de tokens worden opgegeven.

Nadat u uw toepassing hebt geregistreerd, ziet u de **toepassings-id (client)** onder **instellingen**:

![Toepassings-ID van de geregistreerde toepassing](./media/authenticate-application/application-id.png)

Zie [toepassingen integreren met Azure Active Directory](../active-directory/develop/quickstart-register-app.md)voor meer informatie over het registreren van een toepassing met Azure AD.

> [!IMPORTANT]
> Noteer de **TenantId** en de **ApplicationId**. U hebt deze waarden nodig om de toepassing uit te voeren.

### <a name="create-a-client-secret"></a>Een clientgeheim maken   
De toepassing heeft een client geheim nodig om de identiteit ervan te bewijzen wanneer een token wordt aangevraagd. Voer de volgende stappen uit om het client geheim toe te voegen.

1. Ga naar de registratie van uw app in de Azure Portal als u zich nog niet op de pagina bevindt.
1. Selecteer **certificaten & geheimen** in het menu links.
1. Onder **client geheimen** selecteert u **Nieuw client geheim** om een nieuw geheim te maken.

    ![Nieuw client geheim-knop](./media/authenticate-application/new-client-secret-button.png)
1. Geef een beschrijving op voor het geheim, kies het gewenste verloop interval en selecteer vervolgens **toevoegen**.

    ![Client Secret-pagina toevoegen](./media/authenticate-application/add-client-secret-page.png)
1. Kopieer de waarde van het nieuwe geheim direct naar een veilige locatie. De opvullings waarde wordt slechts één keer weer gegeven.

    ![Clientgeheim](./media/authenticate-application/client-secret.png)

### <a name="permissions-for-the-service-bus-api"></a>Machtigingen voor de Service Bus-API
Als uw toepassing een console toepassing is, moet u een systeem eigen toepassing registreren en API-machtigingen voor **micro soft. ServiceBus** toevoegen aan de **vereiste machtigingenset** . Systeem eigen toepassingen hebben ook een **omleidings-URI** in azure AD nodig, die als een id fungeert. de URI hoeft geen netwerk bestemming te zijn. `https://servicebus.microsoft.com`Voor dit voor beeld gebruiken, omdat de voorbeeld code deze URI al gebruikt.

### <a name="client-libraries-for-token-acquisition"></a>Client bibliotheken voor het verkrijgen van tokens  
Zodra u uw toepassing hebt geregistreerd en de machtiging hebt verleend voor het verzenden/ontvangen van gegevens in Azure Service Bus, kunt u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en het OAuth 2,0-token te verkrijgen. Als u het token wilt verifiëren en verkrijgen, kunt u een van de [micro soft-identiteits platform verificatie bibliotheken](../active-directory/develop/reference-v2-libraries.md) of een andere open-source-bibliotheek gebruiken die ondersteuning biedt voor OpenID Connect of verbinding maken met 1,0. Uw toepassing kan vervolgens het toegangs token gebruiken om een aanvraag voor Azure Service Bus te autoriseren.

Voor een lijst met scenario's waarvoor het verkrijgen van tokens wordt ondersteund, zie het gedeelte [scenario's](https://aka.ms/msal-net-scenarios) van de [micro soft Authentication Library (MSAL) voor .net github-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) opslag plaats.

## <a name="sample-on-github"></a>Voor beeld op GitHub
Zie het volgende voor beeld op GitHub: [toegangs beheer op basis van rollen voor service bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl). 

Gebruik de optie **client geheim aanmelden** en niet de optie **interactief gebruiker aanmelden** . Wanneer u de optie client geheim gebruikt, wordt er geen pop-upvenster weer gegeven. De toepassing maakt gebruik van de Tenant-ID en App-ID voor authenticatie. 

### <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Voordat u het voor beeld kunt uitvoeren, moet u het **app.config** -bestand bewerken en, afhankelijk van uw scenario, de volgende waarden instellen:

- `tenantId`: Stel in op de waarde **TenantId** .
- `clientId`: Stel in op **ApplicationId** -waarde.
- `clientSecret`: Als u zich wilt aanmelden met behulp van het client geheim, maakt u het in azure AD. U kunt ook een web-app of API gebruiken in plaats van een systeem eigen app. Voeg ook de app toe onder **Access Control (IAM)** in de naam ruimte die u eerder hebt gemaakt.
- `serviceBusNamespaceFQDN`: Stel in op de volledige DNS-naam van de zojuist gemaakte Service Bus naam ruimte; bijvoorbeeld `example.servicebus.windows.net` .
- `queueName`: Stel in op de naam van de wachtrij die u hebt gemaakt.
- De omleidings-URI die u in de vorige stappen hebt opgegeven in uw app.

Wanneer u de console toepassing uitvoert, wordt u gevraagd een scenario te selecteren. Selecteer **interactieve gebruikers aanmelding** door het nummer ervan te typen en op ENTER te drukken. In de toepassing wordt een aanmeldings venster weer gegeven waarin u wordt gevraagd om toegang te krijgen tot Service Bus, waarna de service wordt uitgevoerd via het scenario voor verzenden/ontvangen met behulp van de aanmeldings-id.


## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is Azure-op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md)? voor meer informatie over Azure RBAC.
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van Azure-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Azure-roltoewijzingen toevoegen of verwijderen met Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)

Zie de volgende onderwerpen voor meer informatie over de Service Bus-berichtenservice

- [Voor beelden van Azure RBAC Service Bus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl)
- [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
- [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
- [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)