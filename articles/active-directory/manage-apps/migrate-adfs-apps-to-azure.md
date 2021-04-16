---
title: Toepassingsverificatie van AD FS naar Azure Active Directory
description: Leer hoe u Azure Active Directory kunt gebruiken om Active Directory Federation Services (AD FS) te vervangen, zodat gebruikers een aanmelding voor al hun toepassingen krijgen.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.openlocfilehash: 83e506c0a3d0b9718f94d48ea8e6b23f43e811f3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377935"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Toepassingsverificatie verplaatsen van Active Directory Federation Services naar Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) biedt een universeel identiteitsplatform waarmee uw mensen, partners en klanten één identiteit hebben om toegang te krijgen tot toepassingen en samen te werken vanaf elk platform en apparaat. Azure AD heeft [een volledige suite met mogelijkheden voor identiteitsbeheer.](../fundamentals/active-directory-whatis.md) Het standaardiseren van uw toepassingsverificatie en -autorisatie voor Azure AD biedt deze voordelen.

> [!TIP]
> Dit artikel is geschreven voor ontwikkelaars. Projectmanagers en beheerders die van plan zijn om een toepassing te verplaatsen naar Azure AD, moeten overwegen toepassingsverificatie migreren [naar Azure AD te lezen.](migrate-application-authentication-to-azure-active-directory.md)

## <a name="azure-ad-benefits"></a>Voordelen van Azure AD

Als u een on-premises directory hebt die gebruikersaccounts bevat, hebt u waarschijnlijk veel toepassingen waarmee gebruikers worden geverifieerd. Elk van deze apps is geconfigureerd voor gebruikers om toegang te krijgen met behulp van hun identiteit.

Gebruikers kunnen zich ook rechtstreeks verifiëren met uw on-premises Active Directory. Active Directory Federation Services (AD FS) is een op standaarden gebaseerde on-premises identiteitsservice. Het breidt de mogelijkheid uit om eenmalige aanmelding (SSO) te gebruiken tussen vertrouwde zakelijke partners, zodat gebruikers zich niet afzonderlijk voor elke toepassing moeten aanmelden. Dit staat bekend als federatief.

