---
title: Referentie voor auditactiviteiten van Azure Active Directory (Azure AD) | Microsoft Docs
description: Overzicht van de auditactiviteiten die kunnen worden geregistreerd in de auditlogboeken van Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f9976faf2a803e747bce8b851a3555e6d746b0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89231143"
---
# <a name="azure-ad-audit-activity-reference"></a>Referentie voor auditactiviteiten van Azure AD

Met Azure Active Directory-rapporten (Azure AD) kunt u de gegevens ophalen die u nodig hebt om te bepalen hoe uw omgeving wordt uitgevoerd.

De rapportstructuur in Azure AD bestaat uit de volgende onderdelen:

- **Activiteitsrapporten** 
    - [Aanmeldingen](concept-sign-ins.md) : bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers
    - [Audit logboeken](concept-audit-logs.md) : voorziet in traceer baarheid via Logboeken voor alle wijzigingen die worden uitgevoerd door diverse functies in azure AD. 
    
- **Beveiligingsrapporten** 
    - [Risk ante aanmeldingen](../identity-protection/overview-identity-protection.md) : een Risk ante aanmelding is een indicator voor een aanmeldings poging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikers account is. 
    - [Gebruikers die zijn gemarkeerd voor risico](../identity-protection/overview-identity-protection.md) : een Risk ante gebruiker is een indicator voor een gebruikers account dat mogelijk is aangetast. 

In dit artikel worden de auditactiviteiten vermeld die in uw auditlogboeken kunnen worden geregistreerd.

## <a name="access-reviews"></a>Toegangsbeoordelingen

|Controlecategorie|Activiteit|
|---|---|
|Toegangsbeoordelingen|Toegangsbeoordeling beëindigd|
|Toegangsbeoordelingen|Goedkeurder toevoegen om goedkeuring te vragen|
|Toegangsbeoordelingen|Revisor toevoegen voor toegang tot revisie|
|Toegangsbeoordelingen|Toegangsbeoordeling toepassen|
|Toegangsbeoordelingen|Toegangsbeoordeling maken|
|Toegangsbeoordelingen|Programma maken|
|Toegangsbeoordelingen|Aanvraaggoedkeuring maken|
|Toegangsbeoordelingen|Toegangsbeoordeling verwijderen|
|Toegangsbeoordelingen|Programma verwijderen|
|Toegangsbeoordelingen|Programmabeheer koppelen|
|Toegangsbeoordelingen|Onboarding bij Azure AD-toegangsbeoordelingen|
|Toegangsbeoordelingen|Revisor verwijderen uit toegangsbeoordeling|
|Toegangsbeoordelingen|Stoppen beoordeling aanvragen|
|Toegangsbeoordelingen|Toepassen van beoordelingsresultaat aanvragen|
|Toegangsbeoordelingen|RBAC-rollidmaatschap beoordelen|
|Toegangsbeoordelingen|App-toewijzing beoordelen|
|Toegangsbeoordelingen|Groepslidmaatschap controleren|
|Toegangsbeoordelingen|Aanvraag voor goedkeuring aanvraag beoordelen|
|Toegangsbeoordelingen|Programmabeheer ontkoppelen|
|Toegangsbeoordelingen|Toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Status van de onboarding van Azure AD-toegangs beoordelingen bijwerken|
|Toegangsbeoordelingen|Instellingen voor de e-mailmelding van de toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor aantal herhalingen van de toegangs beoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor toegangs beoordeling duur van terugkeer patroon in dagen bijwerken|
|Toegangsbeoordelingen|Instelling van eind type van terugkeer patroon van toegangs beoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor toegangs beoordeling van type terugkeer patroon bijwerken|
|Toegangsbeoordelingen|Herinneringsinstellingen toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Programma bijwerken|
|Toegangsbeoordelingen|Aanvraaggoedkeuring bijwerken|
|Toegangsbeoordelingen|Gebruiker uitgeschakeld|

## <a name="account-provisioning"></a>Account inrichten

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|V2-toepassingsmachtigingen ophalen|
|Toepassingsbeheer|V2-toepassingsservice-principals in de huidige tenant ophalen|
|Toepassingsbeheer|V1-toepassing bijwerken|
|Toepassingsbeheer|V2-toepassing bijwerken|
|Toepassingsbeheer|V2-toepassingsmachtiging bijwerken|
|Toepassingsbeheer|OAuth2PermissionGrant toevoegen|
|Toepassingsbeheer|App-roltoewijzing aan service-principal toevoegen|

