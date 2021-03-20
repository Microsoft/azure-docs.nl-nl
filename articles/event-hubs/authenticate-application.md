---
title: Een toepassing verifiëren voor toegang tot Azure Event Hubs-resources
description: Dit artikel bevat informatie over het verifiëren van een toepassing met Azure Active Directory om toegang te krijgen tot Azure Event Hubs-resources
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913310"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Een toepassing verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs resources
Microsoft Azure biedt geïntegreerde toegangs beheer voor bronnen en toepassingen op basis van Azure Active Directory (Azure AD). Een belang rijk voor deel van het gebruik van Azure AD met Azure Event Hubs is dat u uw referenties niet meer hoeft op te slaan in de code. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. De resource naam voor het aanvragen van een token is `https://eventhubs.azure.net/` en is hetzelfde voor alle Clouds/tenants (voor Kafka-clients is de resource voor het aanvragen van een token `https://<namespace>.servicebus.windows.net` ). Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD een toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om een aanvraag voor Azure Event Hubs-resources te autoriseren.

Wanneer een rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, de Event Hubs naam ruimte of een andere resource. Een Azure AD-beveiliging kan rollen toewijzen aan een gebruiker, een groep, een service-principal voor de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Een roldefinitie is een verzameling machtigingen. Azure RBAC (op rollen gebaseerd toegangs beheer) bepaalt hoe deze machtigingen worden afgedwongen via roltoewijzing. Een roltoewijzing bestaat uit drie elementen: beveiligings-principal, roldefinitie en bereik (ook wel scope of niveau genoemd). Zie [informatie over de verschillende rollen](../role-based-access-control/overview.md)voor meer informatie.

## <a name="built-in-roles-for-azure-event-hubs"></a>Ingebouwde rollen voor Azure Event Hubs
Azure biedt de volgende ingebouwde rollen van Azure voor het machtigen van toegang tot Event Hubs gegevens met behulp van Azure AD en OAuth:

- [Azure Event hubs-gegevens eigenaar](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): gebruik deze rol om volledige toegang tot Event hubs resources te geven.
- [Gegevens verzender van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): gebruik deze rol om toegang te geven tot Event hubs-resources.
- [Gegevens ontvanger van Azure Event hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): gebruik deze rol om toegang te krijgen tot Event hubs resources.   

