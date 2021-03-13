---
title: Toepassings verificatie van AD FS naar Azure Active Directory verplaatsen
description: Meer informatie over het gebruik van Azure Active Directory om Active Directory Federation Services (AD FS) te vervangen, waarbij gebruikers eenmalige aanmelding voor al hun toepassingen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418352"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Toepassingsverificatie verplaatsen van Active Directory Federation Services naar Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) biedt een universeel identiteits platform dat uw gebruikers, partners en klanten een enkele identiteit biedt voor het openen van toepassingen en samen werken vanaf elk platform en apparaat. Azure AD heeft een [volledige reeks mogelijkheden voor identiteits beheer](../fundamentals/active-directory-whatis.md). Het standaardiseren van uw toepassings verificatie en autorisatie voor Azure AD biedt deze voor delen.

> [!TIP]
> Dit artikel is geschreven voor een doel groep van ontwikkel aars. Project managers en beheerders plannen om een toepassing naar Azure AD te verplaatsen, moeten overwegen de [migratie van toepassings verificatie naar Azure ad te](migrate-application-authentication-to-azure-active-directory.md)lezen.

## <a name="azure-ad-benefits"></a>Voor delen van Azure AD

Als u een on-premises map hebt die gebruikers accounts bevat, hebt u waarschijnlijk veel toepassingen die gebruikers verifiëren. Elk van deze apps is geconfigureerd voor gebruikers om toegang te krijgen tot het gebruik van hun identiteit.

Gebruikers kunnen ook rechtstreeks worden geverifieerd met uw on-premises Active Directory. Active Directory Federation Services (AD FS) is een op standaarden gebaseerde on-premises identiteits service. Het biedt de mogelijkheid om de functionaliteit voor eenmalige aanmelding (SSO) te gebruiken tussen vertrouwde zakelijke partners zodat gebruikers zich niet afzonderlijk hoeven aan te melden bij elke toepassing. Dit wordt federatieve identiteit genoemd.

