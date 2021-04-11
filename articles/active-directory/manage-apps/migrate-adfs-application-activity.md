---
title: Gebruik het activiteiten rapport om AD FS-apps te verplaatsen naar Azure Active Directory | Microsoft Docs '
description: Met het rapport activiteit van de Active Directory Federation Services-toepassing (AD FS) kunt u snel toepassingen migreren van AD FS naar Azure Active Directory (Azure AD). Dit hulp programma voor migratie van AD FS identificeert compatibiliteit met Azure AD en biedt migratie richtlijnen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88a4d0f108d4e3c27ce17aaa83aafca38063c9ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589460"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Het rapport AD FS toepassings activiteit gebruiken om toepassingen te migreren naar Azure AD

Veel organisaties gebruiken Active Directory Federation Services (AD FS) voor het bieden van eenmalige aanmelding bij Cloud toepassingen. Er zijn belang rijke voor delen om uw AD FS-toepassingen naar Azure AD te verplaatsen voor verificatie, met name op het gebied van kosten beheer, risico beheer, productiviteit, naleving en governance. Maar u begrijpt welke toepassingen compatibel zijn met Azure AD en het identificeren van specifieke migratie stappen kan tijdrovend zijn.

In het rapport AD FS toepassings activiteit in de Azure Portal kunt u snel zien welke van uw toepassingen kunnen worden gemigreerd naar Azure AD. Het evalueert alle AD FS-toepassingen voor compatibiliteit met Azure AD, controleert op eventuele problemen en biedt hulp bij het voorbereiden van afzonderlijke toepassingen voor migratie. Met het rapport AD FS toepassings activiteit kunt u het volgende doen:

* **Ontdek AD FS toepassingen en bereik uw migratie.** In het rapport AD FS toepassings activiteit wordt een lijst weer gegeven met alle AD FS toepassingen in uw organisatie waarvoor een actieve gebruiker zich heeft aangemeld in de afgelopen 30 dagen. Het rapport geeft een gereedheid voor apps aan voor migratie naar Azure AD. In het rapport worden geen verwante relying party's van micro soft weer gegeven in AD FS zoals Office 365. Voor beelden van relying party's met de naam urn: Federation: MicrosoftOnline.

* **Prioriteiten voor de migratie van toepassingen.** Ontvang het aantal unieke gebruikers dat zich heeft aangemeld bij de toepassing in de afgelopen 1, 7 of 30 dagen om te helpen de ernst of het risico van het migreren van de toepassing te bepalen.
* **Voer migratie tests uit en los problemen op.** De rapportage service voert automatisch tests uit om te bepalen of een toepassing gereed is om te migreren. De resultaten worden weer gegeven in het rapport AD FS toepassings activiteit als migratie status. Als de AD FS configuratie niet compatibel is met een Azure AD-configuratie, krijgt u specifieke richt lijnen voor het adresseren van de configuratie in azure AD.

De gegevens voor de AD FS toepassings activiteit zijn beschikbaar voor gebruikers aan wie een van deze beheerders rollen is toegewezen: globale beheerder, rapport lezer, beveiligings lezer, toepassings beheerder of Cloud toepassings beheerder.

## <a name="prerequisites"></a>Vereisten

