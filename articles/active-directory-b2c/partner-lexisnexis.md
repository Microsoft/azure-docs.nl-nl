---
title: Zelf studie voor het configureren van Azure Active Directory B2C met LexisNexis
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met LexisNexis dat een profilerings-en identiteits validatie service is en wordt gebruikt om de gebruikers identificatie te controleren en uitgebreide risico beoordelingen te bieden op basis van het apparaat van de gebruiker.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97108430"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van LexisNexis met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het integreren van Azure AD B2C met [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis biedt tal van oplossingen, u kunt ze [hier](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)vinden. In deze voorbeeld zelfstudie wordt de **ThreatMetrix** -oplossing van LexisNexis besproken. ThreatMetrix is een profilerings-en identiteits validatie service. Dit wordt gebruikt om de gebruikers identificatie te controleren en uitgebreide risico analyses te bieden op basis van het apparaat van de gebruiker.

Deze integratie biedt een profilering op basis van enkele stukjes gebruikers informatie, die door de gebruiker wordt verstrekt tijdens de registratie. ThreatMetrix bepaalt of de gebruiker zich moet blijven aanmelden of niet. De volgende kenmerken worden in de risico analyse van ThreatMetrix in overweging genomen:

- E-mail
- Telefoonnummer
- Profilerings gegevens verzameld van de computer van de gebruiker

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

De ThreatMetrix-integratie bevat de volgende onderdelen:

- Azure AD B2C: de autorisatie server die verantwoordelijk is voor het verifiëren van de referenties van de gebruiker, ook wel bekend als de ID-provider

- ThreatMetrix: de ThreatMetrix-service voert invoer van de gebruiker uit en combineert deze met profilerings informatie die is verzameld van de computer van de gebruiker om de beveiliging van de gebruikers interactie te controleren.

