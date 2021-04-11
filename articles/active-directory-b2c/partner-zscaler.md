---
title: Zelf studie-Azure Active Directory B2C configureren met Zscaler
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9cd193eb6ff2858440f1cd9a62bdd53d58d6047d
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256290"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Zelf studie: Zscaler persoonlijke toegang configureren met Azure Active Directory B2C

In deze zelf studie leert u hoe u Azure Active Directory B2C (Azure AD B2C)-verificatie kunt integreren met [Zscaler private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA levert op beleid gebaseerde, beveiligde toegang tot persoonlijke toepassingen en assets zonder de kosten, gedoe of beveiligings Risico's van een virtueel particulier netwerk (VPN). De Zscaler Secure Hybrid Access biedt een aanvals oppervlak voor consument gerichte toepassingen wanneer het wordt gecombineerd met Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u het volgende nodig:

- Een Azure-abonnement. Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).  
- [Een Azure AD B2C-Tenant](./tutorial-create-tenant.md) die is gekoppeld aan uw Azure-abonnement.  
- [Een ZPA-abonnement](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Scenariobeschrijving

ZPA-integratie omvat de volgende onderdelen:

- **Azure AD B2C**: de ID-provider (IDP) die verantwoordelijk is voor het controleren van de referenties van de gebruiker. Het is ook verantwoordelijk voor het aanmelden van een nieuwe gebruiker.  
- **ZPA**: de service die verantwoordelijk is voor de beveiliging van de webtoepassing door het afdwingen van toegang met een [nul-vertrouwens relatie](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **De webtoepassing**: host de service waartoe de gebruiker toegang probeert te krijgen.

In het volgende diagram ziet u hoe ZPA integreert met Azure AD B2C.

![Diagram van Zscaler-architectuur, waarin wordt weer gegeven hoe ZPA integreert met Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

De volg orde wordt beschreven in de volgende tabel:

|Stap | Beschrijving |
| :-----:| :-----------|
| 1 | Een gebruiker ontvangt een ZPA-gebruikers portal of een ZPA-toepassing voor browser toegang.
| 2 | ZPA vereist informatie over de gebruikers context voordat deze kan bepalen of de gebruiker toegang heeft tot de webtoepassing. ZPA voert een SAML-omleiding naar de aanmeldings pagina van Azure AD B2C om de gebruiker te verifiëren.  
| 3 | De gebruiker komt op de aanmeldings pagina van Azure AB B2C. Nieuwe gebruikers kunnen zich aanmelden om een account te maken en bestaande gebruikers melden zich aan met hun bestaande referenties. Azure AD B2C valideert de identiteit van de gebruiker.
| 4 | Wanneer de verificatie is geslaagd, stuurt Azure AD B2C de gebruiker terug naar ZPA, samen met de SAML-bevestiging. ZPA verifieert de SAML-bevestiging en stelt de gebruikers context in.
| 5 | ZPA evalueert het toegangs beleid voor de gebruiker. Als de gebruiker toegang tot de webtoepassing toestaat, kan de verbinding worden door gegeven.

## <a name="onboard-to-zpa"></a>Onboarding naar ZPA

In deze zelf studie wordt ervan uitgegaan dat u al een werkende ZPA-instelling hebt. Als u aan de slag gaat met ZPA, raadpleegt u de [stapsgewijze configuratie handleiding voor ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>ZPA integreren met Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Stap 1: Azure AD B2C configureren als een IdP in ZPA

Ga als volgt te werk om Azure AD B2C te configureren als een [IDP in ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Meld u aan bij de [ZPA-beheer Portal](https://admin.private.zscaler.com).

1. Ga naar de configuratie van de **beheer**  >  **IDP**.

1. Selecteer **IDP-configuratie toevoegen**.

   Het deel venster **IDP configuratie toevoegen** wordt geopend.

   ![Scherm opname van het tabblad ' IdP Information ' in het deel venster IdP-configuratie toevoegen.](media/partner-zscaler/add-idp-configuration.png)

1. Selecteer het tabblad **IDP informatie** en Ga als volgt te werk:

   a. Voer **Azure AD B2C** in het vak **naam** in.  
   b. Selecteer onder **eenmalige aanmelding** **gebruiker**.  
   c. Selecteer in de vervolg keuzelijst **domeinen** de verificatie domeinen die u wilt koppelen aan deze IDP.

1. Selecteer **Next**.

1. Selecteer het tabblad **SP meta data** en ga vervolgens als volgt te werk:

   a. Kopieer of noteer de waarde onder **service provider-URL** voor later gebruik.  
   b. Kopieer of noteer de waarde van de **service provider entiteit-id** voor later gebruik.

   ![Scherm opname van het tabblad ' SP meta data ' in het deel venster IdP-configuratie toevoegen.](media/partner-zscaler/sp-metadata.png)

1. Selecteer **onderbreken**.

Nadat u Azure AD B2C hebt geconfigureerd, wordt de rest van de IdP-configuratie hervat.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Stap 2: aangepaste beleids regels configureren in Azure AD B2C

>[!Note]
>Deze stap is alleen vereist als u nog geen aangepaste beleids regels hebt geconfigureerd. Als u al een of meer aangepaste beleids regels hebt, kunt u deze stap overs Laan.

Zie aan de [slag met aangepaste beleids regels in azure Active Directory B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)om aangepast beleid te configureren voor uw Azure AD B2C Tenant.

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Stap 3: ZPA registreren als een SAML-toepassing in Azure AD B2C

Als u een SAML-toepassing in Azure AD B2C wilt configureren, raadpleegt u [een SAML-toepassing registreren in azure AD B2C](./saml-service-provider.md). 

In stap [' uw beleid uploaden '](./saml-service-provider.md#upload-your-policy)kopieert of noteert u de URL van de IDP SAML-meta gegevens die wordt gebruikt door Azure AD B2C. U hebt deze later nodig.

Volg de instructies in stap [' uw toepassing configureren in azure AD B2C '](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c). Werk in stap 4,2 de eigenschappen van het app-manifest als volgt bij:

- Voor **identifierUris**: gebruik de ENTITEITS-id van de service provider die u eerder hebt gekopieerd of genoteerd in ' Step 1.6. b '.  
- Voor **samlMetadataUrl**: sla deze eigenschap over, omdat ZPA geen URL voor SAML-meta gegevens host.  
- Voor **replyUrlsWithType**: gebruik de URL van de service provider die u eerder hebt gekopieerd of genoteerd in ' stap 1.6. a '.  
- Voor **logoutUrl**: sla deze eigenschap over, omdat ZPA geen afmeldings-URL ondersteunt.

De overige stappen zijn niet relevant voor deze zelf studie.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Stap 4: de IdP SAML-meta gegevens uit Azure AD B2C extra heren

Vervolgens moet u een URL voor SAML-meta gegevens verkrijgen met de volgende indeling:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Dit `<tenant-name>` is de naam van uw Azure AD B2C Tenant en `<policy-name>` is de naam van het aangepaste SAML-beleid dat u in de vorige stap hebt gemaakt.

De URL kan bijvoorbeeld zijn `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Open een webbrowser en ga naar de URL voor SAML-meta gegevens. Klik met de rechter muisknop ergens op de pagina, selecteer **Opslaan als** en sla het bestand op uw computer op voor gebruik in de volgende stap.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Stap 5: de IdP-configuratie op ZPA volt ooien

Voltooi de [IDP-configuratie in de ZPA-beheer Portal](https://help.zscaler.com/zpa/configuring-idp-single-sign) die u voorheen eerder hebt geconfigureerd in ' stap 1: Azure AD B2C configureren als een IDP op ZPA '.

1. Ga in de [ZPA-beheer Portal](https://admin.private.zscaler.com)naar **de**  >  **IDP-configuratie**.

1. Selecteer de IdP die u in stap 1 hebt geconfigureerd en selecteer vervolgens **hervatten**.

1. Selecteer in het deel venster **IDP-configuratie toevoegen** het tabblad **IDP maken** en ga vervolgens als volgt te werk:

   a. Upload onder **IDP-meta** gegevensbestand het meta gegevensbestand dat u eerder hebt opgeslagen in ' stap 4: de IDP SAML-meta gegevens uit Azure AD B2C extra heren '.  
   b. Controleer of de **status** van de IDP-configuratie is **ingeschakeld**.  
   c. Selecteer **Opslaan**.

   ![Scherm opname van het tabblad ' IdP maken ' in het deel venster IdP configuratie toevoegen.](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>De oplossing testen

Ga naar een ZPA-gebruikers portal of een toepassing voor browser toegang en test het registratie-of aanmeldings proces. De test moet resulteren in een geslaagde SAML-verificatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aan de slag met aangepast beleid in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [Een SAML-toepassing registreren in Azure AD B2C](./saml-service-provider.md)
- [Stapsgewijze configuratie handleiding voor ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Een IdP configureren voor eenmalige aanmelding](https://help.zscaler.com/zpa/configuring-idp-single-sign)