## <a name="application-proxy"></a>Toepassingsproxy

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Toepassing toevoegen|
|Toepassingsbeheer|Eigenaar aan toepassing toevoegen|
|Toepassingsbeheer|Eigenaar aan service-principal toevoegen|
|Toepassingsbeheer|Beleid aan service-principal toevoegen|
|Mapbeheer|Service-principal toevoegen|
|Mapbeheer|Referenties voor service-principal toevoegen|
|Mapbeheer|Instemmen met toepassing|
|Mapbeheer|Toepassing verwijderen|
|Mapbeheer|Toepassing definitief verwijderen|
|Mapbeheer|OAuth2PermissionGrant verwijderen|
|Mapbeheer|App-roltoewijzing uit service-principal verwijderen|
|Mapbeheer|Eigenaar uit toepassing verwijderen|
|Resource|Eigenaar uit service-principal verwijderen|
|Resource|Beleid uit service-principal verwijderen|
|Resource|Service-principal verwijderen|


## <a name="automated-password-rollover"></a>Automatische wachtwoordoverschakeling

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Referenties voor service-principal verwijderen|


## <a name="b2c"></a>B2C

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Toepassing herstellen|
|Toepassingsbeheer|Toestemming intrekken|
|Toepassingsbeheer|Toepassing bijwerken|
|Toepassingsbeheer|Externe geheimen bijwerken|
|Toepassingsbeheer|Service-principal bijwerken|
|Toepassingsbeheer|Een toegangstoken voor de toepassing uitgeven|
|Toepassingsbeheer|Een autorisatiecode aan de toepassing uitgeven|
|Toepassingsbeheer|Een id_token voor de toepassing uitgeven|
|Toepassingsbeheer|De referenties voor lokale accounts valideren|
|Toepassingsbeheer|Verificatie van de gebruiker valideren|
|Toepassingsbeheer|V2-toepassingsmachtigingen toevoegen|
|Toepassingsbeheer|Een sleutel op basis van een ASCII-geheim aan een CPIM-sleutelcontainer toevoegen|
|Toepassingsbeheer|Een sleutel aan een CPIM-sleutelcontainer toevoegen|
|Toepassingsbeheer|AdminPolicyDatas-SetResources|
|Toepassingsbeheer|AdminUserJourneys-GetResources|
|Toepassingsbeheer|AdminUserJourneys-RemoveResources|
|Verificatie|AdminUserJourneys-SetResources|
|Verificatie|IdentityProvider maken|
|Verificatie|V1-toepassing maken|
|Verificatie|V2-toepassing maken|
|Verificatie|Aangepaste domeinen in de tenant maken|
|Autorisatie|Een nieuwe AdminUserJourney maken|
|Autorisatie|Gelokaliseerde resource-json maken|
|Autorisatie|Nieuwe aangepaste IDP maken|
|Autorisatie|Nieuwe IDP maken|
|Autorisatie|Een B2C-mapresource maken of bijwerken|
|Autorisatie|Beleid maken|
|Autorisatie|TrustFramework-beleid maken|
|Autorisatie|TrustFramework-beleid maken met configureerbaar voorvoegsel|
|Autorisatie|Gebruikerskenmerk maken|
|Autorisatie|CreateTrustFrameworkPolicy|
|Autorisatie|Een nieuwe AdminUserJourney maken of bijwerken|
|Autorisatie|IDP verwijderen|
|Autorisatie|IdentityProvider verwijderen|
|Autorisatie|V1-toepassing verwijderen|
|Autorisatie|V2-toepassing verwijderen|
|Autorisatie|V2-toepassingsmachtiging verwijderen|
|Autorisatie|Een B2C-mapresource verwijderen|
|Autorisatie|Een CPIM-sleutelcontainer verwijderen|
|Autorisatie|TrustFramework-beleid verwijderen|
|Autorisatie|Gebruikerskenmerk verwijderen|
|Autorisatie|B2C-functie inschakelen|
|Autorisatie|B2C-mapresources in een abonnement ophalen|
|Autorisatie|Aangepaste IDP ophalen|
|Autorisatie|IDP ophalen|
|Autorisatie|V1- en V2-toepassingen ophalen|
|Autorisatie|V1-toepassing ophalen|
|Autorisatie|V1-toepassingen ophalen|
|Autorisatie|V2-toepassing ophalen|
|Autorisatie|V2-toepassingen ophalen|
|Autorisatie|Een B2C-Directory resource ophalen|
|Autorisatie|Een lijst met aangepaste domeinen in de tenant ophalen|
|Autorisatie|Een gebruikersbeleving ophalen|
|Autorisatie|Toegestane toepassingsclaims voor gebruikersbeleving ophalen|
|Autorisatie|Toegestane zelf-gecontroleerde claims voor gebruikersbeleving ophalen|
|Autorisatie|Toegestane zelf-gecontroleerde claims voor beleid ophalen|
|Autorisatie|Lijst met beschikbare uitvoerclaims ophalen|
|Autorisatie|Inhouddefinities voor gebruikersbeleving ophalen|
|Autorisatie|IDP's voor een specifieke beheerstroom ophalen|
|Autorisatie|Actieve sleutelmetagegevens voor sleutelcontainer in JWK ophalen|
|Autorisatie|Lijst van alle beheerstromen ophalen|
|Autorisatie|Lijst met labels voor alle beheerstromen voor alle gebruikers ophalen|
|Autorisatie|Lijst met tenants voor een gebruiker ophalen|
|Autorisatie|Zelf-gecontroleerde claims voor lokale accounts ophalen|
|Autorisatie|Gelokaliseerde resource-json ophalen|
|Autorisatie|Bewerkingen van de resourceprovider Microsoft.AzureActiveDirectory ophalen|
|Autorisatie|Beleidsregels ophalen|
|Autorisatie|Beleid ophalen|
|Autorisatie|Resource-eigenschappen van een tenant ophalen|
|Autorisatie|Lijst met ondersteunde IDP's ophalen|
|Autorisatie|Lijst met ondersteunde IDP's van de gebruikersbeleving ophalen|
|Autorisatie|Tenantgegevens ophalen|
|Autorisatie|Voor tenant toegestane functies ophalen|
|Autorisatie|Voor tenant gedefinieerde, aangepaste IDP-lijst ophalen|
|Autorisatie|Voor tenant gedefinieerde IDP-lijst ophalen|
|Autorisatie|Voor tenant gedefinieerde, lokale IDP-lijst ophalen|
|Autorisatie|Tenantgegevens voor een gebruiker ophalen voor het maken van resources|
|Autorisatie|Tenantlijst ophalen|
|Autorisatie|TenantDomains ophalen|
|Autorisatie|De standaard ondersteunde cultuur voor CPIM ophalen|
|Autorisatie|De gegevens van een beheerstroom ophalen|
|Autorisatie|De lijst met UserJourneys voor deze tenant ophalen|
|Autorisatie|De set beschikbare ondersteunde culturen voor CPIM ophalen|
|Autorisatie|TrustFramework-beleid ophalen|
|Autorisatie|TrustFramework-beleid als xml ophalen|
|Autorisatie|Gebruikerskenmerk ophalen|
|Autorisatie|Gebruikerskenmerken ophalen|
|Autorisatie|Lijst met gebruikersbelevingen ophalen|
|Autorisatie|GetIEFPolicies|
|Autorisatie|GetIdentityProviders|
|Autorisatie|GetTrustFrameworkPolicy|
|Autorisatie|Hiermee haalt u een CPIM-sleutelcontainer in jwk-indeling op|
|Autorisatie|Hiermee haalt u een lijst met sleutelcontainers in de tenant op|
|Autorisatie|Hiermee wordt het type tenant opgehaald|
|Autorisatie|MigrateTenantMetadata|
|Autorisatie|IdentityProvider patchen|
|Autorisatie|PutTrustFrameworkPolicy|
|Autorisatie|PutTrustFrameworkpolicy|
|Autorisatie|Een gebruikersbeleving verwijderen|
|Autorisatie|Een back-up van een CPIM-sleutelcontainer herstellen|
|Autorisatie|V2-toepassingsmachtigingen ophalen|
|Autorisatie|V2-toepassingsservice-principals in de huidige tenant ophalen|
|Autorisatie|Aangepaste IDP bijwerken|
|Autorisatie|IDP bijwerken|
|Autorisatie|Lokale IDP bijwerken|
|Autorisatie|V1-toepassing bijwerken|
|Autorisatie|V2-toepassing bijwerken|
|Autorisatie|V2-toepassingsmachtiging bijwerken|
|Autorisatie|Beleid bijwerken|
|Autorisatie|Gebruikerskenmerk bijwerken|
|Autorisatie|Een versleutelde CPIM-sleutel uploaden|
|Autorisatie|Gebruikersautorisatie: API is uitgeschakeld voor de tenantfunctieset|
|Autorisatie|Gebruikersautorisatie: Gebruiker toegang verleend als 'tenantbeheerder'|
|Autorisatie|Gebruikersautorisatie: De gebruiker heeft toegangsrechten voor 'Geverifieerde gebruikers' gekregen|
|Autorisatie|Controleren of de B2C-functie is ingeschakeld|
|Autorisatie|Controleren of de functie is ingeschakeld|
|Autorisatie|Programma maken|
|Autorisatie|Programma verwijderen|
|Autorisatie|Programmabeheer koppelen|
|Autorisatie|Onboarding bij Azure AD-toegangsbeoordelingen|
|Autorisatie|Programmabeheer ontkoppelen|
|Autorisatie|Programma bijwerken|
|Autorisatie|Desktop-SSO uitschakelen|
|Autorisatie|Desktop-SSO uitschakelen voor een specifiek domein|
|Autorisatie|Toepassingsproxy uitschakelen|
|Autorisatie|Passthrough-verificatie uitschakelen|
|Autorisatie|Desktop-SSO inschakelen|
|Mapbeheer|Desktop-SSO inschakelen voor een specifiek domein|
|Mapbeheer|Toepassingsproxy inschakelen|
|Mapbeheer|Passthrough-verificatie inschakelen|
|Mapbeheer|Aangepaste domeinen in de tenant maken|
|Mapbeheer|B2C-functie inschakelen|
|Mapbeheer|Een lijst met aangepaste domeinen in de tenant ophalen|
|Mapbeheer|Resource-eigenschappen van een tenant ophalen|
|Mapbeheer|Tenantgegevens ophalen|
|Mapbeheer|Voor tenant toegestane functies ophalen|
|Mapbeheer|TenantDomains ophalen|
|Sleutel|Hiermee wordt het type tenant opgehaald|
|Sleutel|Controleren of de B2C-functie is ingeschakeld|
|Sleutel|Controleren of de functie is ingeschakeld|
|Sleutel|Partner aan bedrijf toevoegen|
|Sleutel|Niet-geverifieerd domein toevoegen|
|Sleutel|Geverifieerd domein toevoegen|
|Sleutel|Bedrijf maken|
|Sleutel|Bedrijfsinstellingen maken|
|Sleutel|Bedrijfsinstellingen verwijderen|
|Sleutel|Niveau van partner verlagen|
|Sleutel|Map verwijderd|
|Anders|Map permanent verwijderd|
|Anders|Map staat gepland voor verwijdering|
|Resource|Niveau van bedrijf verhogen tot partner|
|Resource|Rights Management-eigenschappen leegmaken|
|Resource|Partner uit bedrijf verwijderen|
|Resource|Niet-geverifieerd domein verwijderen|
|Resource|Geverifieerd domein verwijderen|
|Resource|Bedrijfsgegevens instellen|
|Resource|DirSync-functie instellen|
|Resource|DirSyncEnabled-vlag instellen|
|Resource|Partnerschap instellen|
|Resource|Drempelwaarde voor onopzettelijk verwijderen instellen|
|Resource|Door het bedrijf toegestane gegevenslocatie instellen|
|Resource|Multinationale functie inschakelen|
|Resource|Mapfunctie voor tenant instellen|
|Resource|Domeinverificatie instellen|
|Resource|Federatie-instellingen voor domein instellen|
|Resource|Wachtwoordbeleid instellen|
|Resource|Rights Management-eigenschappen instellen|
|Resource|Bedrijf bijwerken|
|Resource|Bedrijfsinstellingen bijwerken|
|Resource|Domein bijwerken|
|Resource|Domein verifiëren|
|Resource|Door e-mail geverifieerd domein verifiëren|
|Resource|Onboarding|
|Resource|Waarschuwingsinstellingen bijwerken|
|Resource|Instellingen voor wekelijkse samenvatting bijwerken|
|Resource|Write-back van wachtwoord voor map uitschakelen|
|Resource|Write-back van wachtwoord voor map inschakelen|
|Resource|App-roltoewijzing aan groep toevoegen|
|Resource|Groep toevoegen|
|Resource|Lid aan groep toevoegen|
|Resource|Eigenaar aan groep toevoegen|
|Resource|Groepsinstellingen maken|
|Resource|Groep verwijderen|
|Resource|Groepsinstellingen verwijderen|
|Resource|Het toepassen van een groepslicentie op gebruikers voltooien|
|Resource|Groep definitief verwijderen|
|Resource|App-roltoewijzing uit groep verwijderen|
|Resource|Lid uit groep verwijderen|
|Resource|Eigenaar uit groep verwijderen|
|Resource|Groep herstellen|
|Resource|Groepslicentie instellen|
|Resource|Groep instellen op beheer door gebruiker|
|Resource|Starten met het toepassen van een groepslicentie op gebruikers|
|Resource|Herberekening van groepslicentie activeren|
|Resource|Groep bijwerken|
|Resource|Groepsinstellingen bijwerken|
|Resource|Lid toevoegen|
|Resource|Groep maken|
|Resource|Groep verwijderen|
|Resource|Lid verwijderen|
|Resource|Groep bijwerken|
|Resource|Aanvraag in behandeling voor deelname aan een groep goedkeuren|
|Resource|Aanvraag in behandeling voor deelname aan een groep annuleren|
|Resource|Levenscyclusbeheerbeleid maken|
|Resource|Aanvraag in behandeling voor deelname aan een groep verwijderen|
|Resource|Aanvraag in behandeling voor deelname aan een groep weigeren|
|Resource|Groep vernieuwen|
|Resource|Deelname aan een groep aanvragen|
|Resource|Eigenschappen voor dynamische groep instellen|
|Resource|Levenscyclusbeheerbeleid bijwerken|
|Resource|Een sleutel op basis van een ASCII-geheim aan een CPIM-sleutelcontainer toevoegen|
|Resource|Een sleutel aan een CPIM-sleutelcontainer toevoegen|
|Resource|Een CPIM-sleutelcontainer verwijderen|
|Resource|Een sleutelcontainer verwijderen|
|Resource|Actieve sleutelmetagegevens voor sleutelcontainer in JWK ophalen|
|Resource|Metagegevens voor sleutelcontainer ophalen|
|Resource|Hiermee haalt u een CPIM-sleutelcontainer in jwk-indeling op|
|Resource|Hiermee haalt u een lijst met sleutelcontainers in de tenant op|
|Resource|Een back-up van een CPIM-sleutelcontainer herstellen|
|Resource|Sleutelcontainer opslaan|
|Resource|Een versleutelde CPIM-sleutel uploaden|
|Resource|Een autorisatiecode aan de toepassing uitgeven|
|Resource|Een id_token voor de toepassing uitgeven|


