---
title: Rollen delegeren per beheer taak-Azure Active Directory | Microsoft Docs
description: Rollen voor het delegeren van identiteits taken in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ad48141c69d78096981b89758afd56089093021
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742927"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Beheerders rollen per beheer taak in Azure Active Directory

In dit artikel vindt u de informatie die nodig is voor het beperken van de beheerders machtigingen van een gebruiker door het toewijzen van Mini maal bevoegde rollen in Azure Active Directory (Azure AD). U vindt beheerders taken ingedeeld op functie gebied en de minst bevoegde rol die vereist is om elke taak uit te voeren, samen met aanvullende niet-globale beheerders rollen die de taak kunnen uitvoeren.

## <a name="application-proxy"></a>Toepassingsproxy

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Toepassings proxy-app configureren | Toepassings beheerder | 
Eigenschappen van connector groep configureren | Toepassings beheerder | 
Toepassings registratie maken wanneer de mogelijkheid voor alle gebruikers is uitgeschakeld | Toepassingsontwikkelaar | Cloud toepassings beheerder, toepassings beheerder
Connector groep maken | Toepassings beheerder | 
Connector groep verwijderen | Toepassings beheerder | 
Toepassingsproxy uitschakelen | Toepassings beheerder | 
Connector service downloaden | Toepassings beheerder | 
Alle configuratie lezen | Toepassings beheerder | 

## <a name="external-identitiesb2c"></a>Externe identiteiten/B2C

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Azure AD B2C mappen maken | Alle niet-gast gebruikers ([Zie documentatie](../fundamentals/users-default-permissions.md)) | 
B2C-toepassingen maken | Hoofdbeheerder | 
Bedrijfs toepassingen maken | Beheerder van de cloudtoepassing | Toepassingsbeheerder
B2C-beleid maken, lezen, bijwerken en verwijderen | Beheerder van B2C IEF-beleid | 
Id-providers maken, lezen, bijwerken en verwijderen | Beheerder van externe id-providers | 
Gebruikers stromen voor het opnieuw instellen van wacht woorden maken, lezen, bijwerken en verwijderen | Beheerder van externe id-gebruikersstromen | 
Gebruikers stromen voor het bewerken van profielen maken, lezen, bijwerken en verwijderen | Beheerder van externe id-gebruikersstromen | 
Gebruikers stromen voor het aanmelden maken, lezen, bijwerken en verwijderen | Beheerder van externe id-gebruikersstromen | 
Gebruikers stroom voor het aanmelden maken, lezen, bijwerken en verwijderen |Beheerder van externe id-gebruikersstromen | 
Gebruikers kenmerken maken, lezen, bijwerken en verwijderen | Beheerder van externe id-gebruikersstroomkenmerken | 
Gebruikers maken, lezen, bijwerken en verwijderen | Gebruikersbeheerder
Alle configuratie lezen | Algemene lezer | 
B2C-controle logboeken lezen | Algemene lezer ([Zie documentatie](../../active-directory-b2c/faq.md)) | 

> [!NOTE]
> Azure AD B2C globale lezers hebben niet dezelfde machtigingen als globale Azure AD-beheerders. Als u over Azure AD B2C globale beheerders bevoegdheden beschikt, moet u ervoor zorgen dat u zich in een Azure AD B2C Directory bevindt en niet een Azure AD-adres lijst.

## <a name="company-branding"></a>Aangepaste huisstijl

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Bedrijfshuisstijl configureren | Hoofdbeheerder | 
Alle configuratie lezen | Adreslijst lezers | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md))

## <a name="company-properties"></a>Eigenschappen van het bedrijf

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Eigenschappen van het bedrijf configureren | Hoofdbeheerder | 

## <a name="connect"></a>Verbinding maken

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Passthrough-verificatie | Hoofdbeheerder  | 
Alle configuratie lezen | Algemene lezer | Hoofdbeheerder  |
Naadloze eenmalige aanmelding | Hoofdbeheerder  | 

## <a name="cloud-provisioning"></a>Cloud inrichting

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Passthrough-verificatie | Hybrid Identity-beheerder  | 
Alle configuratie lezen | Algemene lezer | Hybrid Identity-beheerder  |
Naadloze eenmalige aanmelding | Hybrid Identity-beheerder  | 