Zie [schema register rollen](schema-registry-overview.md#azure-role-based-access-control)voor ingebouwde rollen in het schema register.

> [!IMPORTANT]
> De preview-versie ondersteunt het toevoegen van Event Hubs rechten voor gegevens toegang aan de rol eigenaar of bijdrager. De bevoegdheden voor gegevens toegang voor de rol eigenaar en Inzender worden echter niet meer nageleefd. Als u de rol eigenaar of Inzender gebruikt, schakelt u over naar het gebruik van de functie Azure Event Hubs-gegevens eigenaar.


## <a name="authenticate-from-an-application"></a>Verifiëren vanuit een toepassing
Een belang rijk voor deel van het gebruik van Azure AD met Event Hubs is dat uw referenties niet meer in uw code hoeven te worden opgeslagen. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. Azure AD verifieert de beveiligingsprincipal (een gebruiker, een groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD het toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om aanvragen voor Azure Event Hubs te autoriseren.

In de volgende secties ziet u hoe u uw systeem eigen toepassing of webtoepassing kunt configureren voor verificatie met het micro soft Identity-platform 2,0. Zie [overzicht van micro soft Identity platform (v 2.0)](../active-directory/develop/v2-overview.md)voor meer informatie over micro soft identity platform 2,0.

Zie [toegang tot Azure Active Directory webtoepassingen verlenen met behulp van de oauth 2,0 code Grant flow](../active-directory/develop/v2-oauth2-auth-code-flow.md)voor een overzicht van de OAuth 2,0-code subsidie stroom.

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-Tenant
De eerste stap bij het gebruik van Azure AD om Event Hubs resources te autoriseren, registreert uw client toepassing met een Azure AD-Tenant vanuit de [Azure Portal](https://portal.azure.com/). Wanneer u uw client toepassing registreert, geeft u informatie over de toepassing op AD. Azure AD biedt vervolgens een client-ID (ook wel een toepassings-ID genoemd) die u kunt gebruiken om uw toepassing te koppelen aan Azure AD runtime. Zie voor meer informatie over de client-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

De volgende afbeeldingen tonen stappen voor het registreren van een webtoepassing:

![Een toepassing registreren](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de omleidings-URI opgeven. Voor systeem eigen toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidings-URI een geldige URI zijn, omdat deze de URL specificeert waarnaar de tokens worden opgegeven.

Nadat u uw toepassing hebt geregistreerd, ziet u de **toepassings-id (client)** onder **instellingen**:

![Toepassings-ID van de geregistreerde toepassing](./media/authenticate-application/application-id.png)

Zie [toepassingen integreren met Azure Active Directory](../active-directory/develop/quickstart-register-app.md)voor meer informatie over het registreren van een toepassing met Azure AD.


### <a name="create-a-client-secret"></a>Een clientgeheim maken   
De toepassing heeft een client geheim nodig om de identiteit ervan te bewijzen wanneer een token wordt aangevraagd. Voer de volgende stappen uit om het client geheim toe te voegen.

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de instelling **certificaten & geheimen** .
1. Onder **client geheimen** selecteert u **Nieuw client geheim** om een nieuw geheim te maken.
1. Geef een beschrijving op voor het geheim en kies het gewenste verloop interval.
1. Kopieer de waarde van het nieuwe geheim direct naar een veilige locatie. De opvullings waarde wordt slechts één keer weer gegeven.

    ![Clientgeheim](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Azure-rollen toewijzen met behulp van de Azure Portal  
Nadat u de toepassing hebt geregistreerd, wijst u de service-principal van de toepassing toe aan een Event Hubs Azure AD-rol die wordt beschreven in de sectie [rollen voor azure Event hubs](#built-in-roles-for-azure-event-hubs) . 

1. Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw event hubs naam ruimte.
2. Selecteer op de pagina **overzicht** de Event hub waarvoor u een rol wilt toewijzen.

    ![Selecteer uw Event Hub](./media/authenticate-application/select-event-hub.png)
1. Selecteer **Access Control (IAM)** om instellingen voor toegangs beheer voor de Event hub weer te geven. 
1. Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien. Selecteer de knop **toevoegen** op de werk balk en selecteer vervolgens **functie toewijzing toevoegen**. 

    ![Knop toevoegen op de werk balk](./media/authenticate-application/role-assignments-add-button.png)
1. Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **Event hubs rol** die u wilt toewijzen. 
    1. Zoek naar de beveiligingsprincipal **(gebruiker** , groep, Service-Principal) waaraan u de rol wilt toewijzen. Selecteer de **geregistreerde toepassing** in de lijst. 
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een gebruiker](./media/authenticate-application/assign-role-to-user.png)
    4. Ga naar het tabblad **roltoewijzingen** en bevestig de roltoewijzing. In de volgende afbeelding ziet u bijvoorbeeld dat **mywebapp** zich in de rol **Azure Event hubs gegevens afzender** bevindt. 
        
        ![Gebruiker in de lijst](./media/authenticate-application/user-in-list.png)

U kunt vergelijk bare stappen volgen om een rol toe te wijzen aan Event Hubs naam ruimte, resource groep of abonnement. Wanneer u de rol en het bereik ervan hebt gedefinieerd, kunt u dit gedrag testen met voor beelden [in deze github-locatie](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). Raadpleeg [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van de toegang tot Azure-resources met behulp van Azure RBAC en de Azure Portal. 


### <a name="client-libraries-for-token-acquisition"></a>Client bibliotheken voor het verkrijgen van tokens  
Zodra u uw toepassing hebt geregistreerd en de machtiging hebt verleend voor het verzenden/ontvangen van gegevens in azure Event Hubs, kunt u code toevoegen aan uw toepassing om een beveiligingsprincipal te verifiëren en het OAuth 2,0-token te verkrijgen. Als u het token wilt verifiëren en verkrijgen, kunt u een van de [micro soft-identiteits platform verificatie bibliotheken](../active-directory/develop/reference-v2-libraries.md) of een andere open-source-bibliotheek gebruiken die ondersteuning biedt voor OpenID Connect of verbinding maken met 1,0. Uw toepassing kan vervolgens het toegangs token gebruiken om een aanvraag voor Azure Event Hubs te autoriseren.

Voor een lijst met scenario's waarvoor het verkrijgen van tokens wordt ondersteund, zie het gedeelte [scenario's](https://aka.ms/msal-net-scenarios) van de [micro soft Authentication Library (MSAL) voor .net github-](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) opslag plaats.

## <a name="samples"></a>Voorbeelden
- [Micro soft. Azure. Event hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac)-voor beelden. 
    
    In deze voor beelden wordt de oude bibliotheek van **micro soft. Azure. Event hubs** gebruikt, maar u kunt deze eenvoudig bijwerken met de meest recente **Azure. Messa ging. Event hubs** -bibliotheek. Zie de [hand leiding voor het migreren van micro soft. Azure. Event hubs naar Azure. Messa ging. Event hubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md)om het voor beeld te verplaatsen van het gebruik van de oude bibliotheek naar een nieuwe.
- [Voor beelden van Azure. Messa ging. Event hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Dit voor beeld is bijgewerkt om de meest recente **Azure. Messa ging. Event hubs** -bibliotheek te gebruiken.

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is Azure-op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md)? voor meer informatie over Azure RBAC.
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van Azure-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Azure-roltoewijzingen toevoegen met behulp van Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)

Raadpleeg de volgende verwante artikelen:
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Event Hubs bronnen](authenticate-managed-identity.md)
- [Aanvragen voor Azure Event Hubs verifiëren met behulp van hand tekeningen voor gedeelde toegang](authenticate-shared-access-signature.md)
- [Toegang tot Event Hubs resources autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)
- [Toegang tot Event Hubs resources autoriseren met behulp van hand tekeningen voor gedeelde toegang](authorize-access-shared-access-signature.md)