## <a name="core-directory"></a>Hoofddirectory

|Controlecategorie|Activiteit|
|---|---|
|Administratieve eenheden beheren|Een enkel type risico detectie downloaden|
|Administratieve eenheden beheren|Beheerders en status van aanmelding voor wekelijkse samenvatting downloaden|
|Administratieve eenheden beheren|Alle typen risico detectie downloaden|
|Administratieve eenheden beheren|Beschik bare risico detecties voor gebruikers downloaden|
|Administratieve eenheden beheren|Gebruikers voor wie wordt aangegeven dat ze risico lopen downloaden|
|Toepassingsbeheer|Verwerkte batchuitnodigingen|
|Toepassingsbeheer|Geüploade batchuitnodigingen|
|Toepassingsbeheer|Eigenaar aan beleid toevoegen|
|Toepassingsbeheer|Beleid toevoegen|
|Toepassingsbeheer|Beleid verwijderen|
|Toepassingsbeheer|Beleidsreferenties verwijderen|
|Toepassingsbeheer|Beleid bijwerken|
|Toepassingsbeheer|Beleid voor MFA-registratie instellen|
|Toepassingsbeheer|Beleid voor aanmeldingsrisico's instellen|
|Toepassingsbeheer|Beleid voor gebruikersrisico's instellen|
|Toepassingsbeheer|Gebruiksvoorwaarden accepteren|
|Toepassingsbeheer|Gebruiksvoorwaarden maken|
|Toepassingsbeheer|Gebruiksvoorwaarden afwijzen|
|Toepassingsbeheer|Gebruiksvoorwaarden verwijderen|
|Toepassingsbeheer|Gebruiksvoorwaarden bewerken|
|Toepassingsbeheer|Gebruiksvoorwaarden publiceren|
|Toepassingsbeheer|Publicatie van gebruiksvoorwaarden ongedaan maken|
|Toepassingsbeheer|TLS/SSL-certificaat van toepassing toevoegen|
|Toepassingsbeheer|TLS-binding verwijderen|
|Toepassingsbeheer|Connector registreren|
|Toepassingsbeheer|AdminPolicyDatas-RemoveResources|
|Toepassingsbeheer|AdminPolicyDatas-SetResources|
|Toepassingsbeheer|AdminUserJourneys-GetResources|
|Mapbeheer|AdminUserJourneys-RemoveResources|
|Mapbeheer|AdminUserJourneys-SetResources|
|Mapbeheer|IdentityProvider maken|
|Mapbeheer|Een nieuwe AdminUserJourney maken|
|Mapbeheer|Gelokaliseerde resource-json maken|
|Mapbeheer|Nieuwe aangepaste IDP maken|
|Mapbeheer|Nieuwe IDP maken|
|Mapbeheer|Een B2C-mapresource maken of bijwerken|
|Mapbeheer|Beleid maken|
|Mapbeheer|TrustFramework-beleid maken|
|Mapbeheer|TrustFramework-beleid maken met configureerbaar voorvoegsel|
|Mapbeheer|Gebruikerskenmerk maken|
|Mapbeheer|CreateTrustFrameworkPolicy|
|Mapbeheer|IDP verwijderen|
|Mapbeheer|IdentityProvider verwijderen|
|Mapbeheer|Een B2C-mapresource verwijderen|
|Mapbeheer|TrustFramework-beleid verwijderen|
|Mapbeheer|Gebruikerskenmerk verwijderen|
|Mapbeheer|B2C-mapresources in een resourcegroep ophalen|
|Mapbeheer|B2C-mapresources in een abonnement ophalen|
|Mapbeheer|Aangepaste IDP ophalen|
|Mapbeheer|IDP ophalen|
|Mapbeheer|Een B2C-Directory resource ophalen|
|Mapbeheer|Een gebruikersbeleving ophalen|
|Mapbeheer|Toegestane toepassingsclaims voor gebruikersbeleving ophalen|
|Mapbeheer|Toegestane zelf-gecontroleerde claims voor gebruikersbeleving ophalen|
|Mapbeheer|Toegestane zelf-gecontroleerde claims voor beleid ophalen|
|Mapbeheer|Lijst met beschikbare uitvoerclaims ophalen|
|Mapbeheer|Inhouddefinities voor gebruikersbeleving ophalen|
|Mapbeheer|IDP's voor een specifieke beheerstroom ophalen|
|Mapbeheer|Lijst van alle beheerstromen ophalen|
|Mapbeheer|Lijst met labels voor alle beheerstromen voor alle gebruikers ophalen|
|Groepsbeheer|Groeps leden bulksgewijs downloaden-gestart|
|Groepsbeheer|Groeps leden bulksgewijs downloaden-voltooid|
|Groepsbeheer|Groeps leden voor bulk import-gestart|
|Groepsbeheer|Groeps leden Bulk Import-voltooid|
|Groepsbeheer|Groeps leden bulksgewijs verwijderen-gestart|
|Groepsbeheer|Groeps leden bulksgewijs verwijderen-voltooid|
|Groepsbeheer|Bulksgewijs downloaden van groepen-gestart|
|Groepsbeheer|Groepen voor bulksgewijs downloaden-voltooid|
|Groepsbeheer|Lijst met tenants voor een gebruiker ophalen|
|Groepsbeheer|Zelf-gecontroleerde claims voor lokale accounts ophalen|
|Groepsbeheer|Gelokaliseerde resource-json ophalen|
|Groepsbeheer|Bewerkingen van de resourceprovider Microsoft.AzureActiveDirectory ophalen|
|Groepsbeheer|Beleidsregels ophalen|
|Groepsbeheer|Beleid ophalen|
|Groepsbeheer|Lijst met ondersteunde IDP's ophalen|
|Groepsbeheer|Lijst met ondersteunde IDP's van de gebruikersbeleving ophalen|
|Groepsbeheer|Voor tenant gedefinieerde, aangepaste IDP-lijst ophalen|
|Groepsbeheer|Voor tenant gedefinieerde IDP-lijst ophalen|
|Groepsbeheer|Voor tenant gedefinieerde, lokale IDP-lijst ophalen|
|Groepsbeheer|Tenantgegevens voor een gebruiker ophalen voor het maken van resources|
|Groepsbeheer|De standaard ondersteunde cultuur voor CPIM ophalen|
|Groepsbeheer|De gegevens van een beheerstroom ophalen|
|Groepsbeheer|De lijst met UserJourneys voor deze tenant ophalen|
|Groepsbeheer|De set beschikbare ondersteunde culturen voor CPIM ophalen|
|Groepsbeheer|TrustFramework-beleid ophalen|
|Groepsbeheer|TrustFramework-beleid als xml ophalen|
|Groepsbeheer|Gebruikerskenmerk ophalen|
|Beleidsbeheer|Gebruikerskenmerken ophalen|
|Beleidsbeheer|Lijst met gebruikersbelevingen ophalen|
|Beleidsbeheer|GetIEFPolicies|
|Beleidsbeheer|GetIdentityProviders|
|Beleidsbeheer|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Resources verplaatsen|
|Resource|IdentityProvider patchen|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Een gebruikersbeleving verwijderen|
|Resource|Aangepaste IDP bijwerken|
|Resource|IDP bijwerken|
|Resource|Lokale IDP bijwerken|
|Resource|Een B2C-mapresource bijwerken|
|Resource|Beleid bijwerken|
|Resource|Abonnementstatus bijwerken|
|Rolbeheer|Gebruikerskenmerk bijwerken|
|Rolbeheer|Resources verplaatsen valideren|
|Rolbeheer|Apparaat toevoegen|
|Rolbeheer|Apparaatconfiguratie toevoegen|
|Rolbeheer|Geregistreerde eigenaar aan apparaat toevoegen|
|Rolbeheer|Geregistreerde gebruikers aan apparaat toevoegen|
|Rolbeheer|Apparaat verwijderen|
|Rolbeheer|Apparaatconfiguratie verwijderen|
|Rolbeheer|Apparaat niet meer conform|
|Rolbeheer|Apparaat wordt niet meer beheerd|
|Gebruikersbeheer|AccessReview_Review|
|Gebruikersbeheer|AccessReview_Update|
|Gebruikersbeheer|ActivationAborted|
|Gebruikersbeheer|ActivationApproved|
|Gebruikersbeheer|ActivationCanceled|
|Gebruikersbeheer|ActivationRequested|
|Gebruikersbeheer|In aanmerking komend lid aan rol toevoegen|
|Gebruikersbeheer|Lid aan rol toevoegen|
|Gebruikersbeheer|Roltoewijzing aan roldefinitie toevoegen|
|Gebruikersbeheer|Rol vanuit sjabloon toevoegen|
|Gebruikersbeheer|Scoped lid aan rol toevoegen|
|Gebruikersbeheer|Toegevoegd|
|Gebruikersbeheer|Toewijzen|
|Gebruikersbeheer|Bulksgewijs gemaakte gebruikers maken-gestart|
|Gebruikersbeheer|Gebruikers bulksgewijs maken-voltooid|
|Gebruikersbeheer|Gebruikers bulksgewijs verwijderen-gestart|
|Gebruikersbeheer|Gebruikers bulksgewijs verwijderen-voltooid|
|Gebruikersbeheer|Gebruikers met massale down load-gestart|
|Gebruikersbeheer|Gebruikers bulksgewijs downloaden-voltooid|
|Gebruikersbeheer|Verwijderde gebruikers bulksgewijs herstellen-gestart|
|Gebruikersbeheer|Verwijderde gebruikers bulksgewijs herstellen-voltooid|
|Gebruikersbeheer|Gebruikers met massale uitnodiging-gestart|
|Gebruikersbeheer|Bulk-uitnodiging voor gebruikers-voltooid|
|Gebruikersbeheer|Geregistreerde eigenaar van apparaat verwijderen|
|Gebruikersbeheer|Geregistreerde gebruikers van apparaat verwijderen|
|Gebruikersbeheer|In aanmerking komen lid uit rol verwijderen|
|Gebruikersbeheer|Lid uit rol verwijderen|
|Gebruikersbeheer|Roltoewijzing uit roldefinitie verwijderen|
|Gebruikersbeheer|Scoped lid uit rol verwijderen|
|Gebruikersbeheer|Apparaat bijwerken|
|Gebruikersbeheer|Apparaatconfiguratie bijwerken|
|Gebruikersbeheer|Rol bijwerken|