## <a name="connect-health"></a>Connect Health

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Services toevoegen of verwijderen | Eigenaar ([Zie documentatie](../hybrid/how-to-connect-health-operations.md)) | 
Oplossingen voor synchronisatie fout Toep assen | Inzender ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Eigenaar
Meldingen configureren | Inzender ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Eigenaar
Instellingen configureren | Eigenaar ([Zie documentatie](../hybrid/how-to-connect-health-operations.md)) | 
Synchronisatie meldingen configureren | Inzender ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Eigenaar
ADFS-beveiligings rapporten lezen | Beveiligingslezer | Inzender, eigenaar
Alle configuratie lezen | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar
Synchronisatie fouten lezen | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar
Synchronisatie services lezen | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen weer geven | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen weer geven | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen voor synchronisatie service weer geven | Lezer ([Zie documentatie](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Inzender, eigenaar

## <a name="custom-domain-names"></a>Aangepaste domeinnamen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Domeinen beheren | Hoofdbeheerder | 
Alle configuratie lezen | Adreslijst lezers | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md))

## <a name="domain-services"></a>Domain Services

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Azure AD Domain Services exemplaar maken | Hoofdbeheerder | 
Alle Azure AD Domain Services taken uitvoeren | Groep Administrators van Azure AD DC ([Zie de documentatie](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) | 
Alle configuratie lezen | Lezer op een Azure-abonnement met AD DS-service | 

## <a name="devices"></a>Apparaten

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Apparaat uitschakelen | Beheerder van Cloud apparaat | 
Apparaat inschakelen | Beheerder van Cloud apparaat | 
Basis configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md)) | 
BitLocker-sleutels lezen | Beveiligingslezer | Wachtwoord beheerder, beveiligings beheerder

## <a name="enterprise-applications"></a>Bedrijfstoepassingen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Toestemming geven aan alle gedelegeerde machtigingen | Beheerder van de Cloud toepassing | Toepassings beheerder
Toestemming geven aan toepassings machtigingen die niet inclusief Microsoft Graph | Beheerder van de Cloud toepassing | Toepassings beheerder
Toestemming voor het Microsoft Graph van toepassings machtigingen | Beheerder voor bevoorrechte rollen | 
Toestemming geven aan toepassingen die toegang krijgen tot de eigen gegevens | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md)) | 
Bedrijfs toepassing maken | Beheerder van de Cloud toepassing | Toepassings beheerder
Toepassings proxy beheren | Toepassings beheerder | 
Gebruikers instellingen beheren | Hoofdbeheerder | 
Lees toegang voor een groep of een app | Beveiligingslezer | Beveiligings beheerder, gebruikers beheerder
Alle configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md)) | 
Bedrijfs toepassings toewijzingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder
Eigen aren van bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder
Eigenschappen van bedrijfs toepassing bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder
De inrichting van bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder
Self-service voor bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder
Eigenschappen voor eenmalige aanmelding bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Cloud toepassings beheerder, toepassings beheerder

## <a name="entitlement-management"></a>Rechtenbeheer
Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Resources toevoegen aan een catalogus | Gebruikersbeheerder | Met het rechten beheer kunt u deze taak delegeren aan de eigenaar van de catalogus ([Zie de documentatie](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners))
Share point online-sites toevoegen aan catalogus | Globale beheerder


## <a name="groups"></a>Groepen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Licentie toewijzen | Gebruikersbeheerder | 
Groep maken | Groeps beheerder | Gebruikersbeheerder
Toegangs beoordeling van een groep of een app maken, bijwerken of verwijderen | Gebruikersbeheerder | 
Groeps verloop beheren | Gebruikersbeheerder | 
Groepsinstellingen beheren | Groepsbeheerder | Gebruikersbeheerder | 
Alle configuraties lezen (met uitzonde ring van verborgen lidmaatschap) | Adreslijst lezers | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md))
Verborgen lidmaatschap lezen | Groepslid | Groeps eigenaar, wachtwoord beheerder, Exchange-beheerder, share point-beheerder, team beheerder, gebruikers beheerder
Lidmaatschap van groepen met verborgen lidmaatschap lezen | Helpdeskbeheerder | Gebruikers beheerder, team beheerder
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Groepslid maatschap bijwerken | Groeps eigenaar ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Gebruikersbeheerder
Groeps eigenaren bijwerken | Groeps eigenaar ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Gebruikersbeheerder
Eigenschappen van groep bijwerken | Groeps eigenaar ([Zie documentatie](../fundamentals/users-default-permissions.md)) | Gebruikersbeheerder
Groep verwijderen | Groeps beheerder | Gebruikersbeheerder

