---
title: Toepassings verificatie van AD FS naar Azure Active Directory verplaatsen
description: Dit artikel is bedoeld om organisaties te helpen begrijpen hoe toepassingen naar Azure Active Directory kunnen worden verplaatst, met een focus op federatieve SaaS-toepassingen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/10/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: b8ad137389ac11a41872190122eae6405a126b5b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381236"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Toepassingsverificatie verplaatsen van Active Directory Federation Services naar Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) biedt een universeel identiteits platform dat uw gebruikers, partners en klanten een enkele identiteit biedt voor het openen van toepassingen en samen werken vanaf elk platform en apparaat. Azure AD heeft een [volledige reeks mogelijkheden voor identiteits beheer](../fundamentals/active-directory-whatis.md). Door de verificatie van uw toepassing (app) en autorisatie voor Azure AD in te stellen, kunt u profiteren van de voor delen van deze mogelijkheden.

> [!TIP]
> Dit artikel is geschreven voor een doel groep van ontwikkel aars. Project managers en beheerders die de overstap naar Azure AD van een toepassing plannen, moeten overwegen onze [migratie van verificatie van toepassingen naar Azure AD](migrate-application-authentication-to-azure-active-directory.md) -artikel te lezen.

## <a name="introduction"></a>Introductie

Als u een on-premises map hebt die gebruikers accounts bevat, hebt u waarschijnlijk veel toepassingen die gebruikers verifiëren. Elk van deze apps is geconfigureerd voor gebruikers om toegang te krijgen tot het gebruik van hun identiteit.


Gebruikers kunnen ook rechtstreeks worden geverifieerd met uw on-premises Active Directory. Active Directory Federation Services (AD FS) is een standaard op basis van on-premises identiteits service. Met AD FS kunt u de functionaliteit voor eenmalige aanmelding (SSO) gebruiken tussen vertrouwde zakelijke partners zonder dat gebruikers zich afzonderlijk moeten aanmelden bij elke toepassing. Dit wordt aangeduid als Federatie.

Veel organisaties hebben SaaS-apps (Software as a Service) of een aangepaste LOB-app (line-of-Business) die rechtstreeks naar AD FS worden gefederatied, naast Microsoft 365 en op Azure AD gebaseerde apps.