## <a name="identity-protection"></a>Identiteitsbeveiliging

|Controlecategorie|Activiteit|
|---|---|
|Mapbeheer|Verhogen|
|Mapbeheer|Verwijderd|
|Mapbeheer|Wijzigingen van de rolinstelling|
|Anders|ScanAlertsNow|
|Anders|Registreren|
|Anders|Bevoegdheden verlagen|
|Anders|UpdateAlertSettings|
|Anders|UpdateCurrentState|
|Beleidsbeheer|Toegangsbeoordeling beëindigd|
|Beleidsbeheer|Goedkeurder toevoegen om goedkeuring te vragen|
|Beleidsbeheer|Revisor toevoegen voor toegang tot revisie|
|Gebruikersbeheer|Toegangsbeoordeling toepassen|
|Gebruikersbeheer|Toegangsbeoordeling maken|


## <a name="invited-users"></a>Uitgenodigde gebruikers

|Controlecategorie|Activiteit|
|---|---|
|Anders|Aanvraaggoedkeuring maken|
|Anders|Toegangsbeoordeling verwijderen|
|Gebruikersbeheer|Revisor verwijderen uit toegangsbeoordeling|
|Gebruikersbeheer|Toepassen van beoordelingsresultaat aanvragen|
|Gebruikersbeheer|Stoppen beoordeling aanvragen|
|Gebruikersbeheer|App-toewijzing beoordelen|
|Gebruikersbeheer|Groepslidmaatschap controleren|
|Gebruikersbeheer|RBAC-rollidmaatschap beoordelen|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Controlecategorie|Activiteit|
|---|---|
|Groepsbeheer|Aanvraag voor goedkeuring aanvraag beoordelen|
|Groepsbeheer|Toegangsbeoordeling bijwerken|
|Groepsbeheer|Instellingen voor de e-mailmelding van de toegangsbeoordeling bijwerken|
|Groepsbeheer|Instelling voor aantal herhalingen van de toegangs beoordeling bijwerken|
|Groepsbeheer|Instelling voor toegangs beoordeling duur van terugkeer patroon in dagen bijwerken|
|Gebruikersbeheer|Instelling van eind type van terugkeer patroon van toegangs beoordeling bijwerken|
|Gebruikersbeheer|Instelling voor toegangs beoordeling van type terugkeer patroon bijwerken|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Controlecategorie|Activiteit|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Toegevoegd|
|PIM|AddedOutsidePIM|
|PIM|Toewijzen|
|PIM|DismissAlert|
|PIM|Verhogen|
|PIM|ReactivateAlert|
|PIM|Verwijderd|
|PIM|RemovedOutsidePIM|
|PIM|Stoppen beoordeling aanvragen|
|PIM|Wijzigingen van de rolinstelling|
|PIM|ScanAlertsNow|
|PIM|Registreren|
|PIM|Toewijzing|
|PIM|Bevoegdheden verlagen|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Groepsbeheer via selfservice

