---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Onfido
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Onfido voor de verificatie van document-ID en gezichts biometrie
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928627"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van Onfido met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure AD B2C met [Onfido](https://onfido.com/). Onfido is een verificatie-app met document-ID en gezichts biometrie. Zo kunnen bedrijven voldoen aan *de vereisten van uw klant* en identiteit in real-time. Onfido maakt gebruik van geavanceerde op AI gebaseerde identiteits verificatie, die eerst een foto-ID verifieert en deze vervolgens overeenkomt met hun gelaats biometrie. Deze oplossing verbindt een digitale identiteit aan hun echte persoon en biedt een veilige voorbereidings ervaring en vermindert fraude.

In dit voor beeld verbinden we de service van Onfido in de registratie-of aanmeldings stroom om identiteits verificatie uit te voeren. Geïnformeerde beslissingen over het product en de service waartoe de gebruiker toegang heeft, wordt gemaakt op basis van de resultaten van Onfido.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

- Een Onfido- [proef account](https://onfido.com/signup/).

## <a name="scenario-description"></a>Scenariobeschrijving

De Onfido-integratie bevat de volgende onderdelen:

- Azure AD B2C Tenant: de autorisatie server die verantwoordelijk is voor het controleren van de referenties van de gebruiker op basis van het aangepaste beleid dat in de Tenant is gedefinieerd. Het wordt ook wel de ID-provider genoemd. Hiermee wordt de Onfido-client-app gehost, waarmee de gebruikers documenten worden verzameld en verzonden naar de Onfido API-service.

- Onfido-client: een configureerbaar java script collection-hulp programma voor client documenten dat is geïmplementeerd in andere webpagina's. Verzamelt de documenten en voert voorlopige controles uit zoals de grootte en kwaliteit van het document.

- Tussenliggende rest API: levert eind punten voor de Azure AD B2C-Tenant om te communiceren met de Onfido API-service, verwerking van gegevens verwerking en het naleven van de beveiligings vereisten van beide.

- Onfido API-service: de back-end-service die wordt verstrekt door Onfido, waarmee de documenten worden opgeslagen en gecontroleerd die door de gebruiker worden verstrekt.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![scherm afbeelding voor onfido-Architecture-diagram](media/partner-onfido/onfido-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruiker meldt zich aan om een nieuw account te maken en voert informatie in op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld. De Onfido-client-app die wordt gehost in Azure AD B2C, controleert de gebruikers gegevens.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | Met de middelste laag-API worden gebruikers kenmerken verzameld en getransformeerd in een indeling die door Onfido API kan worden verbruikt. Vervolgens verzendt deze naar Onfido.
| 4. | Onfido verbruikt de informatie en verwerkt deze om de gebruikers identificatie te valideren. Vervolgens wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | Met de middelste laag-API worden de gegevens verwerkt en worden relevante gegevens in de juiste JSON-indeling naar Azure AD B2C verzonden.
| 6. | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en naar de map geschreven.

## <a name="onboard-with-onfido"></a>Onboard met Onfido

1. Als u een Onfido-account wilt maken, neemt u contact op met [Onfido](https://onfido.com/signup/).

2. Wanneer een account is gemaakt, maakt u een [API-sleutel](https://documentation.onfido.com/). Live-sleutels zijn Factureerbaar, maar u kunt de [sandbox-sleutels gebruiken om](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) de oplossing te testen. De sandbox-sleutels produceren dezelfde resultaat structuur als Live-sleutels, maar de resultaten worden altijd vooraf bepaald. Documenten worden niet verwerkt of opgeslagen.

>[!NOTE]
> U hebt de sleutel later nodig.

Zie [ONFIDO API documentation](https://documentation.onfido.com) and [Onfido Developer hub](https://developers.onfido.com)(Engelstalig) voor meer informatie over Onfido.  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Azure AD B2C configureren met Onfido

### <a name="part-1---deploy-the-api"></a>Deel 1: de API implementeren

- Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) voor een Azure-service. De code kan worden gepubliceerd vanuit Visual Studio, gevolgd door deze [instructies](/visualstudio/deployment/quickstart-deploy-to-azure).
- CORS instellen, **toegestane oorsprong** toevoegen als https://{your_tenant_name}. b2clogin. com

>[!NOTE]
>U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

#### <a name="adding-sensitive-configuration-settings"></a>Gevoelige configuratie-instellingen toevoegen

Toepassings instellingen kunnen worden geconfigureerd in de [app service in azure](../app-service/configure-common.md#configure-app-settings). Met de app service kunnen instellingen veilig worden geconfigureerd zonder deze in een opslag plaats te controleren. De rest-API moet de volgende instellingen hebben:

| Naam van toepassings instelling | Bron | Notities |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Onfido-account |

### <a name="part-2---deploy-the-ui"></a>Deel 2: de gebruikers interface implementeren

#### <a name="configure-your-storage-location"></a>Uw opslag locatie configureren

1. Een [Blob Storage-container in uw opslag account](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) instellen

2. Sla de bestanden van de gebruikers interface op in de map met de [gebruikers interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) van de BLOB-container.

3. Sta toegang tot de opslag container die u hebt gemaakt toe door de volgende instructies te volgen:

   a. Ga naar **instellingen**  > **toegestaan van oorsprong**, Voer in `https://{your_tenant_name}.b2clogin.com` . Vervang uw-Tenant naam door de naam van uw Azure AD B2C-Tenant. Bijvoorbeeld https://fabrikam.b2clogin.com . Gebruik alleen kleine letters bij het invoeren van de naam van uw Tenant.

   b. Selecteer voor **toegestane methoden** `GET` en `PUT` .

   c. Selecteer **Opslaan**.

#### <a name="update-ui-files"></a>Gebruikers interface bestanden bijwerken

1. Ga in de bestanden van de gebruikers interface naar de map [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Open elk HTML-bestand.

3. {Uw-UI-BLOB-container-URL} zoeken en vervangen door de URL van waar de mappen **ocean_blue**, **distributie** en **assets** van uw gebruikers interface zich bevinden

4. Zoek en vervang {uw-Intermediate-API-URL} door de URL van de tussenliggende API app-service.

#### <a name="upload-your-files"></a>Uw bestanden uploaden

1. Sla de bestanden van de gebruikers interface op in de map met de gebruikers interface van de BLOB-container.

2. Gebruik [Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) om uw bestanden en toegangs machtigingen te beheren.

### <a name="part-3---configure-azure-ad-b2c"></a>Deel 3: Azure AD B2C configureren

#### <a name="replace-the-configuration-values"></a>De configuratie waarden vervangen

Zoek in het meegeleverde [aangepaste beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)de volgende tijdelijke aanduidingen en vervang door de bijbehorende waarden uit uw exemplaar.

| Tijdelijke aanduiding | Vervangen door waarde | Voorbeeld  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | De korte naam van uw Tenant |  "yourtenant" van yourtenant.onmicrosoft.com |
| {your_tenantID} | TenantID van uw Azure AD B2C-Tenant | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID van de IdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID van de ProxyIdentityExperienceFramework-app die is geconfigureerd in uw Azure AD B2C-Tenant | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID van de opslag toepassing van uw Tenant                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Object-ID van de opslag toepassing van uw Tenant                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | Instrumentatie sleutel van uw app Insights-exemplaar *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| De URL van de locatie waar de mappen **ocean_blue**, **distributie** en **assets** van de gebruikers interface zich bevinden | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL van de app service die u hebt ingesteld                                             | `https://yourapp.azurewebsites.net`          |

* App Insights kan zich in een andere Tenant bevindt. Deze stap is optioneel. Verwijder, indien nodig, de bijbehorende TechnicalProfiles en OrchestrationSteps.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Deel 4: het Azure AD B2C-beleid configureren

Raadpleeg dit [document](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) voor instructies over het instellen van uw Azure AD B2C-Tenant en het configureren van beleid.

>[!NOTE]
> Als best practice, raden we aan dat klanten toestemming berichten toevoegen op de pagina met kenmerk verzameling. Informeer gebruikers dat er informatie wordt verzonden naar services van derden voor identiteits verificatie.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het **Framework identiteits ervaring** selecteren.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **Gebruikersstroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. De Onfido-service wordt aangeroepen tijdens de stroom, nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