- Aangepaste rest API: deze API implementeert de integratie tussen Azure AD B2C en de ThreatMetrix-service.

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![scherm afbeelding voor LexisNexis-Architecture-diagram](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Stap | Beschrijving |
|:--------------|:-------------|
|1. | De gebruiker ontvangt op een aanmeldings pagina. Gebruiker selecteert registratie om een nieuw account te maken en gegevens in te voeren op de pagina. Azure AD B2C worden de gebruikers kenmerken verzameld.
| 2. | Azure AD B2C roept de middelste laag-API aan en geeft de gebruikers kenmerken door.
| 3. | Met de middelste laag-API worden gebruikers kenmerken verzameld en getransformeerd in een indeling die door LexisNexis API kan worden verbruikt. Vervolgens verzendt deze naar LexisNexis.  
| 4. | LexisNexis verbruikt de informatie en verwerkt deze om de gebruikers identificatie te valideren op basis van de risico analyse. Vervolgens wordt het resultaat geretourneerd naar de middelste laag-API.
| 5. | Met de middelste laag-API worden de gegevens verwerkt en worden relevante gegevens teruggestuurd naar Azure AD B2C.
| 6. | Azure AD B2C ontvangt informatie terug van de interface van de middelste laag. Als er een fout melding wordt weer gegeven, wordt er een fout bericht voor de gebruiker getoond. Als er een reactie op succes wordt weer gegeven, wordt de gebruiker geverifieerd en krijgt deze toegang.

## <a name="onboard-with-lexisnexis"></a>Onboard met LexisNexis

1. Als u een LexisNexis-account wilt maken, neemt u contact op met [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)

2. Maak een LexisNexis-beleid dat voldoet aan uw vereisten. Gebruik de documentatie die [hier](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd)beschikbaar is.

>[!NOTE]
> De naam van het beleid wordt later gebruikt.

Zodra een account is gemaakt, ontvangt u de informatie die u nodig hebt voor de configuratie van de API. In de volgende secties wordt het proces beschreven.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Azure AD B2C configureren met LexisNexis

### <a name="part-1---deploy-the-api"></a>Deel 1: de API implementeren

Implementeer de meegeleverde [API-code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) voor een Azure-service. De code kan worden gepubliceerd vanuit Visual Studio, gevolgd door deze [instructies](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>U hebt de URL van de geïmplementeerde service nodig om Azure AD te configureren met de vereiste instellingen.

### <a name="part-2---configure-the-api"></a>Deel 2: de API configureren

Toepassings instellingen kunnen worden [geconfigureerd in de app service in azure](../app-service/configure-common.md#configure-app-settings).  Met deze methode kunnen instellingen veilig worden geconfigureerd zonder deze in een opslag plaats te controleren. U moet de volgende instellingen opgeven voor de rest-API:

| Toepassingsinstellingen | Bron | Notities |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | ThreatMetrix-account configuratie |     |
|ThreatMetrix: OrgId | ThreatMetrix-account configuratie |     |
|ThreatMetrix: ApiKey |ThreatMetrix-account configuratie|  |
|ThreatMetrix: beleid | De naam van het beleid dat is gemaakt in ThreatMetrix | |
| BasicAuth:ApiUsername |Een gebruikers naam voor de API definiëren| De gebruikers naam wordt gebruikt in de Azure AD B2C configuratie
| BasicAuth:ApiPassword | Een wacht woord voor de API definiëren | Het wacht woord wordt gebruikt in de Azure AD B2C configuratie

### <a name="part-3---deploy-the-ui"></a>Deel 3: de gebruikers interface implementeren

Deze oplossing maakt gebruik van aangepaste UI-sjablonen die door Azure AD B2C worden geladen. Deze UI-sjablonen doen het profileren dat rechtstreeks naar de ThreatMetrix-service wordt verzonden.

Raadpleeg deze [instructies](./customize-ui-with-html.md#custom-page-content-walkthrough) voor het implementeren van de toegevoegde [gebruikers interface bestanden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) naar een Blob Storage-account. De instructies omvatten het instellen van een Blob Storage-account, het configureren van CORS en het inschakelen van open bare toegang.

De gebruikers interface is gebaseerd op de sjabloon van de [oceaan blauw](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Alle koppelingen in de gebruikers interface moeten worden bijgewerkt om te verwijzen naar de geïmplementeerde locatie. Zoek en vervang in de map gebruikers interface https://yourblobstorage/blobcontainer de geïmplementeerde locatie.

### <a name="part-4---create-api-policy-keys"></a>Deel 4: API-beleids sleutels maken

Raadpleeg dit [document](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) en maak twee beleids sleutels: een voor de API-gebruikers naam en een voor het API-wacht woord dat u hierboven hebt gedefinieerd.

In het voorbeeld beleid worden de volgende sleutel namen gebruikt:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Deel 5: de API-URL bijwerken

Zoek in het opgegeven [TrustFrameworkExtensions-beleid](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)het technische profiel met de naam `Rest-LexisNexus-SessionQuery` en werk `ServiceUrl` het meta gegevens item bij met de locatie van de hierboven geïmplementeerde API.

### <a name="part-6---update-ui-url"></a>Deel 6-URL voor bijwerken van gebruikers interface

Ga in het meegeleverde [TrustFrameworkExtensions-beleid](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)naar zoeken en vervangen om te zoeken naar https://yourblobstorage/blobcontainer/ de locatie waarin de gebruikers interface bestanden worden geïmplementeerd.

>[!NOTE]
> Als best practice, raden we aan dat klanten toestemming berichten toevoegen op de pagina met kenmerk verzameling. Informeer gebruikers dat er informatie wordt verzonden naar services van derden voor identiteits verificatie.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Deel 7: het Azure AD B2C-beleid configureren

Raadpleeg dit [document](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) om [Local accounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) te downloaden en configureer het [beleid](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) voor de Azure AD B2C Tenant.

>[!NOTE]
>Werk het opgegeven beleid bij om te koppelen aan uw specifieke Tenant.

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

7. De ThreatMetrix-puzzel wordt weer gegeven nadat u **door gaan** hebt ingevoerd.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](./custom-policy-overview.md)

- [Aan de slag met aangepast beleid in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
