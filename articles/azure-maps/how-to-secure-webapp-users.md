---
title: Een webtoepassing beveiligen met interactieve eenmalige aanmelding
titleSuffix: Azure Maps
description: Het configureren van een webtoepassing die ondersteuning biedt voor eenmalige aanmelding van Azure AD met Azure Maps Web SDK met behulp van OpenID Connect Connect protocol.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: devx-track-js
ms.openlocfilehash: ebdc4b219e0840c18e6bef8ebfe9b8eefa8faf3b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895564"
---
# <a name="secure-a-web-application-with-user-sign-in"></a>Een webtoepassing beveiligen met gebruikers aanmelding

De volgende hand leiding heeft betrekking op een toepassing die wordt gehost op webservers, onderhoudt meerdere bedrijfs scenario's en implementeert op webservers. De toepassing heeft de vereiste om beveiligde resources te bieden die alleen zijn beveiligd voor Azure AD-gebruikers. Het doel van het scenario is de webtoepassing in te scha kelen om te verifiëren bij Azure AD en Azure Maps REST Api's namens de gebruiker aan te roepen.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Een toepassings registratie maken in azure AD

U moet de webtoepassing maken in azure AD voor gebruikers om zich aan te melden. Deze webtoepassing delegeert vervolgens de gebruikers toegang tot Azure Maps REST-Api's.

1. Selecteer in het Azure Portal in de lijst met Azure-Services **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistratie](./media/how-to-manage-authentication/app-registration.png)

2. Voer een **naam** in, kies een **type ondersteunings account** en geef een OMleidings-URI op die de URL vertegenwoordigt die door Azure AD wordt uitgegeven en de URL waar het kaart besturings element wordt gehost. Zie voor meer informatie Azure AD [scenario: Web-app die de gebruikers aantekent](../active-directory/develop/scenario-web-app-sign-user-overview.md). Voer de stappen uit die worden beschreven in het scenario van Azure AD.  

3. Zodra de registratie van de toepassing is voltooid, controleert u of de aanmelding van de toepassing voor gebruikers werkt. Zodra het aanmelden werkt, kan de toepassing gedelegeerde toegang krijgen tot Azure Maps REST-Api's.
    
4.  Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing. Selecteer vervolgens de optie **API-machtigingen** om  >  **een machtiging toe te voegen**. Onder **api's die mijn organisatie gebruikt**, zoekt en selecteert u **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![API-machtigingen voor apps toevoegen](./media/how-to-manage-authentication/app-permissions.png)

5. Schakel het selectie vakje in naast **toegang Azure Maps** en selecteer vervolgens **machtigingen toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

6. De webtoepassing inschakelen om Azure Maps REST-Api's aan te roepen door de app-registratie te configureren met een toepassings geheim, voor gedetailleerde stappen raadpleegt u [een web-app die web-api's aanroept: app-registratie](../active-directory/develop/scenario-web-app-call-api-app-registration.md). Een geheim is vereist voor de verificatie van Azure AD namens de gebruiker. Het certificaat van de app-registratie of het geheim moet worden opgeslagen in een beveiligde Store zodat de webtoepassing kan worden opgehaald om te verifiëren bij Azure AD. 
   
   * Als de toepassing al een Azure AD-App-registratie heeft geconfigureerd en een geheim, kan deze stap worden overgeslagen.

> [!Tip]
> Als de toepassing wordt gehost in een Azure-omgeving, raden we u aan [beheerde identiteiten te gebruiken voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) en een Azure Key Vault-exemplaar voor het openen van geheimen door [een toegangs token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) voor toegang tot Azure Key Vault geheimen of certificaten te verkrijgen. Zie zelf studie voor het maken van een verbinding met Azure Key Vault om geheimen op te halen met [behulp van beheerde identiteit](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).
   
7. Implementeer een veilig token eindpunt voor de Azure Maps Web-SDK om toegang te krijgen tot een token. 
   
   * Zie [Azure Maps Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/blob/master/src/OpenIdConnect/AzureMapsOpenIdConnectv1/AzureMapsOpenIdConnect/Controllers/TokenController.cs)-voor beelden voor een voor beeld van een token controller. 
   * Zie [token ophalen voor de app](../active-directory/develop/scenario-web-app-call-api-acquire-token.md) uit Azure AD-documentatie voor een niet-AspNetCore implementatie of andere.
   * Het eind punt van het beveiligde token is verantwoordelijk voor het retour neren van een toegangs token voor de geverifieerde en geautoriseerde gebruiker om Azure Maps REST-Api's aan te roepen.

8. Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) configureren voor gebruikers of groepen. Zie [toegang verlenen op basis van rollen voor gebruikers](#grant-role-based-access-for-users-to-azure-maps).

9. Configureer de pagina Webtoepassing met de Azure Maps Web-SDK voor toegang tot het eind punt van het beveiligde token. 

```javascript
var map = new atlas.Map("map", {
        center: [-122.33, 47.64],
        zoom: 12,
        language: "en-US",
        authOptions: {
            authType: "anonymous",
            clientId: "<insert>",  // azure map account client id
            getToken: function (resolve, reject, map) {
                var xhttp = new XMLHttpRequest();
                xhttp.open("GET", "/api/token", true); // the url path maps to the token endpoint.
                xhttp.onreadystatechange = function () {
                    if (this.readyState === 4 && this.status === 200) {
                        resolve(this.responseText);
                    } else if (this.status !== 200) {
                        reject(this.responseText);
                    }
                };

                xhttp.send();
            }
        }
    });
    map.events.add("tokenacquired", function () {
        console.log("token acquired");
    });
    map.events.add("error", function (err) {
        console.log(JSON.stringify(err.error));
    });
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over scenario voor webtoepassingen:
> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](../active-directory/develop/scenario-web-app-sign-user-overview.md)

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Bekijk de voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps Azure AD web app-voor beelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples/tree/master/src/OpenIdConnect)