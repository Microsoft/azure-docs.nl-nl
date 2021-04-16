---
title: Gebruik het activiteitenrapport om de AD FS te verplaatsen naar Azure Active Directory | Microsoft Docs'
description: Met Active Directory Federation Services (AD FS) toepassingsactiviteitrapport kunt u snel toepassingen migreren van AD FS naar Azure Active Directory (Azure AD). Dit hulpprogramma voor migratie voor AD FS identificeert compatibiliteit met Azure AD en biedt richtlijnen voor migratie.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9265a61b0879078332b8ccc2d10e711b4ac8f1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377417"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Gebruik het AD FS toepassingsactiviteitrapport om toepassingen te migreren naar Azure AD

Veel organisaties gebruiken Active Directory Federation Services (AD FS) om een aanmelding voor cloudtoepassingen te bieden. Het verplaatsen van uw AD FS-toepassingen naar Azure AD biedt aanzienlijke voordelen voor verificatie, met name op het gebied van kostenbeheer, risicobeheer, productiviteit, naleving en governance. Maar het kan tijdrovend zijn om te begrijpen welke toepassingen compatibel zijn met Azure AD en om specifieke migratiestappen te identificeren.

Met AD FS activiteitenrapport van de toepassing in de Azure Portal kunt u snel bepalen welke van uw toepassingen kunnen worden gemigreerd naar Azure AD. Het evalueert alle AD FS toepassingen op compatibiliteit met Azure AD, controleert op problemen en biedt richtlijnen voor het voorbereiden van afzonderlijke toepassingen voor migratie. Met het AD FS activiteitenrapport van de toepassing kunt u het volgende doen:

* **Ontdek AD FS toepassingen en bereik van uw migratie.** Het AD FS toepassingsactiviteit bevat alle AD FS toepassingen in uw organisatie die in de afgelopen 30 dagen een actieve gebruikersmelding hebben gehad. Het rapport geeft aan dat apps gereed zijn voor migratie naar Azure AD. Het rapport geeft geen aan Microsoft gerelateerde relying parties weer in AD FS zoals Office 365. Bijvoorbeeld relying parties met de naam 'urn:federation:MicrosoftOnline'.

* **Prioriteer toepassingen voor migratie.** Haal het aantal unieke gebruikers op dat zich in de afgelopen 1, 7 of 30 dagen bij de toepassing heeft aangemeld om het kritieke of risico van de migratie van de toepassing vast te stellen.
* **Voer migratietests uit en los problemen op.** De rapportageservice voert automatisch tests uit om te bepalen of een toepassing gereed is voor migratie. De resultaten worden weergegeven in het rapport AD FS toepassingsactiviteit als een migratiestatus. Als de AD FS niet compatibel is met een Azure AD-configuratie, krijgt u specifieke richtlijnen voor het aanpakken van de configuratie in Azure AD.

De AD FS toepassingsactiviteitsgegevens zijn beschikbaar voor gebruikers aan wie een van deze beheerdersrollen is toegewezen: globale beheerder, rapportlezer, beveiligingslezer, toepassingsbeheerder of cloudtoepassingsbeheerder.

## <a name="prerequisites"></a>Vereisten