Veel organisaties hebben SaaS-(Software as a Service) of aangepaste line-of-Business-Apps die rechtstreeks worden AD FS, naast Microsoft 365 en op Azure AD gebaseerde apps.

  ![Toepassingen die rechtstreeks on-premises zijn verbonden](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> Om de beveiliging van toepassingen te verbeteren, is het doel om één set toegangs beheer en-beleid te hebben in uw on-premises en Cloud omgevingen.

  ![Toepassingen die zijn verbonden via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>Typen apps die moeten worden gemigreerd

Het migreren van alle toepassings verificatie naar Azure AD is optimaal, omdat u slechts één controle vlak hebt voor identiteits-en toegangs beheer.

Uw toepassingen kunnen gebruikmaken van moderne of verouderde protocollen voor verificatie. Wanneer u de migratie naar Azure AD plant, kunt u overwegen om de apps te migreren die gebruikmaken van moderne verificatie protocollen (zoals SAML en open ID Connect). Deze apps kunnen opnieuw worden geconfigureerd voor verificatie met Azure AD via een ingebouwde connector van de Azure-app-galerie, of door de toepassing te registreren in azure AD. Apps die gebruikmaken van oudere protocollen kunnen worden geïntegreerd met toepassings proxy.

Zie voor meer informatie:

* [Azure AD-toepassingsproxy gebruiken om on-premises apps voor externe gebruikers te publiceren](what-is-application-proxy.md).
* [Wat is toepassingsbeheer?](what-is-application-management.md)
* [AD FS toepassings activiteiten rapport voor het migreren van toepassingen naar Azure AD](migrate-adfs-application-activity.md).
* [Bewaak AD FS met behulp van Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Het migratie proces

Test uw apps en configuratie tijdens het proces van het verplaatsen van uw app-verificatie naar Azure AD. U wordt aangeraden bestaande test omgevingen te blijven gebruiken voor migratie tests wanneer u overstapt op de productie omgeving. Als er op dit moment geen test omgeving beschikbaar is, kunt u er een instellen met behulp van [Azure app service](https://azure.microsoft.com/services/app-service/) of [Azure virtual machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), afhankelijk van de architectuur van de toepassing.

U kunt ervoor kiezen om een afzonderlijke test van Azure AD-Tenant in te stellen voor het ontwikkelen van uw app-configuraties.

Uw migratie proces kan er als volgt uitzien:

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>Fase 1: huidige status: de productie-app verifieert met AD FS

  ![Migratie fase 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>Fase 2: (optioneel) een test exemplaar van de app naar de Azure AD-Tenant testen

Werk de configuratie bij om uw test exemplaar van de app te laten wijzen naar een Azure AD-Tenant en breng de gewenste wijzigingen aan. De app kan worden getest met gebruikers in de Azure AD-Tenant testen. Tijdens het ontwikkelings proces kunt u hulpprogram ma's zoals [Fiddler](https://www.telerik.com/fiddler) gebruiken om aanvragen en antwoorden te vergelijken en te controleren.

Als het niet haalbaar is om een afzonderlijke test Tenant in te stellen, kunt u deze fase overs Laan en een test exemplaar van de app naar uw productie-Azure AD-Tenant verwijzen, zoals beschreven in fase 3 hieronder.

  ![Migratie fase 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>Fase 3: een test exemplaar van de app naar de productie-Tenant van Azure AD verwijzen

Werk de configuratie bij zodat uw test instantie van de app naar uw productie-Azure AD-Tenant verwijst. U kunt nu testen met gebruikers in uw productie Tenant. Lees, indien nodig, de sectie in dit artikel over overgangen van gebruikers.

  ![Migratie fase 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>Fase 4: de productie-app naar de productie-Azure AD-Tenant verwijzen

Werk de configuratie van uw productie-app bij zodat deze verwijst naar uw productie Azure AD-Tenant.

  ![Migratie fase 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Apps die met AD FS worden geverifieerd, kunnen Active Directory groepen gebruiken voor machtigingen. Gebruik [Azure AD Connect Sync](../hybrid/how-to-connect-sync-whatis.md) om identiteits gegevens tussen uw on-premises omgeving en Azure ad te synchroniseren voordat u de migratie start. Controleer deze groepen en het lidmaatschap vóór de migratie zodat u toegang tot dezelfde gebruikers kunt verlenen wanneer de toepassing wordt gemigreerd.

### <a name="line-of-business-apps"></a>Line-of-Business-Apps

Uw line-of-Business-Apps zijn de toepassingen die uw organisatie heeft ontwikkeld of die een standaard verpakt product zijn. Voor beelden zijn apps die zijn gebouwd op Windows Identity Foundation en share point-apps (niet share point online).

Line-of-Business-Apps die gebruikmaken van OAuth 2,0, OpenID Connect Connect of WS-Federation, kunnen worden geïntegreerd met Azure AD als [app-registraties](../develop/quickstart-register-app.md). Integreer aangepaste apps die gebruikmaken van SAML 2,0 of WS-Federation als [niet-galerie toepassingen](add-application-portal.md) op de pagina bedrijfs toepassingen in de [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Eenmalige aanmelding op basis van SAML

Apps die gebruikmaken van SAML 2,0 voor authenticatie, kunnen worden geconfigureerd voor SSO ( [Single Sign-on](what-is-single-sign-on.md) ) op basis van SAML. Met SSO op basis van SAML kunt u gebruikers toewijzen aan specifieke toepassings rollen op basis van regels die u in uw SAML-claims definieert.

Als u een SaaS-toepassing voor op SAML gebaseerde SSO wilt configureren, raadpleegt u [Quick Start: eenmalige aanmelding op basis van SAML instellen](add-application-portal-setup-sso.md).

  ![SSO SAML-gebruikers afbeeldingen ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

Veel SaaS-toepassingen hebben een [toepassingsspecifieke zelf studie](../saas-apps/tutorial-list.md) die u doorloopt bij de configuratie van op SAML gebaseerde SSO.

  ![app-zelf studie](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Sommige apps kunnen eenvoudig worden gemigreerd. Apps met complexere vereisten, zoals aangepaste claims, vereisen mogelijk aanvullende configuratie in azure AD en/of Azure AD Connect. Zie [How to: claims aanpassen die worden verzonden in tokens voor een specifieke app in een Tenant (preview)](../develop/active-directory-claims-mapping.md)voor meer informatie over ondersteunde claim toewijzingen.

Houd bij het toewijzen van kenmerken de volgende beperkingen in acht:

* Niet alle kenmerken die kunnen worden uitgegeven in AD FS in azure AD worden weer gegeven als kenmerken die moeten worden verzonden naar SAML-tokens, zelfs als deze kenmerken worden gesynchroniseerd. Wanneer u het kenmerk bewerkt, ziet u in de vervolg keuzelijst met **waarden** de verschillende kenmerken die beschikbaar zijn in azure AD. Controleer de configuratie van de [onderwerpen over Azure AD Connect synchronisatie](../hybrid/how-to-connect-sync-whatis.md) om ervoor te zorgen dat een vereist kenmerk, bijvoorbeeld **sAMAccountName**, wordt gesynchroniseerd met Azure AD. U kunt de extensie kenmerken gebruiken om een claim te verzenden die geen deel uitmaakt van het standaard gebruikers schema in azure AD.
* In de meest voorkomende scenario's zijn alleen de **NameID**-claim en andere algemene gebruikers-id-claims vereist voor een app. Als u wilt bepalen of er aanvullende claims zijn vereist, kunt u onderzoeken welke claims u uitgeeft van AD FS.
* Niet alle claims kunnen worden uitgegeven, omdat sommige claims worden beveiligd in azure AD.
* De mogelijkheid om versleutelde SAML-tokens te gebruiken, is nu beschikbaar als preview-versie. Zie [How to: claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md).

### <a name="software-as-a-service-saas-apps"></a>SaaS-apps (Software as a Service)

Als uw gebruikers zich aanmelden bij SaaS-apps zoals Sales Force, ServiceNow of workday en zijn geïntegreerd met AD FS, gebruikt u federatieve aanmelding voor SaaS-apps.

De meeste SaaS-toepassingen kunnen worden geconfigureerd in azure AD. Micro soft heeft veel vooraf geconfigureerde verbindingen met SaaS-apps in de  [Azure AD-App-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), waardoor uw overgang eenvoudiger wordt. SAML 2,0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure AD-App-galerie of als [niet-galerie toepassingen](add-application-portal.md).

Apps die gebruikmaken van OAuth 2,0 of OpenID Connect Connect, kunnen op vergelijk bare wijze worden geïntegreerd met Azure AD als [app-registraties](../develop/quickstart-register-app.md). Apps die gebruikmaken van verouderde protocollen kunnen [azure AD-toepassingsproxy](application-proxy.md) gebruiken om te verifiëren met Azure AD.

Voor problemen met het voorbereiden van uw SaaS-apps kunt u contact opnemen met de [ondersteunings alias voor SaaS-toepassings integratie](mailto:SaaSApplicationIntegrations@service.microsoft.com).

### <a name="saml-signing-certificates-for-sso"></a>SAML-handtekening certificaten voor SSO

Handtekening certificaten vormen een belang rijk onderdeel van elke SSO-implementatie. Azure AD maakt de handtekening certificaten om op SAML gebaseerde federatieve SSO te maken voor uw SaaS-toepassingen. Als u een van beide galerie-of niet-galerie toepassingen hebt toegevoegd, configureert u de toegevoegde toepassing met behulp van de optie Federated SSO. Zie [certificaten beheren voor federatieve eenmalige aanmelding in azure Active Directory](manage-certificates-for-federated-single-sign-on.md).

### <a name="saml-token-encryption"></a>SAML-token versleuteling

Zowel AD FS als Azure AD bieden token versleuteling: de mogelijkheid om de SAML-beveiligings verklaringen te versleutelen die naar toepassingen gaan. De beweringen worden versleuteld met een open bare sleutel en ontsleuteld door de ontvangende toepassing met de bijbehorende persoonlijke sleutel. Wanneer u token versleuteling configureert, uploadt u X. 509-certificaat bestanden om de open bare sleutels op te geven.

Zie [How to: Configure Azure AD SAML token Encryption](howto-saml-token-encryption.md)(Engelstalig) voor meer informatie over Azure AD SAML-token versleuteling en hoe u deze kunt configureren.  

> [!NOTE]
> Token versleuteling is een functie van Azure Active Directory (Azure AD) Premium. Zie [prijzen voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over Azure AD-edities,-functies en-prijzen.

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Apps en configuraties die vandaag kunnen worden verplaatst

Apps die u vandaag eenvoudig kunt verplaatsen, zijn SAML 2,0-apps die gebruikmaken van de standaardset configuratie-elementen en claims. Deze standaard items zijn:

* User Principal Name
* E-mailadres
* Voornaam
* Achternaam
* Alternatief kenmerk als SAML **NameID**, met inbegrip van het Azure AD e-mailkenmerk, e-mailvoorvoegsel, werknemer-id, extensiekenmerken 1-15, of on-premises **SamAccountName**-kenmerk. Zie voor meer informatie [De NameIdentifier-claim bewerken](../develop/active-directory-saml-claims-customization.md).
* Aangepaste claims.

De volgende aanvullende configuratie stappen zijn vereist om te migreren naar Azure AD:

* Aangepaste autorisatie of MFA-regels (multi-factor Authentication) in AD FS. U kunt ze configureren met behulp van de functie [voorwaardelijke toegang van Azure AD](../conditional-access/overview.md) .
* Apps met meerdere antwoord-URL-eind punten. U kunt deze configureren in azure AD met behulp van Power shell of de Azure Portal-interface.
* WS-Federation-apps, zoals SharePoint-apps waarvoor versie 1.1-SAML-tokens vereist zijn. U kunt ze hand matig configureren met Power shell. U kunt ook een vooraf geïntegreerde algemene sjabloon toevoegen voor share point-en SAML 1,1-toepassingen uit de galerie. Het SAML 2,0-protocol wordt ondersteund.
* Regels voor de uitgifte van complexe claims. Zie voor informatie over ondersteunde claim toewijzingen:
   *  [Claim toewijzing in azure Active Directory](../develop/active-directory-claims-mapping.md).
   * [Het aanpassen van claims die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in azure Active Directory](../develop/active-directory-saml-claims-customization.md).

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps en configuratie die momenteel niet worden ondersteund in Azure AD

Apps waarvoor bepaalde mogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd.

#### <a name="protocol-capabilities"></a>Protocol mogelijkheden

Apps waarvoor de volgende protocol mogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd:

* Ondersteuning voor het ActAs-patroon van WS-Trust
* SAML-artefactomzetting
* Handtekening verificatie van ondertekende SAML-aanvragen
  > [!Note]
  > Ondertekende aanvragen worden geaccepteerd, maar de hand tekening is niet geverifieerd.

  Omdat Azure AD alleen het token retourneert naar de eind punten die vooraf zijn geconfigureerd in de toepassing, is hand tekening verificatie waarschijnlijk niet in de meeste gevallen vereist.

#### <a name="claims-in-token-capabilities"></a>Claims in token mogelijkheden

Apps waarvoor de volgende claims in de tokens nodig zijn, kunnen vandaag niet worden gemigreerd.

* Claims uit kenmerk archieven, behalve de Azure AD-Directory, tenzij deze gegevens worden gesynchroniseerd met Azure AD. Zie [Azure AD Synchronization API overview](/graph/api/resources/synchronization-overview?view=graph-rest-beta)(Engelstalig) voor meer informatie.
* Uitgifte van kenmerken van meerdere waarden in de Directory. Zo kan er op dit moment geen claim met meerdere waarden worden uitgegeven voor proxy adressen.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>App-instellingen van AD FS toewijzen aan Azure AD

Voor de migratie moet worden beoordeeld hoe de toepassing on-premises is geconfigureerd en wordt die configuratie vervolgens toegewezen aan Azure AD. AD FS en Azure AD werken op soortgelijke wijze, dus de concepten betreffende het configureren van vertrouwensrelaties, URL's voor aan- en afmelden, en id's zijn op beide producten van toepassing. Documenteer de AD FS configuratie-instellingen van uw toepassingen, zodat u ze eenvoudig kunt configureren in azure AD.

### <a name="map-app-configuration-settings"></a>Configuratie-instellingen van kaart-app

De volgende tabel beschrijft een aantal van de meest voorkomende toewijzing van instellingen tussen een AD FS Relying Party-vertrouwens relatie met Azure AD Enter prise-toepassing:

* AD FS: Zoek de instelling in de vertrouwens relatie van AD FS Relying Party voor de app. Klik met de rechter muisknop op de Relying Party en selecteer Eigenschappen.
* Azure AD: de instelling wordt geconfigureerd in [Azure Portal](https://portal.azure.com/) van de SSO-eigenschappen van elke toepassing.

| Configuratie-instelling| AD FS| Configureren in azure AD| SAML-token |
| - | - | - | - |
| **Aanmeldings-URL van app** <p>De URL waarmee de gebruiker zich moet aanmelden bij de app in een SAML-stroom die is geïnitieerd door een service provider (SP).| N.v.t.| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| N.v.t. |
| **Antwoord-URL van app** <p>De URL van de app vanuit het perspectief van de ID-provider (IdP). De IdP verzendt de gebruiker en het token hier nadat de gebruiker zich heeft aangemeld bij de IdP.  Dit wordt ook wel het **consument eindpunt van SAML-bewering** genoemd.| Selecteer het tabblad **eind punten**| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| Doel element in het SAML-token. Voorbeeldwaarde: `https://contoso.my.salesforce.com` |
| **App-URL voor afmelden** <p>Dit is de URL waarnaar afmeldings aanvragen worden verzonden wanneer een gebruiker zich afmeldt vanuit een app. De IdP verzendt de aanvraag voor het afmelden van de gebruiker van alle andere apps ook.| Selecteer het tabblad **eind punten**| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| N.v.t. |
| **App-id** <p>Dit is de app-id uit het perspectief van de IdP. De waarde van de aanmeldings-URL wordt vaak (maar niet altijd) gebruikt als de id.  Soms roept de app de entiteit-ID aan.| Het tabblad **id's** selecteren|Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| Wordt toegewezen aan het element **doel groep** in het SAML-token. |
| **Federatieve metagegevens van app** <p>Dit is de locatie van de federatieve meta gegevens van de app. De IdP gebruikt deze om bepaalde configuratie-instellingen automatisch bij te werken, zoals eindpunten of versleutelingscertificaten.| Selecteer het tabblad **controle**| N.v.t. Azure AD biedt geen ondersteuning voor het rechtstreeks gebruiken van Application Federation-meta gegevens. U kunt de federatieve meta gegevens hand matig importeren.| N.v.t. |
| **Gebruikers-id/naam-ID** <p>Kenmerk dat wordt gebruikt om voor de app op unieke wijze de gebruikers-id aan te geven vanuit Azure AD of AD FS.  Dit kenmerk is doorgaans de UPN of het e-mail adres van de gebruiker.| Claim regels. In de meeste gevallen geeft de claim regel een claim uit met een type dat eindigt op de **NameIdentifier**.| U kunt de id vinden onder de header **gebruikers kenmerken en claims**. De UPN wordt standaard gebruikt| Wordt toegewezen aan het **NameID** -element in het SAML-token. |
| **Andere claims** <p>Voor beelden van andere claim gegevens die vaak van de IdP naar de app worden verzonden, zijn voor naam, achternaam, e-mail adres en groepslid maatschap.| In AD FS kunt u deze gegevens vinden als andere claimregels op de Relying Party.| U kunt de id vinden onder de header **gebruikers kenmerken & claims**. Selecteer **Beeld** en bewerk alle andere gebruikerskenmerken.| N.v.t. |

### <a name="map-identity-provider-idp-settings"></a>IdP-instellingen (kaart-id-provider)

Configureer uw toepassingen zodat deze verwijzen naar Azure AD versus AD FS voor SSO. Hier richten we zich op SaaS-apps die het SAML-protocol gebruiken. Dit concept wordt echter ook uitgebreid naar aangepaste line-of-Business-Apps.

> [!NOTE]
> De configuratie waarden voor Azure AD volgen het patroon waar uw Azure-Tenant-id {Tenant-id} vervangt en de toepassings-id vervangt {Application-id}. U vindt deze informatie in de [Azure Portal](https://portal.azure.com/) onder **Azure Active Directory > eigenschappen**:

* Selecteer Directory-ID om uw Tenant-ID weer te geven.
* Selecteer de toepassings-ID om uw toepassings-ID weer te geven.

 Wijs op een hoog niveau de volgende belang rijke SaaS-apps configuratie-elementen toe aan Azure AD.

| Element| Configuratiewaarde |
| - | - |
| Verlener van ID-provider| https: \/ /STS.Windows.net/{Tenant-id}/ |
| Aanmeldings-URL van ID-provider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Afmeldings-URL van ID-provider| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Locatie van federatieve meta gegevens| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>SSO-instellingen voor SaaS-apps toewijzen

SaaS-apps moeten weten waar verificatie aanvragen moeten worden verzonden en hoe de ontvangen tokens moeten worden gevalideerd. In de volgende tabel worden de elementen beschreven voor het configureren van SSO-instellingen in de app en hun waarden of locaties binnen AD FS en Azure AD

| Configuratie-instelling| AD FS| Configureren in azure AD |
| - | - | - |
| **URL voor IdP-aanmelding** <p>Aanmeldings-URL van de IdP vanuit het perspectief van de app (waarnaar de gebruiker wordt omgeleid voor aanmelding).| De aanmeldings-URL van AD FS is de naam van het AD FS Federation-service gevolgd door '/adfs/ls/. ' <p>Bijvoorbeeld: `https://fs.contoso.com/adfs/ls/`| Vervang {Tenant-id} door uw Tenant-ID. <p> Voor apps die gebruikmaken van het SAML-P-protocol: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Voor apps die gebruikmaken van het WS-Federation-Protocol: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP-afmeldings-URL**<p>De afmeldings-URL van de IdP vanuit het perspectief van de app (waarnaar de gebruiker wordt omgeleid wanneer ze zich afmelden bij de app).| De afmeldings-URL is hetzelfde als de aanmeldings-URL of de URL waaraan ' wa = wsignout 1.0 ' is toegevoegd. Bijvoorbeeld: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Vervang {Tenant-id} door uw Tenant-ID.<p>Voor apps die gebruikmaken van het SAML-P-protocol:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Voor apps die gebruikmaken van het WS-Federation-Protocol: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Certificaat voor token-ondertekening**<p>De IdP maakt gebruik van de persoonlijke sleutel van het certificaat voor het ondertekenen van uitgegeven tokens. Er wordt gecontroleerd of het token afkomstig is van de dezelfde IdP die de app is geconfigureerd om te vertrouwen.| U vindt het AD FS-certificaat voor token-ondertekening in AD FS-beheer onder **Certificaten**.| Zoek het in de Azure Portal in de eigenschappen voor **eenmalige aanmelding** van de toepassing onder het **SAML-handtekening certificaat** voor koptekst. Daar kunt u het certificaat downloaden om het te uploaden naar de app.  <p>Als de toepassing meer dan één certificaat heeft, kunt u alle certificaten vinden in het XML-bestand met federatieve meta gegevens. |
| **ID/"verlener"**<p>De id van de IdP vanuit het perspectief van de app (ook wel de ' uitgevers-ID ' genoemd).<p>In het SAML-token wordt de waarde weer gegeven als het element van de verlener.| De id voor AD FS is doorgaans de Federation service-id in AD FS beheer onder **service > Federation service-eigenschappen bewerken**. Bijvoorbeeld: `http://fs.contoso.com/adfs/services/trust`| Vervang {Tenant-id} door uw Tenant-ID.<p>https: \/ /STS.Windows.net/{Tenant-id}/ |
| **IdP federatieve meta gegevens**<p>Locatie van de openbaar beschik bare federatieve meta gegevens van de IdP. (Sommige apps gebruiken federatiemetagegevens als alternatief voor het afzonderlijk door de beheerder configureren van URL's, id en tokenhandtekeningcertificaat.)| Zoek de URL van de AD FS federatieve meta gegevens in AD FS beheer onder **Service >-eind punten > meta gegevens > type: federatieve meta gegevens**. Bijvoorbeeld: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| De overeenkomstige waarde voor Azure AD volgt het patroon [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Vervang {Tenant domainname} door de naam van uw Tenant in de notatie ' contoso.onmicrosoft.com '.   <p>Zie voor meer informatie [Federatiemetagegevens](../azuread-dev/azure-ad-federation-metadata.md). |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>AD FS beveiligings beleid vertegenwoordigen in azure AD

Wanneer u uw app-verificatie naar Azure AD verplaatst, maakt u toewijzingen van bestaande beveiligings beleidsregels aan hun equivalente of alternatieve varianten die beschikbaar zijn in azure AD. Als u er zeker van wilt zijn dat deze toewijzingen kunnen worden uitgevoerd terwijl de door uw app-eigen aren vereiste beveiligings normen worden opgegeven, is de rest van de app-migratie aanzienlijk eenvoudiger.

Voor elk regel voorbeeld laten we zien hoe de regel eruitziet in AD FS, de AD FS regel taal equivalent code en hoe dit wordt toegewezen aan Azure AD.

### <a name="map-authorization-rules"></a>Toewijzings autorisatie regels

Hier volgen enkele voor beelden van verschillende typen autorisatie regels in AD FS en hoe u deze toewijst aan Azure AD.

#### <a name="example-1-permit-access-to-all-users"></a>Voor beeld 1: toegang tot alle gebruikers toestaan

Toegang tot alle gebruikers in AD FS toestaan:

  ![Scherm afbeelding toont het dialoog venster Eén Sign-On instellen met SAML.](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

Dit wordt op een van de volgende manieren toegewezen aan Azure AD:

1. Stel de **gebruikers toewijzing** in op **Nee**.

    ![Toegangs beheer beleid voor SaaS-apps bewerken ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > Voor het instellen van de **gebruikers toewijzing vereist** voor **Ja** moeten gebruikers zijn toegewezen aan de toepassing om toegang te krijgen. Wanneer dit is ingesteld op **Nee**, hebben alle gebruikers toegang. Deze schakel optie bepaalt niet wat gebruikers te zien krijgen in de ervaring **mijn apps** .

1. Wijs op het **tabblad gebruikers en groepen** de toepassing toe aan de groep **alle gebruikers** automatisch. U moet [dynamische groepen](../enterprise-users/groups-create-rule.md) in uw Azure AD-Tenant inschakelen voor de standaard groep **alle gebruikers** die beschikbaar moet zijn.

    ![Mijn SaaS-apps in azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>Voor beeld 2: een groep expliciet toestaan

Expliciete groeps autorisatie in AD FS:

  ![Scherm afbeelding toont het dialoog venster regel bewerken voor de claim regel domein Administrators toestaan.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

Deze regel toewijzen aan Azure AD:

1. Maak in [](https://portal.azure.com/)de Azure Portal [een gebruikers groep](../fundamentals/active-directory-groups-create-azure-portal.md) die overeenkomt met de groep gebruikers van AD FS.
1. App-machtigingen toewijzen aan de groep:

    ![Toewijzing toevoegen ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>Voor beeld 3: een specifieke gebruiker machtigen

Expliciete gebruikers autorisatie in AD FS:

  ![Scherm afbeelding toont het dialoog venster regel bewerken voor de regel een specifieke gebruiker claim toestaan met een binnenkomend claim type Primary S I D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Deze regel toewijzen aan Azure AD:

* Voeg in de [Azure Portal](https://portal.azure.com/)een gebruiker toe aan de app via het tabblad toewijzing toevoegen van de app, zoals hieronder wordt weer gegeven:

    ![Mijn SaaS-apps in azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Multi-factor Authentication-regels toewijzen

Een on-premises implementatie van [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) en AD FS nog steeds werkt na de migratie, omdat u federatieve met AD FS hebt. U kunt echter overwegen om te migreren naar de ingebouwde MFA-mogelijkheden van Azure die zijn gekoppeld aan de werk stromen voor voorwaardelijke toegang van Azure AD.

Hier volgen enkele voor beelden van typen MFA-regels in AD FS en hoe u deze kunt toewijzen aan Azure AD op basis van verschillende voor waarden.

MFA-regel instellingen in AD FS:

  ![In de Azure Portal scherm afbeelding worden voor waarden voor Azure A D weer gegeven.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Voor beeld 1: MFA afdwingen op basis van gebruikers/groepen

De selector gebruikers/groepen is een regel waarmee u MFA kunt afdwingen voor een per groep (groeps-SID) of per gebruiker (primaire SID). Afgezien van de toewijzingen voor gebruikers/groepen, zijn alle extra selectie vakjes in de gebruikers interface van de AD FS MFA-configuratie als aanvullende regels die worden geëvalueerd nadat de regel gebruikers/groepen wordt afgedwongen.

MFA-regels opgeven voor een gebruiker of een groep in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. Selecteer **Toewijzingen**. Voeg de gebruiker (s) of groep (en) toe waarvoor u MFA wilt afdwingen.
1. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:

    ‎![Scherm afbeelding toont het deel venster toekenning waar u toegang kunt verlenen.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Voor beeld 2: MFA afdwingen voor niet-geregistreerde apparaten

MFA-regels opgeven voor niet-geregistreerde apparaten in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. De **toewijzingen** instellen voor **alle gebruikers**.
1. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:

    ![Scherm afbeelding toont het deel venster toekenning waar u toegang kunt verlenen en andere beperkingen moet opgeven.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

Wanneer u de optie **voor meerdere besturings elementen zo** instelt dat **een van de geselecteerde besturings elementen vereist** is, betekent dit dat als een van de voor waarden die door het selectie vakje worden opgegeven door de gebruiker wordt voldaan aan de gebruiker, toegang tot uw app wordt verleend.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Voor beeld 3: MFA afdwingen op basis van locatie

MFA-regels opgeven op basis van de locatie van een gebruiker in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).
1. De **toewijzingen** instellen voor **alle gebruikers**.
1. [Benoemde locaties configureren in azure AD](../reports-monitoring/quickstart-configure-named-locations.md). Anders wordt de Federatie van binnen uw bedrijfs netwerk vertrouwd.
1. Configureer de **regels voor voor waarden** om de locaties op te geven waarvoor u MFA wilt afdwingen.

    ![Scherm afbeelding toont het deel venster locaties voor regels voor voor waarden.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:

    ![Toegangs beheer beleid toewijzen](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>Verzend kenmerken toewijzen als claim regel

Verzend kenmerken als claim regel in AD FS:

  ![Scherm afbeelding toont het dialoog venster regel bewerken voor het verzenden van kenmerken als claims.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

De regel toewijzen aan Azure AD:

1. Selecteer in de [Azure Portal](https://portal.azure.com/) **bedrijfs toepassingen** en vervolgens **eenmalige aanmelding** om de configuratie van op SAML gebaseerde aanmelding weer te geven:

    ![Scherm afbeelding toont de pagina voor eenmalige aanmelding voor uw bedrijfs toepassing.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. Selecteer **bewerken** (gemarkeerd) om de kenmerken te wijzigen:

    ![Dit is de pagina voor het bewerken van gebruikers kenmerken en claims](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>Ingebouwd toegangs beheer beleid toewijzen

Ingebouwd toegangs beheer beleid in AD FS 2016:

  ![Azure AD ingebouwde toegangs beheer](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Als u ingebouwde beleids regels in azure AD wilt implementeren, gebruikt u een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) en configureert u de toegangs elementen, of gebruikt u de ontwerp functie voor aangepaste beleids regels in AD FS 2016 voor het configureren van beleid voor toegangs beheer. De regel Editor bevat een uitgebreide lijst met vergunningen en behalve opties die u kunnen helpen bij het maken van allerlei soorten permutaties.

  ![Beleid voor toegangs beheer van Azure AD](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

In deze tabel hebben we een aantal nuttige vergunningen en uitzonde ringen weer gegeven, en hoe deze worden toegewezen aan Azure AD.

| Optie | Hoe kan ik de optie toestaan in azure AD configureren?| Hoe kunt u de optie behalve in azure AD configureren? |
| - | - | - |
| Van specifieke netwerk| Wordt toegewezen aan een [benoemde locatie](../reports-monitoring/quickstart-configure-named-locations.md) in azure AD| Gebruik de optie **uitsluiten** voor [vertrouwde locaties](../conditional-access/location-condition.md) |
| Van specifieke groepen| [Een toewijzing van een gebruiker of groep instellen](assign-user-or-group-access-portal.md)| Gebruik de optie **uitsluiten** in gebruikers en groepen |
| Van apparaten met specifieke vertrouwens niveau| Stel dit in op basis van het besturings element voor de status van de **apparaten** onder toewijzingen-> omstandigheden| Gebruik de optie **uitsluiten** onder Apparaatstatus en **alle apparaten** opnemen |
| Met specifieke claims in de aanvraag| Deze instelling kan niet worden gemigreerd| Deze instelling kan niet worden gemigreerd |

Hier volgt een voor beeld van het configureren van de uitsluitings optie voor vertrouwde locaties in de Azure Portal:

  ![Scherm opname van toewijzings beleid voor toegangs beheer](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Gebruikers overstappen van AD FS naar Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS groepen in azure AD synchroniseren

Wanneer u autorisatie regels toewijst, kunnen apps die verifiëren met AD FS Active Directory groepen gebruiken voor machtigingen. In dat geval gebruikt u [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) om deze groepen te synchroniseren met Azure AD voordat u de toepassingen migreert. Zorg ervoor dat u deze groepen en het lidmaatschap verifieert voordat u de migratie uitvoert, zodat u toegang tot dezelfde gebruikers kunt verlenen wanneer de toepassing wordt gemigreerd.

Zie [vereisten voor het gebruik van groeps kenmerken gesynchroniseerd vanuit Active Directory](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie.

### <a name="set-up-user-self-provisioning"></a>Zelf inrichting van de gebruiker instellen

Sommige SaaS-toepassingen bieden ondersteuning voor de mogelijkheid om gebruikers zelf in te richten wanneer ze zich voor het eerst aanmelden bij de toepassing. In azure AD verwijst app-inrichting naar het automatisch maken van gebruikers-id's en-rollen in de Cloud toepassingen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) waartoe gebruikers toegang moeten hebben. Gebruikers die zijn gemigreerd, hebben al een account in de SaaS-toepassing. Nieuwe gebruikers die zijn toegevoegd na de migratie, moeten worden ingericht. Test het [inrichten van SaaS-apps](../app-provisioning/user-provisioning.md) zodra de toepassing is gemigreerd.

### <a name="sync-external-users-in-azure-ad"></a>Externe gebruikers synchroniseren in azure AD

Uw bestaande externe gebruikers kunnen op de volgende twee manieren worden ingesteld in AD FS:

* **Externe gebruikers met een lokaal account binnen uw organisatie**: u kunt deze accounts blijven gebruiken op dezelfde manier als uw interne gebruikers accounts werken. Deze externe gebruikers accounts hebben een principe naam binnen uw organisatie, hoewel het e-mail adres van de account extern kan wijzen. Wanneer u de migratie uitvoert, kunt u profiteren van de voor delen die [Azure AD B2B](../external-identities/what-is-b2b.md) biedt door deze gebruikers te migreren om hun eigen bedrijfs identiteit te gebruiken wanneer een dergelijke identiteit beschikbaar is. Dit stroomlijnt het proces van aanmelding voor die gebruikers, aangezien ze vaak zijn aangemeld met hun eigen bedrijfs aanmelding. Het beheer van uw organisatie is ook eenvoudiger, omdat u geen accounts voor externe gebruikers hoeft te beheren.
* **Federatieve externe identiteiten**: als u momenteel een federeren hebt met een externe organisatie, hebt u een aantal stappen die u kunt nemen.
  * [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../external-identities/add-users-administrator.md). U kunt proactief B2B-samenwerkings uitnodigingen van de Azure AD-beheer Portal naar de partner organisatie voor afzonderlijke leden verzenden om de apps en assets te blijven gebruiken waarmee ze worden gebruikt.
  * [Maak een self-service-aanmeld werk stroom](../external-identities/self-service-portal.md) die een aanvraag voor afzonderlijke gebruikers in uw partner organisatie genereert met behulp van de API voor B2B-uitnodiging.

Ongeacht hoe uw bestaande externe gebruikers zijn geconfigureerd, zijn er waarschijnlijk machtigingen die zijn gekoppeld aan hun account, hetzij in groepslid maatschappen of specifieke machtigingen. Bepaal of deze machtigingen moeten worden gemigreerd of opgeschoond. Accounts in uw organisatie die een externe gebruiker vertegenwoordigen, moeten worden uitgeschakeld wanneer de gebruiker naar een externe identiteit is gemigreerd. Het migratie proces moet worden besproken bij uw zakelijke partners, omdat er mogelijk een onderbreking is in de mogelijkheid om verbinding te maken met uw resources.

## <a name="migrate-and-test-your-apps"></a>Uw apps migreren en testen

Volg het migratie proces dat in dit artikel wordt beschreven. Ga vervolgens naar de [Azure Portal](https://aad.portal.azure.com/) om te testen of de migratie is geslaagd.

Volg deze instructies:

1. Selecteer   >  **alle toepassingen** in bedrijfs toepassingen en zoek uw app in de lijst.
1. Selecteer   >  **gebruikers en groepen** beheren om ten minste één gebruiker of groep aan de app toe te wijzen.
1. Selecteer   >  **voorwaardelijke toegang** beheren. Controleer uw lijst met beleids regels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md).

Afhankelijk van hoe u uw app configureert, controleert u of SSO goed werkt.

| Verificatietype| Testen |
| :- | :- |
| OAuth/OpenID Connect Connect| Selecteer **bedrijfs toepassingen > machtigingen** en zorg ervoor dat u hebt ingestemd op de toepassing in de gebruikers instellingen voor uw app.|
| Op SAML gebaseerde SSO | U kunt de knop [SAML-instellingen testen](debug-saml-sso-issues.md) vinden onder **eenmalige aanmelding**. |
| Password-Based SSO |  Down load en installeer de [MyApps Secure Sign](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [in extension](../user-help/my-apps-portal-end-user-access.md). Deze uitbrei ding helpt u bij het starten van de Cloud-apps van uw organisatie waarvoor u een SSO-proces moet gebruiken. |
| Toepassingsproxy | Zorg ervoor dat uw connector wordt uitgevoerd en aan uw toepassing is toegewezen. Ga naar de [probleemoplossings gids voor toepassings proxy](application-proxy-troubleshoot.md) voor verdere ondersteuning. |

> [!NOTE]
> Cookies van de oude AD FS omgeving blijven op de computer van de gebruiker aanwezig. Deze cookies kunnen problemen met de migratie veroorzaken, omdat gebruikers kunnen worden omgeleid naar de oude AD FS aanmeldings omgeving versus de nieuwe Azure AD-aanmelding. Mogelijk moet u de cookies van de gebruikers browser hand matig Wissen of een script gebruiken. U kunt ook de System Center Configuration Manager of een soortgelijk platform gebruiken.

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten zijn opgetreden bij het testen van de gemigreerde toepassingen, kan het oplossen van problemen de eerste stap zijn voordat u terugkeert naar de bestaande AD FS relying Party's. Lees [hoe u op SAML gebaseerde eenmalige aanmelding op toepassingen in azure Active Directory kunt opsporen](debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Migratie terugdraaien

Als de migratie mislukt, wordt u aangeraden de bestaande relying party's op de AD FS servers te verlaten en de toegang tot de relying party's te verwijderen. Dit maakt snelle terugval mogelijk als dit nodig is tijdens de implementatie.

### <a name="employee-communication"></a>Communicatie met werk nemer

Hoewel het venster geplande onderbreking zelf een minimum kan zijn, moet u nog steeds plannen voor het proactief communiceren van deze tijds bestekn aan werk nemers tijdens het overschakelen van AD FS naar Azure AD. Zorg ervoor dat uw app-ervaring een feedback knop of pointers naar uw Help Desk heeft voor problemen.

Zodra de implementatie is voltooid, kunt u gebruikers op de hoogte stellen van de geslaagde implementatie en deze herinneren aan de stappen die ze moeten uitvoeren.

* Geef gebruikers de opdracht [mijn apps](https://myapps.microsoft.com) te gebruiken voor toegang tot alle gemigreerde toepassingen.
* Herinner gebruikers die ze nodig hebben om hun MFA-instellingen bij te werken.
* Als Self-Service wacht woord opnieuw instellen is geïmplementeerd, moeten gebruikers mogelijk hun verificatie methoden bijwerken of verifiëren. Zie [MFA](https://aka.ms/mfatemplates) en [SSPR](https://aka.ms/ssprtemplates) -communicatie sjablonen voor eind gebruikers.

### <a name="external-user-communication"></a>Communicatie van externe gebruikers

Deze groep gebruikers is doorgaans de meest kritieke impact op het geval van problemen. Dit geldt met name als uw beveiligings postuur een andere set regels voor voorwaardelijke toegang of risico profielen voor externe partners bepaalt. Zorg ervoor dat externe partners op de hoogte zijn van het migratie schema voor de Cloud en een periode hebben waarin ze worden aangemoedigd om deel te nemen aan een pilot implementatie waarmee alle stromen die uniek zijn voor externe samen werking, worden getest. Ten slotte moet u ervoor zorgen dat ze toegang krijgen tot de Help Desk wanneer er problemen zijn.

## <a name="next-steps"></a>Volgende stappen

* Lees  [toepassings verificatie migreren naar Azure AD](https://aka.ms/migrateapps/whitepaper).
* [Voorwaardelijke toegang](../conditional-access/overview.md) en [MFA](../authentication/concept-mfa-howitworks.md)instellen.
* Probeer een stapsgewijs code voorbeeld:[AD FS naar Azure AD-toepassings migratie Playbook voor ontwikkel aars](https://aka.ms/adfsplaybook).
