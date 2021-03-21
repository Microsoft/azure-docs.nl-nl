---
title: Een daemon-toepassing beveiligen
titleSuffix: Azure Maps
description: Gebruik de Azure Portal om verificatie te beheren voor het configureren van een vertrouwde daemon-toepassing.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895644"
---
# <a name="secure-a-daemon-application"></a>Een daemon-toepassing beveiligen

De volgende hand leiding is voor achtergrond processen, timers en taken die worden gehost in een vertrouwde en beveiligde omgeving. Voor beelden zijn onder andere Azure-webtaken, Azure function-apps, Windows-Services en andere betrouw bare achtergrond Services.

> [!Tip]
> Micro soft raadt aan om Azure Active Directory (Azure AD) en Azure RBAC (op rollen gebaseerd toegangs beheer) te implementeren voor productie toepassingen. Zie [Azure Maps-verificatie](./azure-maps-authentication.md)voor een overzicht van concepten.

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Scenario: gedeelde sleutel verificatie

Nadat u een Azure Maps account hebt gemaakt, worden de primaire en secundaire sleutels gegenereerd. U wordt aangeraden om de primaire sleutel als abonnements sleutel te gebruiken wanneer u [gebruikmaakt van gedeelde sleutel verificatie om Azure Maps aan te roepen](./azure-maps-authentication.md#shared-key-authentication). U kunt een secundaire sleutel gebruiken in scenario's zoals wijzigingen in de Rolling sleutel. Zie [verificatie in azure Maps](./azure-maps-authentication.md)voor meer informatie.

### <a name="securely-store-shared-key"></a>Gedeelde sleutel veilig opslaan

De primaire en secundaire sleutel geven autorisatie toe voor alle Api's voor het Maps-account. Toepassingen moeten de sleutels in een beveiligd archief opslaan, zoals Azure Key Vault. De toepassing moet de gedeelde sleutel ophalen als Azure Key Vault geheim om te voor komen dat de gedeelde sleutel wordt opgeslagen in onbewerkte tekst in de toepassings configuratie. Zie [Azure Key Vault ontwikkelaars handleiding](../key-vault/general/developers-guide.md)voor meer informatie over het configureren van een Azure Key Vault.

De volgende stappen beschrijven dit proces:

1. Maak een Azure Key Vault.
2. Een Azure AD-service-principal maken door een app-registratie of beheerde identiteit te maken, is de gemaakte Principal verantwoordelijk voor toegang tot Azure Key Vault.
3. Wijs de machtiging toegang tot Azure-sleutel geheimen toe aan de Service-Principal `Get` .
4. Wijs tijdelijk toegang toe aan geheimen `Set` machtigingen voor u als ontwikkelaar.
5. Stel de gedeelde sleutel in de Key Vault geheimen in en verwijs naar de geheime ID als configuratie voor de daemon-toepassing en verwijder de machtigingen voor uw geheimen `Set` .
6. Implementeer Azure AD-verificatie in de daemon-toepassing om het gedeelde sleutel geheim op te halen van Azure Key Vault.
7. Azure Maps REST API aanvraag met gedeelde sleutel maken.

> [!Tip]
> Als de app wordt gehost in een Azure-omgeving, moet u een beheerde identiteit implementeren om de kosten en complexiteit van het beheer van een geheim voor verificatie bij Azure Key Vault te verminderen. Raadpleeg de volgende Azure Key Vault- [zelf studie om verbinding te maken via een beheerde identiteit](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

De daemon-toepassing is verantwoordelijk voor het ophalen van de gedeelde sleutel uit een beveiligde opslag. Voor de implementatie met Azure Key Vault is verificatie via Azure AD vereist om toegang te krijgen tot het geheim. In plaats daarvan moedigen we directe Azure AD-verificatie aan Azure Maps als gevolg van de extra complexiteit en operationele vereisten voor het gebruik van gedeelde sleutel verificatie.

> [!IMPORTANT]
> Om het opnieuw genereren van sleutels te vereenvoudigen, raden we aan dat toepassingen één sleutel tegelijk gebruiken. Toepassingen kunnen vervolgens de niet-gebruikte sleutel opnieuw genereren en de nieuwe opnieuw gegenereerde sleutel implementeren in een beveiligd geheim archief, zoals Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Scenario: toegangs beheer op basis van rollen in azure AD

Zodra een Azure Maps account is gemaakt, is de Azure Maps `x-ms-client-id` waarde aanwezig op de pagina Azure Portal verificatie Details. Deze waarde vertegenwoordigt het account dat wordt gebruikt voor REST API aanvragen. Deze waarde moet worden opgeslagen in de toepassings configuratie en opgehaald voordat HTTP-aanvragen worden gemaakt. Het doel van het scenario is de daemon-toepassing in te scha kelen voor verificatie bij Azure AD en Azure Maps REST-Api's aan te roepen.

> [!Tip]
> We raden u aan om te hosten op Azure Virtual Machines, Virtual Machine Scale Sets of App Services om voor delen van beheerde identiteits onderdelen in te scha kelen.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon gehost op Azure-resources

Wanneer u Azure-resources uitvoert, configureert u beheerde identiteiten van Azure om goedkope, minimale referentie beheer inspanning mogelijk te maken. 

Zie [overzicht van beheerde identiteiten](../active-directory/managed-identities-azure-resources/overview.md) , zodat de toepassing toegang kan krijgen tot een beheerde identiteit.

Voor delen van beheerde identiteit:

* Door Azure System beheerde x509-certificerings verificatie met open bare sleutel.
* Azure AD-beveiliging met x509-certificaten in plaats van client geheimen.
* Azure beheert en vernieuwt alle certificaten die zijn gekoppeld aan de beheerde ID-resource.
* Vereenvoudigd beheer van referentie bewerkingen door de nood zaak van een beveiligde geheime archief service, zoals Azure Key Vault, te verwijderen. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon gehost op niet-Azure-resources

Wanneer een niet-Azure-omgeving wordt uitgevoerd, zijn er geen beheerde identiteiten beschikbaar. Daarom moet u een Service-Principal configureren via een Azure AD-toepassings registratie voor de daemon-toepassing.

1. Selecteer in het Azure Portal in de lijst met Azure-Services **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistratie](./media/how-to-manage-authentication/app-registration.png)

2. Als u uw app al hebt geregistreerd, gaat u verder met de volgende stap. Als u uw app nog niet hebt geregistreerd, voert u een **naam** in, kiest u een **type ondersteunings account** en selecteert u vervolgens **registreren**.  

    > [!div class="mx-imgBorder"]
    > ![Details van app-registratie](./media/how-to-manage-authentication/app-create.png)

3. Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing. Selecteer vervolgens onder **app-registraties** de optie **API-machtigingen**  >  **een machtiging toevoegen**. Onder **api's die mijn organisatie gebruikt**, zoekt en selecteert u **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![API-machtigingen voor apps toevoegen](./media/how-to-manage-authentication/app-permissions.png)

4. Schakel het selectie vakje in naast **toegang Azure Maps** en selecteer vervolgens **machtigingen toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

5. Voer de volgende stappen uit om een client geheim te maken of het certificaat te configureren.

    * Als uw toepassing gebruikmaakt van server-of toepassings verificatie, gaat u op de pagina app-registratie naar **certificaten & geheimen**. Upload vervolgens een certificaat met een open bare sleutel of maak een wacht woord door **Nieuw client geheim** te selecteren.

        > [!div class="mx-imgBorder"]
        > ![Een client geheim maken](./media/how-to-manage-authentication/app-keys.png)

    * Nadat u **toevoegen** hebt geselecteerd, kopieert u het geheim en slaat u het veilig op in een service zoals Azure Key Vault. Raadpleeg [Azure Key Vault ontwikkelaars handleiding](../key-vault/general/developers-guide.md) om het certificaat of geheim veilig op te slaan. U gebruikt dit geheim om tokens van Azure AD op te halen.

        > [!div class="mx-imgBorder"]
        > ![Een clientgeheim toevoegen](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Op rollen gebaseerde toegang verlenen voor de daemon-toepassing Azure Maps

U verleent op *rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)* door de gemaakte beheerde identiteit of de Service-Principal toe te wijzen aan een of meer Azure Maps roldefinities. Als u de definities van Azure-functies wilt weer geven die beschikbaar zijn voor Azure Maps, gaat u naar **toegangs beheer (IAM)**. Selecteer **rollen** en zoek vervolgens naar rollen die beginnen met *Azure Maps*. Deze Azure Maps rollen zijn de rollen waaraan u toegang kunt verlenen.

> [!div class="mx-imgBorder"]
> ![Beschik bare rollen weer geven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Ga naar uw **Azure Maps-account**. Selecteer **Toegangsbeheer (IAM)** > **Roltoewijzingen**.

    > [!div class="mx-imgBorder"]
    > ![Toegang verlenen met behulp van Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Voeg op het tabblad **roltoewijzingen** een roltoewijzing **toe** . 

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de rollen toewijzingen met toevoegen geselecteerd.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Selecteer een ingebouwde Azure Maps roldefinitie, zoals **Azure Maps gegevens lezer** of **Azure Maps data contributor**. Onder **toegang toewijzen aan**, selecteert u **Azure AD-gebruiker,-groep of Service-Principal** of beheerde identiteit met door de **gebruiker toegewezen beheerde** identiteits  /  **systeem toegewezen beheerde identiteit**. Selecteer de principal. Selecteer vervolgens **Opslaan**.

    > [!div class="mx-imgBorder"]
    > ![Roltoewijzing toevoegen](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. U kunt bevestigen dat de roltoewijzing is toegepast op het tabblad roltoewijzing.

## <a name="request-token-with-managed-identity"></a>Token aanvragen met beheerde identiteit

Wanneer een beheerde identiteit is geconfigureerd voor de hosting bron, gebruikt u de Azure SDK of REST API om een token voor Azure Maps te verkrijgen, raadpleegt u details over [het ophalen van een toegangs token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). Na de hand leiding is de verwachting dat er een toegangs token wordt geretourneerd dat kan worden gebruikt voor REST API aanvragen.

## <a name="request-token-with-application-registration"></a>Token aanvragen met toepassings registratie

Nadat u uw app hebt geregistreerd en aan Azure Maps hebt gekoppeld, kunt u toegangs tokens aanvragen.

* Resource-ID voor Azure AD `https://atlas.microsoft.com/`
* Azure AD-app-ID
* Azure AD-tenant-id
* Client geheim registreren Azure AD-app

Aanvraag:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Reactie:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Zie [verificatie scenario's voor Azure AD](../active-directory/develop/authentication-vs-authorization.md)voor meer gedetailleerde voor beelden.

## <a name="next-steps"></a>Volgende stappen

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Bekijk de voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-voor beelden](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)