---
title: Zelf studie voor het configureren van Azure Active Directory B2C met Zscaler
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095984"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Zelf studie voor het configureren van Zscaler-persoonlijke toegang met Azure Active Directory B2C voor beveiligde hybride toegang

In deze zelf studie leert u hoe u Azure AD B2C-verificatie kunt integreren met [Zscaler private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA levert op beleid gebaseerde, beveiligde toegang tot persoonlijke toepassingen en assets zonder de kosten, gedoe of beveiligings Risico's van een virtueel particulier netwerk (VPN). De Secure Hybrid Access-aanbieding van Zscaler maakt een aanvals oppervlak voor consumenten toepassingen mogelijk in combi natie met Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) die is gekoppeld aan uw Azure-abonnement.

- [ZPA-abonnement](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Scenariobeschrijving

De ZPA-integratie bevat de volgende onderdelen:

- Azure AD B2C-de ID-provider (IdP) die verantwoordelijk is voor het controleren van de referenties van de gebruiker. Het is ook verantwoordelijk voor het aanmelden van een nieuwe gebruiker.

- ZPA: de service die verantwoordelijk is voor de beveiliging van de webtoepassing door het afdwingen van de [toegang tot nul vertrouwen](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Webtoepassing-host de service waartoe de gebruiker toegang probeert te krijgen.

In het volgende diagram ziet u hoe ZPA integreert met Azure AD B2C.

![Afbeelding toont het zscaler-architectuur diagram](media/partner-zscaler/zscaler-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt een ZPA-gebruikers portal of een toepassing voor toegang tot een ZPA-browser.
| 2. | ZPA vereist de gebruikers context informatie voordat deze kan bepalen of de gebruiker toegang heeft tot de webtoepassing. ZPA voert een SAML-omleiding naar de aanmeldings pagina van Azure AD B2C om de gebruiker te verifiëren.  
| 3. | Gebruiker arriveert op de aanmeldings pagina van Azure AB B2C. Als het een nieuwe gebruiker is, meldt de gebruiker zich aan om een nieuw account te maken. Een bestaande gebruiker meldt zich aan met hun bestaande referenties. Azure AD B2C valideert de identiteit van de gebruiker.
| 4. | Wanneer de verificatie is geslaagd, stuurt Azure AD B2C de gebruiker terug naar ZPA, samen met de SAML-bevestiging. ZPA verifieert de SAML-bevestiging en stelt de gebruikers context in.
| 5. | ZPA evalueert het toegangs beleid voor de gebruiker. Als de gebruiker toegang tot de webtoepassing heeft, wordt de verbinding door gegeven.

## <a name="onboard-to-zpa"></a>Onboarding naar ZPA

In deze zelf studie wordt ervan uitgegaan dat u al een werkende installatie van ZPA hebt. Als u aan de slag gaat met ZPA, raadpleegt u de [stapsgewijze configuratie handleiding voor ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integreren met Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Deel 1: Azure AD B2C configureren als een IdP in ZPA

Azure AD B2C configureren als een [ID-provider (IDP) op ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Meld u aan bij de [ZPA-beheer Portal](https://admin.private.zscaler.com)

2. Ga naar de  >  **IDP-configuratie** voor beheer

3. Selecteer **IDP-configuratie toevoegen**

4. Voer voor **1 IDP-informatie** het volgende in:

   a. **Naam**: Azure AD B2C

   b. **Eenmalige aanmelding**: gebruiker selecteren

   c. **Domeinen**: Kies de verificatie domeinen die u aan deze IDP wilt koppelen en selecteer vervolgens **gereed**

   ![Afbeelding toont de IDP-informatie](media/partner-zscaler/add-idp-configuration.png)

5. Selecteer **Volgende**

6. Voor **2 SP-meta gegevens**:

   a. Kopieer de URL van de service provider en noteer deze voor later gebruik.

   b. Kopieer de entiteits-ID van de service provider en noteer deze voor later gebruik.

   ![Afbeelding toont de gegevens van de SP-meta gegevens](media/partner-zscaler/sp-metadata.png)

7. **Onderbreken** selecteren

De rest van de IdP-configuratie wordt hervat na het configureren van Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Deel 2: aangepast beleid configureren in Azure AD B2C

>[!Note]
>Deze stap is alleen vereist als u nog geen aangepast beleid hebt. Als u al een of meer aangepaste beleids regels hebt, kunt u deze stap overs Laan.

Het artikel aan de [slag met aangepast beleid in azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) bevat instructies over het configureren van aangepaste beleids regels op uw Azure AD B2C-Tenant.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Deel 3: ZPA registreren als een SAML-toepassing in Azure AD B2C

Het artikel [een SAML-toepassing registreren in azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) biedt instructies voor het configureren van een SAML-toepassing in azure AD B2C. In [stap 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)krijgt u de URL van de IDP SAML-meta gegevens die wordt gebruikt door Azure AD B2C. U hebt dit nodig voor later gebruik.

Volg de instructies in [stap 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). In stap 4,2 van de instructie moet u het app-manifest bijwerken. Werk de eigenschappen als volgt bij:

- **identifierUris**: gebruik de ENTITEITS-id van de service provider die wordt vermeld in **deel 1, stap 6a**.

- **samlMetadataUrl**: sla deze eigenschap over als ZPA geen URL voor SAML-meta gegevens host.

- **replyUrlsWithType**: gebruik de URL van de service provider die wordt vermeld in **deel 1, stap 6b**.

- **logoutUrl**: sla deze eigenschap over als ZPA geen afmeldings-URL ondersteunt.

De overige stappen zijn niet relevant voor deze zelf studie.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Deel 4: de IdP SAML-meta gegevens uit Azure AD B2C extra heren

In de vorige stap moet u een URL voor SAML-meta gegevens verkrijgen met de volgende indeling:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

waarbij `<tenant-name>` de naam is van uw Azure AD B2C Tenant en `<policy-name>` de naam is van het aangepaste SAML-beleid dat u in de laatste stap hebt gemaakt.

Bijvoorbeeld: https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata

Open een webbrowser en navigeer naar de URL voor SAML-meta gegevens. Wanneer de pagina wordt geladen, klikt u met de rechter muisknop op een wille keurige plaats op de pagina. Selecteer **pagina opslaan als** en sla het bestand op uw computer op. u gebruikt deze in het volgende gedeelte.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Deel 5: IdP-configuratie volt ooien op ZPA

Voltooi de [IDP-configuratie in de ZPA-beheer Portal](https://help.zscaler.com/zpa/configuring-idp-single-sign) die gedeeltelijk is geconfigureerd in deel 1:

1. Meld u aan bij de [ZPA-beheer Portal](https://admin.private.zscaler.com)

2. Ga naar de configuratie van de **beheer**  >  **IDP**.

3. Selecteer de IdP die is geconfigureerd in deel 1 en selecteer **hervatten**.

4. Voor **3 IDP maken**

   a. Ga naar **IDP-meta gegevensbestand**  >  **bestand selecteren** en upload het meta gegevensbestand dat u hebt opgeslagen in deel 4.

   b. Controleer of de **status** van de IDP-configuratie is **ingeschakeld**.

   c. Selecteer **Opslaan**.

      ![Afbeelding toont de informatie over het maken van IDP](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>De oplossing testen

Ga naar een ZPA-gebruikers portal of een toepassing voor toegang tot een browser en test het registratie-of aanmeldings proces. Dit moet resulteren in een geslaagde SAML-verificatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aan de slag met aangepast beleid in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Een SAML-toepassing registreren in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Stapsgewijze configuratie handleiding voor ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Een IdP configureren voor eenmalige aanmelding](https://help.zscaler.com/zpa/configuring-idp-single-sign)