Veel organisaties hebben Software as a Service (SaaS) of aangepaste Line-Of-Business-apps die rechtstreeks naar AD FS zijn ge federeerd, naast Microsoft 365- en Azure AD-apps.

  ![Toepassingen die rechtstreeks on-premises zijn verbonden](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Om de beveiliging van toepassingen te verbeteren, is het uw doel om één set toegangsbesturingselementen en beleidsregels te hebben in uw on-premises omgevingen en cloudomgevingen.

  ![Toepassingen die zijn verbonden via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typen apps die moeten worden gemigreerd

Het migreren van al uw toepassingsverificatie naar Azure AD is optimaal, omdat u hierdoor één besturingsvlak hebt voor identiteits- en toegangsbeheer.

Uw toepassingen kunnen moderne of verouderde protocollen gebruiken voor verificatie. Wanneer u de migratie naar Azure AD plant, kunt u overwegen eerst de apps te migreren die gebruikmaken van moderne verificatieprotocollen (zoals SAML en Open ID Connect). Deze apps kunnen opnieuw worden geconfigureerd voor verificatie bij Azure AD via een ingebouwde connector vanuit de Azure-app Gallery of door de toepassing te registreren in Azure AD. Apps die gebruikmaken van oudere protocollen kunnen worden geïntegreerd met behulp van toepassingsproxy.

Zie voor meer informatie:

* [Met behulp van Azure AD toepassingsproxy on-premises apps te publiceren voor externe gebruikers.](what-is-application-proxy.md)
* [Wat is toepassingsbeheer?](what-is-application-management.md)
* [AD FS toepassingsactiviteitrapport voor het migreren van toepassingen naar Azure AD.](migrate-adfs-application-activity.md)
* [Controleer AD FS met Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Het migratieproces

Test uw apps en configuratie tijdens het verplaatsen van uw app-verificatie naar Azure AD. U wordt aangeraden bestaande testomgevingen te blijven gebruiken voor migratietests wanneer u naar de productieomgeving overstapt. Als een testomgeving momenteel niet beschikbaar is, kunt u er een instellen met behulp van [Azure App Service](https://azure.microsoft.com/services/app-service/) of [Azure Virtual Machines,](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)afhankelijk van de architectuur van de toepassing.

U kunt ervoor kiezen om een afzonderlijke Azure AD-testten tenant in te stellen voor het ontwikkelen van uw app-configuraties.

Uw migratieproces kan er als volgende uitzien:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Fase 1 : huidige status: de productie-app wordt geverifieerd met AD FS

  ![Migratiefase 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Fase 2 : (optioneel) Een test-exemplaar van de app naar de Azure AD-test-tenant laten wijzen

Werk de configuratie bij om uw test-exemplaar van de app naar een Azure AD-test-tenant te laten wijzen en eventuele vereiste wijzigingen aan te brengen. De app kan worden getest met gebruikers in de Test Azure AD-tenant. Tijdens het ontwikkelingsproces kunt u hulpprogramma's zoals [Fiddler gebruiken om](https://www.telerik.com/fiddler) aanvragen en antwoorden te vergelijken en te verifiëren.

Als het niet haalbaar is om een afzonderlijke test-tenant in te stellen, slaat u deze fase over en laat u een test-exemplaar van de app naar uw Azure AD-productie-tenant wijzen, zoals beschreven in fase 3 hieronder.

  ![Migratiefase 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Fase 3: een test-exemplaar van de app naar de Azure AD-productie-tenant laten wijzen

Werk de configuratie bij om uw test-exemplaar van de app te laten wijzen naar uw Azure AD-productie-tenant. U kunt nu testen met gebruikers in uw productie-tenant. Lees indien nodig de sectie van dit artikel over het overstappen van gebruikers.

  ![Migratiefase 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Fase 4: de productie-app naar de Azure AD-productie-tenant laten wijzen

Werk de configuratie van uw productie-app bij om te wijzen naar uw Azure AD-productie-tenant.

  ![Migratiefase 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Apps die worden geverifieerd met AD FS kunnen Active Directory-groepen gebruiken voor machtigingen. Gebruik [Azure AD Connect om](../hybrid/how-to-connect-sync-whatis.md) identiteitsgegevens te synchroniseren tussen uw on-premises omgeving en Azure AD voordat u begint met de migratie. Controleer deze groepen en het lidmaatschap vóór de migratie, zodat u dezelfde gebruikers toegang kunt verlenen wanneer de toepassing wordt gemigreerd.

### <a name="line-of-business-apps"></a>Line-Of-Business-apps

Uw Line-Of-Business-apps zijn de apps die uw organisatie heeft ontwikkeld of die een standaard verpakt product zijn. Voorbeelden hiervan zijn apps die zijn gebouwd op Windows Identity Foundation- en SharePoint-apps (niet SharePoint Online).

Line-Of-Business-apps die gebruikmaken van OAuth 2.0, OpenID Connect of WS-Federation kunnen worden geïntegreerd met Azure AD als [app-registraties.](../develop/quickstart-register-app.md) Integreer aangepaste apps die gebruikmaken van SAML 2.0 of WS-Federation als [niet-galerietoepassingen](add-application-portal.md) op de pagina bedrijfstoepassingen in [de Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Een aanmelding op basis van SAML

Apps die SAML 2.0 voor verificatie gebruiken, kunnen worden geconfigureerd voor eenmalige aanmelding op basis van [SAML.](what-is-single-sign-on.md) Met eenmalige aanmelding op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassingsrollen op basis van regels die u in uw SAML-claims definieert.

Zie [Quickstart: Eenmalige](add-application-portal-setup-sso.md)aanmelding op basis van SAML instellen voor het configureren van een SaaS-toepassing voor eenmalige aanmelding op basis van SAML.

  ![Schermopnamen van SAML-gebruikers voor eenmalige aanmelding ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Veel SaaS-toepassingen hebben een [toepassingsspecifieke zelfstudie](../saas-apps/tutorial-list.md) die u door de configuratie voor eenmalige aanmelding op basis van SAML gaat helpen.

  ![zelfstudie over app](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Sommige apps kunnen eenvoudig worden gemigreerd. Voor apps met complexere vereisten, zoals aangepaste claims, is mogelijk aanvullende configuratie in Azure AD en/of Azure AD Connect. Zie How [to: Customize claims emitted in tokens for a specific app in a tenant (Preview) (Claims](../develop/active-directory-claims-mapping.md)aanpassen die worden weergegeven in tokens voor een specifieke app in een tenant (preview) voor meer informatie over ondersteunde claimtoewijzingen.

Houd rekening met de volgende beperkingen bij het toewijzen van kenmerken:

* Niet alle kenmerken die kunnen worden uitgegeven in AD FS worden in Azure AD als kenmerken voor het genereren van SAML-tokens, zelfs als deze kenmerken zijn gesynchroniseerd. Wanneer u het kenmerk bewerkt, worden in **de** vervolgkeuzelijst Waarde de verschillende kenmerken weergegeven die beschikbaar zijn in Azure AD. Controleer [Azure AD Connect onderwerpen synchroniseren](../hybrid/how-to-connect-sync-whatis.md) om ervoor te zorgen dat een vereist kenmerk, bijvoorbeeld **samAccountName,** wordt gesynchroniseerd met Azure AD. U kunt de extensiekenmerken gebruiken om een claim te maken die geen deel uitmaakt van het standaardgebruikersschema in Azure AD.
* In de meest voorkomende scenario's zijn alleen de **NameID**-claim en andere algemene gebruikers-id-claims vereist voor een app. Als u wilt bepalen of er aanvullende claims vereist zijn, bekijkt u welke claims u uit AD FS.
* Niet alle claims kunnen worden uitgegeven, omdat sommige claims zijn beveiligd in Azure AD.
* De mogelijkheid om versleutelde SAML-tokens te gebruiken, is nu beschikbaar als preview-versie. Zie How to: customize claims issued in the SAML token for enterprise applications (Claims aanpassen [die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen).](../develop/active-directory-saml-claims-customization.md)

### <a name="software-as-a-service-saas-apps"></a>SaaS-apps (Software as a Service)

Als uw gebruikers zich aanmelden bij SaaS-apps zoals Salesforce, ServiceNow of Workday en zijn geïntegreerd met AD FS, gebruikt u federatieve aanmelding voor SaaS-apps.

De meeste SaaS-toepassingen kunnen worden geconfigureerd in Azure AD. Microsoft heeft veel vooraf geconfigureerde verbindingen met SaaS-apps in de  [Azure AD-app-galerie,](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)waardoor uw overgang eenvoudiger wordt. SAML 2.0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure [AD-app-galerie of als niet-galerietoepassingen.](add-application-portal.md)

Apps die gebruikmaken van OAuth 2.0 of OpenID Connect kunnen op dezelfde manier worden geïntegreerd met Azure AD als [app-registraties.](../develop/quickstart-register-app.md) Apps die gebruikmaken van verouderde protocollen kunnen [Azure AD-toepassingsproxy](application-proxy.md) voor verificatie met Azure AD.

Voor problemen met de onboarding van uw SaaS-apps kunt u contact opnemen met de [ondersteuningsalias voor SaaS-toepassingsintegratie.](mailto:SaaSApplicationIntegrations@service.microsoft.com)

### <a name="saml-signing-certificates-for-sso"></a>SAML-handtekeningcertificaten voor eenmalige aanmelding

Ondertekeningscertificaten zijn een belangrijk onderdeel van een SSO-implementatie. Azure AD maakt de handtekeningcertificaten om federatief op SAML gebaseerde eenmalige aanmelding voor uw SaaS-toepassingen tot stand te brengen. Zodra u galerietoepassingen of niet-galerietoepassingen toevoegt, configureert u de toegevoegde toepassing met behulp van de optie federatief eenmalige aanmelding. Zie [Certificaten voor federatief een aanmelding](manage-certificates-for-federated-single-sign-on.md)beheren in Azure Active Directory .

### <a name="saml-token-encryption"></a>SAML-tokenversleuteling

Zowel AD FS als Azure AD bieden tokenversleuteling: de mogelijkheid om de SAML-beveiligings assertie te versleutelen die naar toepassingen gaan. De assertie wordt versleuteld met een openbare sleutel en ontsleuteld door de ontvangende toepassing met de bijbehorende persoonlijke sleutel. Wanneer u tokenversleuteling configureert, uploadt u X.509-certificaatbestanden om de openbare sleutels op te geven.

Zie How to: Configure Azure AD SAML token encryption (Azure AD SAML-tokenversleuteling configureren) voor meer informatie over Azure AD [SAML-tokenversleuteling](howto-saml-token-encryption.md)en hoe u deze kunt configureren.  

> [!NOTE]
> Tokenversleuteling is een Azure Active Directory (Azure AD) Premium-functie. Zie Prijzen voor Azure AD voor meer informatie over Azure AD-edities, -functies en [-prijzen.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Apps en configuraties die vandaag nog kunnen worden verplaatst

Apps die u nu eenvoudig kunt verplaatsen, zijn ONDER andere SAML 2.0-apps die gebruikmaken van de standaardset configuratie-elementen en claims. Deze standaarditems zijn:

* User Principal Name
* E-mailadres
* Voornaam
* Achternaam
* Alternatief kenmerk als SAML **NameID**, met inbegrip van het Azure AD e-mailkenmerk, e-mailvoorvoegsel, werknemer-id, extensiekenmerken 1-15, of on-premises **SamAccountName**-kenmerk. Zie voor meer informatie [De NameIdentifier-claim bewerken](../develop/active-directory-saml-claims-customization.md).
* Aangepaste claims.

Hieronder zijn aanvullende configuratiestappen vereist om te migreren naar Azure AD:

* Aangepaste autorisatie- of MFA-regels (Multi-Factor Authentication) in AD FS. U configureert deze met behulp van de [functie Voorwaardelijke toegang van Azure AD.](../conditional-access/overview.md)
* Apps met meerdere antwoord-URL-eindpunten. U configureert ze in Azure AD met behulp van PowerShell of de Azure Portal interface.
* WS-Federation-apps, zoals SharePoint-apps waarvoor versie 1.1-SAML-tokens vereist zijn. U kunt ze handmatig configureren met behulp van PowerShell. U kunt ook een vooraf geïntegreerde algemene sjabloon voor SharePoint- en SAML 1.1-toepassingen toevoegen vanuit de galerie. We ondersteunen het SAML 2.0-protocol.
* Bij de uitgifte van complexe claims worden regels getransformeerd. Zie voor meer informatie over ondersteunde claimtoewijzingen:
   *  [Claimtoewijzing in Azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Het aanpassen van claims die zijn uitgegeven in het SAML-token voor bedrijfstoepassingen in Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps en configuratie die momenteel niet worden ondersteund in Azure AD

Apps waarvoor bepaalde mogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd.

#### <a name="protocol-capabilities"></a>Protocolmogelijkheden

Apps waarvoor de volgende protocolmogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd:

* Ondersteuning voor het WS-Trust ActAs-patroon
* SAML-artefactomzetting
* Handtekeningverificatie van ondertekende SAML-aanvragen
  > [!Note]
  > Ondertekende aanvragen worden geaccepteerd, maar de handtekening wordt niet geverifieerd.

  Aangezien Azure AD alleen het token retourneert naar eindpunten die vooraf zijn geconfigureerd in de toepassing, is handtekeningverificatie in de meeste gevallen waarschijnlijk niet vereist.

#### <a name="claims-in-token-capabilities"></a>Claims in tokenmogelijkheden

Apps waarvoor de volgende claims in tokenmogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd.

* Claims van kenmerkopslagen die niet de Azure AD-directory zijn, tenzij die gegevens worden gesynchroniseerd met Azure AD. Zie overzicht van de [Azure AD-synchronisatie-API voor meer informatie.](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
* Uitgifte van mapkenmerken met meerdere waarden. We kunnen op dit moment bijvoorbeeld geen claim met meerdere waarde uitgeven voor proxyadressen.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>App-instellingen van AD FS aan Azure AD

Voor migratie moet worden beoordeeld hoe de toepassing on-premises is geconfigureerd en die configuratie vervolgens toewijzen aan Azure AD. AD FS en Azure AD werken op soortgelijke wijze, dus de concepten betreffende het configureren van vertrouwensrelaties, URL's voor aan- en afmelden, en id's zijn op beide producten van toepassing. Documenteren AD FS configuratie-instellingen van uw toepassingen, zodat u ze eenvoudig kunt configureren in Azure AD.

### <a name="map-app-configuration-settings"></a>App-configuratie-instellingen in kaart brengen

De volgende tabel beschrijft enkele van de meest voorkomende toewijzing van instellingen tussen een AD FS Relying Party Trust naar Azure AD Enterprise-toepassing:

* AD FS: zoek de instelling in de AD FS Relying Party Trust voor de app. Klik met de rechtermuisknop op de relying party en selecteer Eigenschappen.
* Azure AD: de instelling wordt geconfigureerd [binnen](https://portal.azure.com/) Azure Portal in de SSO-eigenschappen van elke toepassing.

| Configuratie-instelling| AD FS| Configureren in Azure AD| SAML-token |
| - | - | - | - |
| **Aanmeldings-URL van app** <p>De URL voor de gebruiker om zich aan te melden bij de app in een SAML-stroom die is geïnitieerd door een serviceprovider (SP).| N.v.t.| Open Standaard SAML-configuratie vanuit een aanmelding op basis van SAML| N.v.t. |
| **Antwoord-URL van app** <p>De URL van de app vanuit het perspectief van de id-provider (IdP). De IdP verzendt de gebruiker en het token hier nadat de gebruiker zich heeft aangemeld bij de IdP.  ‎This is also known as **SAML assertion consumer endpoint**.| Selecteer het **tabblad Eindpunten**| Open Standaard SAML-configuratie vanuit een aanmelding op basis van SAML| Het doelelement in het SAML-token. Voorbeeldwaarde: `https://contoso.my.salesforce.com` |
| **App-URL voor afmelden** <p>Dit is de URL waar aanvragen voor het opschonen van uitloggen naar worden verzonden wanneer een gebruiker zich bij een app af meldt. De IdP verzendt de aanvraag om de gebruiker ook af te melden bij alle andere apps.| Selecteer het **tabblad** Eindpunten| Open Standaard SAML-configuratie vanuit een aanmelding op basis van SAML| N.v.t. |
| **App-id** <p>Dit is de app-id vanuit het perspectief van de IdP. De waarde van de aanmeldings-URL wordt vaak (maar niet altijd) gebruikt als de id.  In de app wordt dit soms de entiteits-id genoemd.| Selecteer het **tabblad Id's**|Open Standaard SAML-configuratie vanuit een aanmelding op basis van SAML| Komt in kaart **met het** element Doelgroep in het SAML-token. |
| **Federatieve metagegevens van app** <p>Dit is de locatie van de federatiemetagegevens van de app. De IdP gebruikt deze om bepaalde configuratie-instellingen automatisch bij te werken, zoals eindpunten of versleutelingscertificaten.| Selecteer het **tabblad** Bewaking| N.v.t. Azure AD biedt geen ondersteuning voor het rechtstreeks gebruiken van federatiemetagegevens van toepassingen. U kunt de federatiemetagegevens handmatig importeren.| N.v.t. |
| **Gebruikers-id/naam-id** <p>Kenmerk dat wordt gebruikt om voor de app op unieke wijze de gebruikers-id aan te geven vanuit Azure AD of AD FS.  Dit kenmerk is doorgaans de UPN of het e-mailadres van de gebruiker.| Claimregels. In de meeste gevallen geeft de claimregel een claim uit met een type dat eindigt op **de NameIdentifier**.| U vindt de id onder de header **Gebruikerskenmerken en claims.** Standaard wordt de UPN gebruikt| Komt in kaart **met het NameID-element** in het SAML-token. |
| **Andere claims** <p>Voorbeelden van andere claimgegevens die doorgaans van de IdP naar de app worden verzonden, zijn voornaam, achternaam, e-mailadres en groepslidmaatschap.| In AD FS kunt u deze gegevens vinden als andere claimregels op de Relying Party.| U vindt de id onder de kop **Gebruikerskenmerken & Claims.** Selecteer **Beeld** en bewerk alle andere gebruikerskenmerken.| N.v.t. |

### <a name="map-identity-provider-idp-settings"></a>IdP-instellingen (Map Identity Provider)

Configureer uw toepassingen zo dat ze naar Azure AD wijzen AD FS voor eenmalige aanmelding. Hier richten we ons op SaaS-apps die gebruikmaken van het SAML-protocol. Dit concept geldt echter ook voor aangepaste Line-Of-Business-apps.

> [!NOTE]
> De configuratiewaarden voor Azure AD volgen het patroon waarbij uw Azure-tenant-id {tenant-id} vervangt en de toepassings-id {toepassings-id} vervangt. U vindt deze informatie in de [Azure Portal](https://portal.azure.com/) onder **Azure Active Directory > Eigenschappen:**

* Selecteer Map-id om uw tenant-id te zien.
* Selecteer Toepassings-id om uw toepassings-id te zien.

 Wijs op hoog niveau de volgende belangrijke configuratie-elementen voor SaaS-apps toe aan Azure AD.

| Element| Configuratiewaarde |
| - | - |
| Verlener van id-provider| https: \/ /sts.windows.net/{tenant-id}/ |
| Aanmeldings-URL van id-provider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Aanmeldings-URL van id-provider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Locatie van federatiemetagegevens| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>Instellingen voor eenmalige aanmelding voor SaaS-apps in kaart brengen

SaaS-apps moeten weten waar verificatieaanvragen moeten worden verzenden en hoe de ontvangen tokens moeten worden gevalideerd. In de volgende tabel worden de elementen beschreven voor het configureren van SSO-instellingen in de app en de waarden of locaties binnen AD FS en Azure AD

| Configuratie-instelling| AD FS| Configureren in Azure AD |
| - | - | - |
| **IdP-aanmeldings-URL** <p>Aanmeldings-URL van de IdP vanuit het perspectief van de app (waar de gebruiker wordt omgeleid voor aanmelding).| De AD FS aanmeldings-URL is de AD FS federation-servicenaam gevolgd door '/adfs/ls/'. <p>Bijvoorbeeld: `https://fs.contoso.com/adfs/ls/`| Vervang {tenant-id} door uw tenant-id. <p> Voor apps die gebruikmaken van het SAML-P-protocol: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Voor apps die gebruikmaken van het WS-Federation protocol: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP-aanmeldings-URL**<p>Aanmeldings-URL van de IdP vanuit het perspectief van de app (waar de gebruiker wordt omgeleid wanneer deze zich bij de app wil aanmelden).| De aanmeldings-URL is hetzelfde als de aanmeldings-URL of dezelfde URL met 'wa=wsignout1.0' toegevoegd. Bijvoorbeeld: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Vervang {tenant-id} door uw tenant-id.<p>Voor apps die gebruikmaken van het SAML-P-protocol:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Voor apps die gebruikmaken van het WS-Federation protocol: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificaat voor token-ondertekening**<p>De IdP gebruikt de persoonlijke sleutel van het certificaat om uitgegeven tokens te ondertekenen. Er wordt gecontroleerd of het token afkomstig is van de dezelfde IdP die de app is geconfigureerd om te vertrouwen.| U vindt het AD FS-certificaat voor token-ondertekening in AD FS-beheer onder **Certificaten**.| U vindt deze in de Azure Portal in de eigenschappen voor een een aanmelding van de toepassing onder de kop **SAML-handtekeningcertificaat**.  Daar kunt u het certificaat downloaden om het te uploaden naar de app.  <p>Als de toepassing meer dan één certificaat heeft, kunt u alle certificaten vinden in het XML-bestand met federatiemetagegevens. |
| **Id/ "verificator"**<p>Id van de IdP vanuit het perspectief van de app (ook wel de 'vergever-id' genoemd).<p>‎In the SAML token, the value appears as the Issuer element.| De id voor AD FS is doorgaans de federation-service-id in AD FS Management onder **Service > Edit Federation Service Properties**. Bijvoorbeeld: `http://fs.contoso.com/adfs/services/trust`| Vervang {tenant-id} door uw tenant-id.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **IdP-federatiemetagegevens**<p>Locatie van de openbaar beschikbare federatiemetagegevens van de IdP. (Sommige apps gebruiken federatiemetagegevens als alternatief voor het afzonderlijk door de beheerder configureren van URL's, id en tokenhandtekeningcertificaat.)| Zoek de URL AD FS federatiemetagegevens in AD FS Management onder **Service > Endpoints > Metadata > Type: Federation Metadata**. Bijvoorbeeld: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| De bijbehorende waarde voor Azure AD volgt het patroon [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Vervang {TenantDomainName} door de naam van uw tenant in de indeling 'contoso.onmicrosoft.com'.   <p>Zie voor meer informatie [Federatiemetagegevens](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Beveiligingsbeleid AD FS in Azure AD vertegenwoordigen

Wanneer u uw app-verificatie verplaatst naar Azure AD, maakt u toewijzingen van bestaand beveiligingsbeleid aan hun equivalente of alternatieve varianten die beschikbaar zijn in Azure AD. Door ervoor te zorgen dat deze toewijzingen kunnen worden uitgevoerd terwijl wordt voldaan aan de beveiligingsstandaarden die uw app-eigenaren nodig hebben, wordt de rest van de app-migratie aanzienlijk eenvoudiger.

Voor elk regelvoorbeeld laten we zien hoe de regel eruit ziet in AD FS, de equivalente code van de AD FS-regeltaal en hoe deze wordt toegerekend aan Azure AD.

### <a name="map-authorization-rules"></a>Autorisatieregels voor kaarten

Hier volgen enkele voorbeelden van verschillende typen autorisatieregels in AD FS en hoe u deze toe te passen op Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Voorbeeld 1: toegang tot alle gebruikers toestaan

Toegang tot alle gebruikers in AD FS:

  ![Schermopname van het dialoogvenster Single Sign-On instellen met SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Dit wordt op een van de volgende manieren aan Azure AD toe te wijzen:

1. Stel **Gebruikerstoewijzing vereist** in op **Nee.**

    ![toegangsbeheerbeleid bewerken voor SaaS-apps ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Als **u Gebruikerstoewijzing vereist** **instelt op Ja,** moeten gebruikers zijn toegewezen aan de toepassing om toegang te krijgen. Als deze is **ingesteld op Nee,** hebben alle gebruikers toegang. Deze schakelknop heeft geen controle over wat gebruikers in de Mijn apps **zien.**

1. Wijs **op het tabblad Gebruikers en groepen** uw toepassing toe aan de automatische groep Alle gebruikers.  U moet [Dynamische groepen inschakelen](../enterprise-users/groups-create-rule.md) in uw Azure AD-tenant om de **standaardgroep Alle gebruikers** beschikbaar te maken.

    ![Mijn SaaS-apps in Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Voorbeeld 2: Een groep expliciet toestaan

Expliciete groepsautorisatie in AD FS:

  ![Schermopname van het dialoogvenster Regel bewerken voor de regel Claim van domeinad administrators toestaan.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Deze regel aan Azure AD toe te wijs:

1. Maak in [Azure Portal](https://portal.azure.com/) [een gebruikersgroep die](../fundamentals/active-directory-groups-create-azure-portal.md) overeenkomt met de groep gebruikers van AD FS.
1. App-machtigingen toewijzen aan de groep:

    ![Toewijzing toevoegen ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Voorbeeld 3: Een specifieke gebruiker machtigen

Expliciete gebruikersautorisatie in AD FS:

  ![Schermopname van het dialoogvenster Regel bewerken voor de regel Een specifieke gebruiker claim toestaan met een binnenkomend claimtype van primaire S IDE.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Deze regel aan Azure AD toe te wijs:

* Voeg in [Azure Portal](https://portal.azure.com/)een gebruiker toe aan de app via het tabblad Toewijzing toevoegen van de app, zoals hieronder wordt weergegeven:

    ![Mijn SaaS-apps in Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication-regels in kaart brengen

Een on-premises implementatie van [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) en AD FS werkt nog steeds na de migratie omdat u federatief bent met AD FS. Overweeg echter om te migreren naar de ingebouwde MFA-mogelijkheden van Azure die zijn gekoppeld aan de werkstromen voor voorwaardelijke toegang van Azure AD.

Hier volgen enkele voorbeelden van typen MFA-regels in AD FS en hoe u deze kunt in kaart brengen in Azure AD op basis van verschillende voorwaarden.

MFA-regelinstellingen in AD FS:

  ![Schermopname van Voorwaarden voor Azure A D in de Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Voorbeeld 1: MFA afdwingen op basis van gebruikers/groepen

De gebruikers-/groepen-selector is een regel waarmee u MFA kunt afdwingen per groep (groeps-SID) of per gebruiker (primaire SID). Afgezien van de toewijzingen van gebruikers/groepen, werken alle aanvullende selectievakjes in de gebruikersinterface voor MFA-configuratie van AD FS als aanvullende regels die worden geëvalueerd nadat de regel voor gebruikers/groepen is afgedwongen.

MFA-regels opgeven voor een gebruiker of een groep in Azure AD:

1. Maak een [nieuw beleid voor voorwaardelijke toegang.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Selecteer **Toewijzingen**. Voeg de gebruiker(s) of groepen toe waarvoor u MFA wilt afdwingen.
1. Configureer de opties voor toegangsbesturingselementen zoals hieronder wordt weergegeven: 

    ‎![Schermopname van het deelvenster Verlenen waar u toegang kunt verlenen.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Voorbeeld 2: MFA afdwingen voor niet-geregistreerde apparaten

MFA-regels opgeven voor niet-geregistreerde apparaten in Azure AD:

1. Maak een [nieuw beleid voor voorwaardelijke toegang.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Stel **Toewijzingen in** op **Alle gebruikers.**
1. Configureer de opties voor toegangsbesturingselementen zoals hieronder wordt weergegeven: 

    ![Schermopname van het deelvenster Verlenen waar u toegang kunt verlenen en andere beperkingen kunt opgeven.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Wanneer u  de optie Voor meerdere besturingselementen in stelt op Een van de geselecteerde besturingselementen **vereisen,** betekent dit dat als aan een van de voorwaarden die zijn opgegeven door de gebruiker wordt voldaan, de gebruiker toegang krijgt tot uw app.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Voorbeeld 3: MFA afdwingen op basis van locatie

Geef MFA-regels op op basis van de locatie van een gebruiker in Azure AD:

1. Maak een [nieuw beleid voor voorwaardelijke toegang.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)
1. Stel **Toewijzingen in** op **Alle gebruikers.**
1. [Benoemde locaties configureren in Azure AD.](../reports-monitoring/quickstart-configure-named-locations.md) Anders wordt federatie vanuit uw bedrijfsnetwerk vertrouwd.
1. Configureer **de voorwaardenregels** om de locaties op te geven waarvoor u MFA wilt afdwingen.

    ![Schermopname van het deelvenster Locaties voor voorwaardenregels.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Configureer de opties voor toegangsbesturingselementen zoals hieronder wordt weergegeven: 

    ![Toegangsbeheerbeleid voor kaarten](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Kenmerk voor het in kaart brengen van claims als claimregel

Kenmerken als claimregel in de volgende AD FS:

  ![Schermopname van het dialoogvenster Regel bewerken voor Kenmerken als claims weergeven.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

De regel aan Azure AD toe te wijsen:

1. Selecteer in [Azure Portal](https://portal.azure.com/)bedrijfstoepassingen **en** vervolgens Een aanmelding om **de** op SAML gebaseerde aanmeldingsconfiguratie te bekijken:

    ![Schermopname van de pagina Een aanmelding voor uw Ondernemingstoepassing.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Selecteer **Bewerken** (gemarkeerd) om de kenmerken te wijzigen:

    ![Dit is de pagina voor het bewerken van gebruikerskenmerken en claims](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Ingebouwde beleidsregels voor toegangsbeheer in kaart brengen

Ingebouwd toegangsbeheerbeleid in AD FS 2016:

  ![Ingebouwde toegangsbeheer voor Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Als u ingebouwd beleid in Azure [](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) AD wilt implementeren, gebruikt u een nieuw beleid voor voorwaardelijke toegang en configureert u het toegangsbeheer of gebruikt u de aangepaste beleidsontwerper in AD FS 2016 om toegangscontrolebeleid te configureren. De regeleditor heeft een volledige lijst met opties Toestaan en Behalve die u kunnen helpen bij het maken van allerlei soorten permutaties.

  ![Toegangsbeheerbeleid voor Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

In deze tabel hebben we een aantal handige opties voor Toestaan en Behalve vermeld en hoe deze worden weergegeven in Azure AD.

| Optie | De optie Toestaan configureren in Azure AD| De optie Except configureren in Azure AD |
| - | - | - |
| Van specifieke netwerk| Komt in Kaart [met benoemde](../reports-monitoring/quickstart-configure-named-locations.md) locatie in Azure AD| De optie **Uitsluiten gebruiken** voor [vertrouwde locaties](../conditional-access/location-condition.md) |
| Van specifieke groepen| [Een gebruikers-/groepentoewijzing instellen](assign-user-or-group-access-portal.md)| De optie **Uitsluiten** gebruiken in Gebruikers en groepen |
| Van apparaten met een specifiek vertrouwensniveau| Stel dit in vanuit het **besturingselement Apparaattoestand** onder Toewijzingen -> voorwaarden| Gebruik de **optie Uitsluiten** onder Apparaattoestandvoorwaarde en Alle **apparaten opnemen** |
| Met specifieke claims in de aanvraag| Deze instelling kan niet worden gemigreerd| Deze instelling kan niet worden gemigreerd |

Hier is een voorbeeld van hoe u de optie Uitsluiten configureert voor vertrouwde locaties in de Azure Portal:

  ![Schermopname van beleid voor toegangsbeheer toewijzen](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Gebruikers overstappen van AD FS naar Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Groepen AD FS synchroniseren in Azure AD

Wanneer u autorisatieregels toekent, kunnen apps die AD FS Active Directory-groepen gebruiken voor machtigingen. In dat geval gebruikt [u](https://go.microsoft.com/fwlink/?LinkId=615771) Azure AD Connect om deze groepen te synchroniseren met Azure AD voordat u de toepassingen migreert. Controleer deze groepen en het lidmaatschap vóór de migratie, zodat u dezelfde gebruikers toegang kunt verlenen wanneer de toepassing wordt gemigreerd.

Zie Vereisten voor het gebruik van groepskenmerken die zijn gesynchroniseerd [vanuit Active Directory voor meer informatie.](../hybrid/how-to-connect-fed-group-claims.md)

### <a name="set-up-user-self-provisioning"></a>Zelf inrichten van gebruikers instellen

Sommige SaaS-toepassingen ondersteunen de mogelijkheid om gebruikers zelf in terichten wanneer ze zich voor het eerst aanmelden bij de toepassing. In Azure AD verwijst app-inrichting naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud[(SaaS)-toepassingen](https://azure.microsoft.com/overview/what-is-saas/)die gebruikers nodig hebben. Gebruikers die zijn gemigreerd, hebben al een account in de SaaS-toepassing. Nieuwe gebruikers die na de migratie worden toegevoegd, moeten worden ingericht. De [inrichting van SaaS-apps testen](../app-provisioning/user-provisioning.md) zodra de toepassing is gemigreerd.

### <a name="sync-external-users-in-azure-ad"></a>Externe gebruikers synchroniseren in Azure AD

Uw bestaande externe gebruikers kunnen op deze twee manieren worden ingesteld op AD FS:

* **Externe gebruikers met een lokaal account binnen uw organisatie:** u blijft deze accounts op dezelfde manier gebruiken als uw interne gebruikersaccounts. Deze externe gebruikersaccounts hebben een principenaam binnen uw organisatie, hoewel het e-mailadres van het account extern kan wijzen. Naarmate u verder gaat met de migratie, kunt u profiteren van de voordelen die [Azure AD B2B](../external-identities/what-is-b2b.md) biedt door deze gebruikers te migreren om hun eigen bedrijfsidentiteit te gebruiken wanneer een dergelijke identiteit beschikbaar is. Dit stroomlijnt het aanmeldingsproces voor die gebruikers, omdat ze vaak zijn aangemeld met hun eigen zakelijke aanmelding. Het beheer van uw organisatie is ook eenvoudiger, omdat u geen accounts voor externe gebruikers moet beheren.
* **Federatief externe identiteiten:** als u momenteel federeert met een externe organisatie, kunt u het volgende op een aantal manieren doen:
  * [Voeg Azure Active Directory B2B-samenwerking toe aan de Azure Portal](../external-identities/add-users-administrator.md). U kunt proactief uitnodigingen voor B2B-samenwerking verzenden vanuit de Azure AD-beheerportal naar de partnerorganisatie, zodat afzonderlijke leden de apps en assets blijven gebruiken die ze gewend zijn.
  * [Maak een selfservice B2B-aanmeldingswerkstroom](../external-identities/self-service-portal.md) die een aanvraag genereert voor afzonderlijke gebruikers in uw partnerorganisatie met behulp van de B2B-uitnodigings-API.

Ongeacht hoe uw bestaande externe gebruikers zijn geconfigureerd, ze hebben waarschijnlijk machtigingen die zijn gekoppeld aan hun account, in groepslidmaatschap of specifieke machtigingen. Evalueer of deze machtigingen moeten worden gemigreerd of opgeschoond. Accounts binnen uw organisatie die een externe gebruiker vertegenwoordigen, moeten worden uitgeschakeld zodra de gebruiker is gemigreerd naar een externe identiteit. Het migratieproces moet worden besproken met uw zakelijke partners, omdat de mogelijkheid om verbinding te maken met uw resources mogelijk wordt onderbroken.

## <a name="migrate-and-test-your-apps"></a>Uw apps migreren en testen

Volg het migratieproces dat in dit artikel wordt beschreven. Ga vervolgens naar de [Azure Portal](https://aad.portal.azure.com/) om te testen of de migratie is geslaagd.

Volg deze instructies:

1. Selecteer **Bedrijfstoepassingen**  >  **Alle toepassingen** en zoek uw app in de lijst.
1. Selecteer **Gebruikers**  >  **en groepen beheren om** ten minste één gebruiker of groep aan de app toe te wijzen.
1. Selecteer **Voorwaardelijke**  >  **toegang beheren.** Controleer uw lijst met beleidsregels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang.](../conditional-access/overview.md)

Controleer of eenmalige aanmelding goed werkt, afhankelijk van hoe u uw app configureert.

| Verificatietype| Testen |
| :- | :- |
| OAuth/OpenID Connect| Selecteer **Bedrijfstoepassingen > Machtigingen** en zorg ervoor dat u toestemming hebt gegeven voor de toepassing in de gebruikersinstellingen voor uw app.|
| Op SAML gebaseerde SSO | Gebruik de [knop TEST SAML Settings](debug-saml-sso-issues.md) onder Single **Sign-On**. |
| Password-Based SSO |  Download en installeer de [myApps-extensie](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [voor veilig aanmelden.](../user-help/my-apps-portal-end-user-access.md) Met deze extensie kunt u een van de cloud-apps van uw organisatie starten waarvoor u een SSO-proces moet gebruiken. |
| Toepassingsproxy | Zorg ervoor dat de connector wordt uitgevoerd en is toegewezen aan uw toepassing. Ga naar [toepassingsproxy gids voor probleemoplossing](application-proxy-troubleshoot.md) voor meer hulp. |

> [!NOTE]
> Cookies van de oude AD FS op de gebruikersmachines. Deze cookies kunnen problemen met de migratie veroorzaken, omdat gebruikers kunnen worden omgeleid naar de oude AD FS aanmeldingsomgeving versus de nieuwe Azure AD-aanmelding. Mogelijk moet u de cookies in de gebruikersbrowser handmatig verwijderen of een script gebruiken. U kunt ook de System Center Configuration Manager of een vergelijkbaar platform gebruiken.

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten optreden tijdens de test van de gemigreerde toepassingen, kan het oplossen van problemen de eerste stap zijn voordat u terugvallen op de bestaande AD FS Relying Parties. Zie [How to debug SAML-based single sign-on to applications in Azure Active Directory](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Migratie terugdraaien

Als de migratie mislukt, wordt u aangeraden de bestaande Relying Parties op de AD FS servers te laten en de toegang tot de Relying Parties te verwijderen. Dit maakt een snelle terugval mogelijk, indien nodig tijdens de implementatie.

### <a name="employee-communication"></a>Communicatie van werknemers

Hoewel het geplande storingsvenster zelf minimaal kan zijn, moet u deze tijdsbestekken proactief naar werknemers communiceren terwijl u overschakelt van AD FS naar Azure AD. Zorg ervoor dat uw app-ervaring een feedbackknop heeft of dat er naar uw helpdesk wordt wijzer voor problemen.

Zodra de implementatie is voltooid, kunt u gebruikers informeren over de geslaagde implementatie en hen eraan herinneren welke stappen ze moeten nemen.

* Instrueren dat gebruikers [Mijn apps](https://myapps.microsoft.com) om toegang te krijgen tot alle gemigreerde toepassingen.
* Gebruikers eraan herinneren dat ze mogelijk hun MFA-instellingen moeten bijwerken.
* Als Self-Service wachtwoord opnieuw instellen is geïmplementeerd, moeten gebruikers mogelijk hun verificatiemethoden bijwerken of verifiëren. Zie [MFA-](https://aka.ms/mfatemplates) en [SSPR-communicatiesjablonen](https://aka.ms/ssprtemplates) voor eindgebruikers.

### <a name="external-user-communication"></a>Communicatie van externe gebruikers

Deze groep gebruikers wordt meestal het meest beïnvloed in het geval van problemen. Dit geldt met name als uw beveiligingsstatus een andere set regels voor voorwaardelijke toegang of risicoprofielen voor externe partners bepaalt. Zorg ervoor dat externe partners op de hoogte zijn van het cloudmigratieschema en een tijdsbestek hebben waarin ze worden aangemoedigd deel te nemen aan een testimplementatie waarmee alle stromen worden getest die uniek zijn voor externe samenwerking. Zorg er ten slotte voor dat ze een manier hebben om toegang te krijgen tot uw helpdesk voor het geval er problemen zijn.

## <a name="next-steps"></a>Volgende stappen

* Lees [Toepassingsverificatie migreren naar Azure AD.](https://aka.ms/migrateapps/whitepaper)
* Stel [voorwaardelijke toegang en](../conditional-access/overview.md) [MFA in.](../authentication/concept-mfa-howitworks.md)
* Probeer een stapsgewijs codevoorbeeld: AD FS[azure AD-playbook voor toepassingsmigratie voor ontwikkelaars](https://aka.ms/adfsplaybook).
