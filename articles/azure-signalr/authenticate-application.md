---
title: Een toepassing verifiëren voor toegang tot de Azure signalerings service
description: Dit artikel bevat informatie over het verifiëren van een toepassing met Azure Active Directory om toegang te krijgen tot de Azure signalerings service
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 97386b18360e22b457dbcdda53c4f81e7d4ed272
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797522"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Een toepassing met Azure Active Directory verifiëren om toegang te krijgen tot de Azure signalerings service
Microsoft Azure biedt geïntegreerde toegangs beheer voor bronnen en toepassingen op basis van Azure Active Directory (Azure AD). Een belang rijk voor deel van het gebruik van Azure AD met de Azure signalerings service is dat u uw referenties niet meer hoeft op te slaan in de code. In plaats daarvan kunt u een OAuth 2,0-toegangs token aanvragen bij het micro soft Identity-platform. De resource naam voor het aanvragen van een token is `https://signalr.azure.com/` . Azure AD verifieert de beveiligingsprincipal (een toepassing, resource groep of Service-Principal) die de toepassing uitvoert. Als de verificatie slaagt, retourneert Azure AD een toegangs token voor de toepassing en kan de toepassing vervolgens het toegangs token gebruiken om de service resources van Azure signalering te autoriseren.

Wanneer een rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden toegepast op het niveau van het abonnement, de resource groep of de Azure signalerings resource. Een Azure AD-beveiliging kan rollen toewijzen aan een gebruiker, een groep, een service-principal voor de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Een roldefinitie is een verzameling machtigingen. Op rollen gebaseerd toegangs beheer (RBAC) bepaalt hoe deze machtigingen worden afgedwongen via roltoewijzing. Een roltoewijzing bestaat uit drie elementen: beveiligings-principal, roldefinitie en bereik (ook wel scope of niveau genoemd). Zie [informatie over de verschillende rollen](../role-based-access-control/overview.md)voor meer informatie.

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Uw toepassing registreren bij een Azure AD-Tenant
De eerste stap bij het gebruik van Azure AD voor het autoriseren van Azure signalerings service bronnen is het registreren van uw toepassing met een Azure AD-Tenant vanuit het [Azure Portal](https://portal.azure.com/). Wanneer u uw toepassing registreert, geeft u informatie over de toepassing op AD. Azure AD biedt vervolgens een client-ID (ook wel een toepassings-ID genoemd) die u kunt gebruiken om uw toepassing te koppelen aan Azure AD runtime. Zie voor meer informatie over de client-ID [toepassings-en Service-Principal-objecten in azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

De volgende afbeeldingen tonen stappen voor het registreren van een webtoepassing:

![Een toepassing registreren](./media/authenticate/app-registrations-register.png)

> [!Note]
> Als u uw toepassing registreert als een systeem eigen toepassing, kunt u een geldige URI voor de omleidings-URI opgeven. Voor systeem eigen toepassingen hoeft deze waarde geen echte URL te zijn. Voor webtoepassingen moet de omleidings-URI een geldige URI zijn, omdat deze de URL specificeert waarnaar de tokens worden opgegeven.

Nadat u uw toepassing hebt geregistreerd, ziet u de **toepassings-id (client)** onder **instellingen**:

![Toepassings-ID van de geregistreerde toepassing](./media/authenticate/application-id.png)

Zie [toepassingen integreren met Azure Active Directory](../active-directory/develop/quickstart-register-app.md)voor meer informatie over het registreren van een toepassing met Azure AD.


### <a name="create-a-client-secret"></a>Een clientgeheim maken   
De toepassing heeft een client geheim nodig om de identiteit ervan te bewijzen wanneer een token wordt aangevraagd. Voer de volgende stappen uit om het client geheim toe te voegen.

1. Ga naar de registratie van uw app in de Azure Portal.
1. Selecteer de instelling **certificaten & geheimen** .
1. Onder **client geheimen** selecteert u **Nieuw client geheim** om een nieuw geheim te maken.
1. Geef een beschrijving op voor het geheim en kies het gewenste verloop interval.
1. Kopieer de waarde van het nieuwe geheim direct naar een veilige locatie. De opvullings waarde wordt slechts één keer weer gegeven.

![Een client geheim maken](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Een certificaat uploaden

U kunt ook een certificering uploaden in plaats van een client geheim te maken.

![Een certificering uploaden](./media/authenticate/certification.png)

## <a name="add-rbac-roles-using-the-azure-portal"></a>RBAC-rollen toevoegen met behulp van de Azure Portal  
Raadpleeg [dit artikel](..//role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van de toegang tot Azure-resources met RBAC en de Azure Portal. 

Nadat u het juiste bereik voor een roltoewijzing hebt bepaald, navigeert u naar die resource in de Azure Portal. Geef de instellingen voor toegangs beheer (IAM) voor de resource weer en volg deze instructies voor het beheren van roltoewijzingen:

1. Navigeer in het [Azure Portal](https://portal.azure.com/)naar uw signaal bron.
1. Selecteer **Access Control (IAM)** om de instellingen voor toegangs beheer voor de Azure-Signa lering weer te geven. 
1. Selectter het tabblad **Roltoewijzingen** om de lijst met roltoewijzingen te zien. Selecteer de knop **toevoegen** op de werk balk en selecteer vervolgens **functie toewijzing toevoegen**. 

    ![Knop toevoegen op de werk balk](./media/authenticate/role-assignments-add-button.png)

1. Voer op de pagina **roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de **Azure-seingevings functie** die u wilt toewijzen. 
    1. Zoek naar de beveiligingsprincipal **(gebruiker** , groep, Service-Principal) waaraan u de rol wilt toewijzen.
    1. Selecteer **Opslaan** om de roltoewijzing op te slaan. 

        ![Rol toewijzen aan een toepassing](./media/authenticate/assign-role-to-application.png)

    1. De identiteit waaraan u de rol hebt toegewezen, wordt weer gegeven onder die rol. De volgende afbeelding toont bijvoorbeeld de toepassing `signalr-dev` en `signalr-service` bevindt zich in de rol van de app-server functie. 
        
        ![De lijst roltoewijzing](./media/authenticate/role-assignment-list.png)

U kunt vergelijk bare stappen volgen om een rol toe te wijzen aan een resource groep of een abonnement. Wanneer u de rol en het bereik ervan hebt gedefinieerd, kunt u dit gedrag testen met voor beelden [in deze github-locatie](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Voorbeeld codes tijdens het configureren van uw app server.

Voeg de volgende opties toe wanneer `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

U kunt de `ConnectionString` in uw bestand gewoon configureren `appsettings.json` .

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Wanneer u gebruikt `certificate` , wijzigt `clientSecret` u de als `clientCert` volgt:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Volgende stappen
- Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)? voor meer informatie over RBAC.
- Zie de volgende artikelen voor meer informatie over het toewijzen en beheren van Azure-roltoewijzingen met Azure PowerShell, Azure CLI of de REST API:
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure CLI](../role-based-access-control/role-assignments-cli.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met de REST API](../role-based-access-control/role-assignments-rest.md)
    - [Op rollen gebaseerd toegangs beheer (RBAC) beheren met Azure Resource Manager sjablonen](../role-based-access-control/role-assignments-template.md)

Raadpleeg de volgende verwante artikelen:
- [Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot de Azure signalerings service](authenticate-managed-identity.md)
- [Toegang tot de Azure signalerings service autoriseren met behulp van Azure Active Directory](authorize-access-azure-active-directory.md)