## <a name="identity-protection"></a>Identiteitsbeveiliging

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Waarschuwings meldingen configureren| Beveiligingsbeheer | 
MFA-beleid configureren en in-of uitschakelen| Beveiligingsbeheer | 
Beleid voor aanmeldings Risico's configureren en in-of uitschakelen| Beveiligingsbeheer | 
Beleid voor gebruikers Risico's configureren en in-of uitschakelen | Beveiligingsbeheer | 
Wekelijkse samen vattingen configureren | Beveiligingsbeheer| 
Alle risico detecties verwijderen | Beveiligingsbeheer | 
Probleem oplossen of negeren | Beveiligingsbeheer | 
Alle configuratie lezen | Beveiligingslezer | 
Alle risico detecties lezen | Beveiligingslezer | 
Lees lekken | Beveiligingslezer | 

## <a name="licenses"></a>Licenties

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Alle configuratie lezen | Adreslijst lezers | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md))
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Probeer of koop een abonnement | Factureringsbeheerder | 


## <a name="monitoring---audit-logs"></a>Controle logboeken

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Audit logboeken lezen | Rapport lezer | Beveiligings lezer, beveiligings beheerder

## <a name="monitoring---sign-ins"></a>Bewakings-aanmeldingen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Aanmeld logboeken lezen | Rapport lezer | Beveiligings lezer, beveiligings beheerder

## <a name="multi-factor-authentication"></a>Meervoudige verificatie

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle bestaande app-wacht woorden verwijderen die zijn gegenereerd door de geselecteerde gebruikers | Hoofdbeheerder | 
MFA uitschakelen | Hoofdbeheerder | 
MFA inschakelen | Hoofdbeheerder | 
Instellingen voor MFA-service beheren | Hoofdbeheerder | 
Vereisen dat geselecteerde gebruikers opnieuw contact methoden opgeven | Verificatiebeheerder | 
Multi-factor Authentication herstellen op alle onthouden apparaten  | Verificatiebeheerder | 

## <a name="mfa-server"></a>MFA-server

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruikers blok keren/deblokkeren | Hoofdbeheerder | 
Account vergrendeling configureren | Hoofdbeheerder | 
Cache regels configureren | Hoofdbeheerder | 
Fraude waarschuwing configureren | Hoofdbeheerder
Meldingen configureren | Hoofdbeheerder | 
Eenmalige bypass configureren | Hoofdbeheerder | 
Instellingen voor telefoon gesprek configureren | Hoofdbeheerder | 
Providers configureren | Hoofdbeheerder | 
Server instellingen configureren | Hoofdbeheerder | 
Activiteiten rapport lezen | Algemene lezer | 
Alle configuratie lezen | Algemene lezer | 
Server status lezen | Algemene lezer |  

## <a name="organizational-relationships"></a>Organisatierelaties

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Id-providers beheren | Beheerder van externe id-providers | 
Instellingen beheren | Hoofdbeheerder | 
Gebruiks voorwaarden beheren | Hoofdbeheerder | 
Alle configuratie lezen | Algemene lezer | 

## <a name="password-reset"></a>Wachtwoord opnieuw instellen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Verificatie methoden configureren | Hoofdbeheerder |
Aanpassing configureren | Hoofdbeheerder |
Melding configureren | Hoofdbeheerder |
On-premises integratie configureren | Hoofdbeheerder |
Eigenschappen voor het opnieuw instellen van wacht woorden configureren | Gebruikersbeheerder | Hoofdbeheerder
Registratie configureren | Hoofdbeheerder |
Alle configuratie lezen | Beveiligingsbeheer | Gebruikersbeheerder |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruikers toewijzen aan rollen | Beheerder voor bevoorrechte rollen | 
Rolinstellingen configureren | Beheerder voor bevoorrechte rollen | 
Controle activiteit weer geven | Beveiligingslezer | 
Rollidmaatschap weer geven | Beveiligingslezer | 

## <a name="roles-and-administrators"></a>Rollen en beheerders

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Roltoewijzingen beheren | Beheerder voor bevoorrechte rollen | 
Lees toegang voor een Azure AD-rol  | Beveiligingslezer | Beveiligings beheerder, beheerder van geprivilegieerde rol
Alle configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md)) | 