* Uw organisatie moet momenteel gebruikmaken van AD FS toegang tot toepassingen.
* Azure AD Connect Health moet zijn ingeschakeld in uw Azure AD-tenant.
* De Azure AD Connect Health voor AD FS agent moet zijn geïnstalleerd.
   * [Meer informatie over Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Aan de slag met het instellen Azure AD Connect Health en installeren van AD FS agent](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Er zijn een aantal redenen waarom u niet alle toepassingen ziet die u verwacht nadat u de Azure AD Connect Health. In AD FS rapport over toepassingsactiviteiten worden alleen AD FS relying parties met gebruikersmeldingen in de afgelopen 30 dagen. In het rapport worden ook geen aan Microsoft gerelateerde relying parties zoals Office 365 weergegeven.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Ontdek AD FS toepassingen die kunnen worden gemigreerd 

Het AD FS van de toepassingsactiviteit is beschikbaar in de Azure Portal onder Azure AD Usage & insights reporting(Rapportage **van Azure AD-gebruiksgegevens).** Het AD FS toepassingsactiviteit analyseert elke AD FS toepassing om te bepalen of deze kan worden gemigreerd zoals het is of dat er aanvullende controle nodig is. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een beheerdersrol die toegang heeft tot AD FS-toepassingsactiviteitgegevens (globale beheerder, rapportlezer, beveiligingslezer, toepassingsbeheerder of cloudtoepassingsbeheerder).

2. Selecteer **Azure Active Directory** en selecteer vervolgens **Bedrijfstoepassingen.**

3. Selecteer **onder** Activiteit de optie **Usage & Insights** en selecteer vervolgens **AD FS application activity** om een lijst met alle AD FS in uw organisatie te openen.

   ![AD FS toepassingsactiviteit](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Voor elke toepassing in de lijst AD FS toepassingsactiviteit bekijkt u de **Migratiestatus:**

   * **Gereed voor migratie** betekent dat AD FS toepassingsconfiguratie volledig wordt ondersteund in Azure AD en in de huidige staat kan worden gemigreerd.

   * **Controle van** behoeften betekent dat sommige van de instellingen van de toepassing kunnen worden gemigreerd naar Azure AD, maar u moet de instellingen controleren die niet kunnen worden gemigreerd zoals ze zijn.

   * **Aanvullende stappen die vereist** zijn, betekent dat Azure AD sommige van de instellingen van de toepassing niet ondersteunt, zodat de toepassing niet in de huidige status kan worden gemigreerd.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>De gereedheid van een toepassing voor migratie evalueren 

1. Klik in AD FS lijst met activiteiten van de toepassing op de status in de **kolom Migratiestatus** om de migratiedetails te openen. U ziet een samenvatting van de configuratietests die zijn geslaagd, samen met mogelijke migratieproblemen.

   ![Details van de migratie](media/migrate-adfs-application-activity/migration-details.png)

2. Klik op een bericht om aanvullende details van de migratieregel te openen. Zie de tabel AD FS toepassingsconfiguratietests hieronder [voor](#ad-fs-application-configuration-tests) een volledige lijst van de geteste eigenschappen.

   ![Details van migratieregel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS toepassingsconfiguratietests testen

De volgende tabel bevat alle configuratietests die worden uitgevoerd op AD FS toepassingen.

|Resultaat  |Pass/Warning/Fail  |Description  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Er is ten minste één niet-migratable-regel gedetecteerd voor AdditionalAuthentication.       | Doorgeven/waarschuwen          | De relying party heeft regels om te vragen om meervoudige verificatie (MFA). Om over te gaan naar Azure AD, zet u deze regels om in beleid voor voorwaardelijke toegang. Als u een on-premises MFA gebruikt, raden we u aan om over te gaan naar Azure AD MFA. [Meer informatie over voorwaardelijke toegang.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Voor Relying Party is AdditionalWSFedEndpoint ingesteld op true.       | Geslaagd/mislukt          | De relying party in AD FS meerdere WS-Fed-eindpunten.Momenteel ondersteunt Azure AD er slechts één.Als u een scenario hebt waarin dit resultaat de migratie blokkeert, laat [het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Relying Party heeft AllowedAuthenticationClassReferences ingesteld.       | Geslaagd/mislukt          | Met deze instelling in AD FS kunt u opgeven of de toepassing is geconfigureerd om alleen bepaalde verificatietypen toe te staan. We raden u aan om voorwaardelijke toegang te gebruiken om deze mogelijkheid te bereiken. Als u een scenario hebt waarin dit resultaat de migratie blokkeert, laat [het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Meer informatie over voorwaardelijke toegang.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Geslaagd/mislukt          | Met deze instelling in AD FS kunt u opgeven of de toepassing is geconfigureerd om cookies voor eenmalige aanmelding en **Altijd vragen om verificatie te negeren.** In Azure AD kunt u de verificatiesessie beheren met behulp van beleidsregels voor voorwaardelijke toegang om soortgelijk gedrag te bereiken. [Meer informatie over het configureren van verificatiesessiebeheer met voorwaardelijke toegang.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party heeft AutoUpdateEnabled ingesteld op true       | Doorgeven/waarschuwen          | Met deze instelling in AD FS kunt u opgeven of AD FS is geconfigureerd om de toepassing automatisch bij te werken op basis van wijzigingen in de federatiemetagegevens. Azure AD ondersteunt dit momenteel niet, maar mag de migratie van de toepassing naar Azure AD niet blokkeren.           |
|Test-ADFSRPClaimsProviderName <br> Relying Party heeft meerdere ClaimsProviders ingeschakeld       | Geslaagd/mislukt          | Met deze instelling in AD FS de id-providers aan waarvan de relying party claims accepteert. In Azure AD kunt u externe samenwerking inschakelen met behulp van Azure AD B2B. [Meer informatie over Azure AD B2B.](../external-identities/what-is-b2b.md)          |
|Test-ADFSRPDelegationAuthorizationRules      | Geslaagd/mislukt          | Voor de toepassing zijn aangepaste autorisatieregels voor delegering gedefinieerd. Dit is een WS-Trust concept dat Azure AD ondersteunt door gebruik te maken van moderne verificatieprotocollen, zoals OpenID Connect en OAuth 2.0. [Meer informatie over het Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Doorgeven/waarschuwen          | Voor de toepassing zijn aangepaste autorisatieregels voor imitatie gedefinieerd.Dit is een WS-Trust concept dat Azure AD ondersteunt door gebruik te maken van moderne verificatieprotocollen, zoals OpenID Connect en OAuth 2.0. [Meer informatie over het Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Er is ten minste één niet-migratable-regel gedetecteerd voor IssuanceAuthorization.       | Doorgeven/waarschuwen          | Voor de toepassing zijn aangepaste autorisatieregels voor uitgifte gedefinieerd in AD FS.Azure AD ondersteunt deze functionaliteit met voorwaardelijke toegang van Azure AD. [Meer informatie over voorwaardelijke toegang.](../conditional-access/overview.md) <br> U kunt de toegang tot een toepassing ook beperken door gebruikers of groepen die zijn toegewezen aan de toepassing. [Meer informatie over het toewijzen van gebruikers en groepen voor toegang tot toepassingen.](./assign-user-or-group-access-portal.md)            |
|Test-ADFSRPIssuanceTransformRules <br> Er is ten minste één niet-migratable-regel gedetecteerd voor IssuanceTransform.       | Doorgeven/waarschuwen          | Voor de toepassing zijn aangepaste uitgiftetransformatorregels gedefinieerd in AD FS. Azure AD biedt ondersteuning voor het aanpassen van de claims die zijn uitgegeven in het token. Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-saml-claims-customization.md)           |
|Test-ADFSRPMonitoringEnabled <br> Bij Relying Party is MonitoringEnabled ingesteld op true.       | Doorgeven/waarschuwen          | Met deze instelling in AD FS kunt u opgeven of AD FS is geconfigureerd om de toepassing automatisch bij te werken op basis van wijzigingen in de federatiemetagegevens. Azure AD ondersteunt dit momenteel niet, maar mag de migratie van de toepassing naar Azure AD niet blokkeren.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Doorgeven/waarschuwen          | AD FS maakt een tijdverschil mogelijk op basis van de notbefore- en NotOnOrAfter-tijden in het SAML-token. Azure AD verwerkt dit standaard automatisch.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Voor Relying Party is RequestMFAFromClaimsProviders ingesteld op true.       | Doorgeven/waarschuwen          | Deze instelling in AD FS bepaalt het gedrag voor MFA wanneer de gebruiker afkomstig is van een andere claimprovider. In Azure AD kunt u externe samenwerking inschakelen met behulp van Azure AD B2B. Vervolgens kunt u beleid voor voorwaardelijke toegang toepassen om gasttoegang te beveiligen. Meer informatie over [Azure AD B2B](../external-identities/what-is-b2b.md) en [voorwaardelijke toegang.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Relying Party heeft SignedSamlRequestsRequired ingesteld op true       | Geslaagd/mislukt          | De toepassing wordt geconfigureerd in AD FS handtekening in de SAML-aanvraag te verifiëren. Azure AD accepteert een ondertekende SAML-aanvraag; De handtekening wordt echter niet geverifieerd. Azure AD biedt verschillende methoden om te beveiligen tegen schadelijke aanroepen. Azure AD gebruikt bijvoorbeeld de antwoord-URL's die in de toepassing zijn geconfigureerd om de SAML-aanvraag te valideren. Azure AD verzendt alleen een token om URL's te beantwoorden die zijn geconfigureerd voor de toepassing. Als u een scenario hebt waarin dit resultaat de migratie blokkeert, laat [het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Doorgeven/waarschuwen         | De toepassing is geconfigureerd voor de levensduur van een aangepast token. De AD FS standaard is één uur.Azure AD ondersteunt deze functionaliteit met behulp van voorwaardelijke toegang. Zie Configure [authentication session management with Conditional Access (Beheer van verificatiesessies configureren met voorwaardelijke toegang) voor meer informatie.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Relying Party is ingesteld om claims te versleutelen. Dit wordt ondersteund door Azure AD       | Geslaagd          | Met Azure AD kunt u het token versleutelen dat naar de toepassing wordt verzonden. Zie Azure [AD SAML-tokenversleuteling configureren voor meer informatie.](./howto-saml-token-encryption.md)          |
|EncryptedNameIdRequiredCheckResult      | Geslaagd/mislukt          | De toepassing is geconfigureerd voor het versleutelen van de nameID-claim in het SAML-token.Met Azure AD kunt u het hele token versleutelen dat naar de toepassing wordt verzonden.Versleuteling van specifieke claims wordt nog niet ondersteund. Zie Azure [AD SAML-tokenversleuteling configureren voor meer informatie.](./howto-saml-token-encryption.md)         |

## <a name="check-the-results-of-claim-rule-tests"></a>De resultaten van claimregeltests controleren

Als u een claimregel voor de toepassing hebt geconfigureerd in AD FS, biedt de ervaring een gedetailleerde analyse voor alle claimregels. U ziet welke claimregels kunnen worden verplaatst naar Azure AD en welke nader moeten worden beoordeeld.

1. Klik in AD FS lijst met toepassingsactiviteiten op de status in de **kolom Migratiestatus** om de migratiedetails te openen. U ziet een samenvatting van de configuratietests die zijn geslaagd, samen met mogelijke migratieproblemen.

2. Vouw op **de pagina Details migratieregel** de resultaten uit om details over mogelijke migratieproblemen weer te geven en aanvullende richtlijnen te krijgen. Zie de tabel De resultaten van claimregeltests controleren hieronder voor een gedetailleerde lijst met geteste [claimregels.](#check-the-results-of-claim-rule-tests)

   In het onderstaande voorbeeld ziet u de details van de migratieregel voor de regel IssuanceTransform. Het bevat de specifieke onderdelen van de claim die moeten worden gecontroleerd en aangepakt voordat u de toepassing naar Azure AD kunt migreren.

   ![Aanvullende richtlijnen voor migratieregel](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Claimregeltests

De volgende tabel bevat alle claimregeltests die worden uitgevoerd op AD FS toepassingen.

|Eigenschap  |Beschrijving  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | De voorwaarde-instructie maakt gebruik van reguliere expressies om te evalueren of de claim overeenkomt met een bepaald patroon.Als u een vergelijkbare functionaliteit in Azure AD wilt bereiken, kunt u onder andere vooraf gedefinieerde transformatie gebruiken, zoals IfEmpty(), StartWith(), Contains(). Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | De voorwaarde-instructie heeft meerdere voorwaarden die moeten worden geëvalueerd voordat u de uitgifte-instructie kunt uitvoeren.Azure AD biedt mogelijk ondersteuning voor deze functionaliteit met de transformatiefuncties van de claim, waar u meerdere claimwaarden kunt evalueren.Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | De claimregel kan niet worden herkend. Zie Claims aanpassen die zijn uitgegeven in het [SAML-token](../develop/active-directory-saml-claims-customization.md)voor bedrijfstoepassingen voor meer informatie over het configureren van claims in Azure AD.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | De voorwaarde-instructie maakt gebruik van een vergever die niet wordt ondersteund in Azure AD.Op dit moment worden in Azure AD geen claims van andere stores op basis van Active Directory of Azure AD gebruikt. Als u hierdoor geen toepassingen naar Azure AD kunt migreren, laat [het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | De voorwaarde-instructie maakt gebruik van een statistische functie om één claim uit te geven of toe te voegen, ongeacht het aantal overeenkomsten.In Azure AD kunt u het kenmerk van een gebruiker evalueren om te bepalen welke waarde voor de claim moet worden gebruikt met functies zoals IfEmpty(), StartWith(), Contains().Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | De voorwaarde-instructie maakt gebruik van een claim die is beperkt in Azure AD. U kunt mogelijk een beperkte claim uitgeven, maar u kunt de bron niet wijzigen of transformatie toepassen. Zie Claims aanpassen die worden [ingediend in tokens voor een specifieke app in Azure AD voor meer informatie.](../develop/active-directory-claims-mapping.md)          |
|EXTERNAL_ATTRIBUTE_STORE      | De uitgifte-instructie maakt gebruik van een kenmerkopslag die verschilt van Active Directory. Op dit moment worden claims van Azure AD niet afkomstig uit winkels die verschillen van Active Directory of Azure AD. Als dit resultaat blokkeert dat u toepassingen naar Azure AD migreert, laat [het ons dan weten.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | De uitgifte-instructie maakt gebruik van ADD om claims toe te voegen aan de binnenkomende claimset. In Azure AD kan dit worden geconfigureerd als meerdere claimtransformaties.Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | De uitgifte-instructie maakt gebruik van reguliere expressies om de waarde te transformeren van de claim die moet worden uitgegeven.Als u vergelijkbare functionaliteit in Azure AD wilt bereiken, kunt u onder andere vooraf gedefinieerde transformatie gebruiken, zoals Extract(), Trim(), ToLower. Zie Claims aanpassen die zijn uitgegeven in het [SAML-token voor bedrijfstoepassingen voor meer informatie.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Kan niet al mijn AD FS in het rapport zien

 Als u de Azure AD Connect-status hebt geïnstalleerd, maar u nog steeds de prompt ziet om deze te installeren of als u niet alle AD FS-toepassingen in het rapport ziet, kan het zijn dat u geen actieve AD FS-toepassingen hebt of als uw AD FS-toepassingen microsoft-toepassing zijn.
 
 In AD FS activiteitenrapport van de toepassing worden alle AD FS toepassingen in uw organisatie vermeld met actieve gebruikers die zich in de afgelopen 30 dagen hebben aanmelden. Het rapport bevat ook geen aan Microsoft gerelateerde relying parties in AD FS zoals Office 365. Relying Parties met de naam 'urn:federation:MicrosoftOnline', 'microsoftonline', 'microsoft:winhello:cert:prov:server' worden bijvoorbeeld niet weergegeven in de lijst.





## <a name="next-steps"></a>Volgende stappen

- [Video: Het rapport met AD FS-activiteit gebruiken om een toepassing te migreren](https://www.youtube.com/watch?v=OThlTA239lU)
- [Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
- [Toegang tot apps beheren](what-is-access-management.md)
- [Azure AD Connect-federatie](../hybrid/how-to-connect-fed-whatis.md)
