---
title: IDology-integratie met Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Leer hoe u een voor beeld van een online-betalings toepassing in Azure AD B2C integreert met IDology. IDology is een identiteits verificatie en controle provider met meerdere oplossingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928678"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van IDology met Azure Active Directory B2C 

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure AD B2C met [IDology](https://www.idology.com/solutions/). IDology is een identiteits verificatie en controle provider met meerdere oplossingen. In dit voor beeld behandelen we ExpectID-oplossing door IDology.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* [Een Azure AD B2C-Tenant](tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

De IDology-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker. Het wordt ook wel de ID-provider genoemd.
- IDology: de IDology-service gebruikt de invoer van de gebruiker en controleert de identiteit van de gebruiker.
- Aangepaste rest API: deze API implementeert de integratie tussen Azure AD en de IDology-service.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![Diagram van IDology-architectuur](media/partner-idology/idology-architecture-diagram.png)

| Stap | Beschrijving |
|------|------|
|1     | Een gebruiker ontvangt een melding op de aanmeldings pagina. |
|2     | De gebruiker selecteert de registratie optie om een nieuw account te maken en gegevens in te voeren op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld. |
|3     | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door. |
|4     | De middelste laag-API verzamelt gebruikers kenmerken en transformeert deze in een indeling die door IDOlogy API kan worden verbruikt. Vervolgens worden de gegevens naar IDology verzonden. |
|5     | IDology verbruikt de informatie en verwerkt deze en retourneert vervolgens het resultaat naar de middelste laag-API. |
|6     | De middelste laag-API verwerkt de gegevens en stuurt de relevante gegevens terug naar Azure AD B2C. |
|7     | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als **er een fout melding wordt** weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op **succes** wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven. |
|      |      |

> [!NOTE]
> Azure AD B2C kunt de klant ook vragen om een stapsgewijze verificatie uit te voeren, maar dit scenario valt buiten het bereik van deze zelf studie.

## <a name="onboard-with-idology"></a>Onboard met IDology

1. IDology biedt diverse oplossingen, die u [hier](https://www.idology.com/solutions/)kunt vinden. Voor dit voor beeld gebruiken we ExpectID.

2. Als u een IDology-account wilt maken, neemt u contact op met [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/).

3. Zodra een account is gemaakt, ontvangt u de informatie die u nodig hebt voor de configuratie van de API. In de volgende secties wordt het proces beschreven.

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Deel 1: de API implementeren

Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) voor een Azure-service. De code kan worden gepubliceerd vanuit Visual Studio, gevolgd door deze [instructies](/visualstudio/deployment/quickstart-deploy-to-azure).

U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

### <a name="part-2---configure-the-api"></a>Deel 2: de API configureren 

Toepassings instellingen kunnen worden [geconfigureerd in app service in azure](../app-service/configure-common.md#configure-app-settings). Met deze methode kunnen instellingen veilig worden geconfigureerd zonder deze in een opslag plaats te controleren. U moet de volgende instellingen opgeven voor de rest-API:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | IDology-account configuratie |     |
|IdologySettings:ApiPassword | IDology-account configuratie |     |
|WebApiSettings:ApiUsername |Een gebruikers naam voor de API definiëren| Gebruikt in de ExtId-configuratie |
|WebApiSettings:ApiPassword | Een wacht woord voor de API definiëren | Gebruikt in de ExtId-configuratie

### <a name="part-3---create-api-policy-keys"></a>Deel 3: API-beleids sleutels maken

Volg dit [document](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) om twee beleids sleutels te maken: één voor de API-gebruikers naam en een voor het API-wacht woord dat u hierboven hebt gedefinieerd.

In het voorbeeld beleid worden de volgende sleutel namen gebruikt:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Deel 4: het Azure AD B2C-beleid configureren

1. Volg dit [document](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) om het [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) te downloaden en het beleid voor de Azure AD B2C Tenant te configureren. Volg de instructies totdat u de sectie **het aangepaste beleid testen** hebt voltooid.

2. Down load [hier](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy)de twee voorbeeld beleidsregels.

3. De twee voorbeeld beleidsregels bijwerken:

   1. Beide beleids regels openen:

      1. Werk in de sectie `Idology-ExpectId-API` het `ServiceUrl` meta gegevens item bij met de locatie van de hierboven geïmplementeerde API.

      1. Vervang `yourtenant` door de naam van uw Azure AD B2C-tenant.
      Als de naam van uw Azure AD B2C Tenant bijvoorbeeld is  `contosotenant` , vervangt u alle exemplaren van  `yourtenant.onmicrosoft.com`   met `contosotenant.onmicrosoft.com` .

   1. Open het bestand TrustFrameworkExtensions.xml:

      1. Het element zoeken  `<TechnicalProfile Id="login-NonInteractive">` . Vervang beide exemplaren van  `IdentityExperienceFrameworkAppId`   door de toepassings-id van de IdentityExperienceFramework-toepassing die u eerder hebt gemaakt.

      1. Vervang beide exemplaren van  `ProxyIdentityExperienceFrameworkAppId`   door de toepassings-id van de ProxyIdentityExperienceFramework-toepassing die u eerder hebt gemaakt.

4. Vervang de SignInorSignUp.xml en TrustFrameworkExtensions.xml die u eerder hebt geüpload naar Azure AD B2C in stap 1 met de twee bijgewerkte voorbeeld beleidsregels.

> [!NOTE]
> Als best practice, raden we aan dat klanten toestemming berichten toevoegen op de pagina met kenmerk verzameling. Informeer gebruikers dat er informatie wordt verzonden naar services van derden voor identiteits verificatie.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C-Tenant en selecteer onder **beleids regels** **gebruikers stromen**.

2. Selecteer de eerder gemaakte **gebruikers stroom**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   1. **Toepassing** -Selecteer de geregistreerde app (voor beeld is JWT).

   1. **Antwoord-URL** : Selecteer de **omleidings-URL**.

   1. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account.

5. Meld u af.

6. Ga door naar de aanmeldings stroom.

7. De IDology-puzzel wordt weer gegeven nadat u **door gaan** hebt opgegeven.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](custom-policy-get-started.md?tabs=applications)