## <a name="security---authentication-methods"></a>Methoden voor beveiligings verificatie

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Verificatie methoden configureren | Hoofdbeheerder | 
Wachtwoord beveiliging configureren | Beveiligingsbeheerder
Slimme vergren deling configureren | Beveiligingsbeheerder
Alle configuratie lezen | Algemene lezer | 

## <a name="security---conditional-access"></a>Beveiliging-voorwaardelijke toegang

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Vertrouwde IP-adressen voor MFA configureren | Beheerder van voorwaardelijke toegang | 
Aangepaste besturings elementen maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Beleidsregels maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteits certificaat maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteits certificaat verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid uitschakelen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Aangepaste besturings elementen beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Benoemde locaties lezen | Beveiligingslezer | Beheerder van voorwaardelijke toegang, beveiligings beheerder

## <a name="security---identity-security-score"></a>Beveiligings Score beveiliging identiteit

Taak | Minst bevoorrechte rol | Aanvullende rollen | 
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Beveiligings Score lezen | Beveiligingslezer | Beveiligingsbeheerder
Gebeurtenis status bijwerken | Beveiligingsbeheerder | 

## <a name="security---risky-sign-ins"></a>Beveiligings risico op aanmeldingen

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | 
Risk ante aanmeldingen lezen | Beveiligingslezer | 

## <a name="security---users-flagged-for-risk"></a>Beveiliging-gebruikers die zijn gemarkeerd voor risico

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle gebeurtenissen sluiten | Beveiligingsbeheer | 
Alle configuratie lezen | Beveiligingslezer | 
Gebruikers lezen die zijn gemarkeerd voor risico | Beveiligingslezer | 

## <a name="users"></a>Gebruikers

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruiker toevoegen aan Directory-rol | Beheerder voor bevoorrechte rollen | 
Gebruiker toevoegen aan groep | Gebruikersbeheerder | 
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Gast gebruiker maken | Gast uitnodiging | Gebruikersbeheerder
Gebruiker maken | Gebruikersbeheerder | 
Gebruikers verwijderen | Gebruikersbeheerder | 
Vernieuwings tokens van beperkte beheerders ongeldig maken (Zie de documentatie) | Gebruikersbeheerder | 
Vernieuwen van tokens van niet-beheerders ongeldig maken (zie documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Vernieuwen van tokens van bevoegde beheerders ongeldig maken (zie documentatie) | Bevoorrechte verificatiebeheerder | 
Basis configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](../fundamentals/users-default-permissions.md) | 
Wacht woord opnieuw instellen voor beperkte beheerders (Zie de documentatie) | Gebruikersbeheerder | 
Het wacht woord van niet-beheerders opnieuw instellen (Zie de documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Wacht woord van bevoegde beheerders opnieuw instellen | Bevoorrechte verificatiebeheerder | 
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Alle eigenschappen bijwerken met uitzonde ring van Principal-naam van gebruiker | Gebruikersbeheerder | 
Principal-naam van gebruiker voor beperkte beheerders bijwerken (Zie de documentatie) | Gebruikersbeheerder | 
De eigenschap User Principal name voor bevoegde beheerders bijwerken (Zie de documentatie) | Hoofdbeheerder | 
Gebruikers instellingen bijwerken | Hoofdbeheerder | 
Verificatie methoden bijwerken | Verificatiebeheerder | Beheerder van geprivilegieerde authenticatie, globale beheerder


## <a name="support"></a>Ondersteuning

Taak | Minst bevoorrechte rol | Aanvullende rollen
---- | --------------------- | ----------------
Ondersteunings ticket verzenden | Servicebeheerder | Toepassings beheerder, Azure Information Protection beheerder, facturerings beheerder, Cloud toepassings beheerder, nalevings beheerder, Dynamics 365-beheerder, Desktop Analytics-beheerder, Exchange-beheerder, wachtwoord beheerder, intune-beheerder, Skype voor bedrijven-beheerder, Power BI beheerder, bevoegde verificatie beheerder, share point-beheerder, teams communicatie beheerder, team beheerder, gebruikers beheerder, werk plek Analytics-beheerder

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerders rollen toewijzen of verwijderen](manage-roles-portal.md)
* [Naslag informatie over Azure AD-beheerders rollen](permissions-reference.md)