* Uw organisatie moet momenteel AD FS gebruiken om toegang te krijgen tot toepassingen.
* Azure AD Connect Health moet zijn ingeschakeld in uw Azure AD-Tenant.
* De Azure AD Connect Health voor AD FS agent moet zijn geïnstalleerd.
   * [Meer informatie over Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Aan de slag met het instellen van Azure AD Connect Health en het installeren van de AD FS agent](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Er zijn een aantal redenen waarom u niet alle toepassingen ziet die u verwacht nadat u Azure AD Connect Health hebt geïnstalleerd. In het rapport AD FS toepassings activiteit wordt alleen AD FS relying party's met gebruikers aanmeldingen in de afgelopen 30 dagen weer gegeven. Daarnaast worden in het rapport geen micro soft gerelateerde relying party's weer gegeven, zoals Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>AD FS toepassingen detecteren die kunnen worden gemigreerd 

Het rapport AD FS toepassings activiteit is beschikbaar in de Azure Portal onder Azure AD- **gebruik & Insights** -rapportage. Het rapport AD FS toepassings activiteit analyseert elke AD FS toepassing om te bepalen of deze kan worden gemigreerd, of dat er extra controle nodig is. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerdersrol die toegang heeft tot AD FS toepassings activiteit gegevens (globale beheerder, rapport lezer, beveiligings lezer, toepassings beheerder of Cloud toepassings beheerder).

2. Selecteer **Azure Active Directory** en selecteer vervolgens **bedrijfs toepassingen**.

3. Selecteer **gebruik & Insights** onder **activiteit** en selecteer vervolgens **AD FS toepassings activiteit** om een lijst met alle AD FS toepassingen in uw organisatie te openen.

   ![Toepassings activiteit AD FS](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Bekijk voor elke toepassing in de lijst AD FS toepassings activiteit de **migratie status**:

   * Als **u klaar bent om te migreren** , wordt de configuratie van de AD FS-toepassing volledig ondersteund in azure AD en kan deze worden gemigreerd.

   * **Beoordeling van vereisten** betekent dat sommige instellingen van de toepassing kunnen worden gemigreerd naar Azure AD, maar u moet de instellingen die niet kunnen worden gemigreerd, controleren.

   * **Aanvullende stappen vereist** betekent dat Azure AD geen enkele van de instellingen van de toepassing ondersteunt, zodat de toepassing niet kan worden gemigreerd in de huidige status.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>De gereedheid van een toepassing voor migratie evalueren 

1. Klik in de lijst AD FS toepassings activiteit op de status in de kolom **migratie status** om de migratie gegevens te openen. U ziet een samen vatting van de configuratie tests die zijn geslaagd, samen met eventuele mogelijke migratie problemen.

   ![Details van de migratie](media/migrate-adfs-application-activity/migration-details.png)

2. Klik op een bericht om meer details over de migratie regel te openen. Zie de tabel met [configuratie tests voor AD FS](#ad-fs-application-configuration-tests) hieronder voor een volledige lijst van de geteste eigenschappen.

   ![Details van de migratie regel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Toepassings configuratie tests AD FS

De volgende tabel bevat alle configuratie tests die worden uitgevoerd op AD FS toepassingen.

|Resultaat  |Geslaagd/waarschuwing/mislukt  |Description  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Er is ten minste één niet-migreerbaar regel gedetecteerd voor AdditionalAuthentication.       | Pass/Warning          | De Relying Party heeft regels om te vragen naar multi-factor Authentication (MFA). Als u wilt overstappen op Azure AD, moet u deze regels vertalen in beleid voor voorwaardelijke toegang. Als u een on-premises MFA gebruikt, raden wij u aan om over te stappen op Azure AD MFA. Meer [informatie over voorwaardelijke toegang](../authentication/concept-mfa-howitworks.md).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Relying Party heeft AdditionalWSFedEndpoint ingesteld op waar.       | Geslaagd/mislukt          | Met de Relying Party in AD FS kunt u meerdere WS-Fed Assertion-eind punten toestaan.Op dit moment ondersteunt Azure AD slechts één.Als u een scenario hebt waarbij dit resultaat de migratie blokkeert, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Relying Party heeft AllowedAuthenticationClassReferences ingesteld.       | Geslaagd/mislukt          | Met deze instelling in AD FS kunt u opgeven of de toepassing zo is geconfigureerd dat alleen bepaalde verificatie typen worden toegestaan. Het is raadzaam om voorwaardelijke toegang te gebruiken om deze functie te verkrijgen. Als u een scenario hebt waarbij dit resultaat de migratie blokkeert, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  Meer [informatie over voorwaardelijke toegang](../authentication/concept-mfa-howitworks.md).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Geslaagd/mislukt          | Met deze instelling in AD FS kunt u opgeven of de toepassing is geconfigureerd voor het negeren van SSO-cookies en er **altijd om verificatie wordt gevraagd**. In azure AD kunt u de verificatie sessie beheren met behulp van beleid voor voorwaardelijke toegang om hetzelfde gedrag te verkrijgen. Meer [informatie over het configureren van verificatie sessie beheer met voorwaardelijke toegang](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party heeft AutoUpdateEnabled ingesteld op waar       | Pass/Warning          | Met deze instelling in AD FS kunt u opgeven of AD FS zo is geconfigureerd dat de toepassing automatisch wordt bijgewerkt op basis van wijzigingen in de federatieve meta gegevens. Azure AD ondersteunt dit vandaag nog niet, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.           |
|Test-ADFSRPClaimsProviderName <br> Voor Relying Party is meerdere ClaimsProviders ingeschakeld       | Geslaagd/mislukt          | Met deze instelling in AD FS worden de id-providers aangeroepen waarvan de Relying Party claims accepteert. In azure AD kunt u externe samen werking inschakelen met behulp van Azure AD B2B. Meer [informatie over Azure AD B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Geslaagd/mislukt          | Voor de toepassing zijn aangepaste autorisatie regels voor delegering gedefinieerd. Dit is een WS-Trust concept dat door Azure AD wordt ondersteund met behulp van moderne verificatie protocollen, zoals OpenID Connect Connect en OAuth 2,0. Meer [informatie over het micro soft Identity-platform](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Er zijn aangepaste autorisatie regels voor imitatie gedefinieerd voor de toepassing.Dit is een WS-Trust concept dat door Azure AD wordt ondersteund met behulp van moderne verificatie protocollen, zoals OpenID Connect Connect en OAuth 2,0. Meer [informatie over het micro soft Identity-platform](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Er is ten minste één niet-migreerbaar regel gedetecteerd voor IssuanceAuthorization.       | Pass/Warning          | Voor de toepassing zijn aangepaste autorisatie regels voor uitgifte in AD FS gedefinieerd.Azure AD biedt ondersteuning voor deze functionaliteit met voorwaardelijke toegang voor Azure AD. Meer [informatie over voorwaardelijke toegang](../conditional-access/overview.md). <br> U kunt de toegang tot een toepassing ook beperken door gebruikers of groepen die zijn toegewezen aan de toepassing. Meer [informatie over het toewijzen van gebruikers en groepen voor toegang tot toepassingen](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> Er is ten minste één niet-migreerbaar regel gedetecteerd voor IssuanceTransform.       | Pass/Warning          | Voor de toepassing zijn aangepaste transformatie regels voor uitgifte opgegeven in AD FS. Azure AD biedt ondersteuning voor het aanpassen van de claims die zijn uitgegeven in het token. Zie claims aanpassen die zijn [uitgegeven in het SAML-token voor zakelijke toepassingen voor](../develop/active-directory-saml-claims-customization.md)meer informatie.           |
|Test-ADFSRPMonitoringEnabled <br> Relying Party heeft MonitoringEnabled ingesteld op waar.       | Pass/Warning          | Met deze instelling in AD FS kunt u opgeven of AD FS zo is geconfigureerd dat de toepassing automatisch wordt bijgewerkt op basis van wijzigingen in de federatieve meta gegevens. Azure AD ondersteunt dit vandaag nog niet, maar de migratie van de toepassing naar Azure AD moet niet worden geblokkeerd.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/Warning          | AD FS maakt een tijd verschil mogelijk op basis van de NotBefore-en NotOnOrAfter-tijden in het SAML-token. Dit wordt door Azure AD automatisch afgehandeld.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Relying Party heeft RequestMFAFromClaimsProviders ingesteld op waar.       | Pass/Warning          | Deze instelling in AD FS bepaalt het gedrag voor MFA als de gebruiker afkomstig is van een andere claim provider. In azure AD kunt u externe samen werking inschakelen met behulp van Azure AD B2B. Vervolgens kunt u beleid voor voorwaardelijke toegang Toep assen om toegang tot de gast te beveiligen. Meer informatie over [Azure AD B2B](../external-identities/what-is-b2b.md) en [voorwaardelijke toegang](../conditional-access/overview.md).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Relying Party heeft SignedSamlRequestsRequired ingesteld op waar       | Geslaagd/mislukt          | De toepassing is geconfigureerd in AD FS om de hand tekening in de SAML-aanvraag te verifiëren. Azure AD accepteert een ondertekende SAML-aanvraag; de hand tekening wordt echter niet gecontroleerd. Azure AD heeft verschillende methoden om te beveiligen tegen schadelijke aanroepen. Azure AD gebruikt bijvoorbeeld de antwoord-Url's die in de toepassing zijn geconfigureerd om de SAML-aanvraag te valideren. Azure AD verzendt alleen een token om Url's te beantwoorden die voor de toepassing zijn geconfigureerd. Als u een scenario hebt waarbij dit resultaat de migratie blokkeert, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/Warning         | De toepassing is geconfigureerd voor een aangepaste levens duur van tokens. De standaard waarde voor AD FS is een uur.Azure AD ondersteunt deze functionaliteit met behulp van voorwaardelijke toegang. Zie [verificatie sessie beheer met voorwaardelijke toegang configureren](../conditional-access/howto-conditional-access-session-lifetime.md)voor meer informatie.          |
|Relying Party is ingesteld om claims te versleutelen. Dit wordt ondersteund door Azure AD       | Geslaagd          | Met Azure AD kunt u het token versleutelen dat naar de toepassing wordt verzonden. Zie [Azure AD SAML-token versleuteling configureren](./howto-saml-token-encryption.md)voor meer informatie.          |
|EncryptedNameIdRequiredCheckResult      | Geslaagd/mislukt          | De toepassing is geconfigureerd voor het versleutelen van de NameID-claim in het SAML-token.Met Azure AD kunt u het volledige token versleutelen dat naar de toepassing wordt verzonden.Versleuteling van specifieke claims wordt nog niet ondersteund. Zie [Azure AD SAML-token versleuteling configureren](./howto-saml-token-encryption.md)voor meer informatie.         |

## <a name="check-the-results-of-claim-rule-tests"></a>De resultaten van claim regel tests controleren

Als u een claim regel hebt geconfigureerd voor de toepassing in AD FS, biedt de ervaring een gedetailleerde analyse voor alle claim regels. U ziet welke claim regels kunnen worden verplaatst naar Azure AD en die verder moeten worden geëvalueerd.

1. Klik in de lijst AD FS toepassings activiteit op de status in de kolom **migratie status** om de migratie gegevens te openen. U ziet een samen vatting van de configuratie tests die zijn geslaagd, samen met eventuele mogelijke migratie problemen.

2. Vouw op de pagina **Details van migratie regel** de resultaten uit om details over mogelijke migratie problemen weer te geven en meer richt lijnen te krijgen. Voor een gedetailleerde lijst met alle geteste claim regels raadpleegt u de tabel met [de resultaten van claim regel tests controleren](#check-the-results-of-claim-rule-tests) hieronder.

   In het onderstaande voor beeld ziet u de details van de migratie regel voor de regel IssuanceTransform. De lijst bevat de specifieke onderdelen van de claim die moeten worden beoordeeld en geadresseerd voordat u de toepassing kunt migreren naar Azure AD.

   ![Meer informatie over de migratie regel](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Claim regel testen

De volgende tabel bevat alle claim regel tests die worden uitgevoerd op AD FS toepassingen.

|Eigenschap  |Beschrijving  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | De instructie voor waarde gebruikt reguliere expressies om te evalueren of de claim overeenkomt met een bepaald patroon.Voor een vergelijk bare functionaliteit in azure AD kunt u een vooraf gedefinieerde trans formatie gebruiken, zoals IfEmpty (), StartWith (), contains (), onder andere. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie.          |
|UNSUPPORTED_CONDITION_CLASS      | De voor waarde-instructie heeft meerdere voor waarden die moeten worden geëvalueerd voordat de uitgifte-instructie kan worden uitgevoerd.Azure AD kan deze functionaliteit ondersteunen met de transformatie functies van de claim waarbij u meerdere claim waarden kunt evalueren.Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie.          |
|UNSUPPORTED_RULE_TYPE      | De claim regel kan niet worden herkend. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie over het configureren van claims in azure AD.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | De instructie condition maakt gebruik van een uitgever die niet wordt ondersteund in azure AD.Op dit moment kunnen Azure AD geen claims van een andere Active Directory of Azure AD in de bron opslaan. Als dit verhindert u het migreren van toepassingen naar Azure AD, [laat het ons dan weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | De instructie voor waarde gebruikt een statistische functie om een enkele claim te verlenen of toe te voegen, ongeacht het aantal overeenkomsten.In azure AD kunt u het kenmerk van een gebruiker evalueren om te bepalen welke waarde moet worden gebruikt voor de claim met functies zoals IfEmpty (), StartWith (), contains (), onder andere.Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie.          |
|RESTRICTED_CLAIM_ISSUED      | De instructie condition maakt gebruik van een claim die is beperkt in azure AD. U kunt mogelijk een beperkte claim uitgeven, maar u kunt de bron niet wijzigen of een trans formatie Toep assen. Zie voor meer informatie [claims aanpassen die zijn verzonden in tokens voor een specifieke app in azure AD](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | De uitgifte-instructie gebruikt een andere kenmerk opslag dan Active Directory. Op dit moment kunnen Azure AD geen claims van een andere Active Directory of Azure AD in de bron opslaan. Als dit ertoe leidt dat u de migratie van toepassingen naar Azure AD blokkeert, [laat het ons weten](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | De uitgifte-instructie gebruikt ADD om claims toe te voegen aan de set binnenkomende claims. In azure AD kan dit worden geconfigureerd als meerdere claim transformaties.Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-claims-mapping.md)voor meer informatie.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | De uitgifte-instructie gebruikt reguliere expressies voor het transformeren van de waarde van de claim die moet worden verzonden.Voor een vergelijk bare functionaliteit in azure AD kunt u vooraf gedefinieerde trans formatie, zoals extractie (), Trim (), ToLower, onder andere gebruiken. Zie [claims aanpassen die zijn uitgegeven in het SAML-token voor zakelijke toepassingen](../develop/active-directory-saml-claims-customization.md)voor meer informatie.          |

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Kan niet alle AD FS-toepassingen in het rapport weer geven

 Als u Azure AD Connect status hebt geïnstalleerd, maar u nog steeds de vraag om de software te installeren ziet of als u niet alle AD FS toepassingen in het rapport kunt zien, is het mogelijk dat u geen actieve AD FS-toepassingen hebt of dat uw AD FS-toepassingen micro soft-toepassing zijn.
 
 In het rapport AD FS toepassings activiteit worden alle AD FS toepassingen in uw organisatie weer gegeven met actieve gebruikers zich in de afgelopen 30 dagen aan. Daarnaast worden in het rapport geen micro soft-gerelateerde relying party's weer gegeven in AD FS zoals Office 365. Voor beelden van relying party's met de naam urn: Federation: MicrosoftOnline ', ' microsoftonline ', ' micro soft: winhello: CERT: Prov: Server ' wordt niet weer gegeven in de lijst.





## <a name="next-steps"></a>Volgende stappen

- [Video: het AD FS activiteiten rapport gebruiken om een toepassing te migreren](https://www.youtube.com/watch?v=OThlTA239lU)
- [Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
- [Toegang tot apps beheren](what-is-access-management.md)
- [Azure AD Connect-federatie](../hybrid/how-to-connect-fed-whatis.md)