![Toepassingen die rechtstreeks on-premises zijn verbonden](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Om de beveiliging van toepassingen te verbeteren, is het doel om één set toegangs beheer en-beleid te hebben in uw on-premises en Cloud omgevingen**.

![Toepassingen die zijn verbonden via Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typen apps die moeten worden gemigreerd

Het migreren van alle toepassings verificatie naar Azure AD is optimaal, omdat u slechts één controle vlak hebt voor identiteits-en toegangs beheer.

Uw toepassingen kunnen gebruikmaken van moderne of verouderde protocollen voor verificatie. Overweeg eerst toepassingen te migreren die gebruikmaken van moderne verificatie protocollen (zoals SAML en open ID Connect). Deze apps kunnen opnieuw worden geconfigureerd voor verificatie met Azure AD via een ingebouwde connector in de app-galerie of door de toepassing te registreren in azure AD. Apps die gebruikmaken van oudere protocollen kunnen worden geïntegreerd met [toepassings proxy](./what-is-application-proxy.md).

Zie [welke typen toepassingen kan ik integreren met Azure AD](./what-is-application-management.md)? voor meer informatie.

U kunt het [rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD](./migrate-adfs-application-activity.md) als u [Azure AD Connect Health hebt ingeschakeld](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Het migratie proces

Tijdens het proces van het verplaatsen van uw app-verificatie naar Azure AD, kunt u uw apps en configuratie adequaat testen. We raden u aan om bestaande test omgevingen te blijven gebruiken voor migratie tests die worden verplaatst naar de productie omgeving. Als er op dit moment geen test omgeving beschikbaar is, kunt u er een instellen met behulp van [Azure app service](https://azure.microsoft.com/services/app-service/) of [Azure virtual machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB), afhankelijk van de architectuur van de toepassing.

U kunt ervoor kiezen om een afzonderlijke test van Azure AD-Tenant in te stellen om te gebruiken tijdens het ontwikkelen van uw app-configuraties.

Uw migratie proces kan er als volgt uitzien:

**Fase 1: huidige status: productie-app verifiëren met AD FS**

![Migratie fase 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Fase 2: optioneel: test instantie van de app die de Azure-Tenant moet testen**

Werk de configuratie bij om uw test exemplaar van de app te laten wijzen naar een Azure AD-Tenant en breng de gewenste wijzigingen aan. De app kan worden getest met gebruikers in de Azure AD-Tenant testen. Tijdens het ontwikkelings proces kunt u hulpprogram ma's zoals [Fiddler](https://www.telerik.com/fiddler) gebruiken om aanvragen en antwoorden te vergelijken en te controleren.

Als het instellen van een afzonderlijke test Tenant niet haalbaar is, slaat u deze fase over en maakt u een test exemplaar van een app en wijst u deze toe aan uw productie-Azure AD-Tenant, zoals beschreven in stap 3 hieronder.

![Migratie fase 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fase 3: de app testen die naar productie-Azure-Tenant verwijst**

Werk de configuratie bij om uw test exemplaar van de app te laten verwijzen naar uw productie-exemplaar van Azure. U kunt nu testen met gebruikers in uw productie-exemplaar. Lees indien nodig het gedeelte over overgang van gebruikers door de sectie van dit artikel.

![Migratie fase 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fase 4: productie-app die verwijst naar productie AD-Tenant**

Werk de configuratie van uw productie toepassing bij zodat deze naar uw productie-Azure-Tenant verwijst.

![Migratie fase 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Apps die met AD FS verifiëren, kunnen Active Directory groepen gebruiken voor machtigingen. Gebruik [Azure AD Connect synchronisatie](../hybrid/how-to-connect-sync-whatis.md) om identiteits gegevens te synchroniseren tussen uw on-premises omgeving en Azure AD voordat u de migratie start. Controleer deze groepen en het lidmaatschap vóór de migratie zodat u toegang tot dezelfde gebruikers kunt verlenen wanneer de toepassing wordt gemigreerd.

### <a name="line-of-business-lob-apps"></a>LOB-apps (line-of-Business)

LOB-apps worden intern ontwikkeld door uw organisatie of beschikbaar als een standaard verpakt product dat is geïnstalleerd in uw Data Center. Voor beelden zijn apps die zijn gebouwd op Windows Identity Foundation en share point-apps (niet share point online).

LOB-apps die gebruikmaken van OAuth 2,0, OpenID Connect Connect of WS-Federation, kunnen worden geïntegreerd met Azure AD als [app-registraties](../develop/quickstart-register-app.md). Integreer aangepaste apps die gebruikmaken van SAML 2,0 of WS-Federation als [niet-galerie toepassingen](./add-application-portal.md) op de pagina bedrijfs toepassingen in de [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Eenmalige aanmelding op basis van SAML

Apps die gebruikmaken van SAML 2,0 voor authenticatie kunnen worden geconfigureerd voor [eenmalige aanmelding op basis van SAML](./what-is-single-sign-on.md) (op SAML gebaseerde SSO). Met [SSO op basis van SAML](./what-is-single-sign-on.md)kunt u gebruikers toewijzen aan specifieke toepassings rollen op basis van regels die u in uw SAML-claims definieert.

Zie [Eenmalige aanmelding op basis van SAML configureren](./view-applications-portal.md) als u een SaaS-toepassing wilt configureren voor eenmalige aanmelding op basis van SAML.

![SSO SAML-gebruikers afbeeldingen ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Veel SaaS-toepassingen hebben een [toepassingsspecifieke zelf studie](../saas-apps/tutorial-list.md) waarmee u de configuratie voor eenmalige aanmelding op basis van SAML kunt door lopen.

![app-zelf studie](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Sommige apps kunnen eenvoudig worden gemigreerd. Voor apps met complexere vereisten, zoals aangepaste claims, is mogelijk aanvullende configuratie in Azure AD en/of Azure AD Connect vereist. Zie [claims mapping in azure Active Directory](../develop/active-directory-claims-mapping.md)voor meer informatie over de ondersteunde claim toewijzingen.

Houd bij het toewijzen van kenmerken de volgende beperkingen in acht:

* Niet alle kenmerken die kunnen worden uitgegeven in AD FS worden weer gegeven in azure AD als kenmerken voor het verzenden naar SAML-tokens, zelfs als deze kenmerken worden gesynchroniseerd. Wanneer u het kenmerk bewerkt, ziet u in de vervolg keuzelijst met waarden de verschillende kenmerken die beschikbaar zijn in azure AD. Controleer [Azure AD Connect synchronisatie](../hybrid/how-to-connect-sync-whatis.md) configuratie om ervoor te zorgen dat een vereist kenmerk, bijvoorbeeld sAMAccountName, wordt gesynchroniseerd met Azure AD. U kunt de extensie kenmerken gebruiken om een claim te verzenden die geen deel uitmaakt van het standaard gebruikers schema in azure AD.

* In de meest voorkomende scenario's zijn alleen de NameID-claim en andere algemene gebruikers-id-claims vereist voor een app. Als u wilt bepalen of er aanvullende claims zijn vereist, kunt u onderzoeken welke claims u uitgeeft van AD FS.

* Niet alle claims kunnen problemen veroorzaken omdat sommige claims worden beveiligd in azure AD.

* De mogelijkheid om versleutelde SAML-tokens te gebruiken, is nu beschikbaar als preview-versie. Zie [How to: claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>SaaS-apps (Software as a Service)

Als uw gebruiker zich aanmeldt bij SaaS-apps zoals Sales Force, ServiceNow of workday, en is geïntegreerd met AD FS, gebruikt u federatieve aanmelding voor SaaS-apps.

De meeste SaaS-toepassingen kunnen al worden geconfigureerd in azure AD. Micro soft heeft veel vooraf geconfigureerde verbindingen met SaaS-apps in de  [Azure AD-App-galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), waardoor uw overgang eenvoudiger wordt. SAML 2,0-toepassingen kunnen worden geïntegreerd met Azure AD via de Azure AD-App-galerie of als [niet-galerie toepassingen](./add-application-portal.md).

Apps die gebruikmaken van OAuth 2.0 of OpenID Connect, kunnen op gelijksoortige wijze worden geïntegreerd met Azure AD als [app-registraties](../develop/quickstart-register-app.md). Apps die gebruikmaken van verouderde protocollen kunnen [azure AD-toepassingsproxy](./application-proxy.md) gebruiken om te verifiëren met Azure AD.

Voor problemen met het voorbereiden van uw SaaS-apps kunt u contact opnemen met de [ondersteunings alias voor SaaS-toepassings integratie](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**SAML-handtekening certificaten voor SSO**: handtekening certificaten vormen een belang rijk onderdeel van elke SSO-implementatie. Azure AD maakt de handtekening certificaten om op SAML gebaseerde federatieve SSO te maken voor uw SaaS-toepassingen. Als u een van beide galerie-of niet-galerie toepassingen hebt toegevoegd, configureert u de toegevoegde toepassing met behulp van de optie Federated SSO. Zie [certificaten beheren voor federatieve eenmalige aanmelding in azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Apps en configuraties die vandaag kunnen worden verplaatst

Apps die u vandaag eenvoudig kunt verplaatsen, zijn SAML 2,0-apps die gebruikmaken van de standaardset configuratie-elementen en claims:

* User Principal Name

* E-mailadres

* Voornaam

* Achternaam

* Alternatief kenmerk als SAML **NameID**, met inbegrip van het Azure AD e-mailkenmerk, e-mailvoorvoegsel, werknemer-id, extensiekenmerken 1-15, of on-premises **SamAccountName**-kenmerk. Zie voor meer informatie [De NameIdentifier-claim bewerken](../develop/active-directory-saml-claims-customization.md).

* Aangepaste claims.

De volgende aanvullende configuratie stappen zijn vereist om te migreren naar Azure AD:

* Aangepaste autorisatie-of Multi-Factor Authentication regels (MFA) in AD FS. U kunt deze configureren met behulp van de functie [voorwaardelijke toegang van Azure AD](../conditional-access/overview.md) .

* Apps met meerdere antwoord-URL-eind punten. U kunt deze configureren in azure AD met behulp van Power shell of in de Azure Portal-interface.

* WS-Federation-apps, zoals SharePoint-apps waarvoor versie 1.1-SAML-tokens vereist zijn. U kunt ze hand matig configureren met Power shell. U kunt ook een vooraf geïntegreerde algemene sjabloon toevoegen voor share point-en SAML 1,1-toepassingen uit de galerie. Het SAML 2,0-protocol wordt ondersteund.

* Regels voor de uitgifte van complexe claims. Zie voor informatie over ondersteunde claim toewijzingen:
   *  [Claimtoewijzing in Azure Active Directory](../develop/active-directory-claims-mapping.md)
   * [Claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen in Azure Active Directory](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Apps en configuratie die momenteel niet worden ondersteund in Azure AD

Apps waarvoor de volgende mogelijkheden zijn vereist, kunnen momenteel niet worden gemigreerd.

**Protocol mogelijkheden**

* Ondersteuning voor het ActAs-patroon van WS-Trust

* SAML-artefactomzetting

* Handtekening verificatie van ondertekende SAML-aanvragen geeft aan dat ondertekende aanvragen worden geaccepteerd, maar de hand tekening niet is geverifieerd.
Omdat Azure AD alleen het token terugstuurt naar de eind punten die in de toepassing zijn geconfigureerd, is handtekening verificatie waarschijnlijk niet in de meeste gevallen vereist.

**Claims in token mogelijkheden**

* Claims uit kenmerk archieven, behalve de Azure AD-Directory, tenzij deze gegevens worden gesynchroniseerd met Azure AD. Zie [Azure AD Synchronization API overview](/graph/api/resources/synchronization-overview?view=graph-rest-beta)(Engelstalig) voor meer informatie.

* Uitgifte van kenmerken van meerdere waarden in de Directory. Zo kan er op dit moment geen claim met meerdere waarden worden uitgegeven voor proxy adressen.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>App-instellingen van AD FS toewijzen aan Azure AD

Voor de migratie begint, moet eerst worden vastgesteld hoe de app on-premises moet worden geconfigureerd en hoe de configuratie aan Azure AD wordt toegewezen. AD FS en Azure AD werken op soort gelijke wijze, dus de concepten van het configureren van vertrouwens relaties, aanmeldings-en afmeldings-Url's en id's zijn in beide gevallen van toepassing. Documenteer de AD FS configuratie-instellingen van uw toepassingen, zodat u ze eenvoudig kunt configureren in azure AD.

### <a name="map-app-configuration-settings"></a>Configuratie-instellingen van kaart-app

De volgende tabel beschrijft een aantal van de meest voorkomende toewijzing van instellingen tussen een AD FS Relying Party-vertrouwens relatie met Azure AD Enter prise-toepassing:

* AD FS: Zoek de instelling in de vertrouwens relatie van AD FS Relying Party voor de app. Klik met de rechter muisknop op de Relying Party en selecteer Eigenschappen.

* Azure AD: de instelling wordt geconfigureerd in [Azure Portal](https://portal.azure.com/) in de eigenschappen voor eenmalige aanmelding van elke toepassing.

| Configuratie-instelling| AD FS| Configureren in azure AD| SAML-token |
| - | - | - | - |
| **Aanmeldings-URL van app** <p>De URL waarmee de gebruiker zich moet aanmelden bij de app in een door SP (service provider) geïnitieerde SAML-stroom.| N.v.t.| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| N.v.t. |
| **Antwoord-URL van app** <p>De URL van de app van het perspectief van de identiteits provider (IdP). De IdP verzendt de gebruiker en het token hier nadat de gebruiker zich heeft aangemeld bij de IdP.  Dit wordt ook wel het **consument eindpunt van SAML-bewering** genoemd.| Selecteer het tabblad **eind punten**| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| Doel element in het SAML-token. Voorbeeldwaarde: `https://contoso.my.salesforce.com` |
| **App-URL voor afmelden** <p>Dit is de URL waarnaar de aanvragen ' afmeldings opschonen ' worden verzonden wanneer een gebruiker zich afmeldt vanuit een app. De IdP verzendt de aanvraag voor het afmelden van de gebruiker van alle andere apps ook.| Selecteer het tabblad **eind punten**| Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| N.v.t. |
| **App-id** <p>Dit is de app-id uit het perspectief van de IdP. De waarde van de aanmeldings-URL wordt vaak gebruikt voor de id (maar niet altijd).  Soms roept de app de entiteit-ID aan.| Het tabblad **id's** selecteren|Basis-SAML-configuratie openen vanuit op SAML gebaseerde aanmelding| Wordt toegewezen aan het element **doel groep** in het SAML-token. |
| **Federatieve metagegevens van app** <p>Dit is de locatie van de federatieve meta gegevens van de app. De IdP gebruikt deze om bepaalde configuratie-instellingen automatisch bij te werken, zoals eindpunten of versleutelingscertificaten.| Selecteer het tabblad **controle**| N.v.t. Azure AD biedt geen ondersteuning voor het rechtstreeks gebruiken van Application Federation-meta gegevens. U kunt de federatieve meta gegevens hand matig importeren.| N.v.t. |
| **Gebruikers-id/naam-ID** <p>Kenmerk dat wordt gebruikt om voor de app op unieke wijze de gebruikers-id aan te geven vanuit Azure AD of AD FS.  Dit kenmerk is doorgaans de UPN of het e-mail adres van de gebruiker.| Claim regels. In de meeste gevallen geeft de claim regel een claim uit met een type dat eindigt op de NameIdentifier.| U kunt de id vinden onder de header **gebruikers kenmerken en claims**. De UPN wordt standaard gebruikt| Wordt toegewezen aan het **NameID** -element in het SAML-token. |
| **Andere claims** <p>Voor beelden van andere claim gegevens die vaak van de IdP naar de app worden verzonden, zijn voor naam, achternaam, e-mail adres en groepslid maatschap.| In AD FS kunt u deze gegevens vinden als andere claimregels op de Relying Party.| U kunt de id vinden onder de header **gebruikers kenmerken & claims**. Selecteer **Beeld** en bewerk alle andere gebruikerskenmerken.| N.v.t. |


### <a name="map-identity-provider-idp-settings"></a>IdP-instellingen (kaart-id-provider)

Configureer uw toepassingen zodat deze verwijzen naar Azure AD versus AD FS voor SSO. Hier richten we zich op SaaS-apps die het SAML-protocol gebruiken. Dit concept wordt echter ook uitgebreid naar aangepaste LOB-apps.

> [!NOTE]
> De configuratie waarden voor Azure AD volgen het patroon waar uw Azure-Tenant-id {Tenant-id} vervangt en de toepassings-id vervangt {Application-id}. U vindt deze informatie in de [Azure Portal](https://portal.azure.com/) onder Azure Active Directory > eigenschappen:

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

Voor elk regel type en de voor beelden wordt hier voorgesteld hoe de regel eruitziet in AD FS, de AD FS regel taal equivalent code en hoe deze kaart in azure AD.

### <a name="map-authorization-rules"></a>Toewijzings autorisatie regels

Hier volgen enkele voor beelden van typen autorisatie regels in AD FS en hoe u deze kunt toewijzen aan Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Voor beeld 1: toegang tot alle gebruikers toestaan

Toegang tot alle gebruikers toestaan, zoals in AD FS:

![Scherm afbeelding toont het dialoog venster Eén Sign-On instellen met SAML.](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Dit wordt op een van de volgende manieren toegewezen aan Azure AD:

[In Azure Portal](https://portal.azure.com/):
* Optie 1: gebruikers toewijzing instellen is vereist voor Nee ![Toegangs beheer beleid voor SaaS-apps bewerken ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Houd er rekening mee dat bij het instellen van de gebruikers toewijzing vereiste overschakelen op Ja vereist is dat gebruikers aan de toepassing worden toegewezen om toegang te krijgen. Wanneer dit is ingesteld op Nee, hebben alle gebruikers toegang. Deze schakel optie bepaalt niet wat voor gebruikers wordt weer gegeven in de ervaring mijn apps.


* Optie 2: wijs op het tabblad gebruikers en groepen de toepassing toe aan de automatische groep "alle gebruikers". <p>
U moet [dynamische groepen](../enterprise-users/groups-create-rule.md) in uw Azure AD-Tenant inschakelen voor de standaard groep alle gebruikers die beschikbaar moet zijn.

   ![Mijn SaaS-apps in azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Voor beeld 2: een groep expliciet toestaan

Expliciete groeps autorisatie in AD FS:


![Scherm afbeelding toont het dialoog venster regel bewerken voor de claim regel domein Administrators toestaan.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Dit is hoe de regel wordt toegewezen aan Azure AD:

In de [Azure Portal](https://portal.azure.com/)maakt u eerst [een gebruikers groep](../fundamentals/active-directory-groups-create-azure-portal.md) die overeenkomt met de groep gebruikers uit AD FS en wijst u vervolgens app-machtigingen toe aan die groep:

![Toewijzing toevoegen ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Voor beeld 3: een specifieke gebruiker machtigen

Expliciete gebruikers autorisatie in AD FS:

![Scherm afbeelding toont het dialoog venster regel bewerken voor de regel een specifieke gebruiker claim toestaan met een binnenkomend claim type Primary S I D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Dit is hoe de regel wordt toegewezen aan Azure AD:

Voeg in de [Azure Portal](https://portal.azure.com/)een gebruiker toe aan de app via het tabblad toewijzing toevoegen van de app, zoals hieronder wordt weer gegeven:

![Mijn SaaS-apps in azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication regels toewijzen

Een on-premises implementatie van [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) en AD FS blijft na de migratie gewoon werken, omdat u federatieve met AD FS. U kunt echter overwegen om te migreren naar de ingebouwde MFA-mogelijkheden van Azure die zijn gekoppeld aan de werk stromen voor voorwaardelijke toegang van Azure AD.

Hier volgen enkele voor beelden van typen MFA-regels in AD FS en hoe u deze kunt toewijzen aan Azure AD op basis van verschillende voor waarden:

MFA-regel instellingen in AD FS:

![In de Azure Portal scherm afbeelding worden voor waarden voor Azure A D weer gegeven.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Voor beeld 1: MFA afdwingen op basis van gebruikers/groepen

De selector gebruiker/groep is een regel waarmee u MFA afdwingt voor een per groep (groeps-SID) of per gebruiker (primaire SID). Afgezien van de toewijzingen van gebruikers/groepen, zijn alle extra selectie vakjes in de gebruikers interface van de AD FS MFA-configuratie als aanvullende regels die worden geëvalueerd nadat de regel van de gebruiker of groep is afgedwongen.


MFA-regels opgeven voor een gebruiker of een groep in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Selecteer **Toewijzingen**. Voeg de gebruiker (s) of groep (en) toe waarvoor u MFA wilt afdwingen.

3. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:

![Scherm afbeelding toont het deel venster toekenning waar u toegang kunt verlenen.](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Voor beeld 2: MFA afdwingen voor niet-geregistreerde apparaten

MFA-regels opgeven voor niet-geregistreerde apparaten in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. De **toewijzingen** instellen voor **alle gebruikers**.

3. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:

![Scherm afbeelding toont het deel venster toekenning waar u toegang kunt verlenen en andere beperkingen moet opgeven.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Wanneer u de optie voor meerdere besturings elementen zo instelt dat een van de geselecteerde besturings elementen vereist is, betekent dit dat als een van de voor waarden die door het selectie vakje worden opgegeven door de gebruiker is voldaan aan de gebruikers, toegang tot uw app wordt verleend.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Voor beeld 3: MFA afdwingen op basis van locatie

MFA-regels opgeven op basis van de locatie van een gebruiker in azure AD:

1. Maak een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. De **toewijzingen** instellen voor **alle gebruikers**.

1. [Benoemde locaties in azure AD configureren](../reports-monitoring/quickstart-configure-named-locations.md) anders Federatie binnen uw bedrijfs netwerk wordt vertrouwd.

1. Configureer de **regels voor voor waarden** om de locaties op te geven waarvoor u MFA wilt afdwingen.

![Scherm afbeelding toont het deel venster locaties voor regels voor voor waarden.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Configureer de opties voor **toegangs beheer** , zoals hieronder wordt weer gegeven:


![Toegangs beheer beleid toewijzen](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Verzend kenmerken toewijzen als claim regel

Hier volgt een voor beeld van hoe kenmerken worden toegewezen in AD FS:

![Scherm afbeelding toont het dialoog venster regel bewerken voor het verzenden van kenmerken als claims.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Dit is hoe de regel wordt toegewezen aan Azure AD:

Selecteer in het [Azure Portal](https://portal.azure.com/) **bedrijfs toepassingen**, **eenmalige aanmelding** en voeg de SAML- **token kenmerken** toe zoals hieronder wordt weer gegeven:

![Scherm afbeelding toont de pagina voor eenmalige aanmelding voor uw bedrijfs toepassing.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Ingebouwd toegangs beheer beleid toewijzen

AD FS 2016 heeft verschillende ingebouwde beleids regels voor toegangs beheer waaruit u kunt kiezen:

![Azure AD ingebouwde toegangs beheer](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


Als u ingebouwde beleids regels wilt implementeren in azure AD, kunt u een [Nieuw beleid voor voorwaardelijke toegang](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) gebruiken en de toegangs elementen configureren, of u kunt de ontwerp functie voor aangepaste beleids regels in AD FS 2016 gebruiken voor het configureren van beleid voor toegangs beheer. De regel Editor bevat een uitgebreide lijst met vergunningen en behalve opties die u kunnen helpen bij het maken van allerlei soorten permutaties.

![Beleid voor toegangs beheer van Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



In deze tabel hebben we een aantal nuttige vergunningen en uitzonde ringen weer gegeven, en hoe deze worden toegewezen aan Azure AD.


| Optie | Hoe kan ik de optie toestaan in azure AD configureren?| Hoe kunt u de optie behalve in azure AD configureren? |
| - | - | - |
| Van specifieke netwerk| Wordt toegewezen aan een [benoemde locatie](../reports-monitoring/quickstart-configure-named-locations.md) in azure AD| Gebruik de optie **uitsluiten** voor [vertrouwde locaties](../conditional-access/location-condition.md) |
| Van specifieke groepen| [Een toewijzing van een gebruiker of groep instellen](./assign-user-or-group-access-portal.md)| Gebruik de optie **uitsluiten** in gebruikers en groepen |
| Van apparaten met specifieke vertrouwens niveau| Stel dit in op basis van het besturings element ' Apparaatstatus ' onder toewijzingen-> voor waarden| Gebruik de optie **uitsluiten** onder Apparaatstatus en **alle apparaten** opnemen |
| Met specifieke claims in de aanvraag| Deze instelling kan niet worden gemigreerd| Deze instelling kan niet worden gemigreerd |


Een voor beeld van het configureren van de exclude-optie voor vertrouwde locaties in de Azure Portal:

![Scherm opname van toewijzings beleid voor toegangs beheer](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Gebruikers overstappen van AD FS naar Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>AD FS groepen in azure AD synchroniseren

Wanneer u autorisatie regels toewijst, kunnen apps die verifiëren met AD FS Active Directory groepen gebruiken voor machtigingen. In dat geval gebruikt u [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) om deze groepen te synchroniseren met Azure AD voordat u de toepassingen migreert. Zorg ervoor dat u deze groepen en het lidmaatschap verifieert voordat u de migratie uitvoert, zodat u toegang tot dezelfde gebruikers kunt verlenen wanneer de toepassing wordt gemigreerd.

Zie [vereisten voor het gebruik van groeps kenmerken gesynchroniseerd vanuit Active Directory](../hybrid/how-to-connect-fed-group-claims.md)voor meer informatie.

### <a name="setup-user-self-provisioning"></a>Zelf inrichting van gebruiker instellen

Sommige SaaS-toepassingen bieden ondersteuning voor de mogelijkheid om gebruikers zelf in te richten wanneer ze zich voor het eerst aanmelden bij de toepassing. In Azure Active Directory (Azure AD) verwijst de term app-inrichting naar het automatisch maken van gebruikersidentiteiten en -rollen in de cloud-toepassingen ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) waartoe gebruikers toegang nodig hebben. Gebruikers die zijn gemigreerd, hebben al een account in de SaaS-toepassing. Nieuwe gebruikers die zijn toegevoegd na de migratie, moeten worden ingericht. Test het [inrichten van SaaS-apps](../app-provisioning/user-provisioning.md) zodra de toepassing is gemigreerd.

### <a name="sync-external-users-in-azure-ad"></a>Externe gebruikers synchroniseren in azure AD

Uw bestaande externe gebruikers kunnen worden ingesteld op twee hoofd manieren binnen AD FS:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externe gebruikers met een lokaal account binnen uw organisatie

U kunt deze accounts blijven gebruiken op dezelfde manier als uw interne gebruikers accounts werken. Deze externe gebruikers accounts hebben een principe naam binnen uw organisatie, hoewel het e-mail adres van de account extern kan wijzen. Wanneer u de migratie uitvoert, kunt u profiteren van de voor delen die [Azure AD B2B](../external-identities/what-is-b2b.md) biedt door deze gebruikers te migreren om hun eigen bedrijfs identiteit te gebruiken wanneer een dergelijke identiteit beschikbaar is. Dit stroomlijnt het aanmeldings proces voor die gebruikers, aangezien ze vaak zijn aangemeld met hun eigen bedrijfs aanmelding. Het beheer van uw organisatie wordt ook vereenvoudigd, omdat u niet langer accounts voor externe gebruikers hoeft te beheren.

#### <a name="federated-external-identities"></a>Federatieve externe identiteiten

Als u momenteel federeren met een externe organisatie, hebt u de volgende stappen:

* [Azure Active Directory B2B-samenwerkings gebruikers toevoegen in de Azure Portal](../external-identities/add-users-administrator.md). U kunt B2B-samen werking proactief verzenden van de Azure AD-beheer Portal naar de partner organisatie voor afzonderlijke leden om door te gaan met het gebruik van de apps en assets waarmee ze worden gebruikt.

* [Maak een self-service-aanmeld werk stroom](../external-identities/self-service-portal.md) die een aanvraag voor afzonderlijke gebruikers in uw partner organisatie genereert met behulp van de API voor B2B-uitnodiging.

Ongeacht hoe uw bestaande externe gebruikers zijn geconfigureerd, zijn er waarschijnlijk machtigingen die zijn gekoppeld aan hun account, hetzij in groepslid maatschappen of specifieke machtigingen. Bepaal of deze machtigingen moeten worden gemigreerd of opgeschoond. Accounts in uw organisatie die een externe gebruiker vertegenwoordigen, moeten worden uitgeschakeld wanneer de gebruiker naar een externe identiteit is gemigreerd. Het migratie proces moet worden besproken bij uw zakelijke partners, omdat er mogelijk een onderbreking is in de mogelijkheid om verbinding te maken met uw resources.

## <a name="migrate-and-test-your-apps"></a>Uw apps migreren en testen

Volg het migratie proces dat in dit artikel wordt beschreven.

Ga vervolgens naar de [Azure Portal](https://aad.portal.azure.com/) om te testen of de migratie is geslaagd. Volg de onderstaande instructies:
1. Selecteer   >  **alle toepassingen** in bedrijfs toepassingen en zoek uw app in de lijst.

1. Selecteer   >  **gebruikers en groepen** beheren om ten minste één gebruiker of groep aan de app toe te wijzen.

1. Selecteer   >  **voorwaardelijke toegang** beheren. Controleer uw lijst met beleids regels en zorg ervoor dat u de toegang tot de toepassing niet blokkeert met een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md).

Afhankelijk van hoe u uw app configureert, controleert u of SSO goed werkt.

| Verificatietype| Testen |
| - | - |
| OAuth/OpenID Connect Connect| Selecteer **bedrijfs toepassingen > machtigingen** en zorg ervoor dat u hebt ingestemd op de toepassing die in uw organisatie moet worden gebruikt in de gebruikers instellingen voor uw app.
‎ |
| Op SAML gebaseerde SSO| U kunt de knop [SAML-instellingen testen](./debug-saml-sso-issues.md) vinden onder **eenmalige aanmelding**.
‎ |
| Password-Based SSO| Down load en installeer de [MyApps Secure Sign](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [in extension](../user-help/my-apps-portal-end-user-access.md). Deze uitbrei ding helpt u bij het starten van de Cloud-apps van uw organisatie waarvoor u een SSO-proces moet gebruiken.
‎ |
| Toepassingsproxy| Zorg ervoor dat uw connector wordt uitgevoerd en aan uw toepassing is toegewezen. Ga naar de [probleemoplossings gids voor toepassings proxy](./application-proxy-troubleshoot.md) voor verdere ondersteuning.
‎ |

> [!NOTE]
> Cookies van de oude AD FS omgeving blijven behouden op de computers van de gebruiker. Deze cookies kunnen problemen met de migratie veroorzaken omdat gebruikers kunnen worden omgeleid naar de oude AD FS aanmeldings omgeving en de nieuwe Azure AD-aanmelding. Mogelijk moet u de cookies van de gebruikers browser hand matig Wissen of een script gebruiken. U kunt ook de System Center Configuration Manager of een soortgelijk platform gebruiken.

### <a name="troubleshoot"></a>Problemen oplossen

Als er fouten zijn opgetreden bij het testen van de gemigreerde toepassingen, kan het oplossen van problemen de eerste stap zijn voordat u terugkeert naar de bestaande AD FS relying Party's. Lees [hoe u op SAML gebaseerde eenmalige aanmelding op toepassingen in azure Active Directory kunt opsporen](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Migratie terugdraaien

Als de migratie mislukt, wordt u aangeraden de bestaande relying party's op de AD FS servers te verlaten en de toegang tot de relying party's te verwijderen. Dit biedt een snelle terugval als dat nodig is tijdens de implementatie.

### <a name="end-user-communication"></a>Communicatie door eind gebruikers

Hoewel het venster voor de geplande onderbreking zelf mini maal kan zijn, moet u nog steeds plannen voor het proactief communiceren van deze tijds bestekn aan werk nemers, terwijl u de cut van AD FS naar Azure AD brengt. Zorg ervoor dat uw app-ervaring een feedback knop of pointers naar uw Help Desk heeft voor problemen.

Zodra de implementatie is voltooid, kunt u communicatie verzenden die de gebruikers van de geslaagde implementatie informeert en ze eraan herinneren dat er nieuwe stappen moeten worden uitgevoerd.

* Geef gebruikers de opdracht [mijn apps](https://myapps.microsoft.com) te gebruiken voor toegang tot alle gemigreerde toepassingen.

* Herinner gebruikers die ze nodig hebben om hun MFA-instellingen bij te werken.

* Als Self-Service wacht woord opnieuw instellen is geïmplementeerd, moeten gebruikers mogelijk hun verificatie methoden bijwerken of verifiëren. Zie [MFA](https://aka.ms/mfatemplates) en [SSPR](https://aka.ms/ssprtemplates) -communicatie sjablonen voor eind gebruikers.

Communicatie met externe gebruikers: deze groep gebruikers is doorgaans de meest kritieke impact in het geval van problemen. Dit geldt met name als uw beveiligings postuur een andere set regels voor voorwaardelijke toegang of risico profielen voor externe partners bepaalt. Zorg ervoor dat externe partners op de hoogte zijn van het migratie schema voor de Cloud en een periode hebben waarin ze worden aangemoedigd om deel te nemen aan een pilot implementatie waarmee alle stromen die uniek zijn voor externe samen werking, worden getest. Ten slotte moet u ervoor zorgen dat ze toegang hebben tot de Help Desk in het geval van problemen.

## <a name="next-steps"></a>Volgende stappen

[Migratie van toepassings verificatie naar Azure AD](https://aka.ms/migrateapps/whitepaper) lezen<p>
[Voorwaardelijke toegang](../conditional-access/overview.md) en [MFA](../authentication/concept-mfa-howitworks.md) instellen

Probeer een stapsgewijs code voorbeeld:[AD FS naar Azure AD-toepassings migratie Playbook voor ontwikkel aars](https://aka.ms/adfsplaybook)