|Controlecategorie|Activiteit|
|---|---|
|Groepsbeheer|Gebruikerswachtwoord opnieuw instellen|
|Groepsbeheer|Gebruiker herstellen|
|Groepsbeheer|Geforceerd wijzigen van gebruikerswachtwoord instellen|
|Groepsbeheer|Gebruikersbeheerder instellen|
|Groepsbeheer|Instellen dat oath-token-metagegevens voor gebruikers zijn ingeschakeld|
|Groepsbeheer|Tijdstempel StsRefreshTokenValidFrom bijwerken|
|Groepsbeheer|Externe geheimen bijwerken|
|Groepsbeheer|Gebruiker bijwerken|
|Groepsbeheer|Beheerder genereert een tijdelijk wachtwoord|


## <a name="self-service-password-management"></a>Wachtwoordbeheer via selfservice

|Controlecategorie|Activiteit|
|---|---|
|Mapbeheer|Beheerders vereisen dat de gebruiker het wachtwoord opnieuw instelt|
|Mapbeheer|Externe gebruiker aan toepassing toewijzen|
|Gebruikersbeheer|E-mail niet verzonden, gebruiker heeft zich afgemeld|
|Gebruikersbeheer|Externe gebruiker uitnodigen|
|Gebruikersbeheer|Uitnodiging externe gebruiker inwisselen|
|Gebruikersbeheer|Virale tenant maken|
|Gebruikersbeheer|Virale gebruiker maken|
|Gebruikersbeheer|Gebruikerswachtwoordregistratie|
|Gebruikersbeheer|Gebruikerswachtwoordherstel|
|Gebruikersbeheer|Selfservice voor wachtwoord opnieuw instellen geblokkeerd|


## <a name="terms-of-use"></a>Gebruiksvoorwaarden

|Controlecategorie|Activiteit|
|---|---|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden accepteren|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden maken|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden afwijzen|
|Gebruiksvoorwaarden|Toestemming verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden bewerken|
|Gebruiksvoorwaarden|Gebruiks voorwaarden verlopen|
|Gebruiksvoorwaarden|Gebruiks voorwaarden voor hard verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden publiceren|
|Gebruiksvoorwaarden|Publicatie van gebruiksvoorwaarden ongedaan maken|


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure AD-rapporten](overview-reports.md).
- [Rapport controle logboeken](concept-audit-logs.md). 
- [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)