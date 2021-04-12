---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Experian
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Experian voor identificatie controle en controle op basis van gebruikers kenmerken om fraude te voor komen.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 84e6f76bcae55ed905e11c46df66478908529970
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258024"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van Experian met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure AD B2C met [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian biedt diverse oplossingen, die u [hier](https://www.experian.com/)kunt vinden.

In dit voor beeld wordt het geïntegreerde digitale identiteits-Experian en het fraude risico platform **CrossCore** gebruikt. CrossCore is een ID-verificatie service die wordt gebruikt om de gebruikers identificatie te controleren. Er wordt een risico analyse uitgevoerd op basis van verschillende gegevens die door de gebruiker tijdens de registratie worden verstrekt. CrossCore wordt gebruikt om te bepalen of de gebruiker zich moet blijven aanmelden of niet. De volgende kenmerken kunnen worden gebruikt in de CrossCore-risico analyse:

- E-mail
- IP-adres
- Voornaam
- Middelste naam
- Achternaam
- Adres
- Plaats
- Provincie
- Postcode
- Land/regio
- Telefoonnummer

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

De Experian-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker, ook wel bekend als de ID-provider

- Experian: de Experian-service gebruikt de invoer van de gebruiker en verifieert de identiteit van de gebruiker

- Aangepaste rest API: deze API implementeert de integratie tussen Azure AD B2C en de Experian-service.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![scherm afbeelding voor Experian-Architecture-diagram](media/partner-experian/experian-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruiker selecteert registratie om een nieuw account te maken en voert informatie in op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | Met de middelste laag-API worden gebruikers kenmerken verzameld en getransformeerd in een indeling die door Experian API kan worden verbruikt. Vervolgens verzendt deze naar Experian.
| 4. | Experian verbruikt de informatie en verwerkt deze om de gebruikers identificatie te valideren op basis van de risico analyse. Vervolgens wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | Met de middelste laag-API worden de gegevens verwerkt en worden relevante gegevens in de juiste JSON-indeling naar Azure AD B2C verzonden.
| 6. | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="onboard-with-experian"></a>Onboard met Experian

1. Als u een Experian-account wilt maken, neemt u contact op met [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. Zodra een account is gemaakt, ontvangt u de informatie die u nodig hebt voor de configuratie van de API. In de volgende secties wordt het proces beschreven.

## <a name="configure-azure-ad-b2c-with-experian"></a>Azure AD B2C configureren met Experian

### <a name="part-1---deploy-the-api"></a>Deel 1: de API implementeren

Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) voor een Azure-service. De code kan worden gepubliceerd vanuit Visual Studio, gevolgd door deze [instructies](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

### <a name="part-2---deploy-the-client-certificate"></a>Deel 2: het client certificaat implementeren

De aanroep van de Experian-API wordt beveiligd door een client certificaat. Dit client certificaat wordt verzorgd door Experian. Volg de instructies in dit [document](../app-service/environment/certificates.md#private-client-certificate)om het certificaat te uploaden naar de Azure-app-service. In het voor beeld wordt gebruikgemaakt van de volgende sleutels stappen in het proces:

- Het certificaat uploaden

- Stel de `WEBSITE_LOAD_ROOT_CERTIFICATES` sleutel in met de vinger afdruk van het certificaat.

### <a name="part-3---configure-the-api"></a>Deel 3: de API configureren

Toepassings instellingen kunnen worden [geconfigureerd in de app service in azure](../app-service/configure-common.md#configure-app-settings). Met deze methode kunnen instellingen veilig worden geconfigureerd zonder deze in een opslag plaats te controleren. U moet de volgende instellingen opgeven voor de rest-API:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Experian-account configuratie |     |
|CrossCoreConfig:OrgCode | Experian-account configuratie |     |
|CrossCore:ApiEndpoint |Experian-account configuratie|  |
|CrossCore:ClientReference | Experian-account configuratie | |
| CrossCore:ModelCode |Experian-account configuratie|
| CrossCore:OrgCode | Experian-account configuratie |
| CrossCore:SignatureKey  | Experian-account configuratie |
| CrossCore: TenantId  | Experian-account configuratie |
| CrossCore: CertificateThumbprint | Experian-certificaat |
| BasicAuth:ApiUsername | Een gebruikers naam voor de API definiëren | Gebruikt in de ExtId-configuratie |
| BasicAuth:ApiPassword | Een wacht woord voor de API definiëren | Gebruikt in de ExtId-configuratie

### <a name="part-4---create-api-policy-keys"></a>Deel 4: API-beleids sleutels maken

Raadpleeg dit [document](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) en maak twee beleids sleutels: een voor de API-gebruikers naam en een voor het API-wacht woord dat u hierboven hebt gedefinieerd voor http-basis verificatie.

>[!NOTE]
>U hebt de sleutels nodig voor het later configureren van het beleid.

### <a name="part-5---replace-the-configuration-values"></a>Deel 5: de configuratie waarden vervangen

Zoek in het meegeleverde [aangepaste beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)de volgende tijdelijke aanduidingen en vervang door de bijbehorende waarden uit uw exemplaar

|                      Tijdelijke aanduiding                       |                                   Vervangen door waarde                                 |                   Voorbeeld                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | De korte naam van uw Tenant                                                           | "yourtenant" van yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Azure AD B2C naam van uw TrustFrameworkBase-beleid                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID van de IdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID van de ProxyIdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID van de opslag toepassing van uw Tenant                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Object-ID van de opslag toepassing van uw Tenant                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | De naam van de sleutel van de gebruikers naam die u [hier](#part-4---create-api-policy-keys) hebt gemaakt             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | De naam van de wachtwoord sleutel die u [hier](#part-4---create-api-policy-keys) hebt gemaakt             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | URL van de app service die u hebt ingesteld                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Deel 6: het Azure AD B2C-beleid configureren

Raadpleeg dit [document](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#custom-policy-starter-pack) voor instructies over het instellen van uw Azure AD B2C-Tenant en het configureren van beleid.

>[!NOTE]
>Dit voorbeeld beleid is gebaseerd op het [lokale account Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Als best practice, raden we aan dat klanten toestemming berichten toevoegen op de pagina met kenmerk verzameling. Informeer gebruikers dat er informatie wordt verzonden naar services van derden voor identiteits verificatie.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels **gebruikers stromen**.

2. Selecteer de eerder gemaakte **gebruikers stroom**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. Afmelden

6. De aanmeldings stroom door lopen  

7. De CrossCore-puzzel wordt weer gegeven nadat u **door gaan** hebt ingevoerd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
