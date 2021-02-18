---
title: Veelgestelde vragen (FAQ)-Azure Active Directory | Microsoft Docs
description: Veelgestelde vragen en antwoorden over Azure en Azure Active Directory, wachtwoord beheer en toegang tot toepassingen.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3b9f76eba9697d6ad47ecf8dc51689538923f88
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101094481"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Veelgestelde vragen over Azure Active Directory
Azure Active Directory (Azure AD) is een uitgebreide IDaaS-oplossing (Identity as a Service) waarin alle aspecten van identiteit, toegangsbeheer en beveiliging zijn opgenomen.

Zie [Wat is Azure Active Directory?](active-directory-whatis.md) voor meer informatie.


## <a name="access-azure-and-azure-active-directory"></a>Toegang tot Azure en Azure Active Directory
**V: Waarom krijg ik ' geen abonnementen gevonden ' wanneer ik Azure AD wil openen in de Azure Portal?**

**A:** Voor toegang tot Azure Portal moet elke gebruiker machtigingen hebben in een Azure-abonnement. Als u geen betaalde Microsoft 365 of Azure AD-abonnement hebt, moet u een gratis [Azure-account](https://azure.microsoft.com/free/
) of een betaald abonnement activeren.

Zie voor meer informatie:

* [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**V: wat is de relatie tussen Azure AD, Microsoft 365 en Azure?**

**A:** Azure AD biedt u een algemene identiteit en toegang tot alle webservices. Of u nu gebruikmaakt van Microsoft 365, Microsoft Azure, intune of anderen, u maakt al gebruik van Azure AD om aanmelding en toegangs beheer voor al deze services in te scha kelen.

Alle gebruikers die webservices kunnen gebruiken, worden gedefinieerd als gebruikersaccounts in een of meer exemplaren van Azure AD. U kunt deze accounts instellen voor gratis Azure AD-functies, zoals toegang tot cloudtoepassingen.

Betaalde services van Azure AD, zoals Enterprise Mobility + Security, vormen een aanvulling op andere webservices, zoals Microsoft 365 en Microsoft Azure met uitgebreide beheer-en beveiligings oplossingen op ondernemings niveau.

---

**V: wat zijn de verschillen tussen eigenaar en globale beheerder?**

**A:** De persoon die zich aanmeldt voor een Azure-abonnement wordt standaard toegewezen aan de rol van eigenaar voor Azure-resources. Een eigenaar kan een Microsoft-account of een werk-of school account gebruiken uit de directory waaraan het Azure-abonnement is gekoppeld.  Deze rol is gemachtigd om services in Azure Portal te beheren.

Als anderen zich moeten aanmelden en toegang moeten krijgen tot services met hetzelfde abonnement, kunt u ze de juiste [ingebouwde rol](../../role-based-access-control/built-in-roles.md)toewijzen. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

De persoon die zich aanmeldt voor een Azure-abonnement krijgt standaard de rol van globale beheerder voor de Directory. De globale beheerder heeft toegang tot alle functies van Azure AD-Directory. Azure AD heeft een andere set beheerders rollen voor het beheren van de Directory-en identiteits functies. Deze beheerders hebben toegang tot verschillende functies in de Azure Portal. De rol van de beheerder bepaalt wat ze kunnen doen, zoals het maken of bewerken van gebruikers, het toewijzen van beheerders rollen aan anderen, het opnieuw instellen van gebruikers wachtwoorden, het beheren van gebruikers licenties of het beheren van domeinen.  Zie [een gebruiker toewijzen aan beheerders rollen in azure Active Directory](active-directory-users-assign-role-azure-portal.md) en [beheerders rollen toewijzen in azure Active Directory](../roles/permissions-reference.md)voor meer informatie over Azure AD-Directory beheerders en hun rollen.

Daarnaast hebben betaalde services van Azure AD, zoals Enterprise Mobility + Security, een aanvulling op andere webservices, zoals Microsoft 365 en Microsoft Azure, met uitgebreide beheer-en beveiligings oplossingen op ondernemings niveau.

---
**V: Is er een rapport waarin staat wanneer mijn Azure AD-gebruikerslicenties verlopen?**

**A:** Geen.  Dit is momenteel niet beschikbaar.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Aan de slag met hybride Azure AD


**V: Hoe verlaat ik een tenant wanneer ik ben toegevoegd als samenwerker?**

**A:** Wanneer u als samenwerker bent toegevoegd aan de tenant van een andere organisatie, kunt u de 'tenantschakelaar' in de rechterbovenhoek gebruiken om te schakelen tussen tenants.  Op dit moment is er geen manier om de organisatie die u heeft uitgenodigd, te verlaten. Microsoft is bezig om deze functie te ontwikkelen.  Tot deze functie beschikbaar is, kunt u de organisatie die u heeft uitgenodigd, vragen om u uit de tenant te verwijderen.

---
**V: Hoe kan ik mijn on-premises directory verbinden met Azure AD?**

**A:** U kunt uw on-premises directory verbinden met Azure AD via Azure AD Connect.

Zie [Integrating your on-premises identities with Azure Active Directory](../hybrid/whatis-hybrid-identity.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.

---
**V: Hoe stel ik eenmalige aanmelding in tussen mijn on-premises directory en mijn cloudtoepassingen?**

**A:** U hoeft eenmalige aanmelding (SSO) alleen in te stellen tussen uw on-premises directory en Azure AD. Zo lang u uw cloudtoepassingen opent via Azure AD, wordt er met de service automatisch voor gezorgd dat uw gebruikers juist worden geverifieerd met hun on-premises referenties.

Het implementeren van SSO vanuit on-premises kan eenvoudig worden gerealiseerd met Federatie oplossingen als Active Directory Federation Services (AD FS) of door het configureren van de wachtwoord-hash-synchronisatie. U kunt beide opties eenvoudig implementeren met behulp van de wizard Azure AD Connect configuratie.

Zie [Integrating your on-premises identities with Azure Active Directory](../hybrid/whatis-hybrid-identity.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.

---
**V: Bevat Azure AD een selfserviceportal voor gebruikers in mijn organisatie?**

**A:** Ja, Azure AD bevat een speciaal [toegangsvenster](https://myapps.microsoft.com). Dit dient als selfserviceportal voor gebruikers en geeft toegang tot toepassingen. Als u een Microsoft 365 klant bent, kunt u veel van dezelfde mogelijkheden in de [Office 365-Portal](https://portal.office.com)vinden.

Zie [Introduction to the Access Panel](../user-help/my-apps-portal-end-user-access.md) (Inleiding tot het toegangsvenster) voor meer informatie.

---
**V: Helpt Azure AD mij bij het beheren van mijn on-premises infrastructuur?**

**A:** Klikt. Azure AD Connect Health is opgenomen in de Azure AD Premium-versie. Azure AD Connect Health helpt u om inzicht te verkrijgen in uw on-premises infrastructuur voor identiteiten en de synchronisatieservices.  

Zie [Monitor your on-premises identity infrastructure and synchronization services in the cloud](../hybrid/whatis-azure-ad-connect.md) (Uw on-premises infrastructuur voor identiteiten en synchronisatieservices in de cloud controleren) voor meer informatie.  

---
## <a name="password-management"></a>Wachtwoordbeheer
**V: kan ik het terugschrijven van wacht woorden van Azure AD gebruiken zonder wachtwoord synchronisatie? (In dit scenario is het mogelijk Azure AD selfservice voor wacht woord opnieuw instellen (SSPR) te gebruiken voor het terugschrijven van het wacht woord en geen wacht woorden in de Cloud op te slaan?)**

**A:** U hoeft uw Active Directory-wachtwoorden niet te synchroniseren naar Azure AD om terugschrijven in te kunnen schakelen. In een federatieve omgeving is eenmalige aanmelding van Azure AD (SSO) afhankelijk van de on-premises directory voor het verifiëren van de gebruiker. In dit geval is er geen on-premises wachtwoord vereist dat moet worden bijgehouden in Azure AD.

---
**V: Hoelang duurt het voordat een wachtwoord on-premises is teruggeschreven naar Active Directory?**

**A:** Het terugschrijven van het wachtwoord wordt in realtime uitgevoerd.

Zie voor meer informatie [Getting started with password management](../authentication/tutorial-enable-sspr.md).

---
**V: Kan ik de functie Terugschrijven van wachtwoord gebruiken met wachtwoorden die worden beheerd door een beheerder?**

**A:** Ja, als u Terugschrijven van wachtwoord hebt ingeschakeld, worden de wachtwoordbewerkingen van een beheerder teruggeschreven naar uw on-premises omgeving.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Zie [Password management frequently asked questions](../authentication/active-directory-passwords-faq.md) (Veelgestelde vragen over wachtwoordbeheer) voor meer antwoorden op vragen over wachtwoorden.
---
**V: wat kan ik doen als ik mijn bestaande Microsoft 365/Azure AD-wacht woord niet meer weet tijdens het wijzigen van mijn wacht woord?**

**A:** In een dergelijke situatie zijn er meerdere oplossingen.  Gebruik selfservice voor wachtwoordherstel (SSPR) als deze optie beschikbaar is.  Of SSPR werkt, is afhankelijk van de configuratie.  Zie voor meer informatie [Hoe werkt de portal voor wachtwoordherstel?](../authentication/howto-sspr-deployment.md).

Voor Microsoft 365 gebruikers kan uw beheerder het wacht woord opnieuw instellen met behulp van de stappen die worden beschreven in [gebruikers wachtwoorden opnieuw instellen](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Beheerders kunnen wachtwoorden van Azure AD-accounts via een van de volgende opties opnieuw instellen:

- [Accounts in Azure Portal opnieuw instellen](active-directory-users-reset-password-azure-portal.md)
- [PowerShell gebruiken](/powershell/module/msonline/set-msoluserpassword)


---
## <a name="security"></a>Beveiliging
**V: Worden accounts na een bepaald aantal mislukte pogingen vergrendeld of wordt een meer geavanceerde strategie gebruikt?**

We gebruiken een geavanceerdere strategie om account te vergrendelen.  Dit is gebaseerd op het IP-adres van de aanvraag en op de ingevoerde wachtwoorden. De duur van de vergrendeling wordt ook langer op basis van de kans dat het een aanval betreft.  

**V: bepaalde (algemene) wacht woorden worden afgewezen met de berichten ' dit wacht woord is veel keer gebruikt ', maar dit verwijst naar wacht woorden die worden gebruikt in de huidige Active Directory?**

Dit verwijst naar wacht woorden die wereld wijd worden gebruikt, zoals de varianten van ' password ' en ' 123456 '.

**V: Worden aanmeldingsaanvragen van twijfelachtige bronnen (botnets, tor-eindpunten) in een B2C-tenant geblokkeerd of is hiervoor een Basic- of Premium-tenant vereist?**

We hebben wel een gateway waarmee aanvragen worden gefilterd en die enige bescherming biedt tegen botnets. Deze wordt toegepast op alle B2C-tenants.

## <a name="application-access"></a>Toegang tot toepassingen

**V: Waar vind ik een lijst met toepassingen die vooraf zijn geïntegreerd met Azure AD en de bijbehorende mogelijkheden?**

**A:** Azure AD bevat meer dan 2.600 vooraf geïntegreerde toepassingen van Microsoft, toepassingsserviceproviders en partners. Alle vooraf geïntegreerde toepassingen bieden ondersteuning voor eenmalige aanmelding (SSO). Via SSO kunt u uw bedrijfsreferenties gebruiken om toegang te krijgen tot uw apps. Een aantal toepassingen ondersteunt ook geautomatiseerde inrichting en het ongedaan maken van de inrichting.

Zie de [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) voor een volledige lijst met vooraf geïntegreerde toepassingen.

---
**V: Wat moet ik doen als de benodigde toepassing niet beschikbaar in de Azure AD Marketplace?**

**A:** Met Azure AD Premium kunt u elke gewenste toepassing toevoegen en configureren. Afhankelijk van de mogelijkheden van uw toepassing en uw voor keuren, kunt u SSO en automatische inrichting configureren.  

Zie voor meer informatie:

* [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](../manage-apps/configure-saml-single-sign-on.md) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](../app-provisioning/use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)

---
**V: Hoe melden gebruikers zich met behulp van Azure AD aan bij toepassingen?**

**A:** In Azure AD kunnen gebruikers op verschillende manieren toepassingen weergeven en openen, zoals:

* Het deelvenster Azure AD Access
* Het start programma voor de Microsoft 365-toepassing
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Zie voor meer informatie [eind gebruikers ervaring voor toepassingen](../manage-apps/end-user-experiences.md).

---
**V: Op welke manieren kunnen met behulp van Azure AD verificatie en eenmalige aanmelding worden ingesteld voor toepassingen?**

**A:** Azure AD biedt ondersteuning voor een groot aantal gestandaardiseerde protocollen voor verificatie en autorisatie, zoals SAML 2.0, OpenID Connect, OAuth 2.0 en WS-Federation. Daarnaast ondersteunt Azure AD het gebruik van wachtwoordkluizen en mogelijkheden voor automatische aanmelding voor apps die alleen ondersteuning bieden voor verificatie op basis van formulieren.  

Zie voor meer informatie:

* [Authentication Scenarios for Azure AD](../develop/authentication-vs-authorization.md) (Verificatiescenario's voor Azure AD)
* [Verificatie- en autorisatieprotocollen](/previous-versions/azure/dn151124(v=azure.100))
* [Eenmalige aanmelding voor toepassingen in azure AD](../manage-apps/what-is-single-sign-on.md)

---
**V: kan ik toepassingen toevoegen die ik on-premises gebruik?**

**A:** Via de Azure AD-toepassingsproxy hebt u eenvoudig en veilig toegang tot on-premises webtoepassingen die u kiest. U kunt deze toepassingen in Azure AD op dezelfde manier openen als uw SaaS-apps (Software as a Service). U hebt geen VPN nodig en u hoeft uw netwerkinfrastructuur niet te wijzigen.  

Zie voor meer informatie [Beveiligde externe toegang verschaffen voor on-premises toepassingen](../manage-apps/application-proxy.md).

---
**V: Hoe maak ik Multi-Factor Authentication verplicht voor gebruikers die toegang hebben tot een bepaalde toepassing?**

**A:** Met voorwaardelijke toegang van Azure AD kunt u voor elke toepassing een uniek toegangs beleid toewijzen. In uw beleid kunt u aangeven dat Multi-Factor Authentication altijd is vereist of alleen wanneer gebruikers niet zijn verbonden met het lokale netwerk.  

Zie [toegang tot Microsoft 365 en andere apps die zijn verbonden met Azure Active Directory beveiligen](../conditional-access/overview.md)voor meer informatie.

---
**V: Wat is geautomatiseerde gebruikersinrichting voor SaaS-apps?**

**A:** Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers-ID's in veel populaire cloud-apps (SaaS) automatiseren.

Zie [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory) voor meer informatie.

---
**V: Kan ik een veilige LDAP-verbinding instellen met Azure AD?**

**A:**  Geen. Azure AD biedt geen ondersteuning voor het LDAP-protocol (Lightweight Directory Access Protocol) of Secure LDAP rechtstreeks. Het is echter mogelijk om Azure AD Domain Services-instantie (Azure AD DS) in te scha kelen op uw Azure AD-Tenant met correct geconfigureerde netwerk beveiligings groepen via Azure-netwerken om LDAP-connectiviteit te bewerkstelligen. Zie [Configure secure LDAP for a Azure Active Directory Domain Services Managed Domain](../../active-directory-domain-services/tutorial-configure-ldaps.md) (Engelstalig) voor meer informatie.
