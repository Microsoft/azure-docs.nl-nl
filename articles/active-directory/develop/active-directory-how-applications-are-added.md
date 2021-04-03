---
title: Hoe en waarom apps worden toegevoegd aan Azure AD
titleSuffix: Microsoft identity platform
description: Wat betekent het voor een toepassing die wordt toegevoegd aan Azure AD en hoe worden deze daar weer geven?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/01/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 1f6fd0160988802e198ff9388cfeb3232b34b100
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96861116"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hoe en waarom toepassingen worden toegevoegd aan Azure AD

Er zijn twee representaties van toepassingen in azure AD:

* [Toepassings objecten](app-objects-and-service-principals.md#application-object) -hoewel er [uitzonde ringen](#notes-and-exceptions)zijn, kunnen toepassings objecten worden beschouwd als de definitie van een toepassing.
* [Service-principals](app-objects-and-service-principals.md#service-principal-object) : kan worden beschouwd als een exemplaar van een toepassing. Service-principals verwijzen doorgaans naar een toepassings object en er kan naar één toepassings object worden verwezen door meerdere service-principals in directory's.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Wat zijn toepassings objecten en waar komen ze vandaan?

U kunt [toepassings objecten](app-objects-and-service-principals.md#application-object) in de Azure portal beheren via de [app registraties](https://aka.ms/appregistrations) . Toepassings objecten beschrijven de toepassing naar Azure AD en kunnen worden beschouwd als de definitie van de toepassing, zodat de service kan weten hoe tokens moeten worden uitgegeven aan de toepassing op basis van de instellingen ervan. Het toepassings object bevinden zich alleen in de basismap, zelfs als dit een multi tenant-toepassing is die service-principals in andere mappen ondersteunt. Het toepassings object kan bestaan uit een van de volgende (evenals aanvullende informatie die hier niet wordt vermeld):

* Naam, logo en uitgever
* Omleidings-Uri's
* Geheimen (symmetrische en/of asymmetrische sleutels die worden gebruikt voor het verifiëren van de toepassing)
* API-afhankelijkheden (OAuth)
* Gepubliceerde Api's/bronnen/bereiken (OAuth)
* App-rollen (RBAC)
* SSO-meta gegevens en-configuratie
* Meta gegevens en configuratie van gebruikers inrichten
* Meta gegevens en configuratie van proxy

Toepassings objecten kunnen worden gemaakt via meerdere paden, waaronder:

* Toepassings registraties in de Azure Portal
* Een nieuwe toepassing maken met Visual Studio en deze configureren voor gebruik van Azure AD-verificatie
* Wanneer een beheerder een toepassing toevoegt uit de app-galerie (waardoor ook een service-principal wordt gemaakt)
* De Microsoft Graph-API of Power shell gebruiken om een nieuwe toepassing te maken
* Veel andere onderdelen, inclusief verschillende ontwikkel ervaringen in Azure en in API Explorer-ervaringen op ontwikkel centrums

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Wat zijn service-principals en waar komen ze vandaan?

U kunt de [service-principals](app-objects-and-service-principals.md#service-principal-object) in de Azure portal beheren via de ervaring van [bedrijfs toepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) . Service-principals zijn datgene wat een toepassing is die verbinding maakt met Azure AD en kan worden beschouwd als het exemplaar van de toepassing in uw Directory. Voor een bepaalde toepassing kan het Maxi maal één toepassings object hebben (dat is geregistreerd in een ' Home ' Directory) en een of meer Service Principal-objecten die exemplaren van de toepassing vertegenwoordigen in elke map waarin deze wordt uitgevoerd. 

De service-principal kan het volgende omvatten:

* Een verwijzing naar een toepassings object via de eigenschap toepassings-ID
* Records van lokale gebruikers-en groeps toepassingen-roltoewijzingen
* Records van lokale gebruikers-en beheerders machtigingen die aan de toepassing zijn verleend
  * Bijvoorbeeld: machtiging voor de toepassing om toegang te krijgen tot de e-mail van een bepaalde gebruiker
* Records van lokale beleids regels met beleid voor voorwaardelijke toegang
* Records met alternatieve lokale instellingen voor een toepassing
  * Claim transformatie regels
  * Kenmerk toewijzingen (gebruikers inrichten)
  * Directory-specifieke app-rollen (als de toepassing aangepaste rollen ondersteunt)
  * Directory-specifieke naam of logo

Net als toepassings objecten kunnen service-principals ook worden gemaakt via meerdere paden, waaronder:

* Wanneer gebruikers zich aanmelden bij een toepassing van derden die is geïntegreerd met Azure AD
  * Tijdens het aanmelden wordt gebruikers gevraagd om machtigingen aan te geven voor de toepassing om toegang te krijgen tot hun profiel en andere machtigingen. De eerste persoon die toestemming verleent, veroorzaakt een service-principal die de toepassing vertegenwoordigt die moet worden toegevoegd aan de Directory.
* Wanneer gebruikers zich aanmelden bij micro soft onlineservices zoals [Microsoft 365](https://products.office.com/)
  * Wanneer u zich abonneert op Microsoft 365 of een proef versie begint, worden een of meer service-principals gemaakt in de map die de verschillende services vertegenwoordigt die worden gebruikt voor het leveren van alle functies die zijn gekoppeld aan Microsoft 365.
  * Sommige Microsoft 365 Services, zoals share point, maken voortdurend service-principals voor veilige communicatie tussen onderdelen, waaronder werk stromen.
* Wanneer een beheerder een toepassing toevoegt uit de app-galerie (Hiermee wordt ook een onderliggend app-object gemaakt)
* Een toepassing voor het gebruik van [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md) toevoegen
* Een toepassing verbinden voor eenmalige aanmelding met SAML of een eenmalige aanmelding met een wacht woord (SSO)
* Programmatisch via de Microsoft Graph-API of Power shell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hoe zijn toepassings objecten en service-principals gerelateerd aan elkaar?

Een toepassing heeft één toepassings object in de basismap waarnaar wordt verwezen door een of meer service-principals in elk van de directory's waar het actief is (inclusief de basismap van de toepassing).

![Hiermee wordt de relatie tussen app-objecten en service-principals weer gegeven][apps_service_principals_directory]

In het voor gaande diagram onderhoudt micro soft twee directory's intern (weer gegeven aan de linkerkant) die worden gebruikt voor het publiceren van toepassingen:

* Eén voor micro soft-apps (adreslijst van micro soft-Services)
* Een voor vooraf geïntegreerde toepassingen van derden (app Gallery Directory)

Toepassings uitgevers/leveranciers die met Azure AD zijn geïntegreerd, moeten een publicatiemap hebben (weer gegeven aan de rechter kant als ' een enkele SaaS-Directory ').

Toepassingen die u zelf toevoegt (wordt weer gegeven als **app (uw)** in het diagram) zijn:

* Apps die u hebt ontwikkeld (geïntegreerd met Azure AD)
* Apps waarmee u verbinding hebt gemaakt voor eenmalige aanmelding
* Apps die u hebt gepubliceerd met behulp van de Azure AD-toepassings proxy

### <a name="notes-and-exceptions"></a>Opmerkingen en uitzonde ringen

* Niet alle service-principals verwijzen terug naar een toepassings object. Toen Azure AD de services die aan toepassingen zijn geleverd, beperkt waren en de Service-Principal voldoende was om een toepassings identiteit in te richten. De oorspronkelijke Service-Principal bevond zich dichter bij de vorm van het Windows Server Active Directory-Service account. Daarom is het nog steeds mogelijk om service-principals te maken via verschillende routes, zoals het gebruik van Azure AD Power shell, zonder eerst een toepassings object te maken. De Microsoft Graph-API vereist een toepassings object voordat u een Service-Principal maakt.
* Niet alle hierboven beschreven informatie wordt momenteel programmatisch weer gegeven. De volgende opties zijn alleen beschikbaar in de gebruikers interface:
  * Claim transformatie regels
  * Kenmerk toewijzingen (gebruikers inrichten)
* Zie de referentie documentatie voor de Microsoft Graph-API voor meer informatie over de Service-Principal en toepassings objecten:
  * [Toepassing](/graph/api/resources/application)
  * [Service-principal](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Waarom kunnen toepassingen worden geïntegreerd met Azure AD?

Toepassingen worden toegevoegd aan Azure AD om gebruik te maken van een of meer van de services die het biedt, waaronder:

* Verificatie en autorisatie van toepassingen
* Gebruikers verificatie en-autorisatie
* Eenmalige aanmelding met Federatie of wacht woord
* Gebruikers inrichten en synchronisatie
* Op rollen gebaseerd toegangs beheer: gebruik de Directory om toepassings rollen te definiëren voor het uitvoeren van op rollen gebaseerde autorisatie controles in een toepassing
* OAuth-autorisatie Services: wordt gebruikt door Microsoft 365 en andere micro soft-toepassingen om toegang te verlenen tot Api's/bronnen
* Toepassingen publiceren en proxy-een toepassing publiceren vanuit een particulier netwerk naar Internet
* Kenmerken van de extensie van het Directory-schema: [Breid het schema van de Service-Principal en gebruikers objecten](active-directory-schema-extensions.md) uit voor het opslaan van aanvullende gegevens in azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Wie is gemachtigd om toepassingen toe te voegen aan mijn Azure AD-exemplaar?

Hoewel er sommige taken zijn die alleen voor globale beheerders kunnen worden uitgevoerd (zoals het toevoegen van toepassingen uit de app-galerie en het configureren van een toepassing voor het gebruik van de toepassings proxy), hebben alle gebruikers in uw Directory standaard rechten om toepassings objecten te registreren die ze ontwikkelen en wijzen op welke toepassingen ze delen/toegang tot hun organisatie gegevens geven via toestemming. Als een persoon de eerste gebruiker in uw directory is om zich aan te melden bij een toepassing en toestemming te verlenen, wordt een Service-Principal in uw Tenant gemaakt. anders wordt de informatie over het verlenen van toestemming opgeslagen op de bestaande service-principal.

Het toestaan van gebruikers om toepassingen te registreren en goed te keuren, kunnen aanvankelijk een geluid hebben over, maar het volgende bedenken:


* Toepassingen kunnen veel jaren gebruikmaken van Windows Server Active Directory voor gebruikers verificatie, zonder dat de toepassing moet worden geregistreerd of geregistreerd in de map. Nu heeft de organisatie de zicht baarheid verbeterd tot precies het aantal toepassingen dat de Directory gebruikt en voor wat doel einde.
* Door deze verantwoordelijkheden aan gebruikers te delegeren, wordt de nood zaak voor het registreren en publiceren van een door de beheerder gestuurde toepassing genegeerd. Met Active Directory Federation Services (ADFS) was het waarschijnlijk dat een beheerder een toepassing heeft toegevoegd als een Relying Party namens hun ontwikkel aars. Ontwikkel aars kunnen nu selfservice Services installeren.
* Gebruikers die zich aanmelden bij toepassingen die hun organisatie accounts gebruiken voor zakelijke doel einden, zijn goed. Als ze de organisatie daarna verlaten, verliezen ze automatisch de toegang tot hun account in de toepassing die ze gebruiken.
* Met een record welke gegevens zijn gedeeld met welke toepassing het goed is. Gegevens zijn verwisselbaar dan ooit en het is handig om een duidelijke record te hebben van wie de gegevens deelt met welke toepassingen.
* API-eigen aars die Azure AD voor OAuth gebruiken, beslissen precies welke machtigingen gebruikers kunnen verlenen aan toepassingen en aan welke machtigingen een beheerder moet instemmen. Alleen beheerders kunnen toestemming geven voor grotere bereiken en meer significante machtigingen, terwijl de toestemming van de gebruiker is afgestemd op de eigen gegevens en mogelijkheden van de gebruikers.
* Wanneer een gebruiker een toepassing toevoegt of toestaat om toegang te krijgen tot hun gegevens, kan de gebeurtenis worden gecontroleerd zodat u de controle rapporten kunt weer geven in de Azure Portal om te bepalen hoe een toepassing aan de Directory is toegevoegd.

Als u nog steeds wilt voor komen dat gebruikers in uw Directory toepassingen registreren en zich niet kunnen aanmelden bij toepassingen zonder goed keuring van de beheerder, zijn er twee instellingen die u kunt wijzigen om deze mogelijkheden uit te scha kelen:

* Om te voor komen dat gebruikers namens hun eigen naam worden gezonden:
  1. Ga in het Azure Portal naar de sectie [gebruikers instellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) onder bedrijfs toepassingen.
  2. Wijzigings **gebruikers kunnen toestemming geven voor apps die toegang hebben tot Bedrijfs gegevens namens hun naam** **.**
     
     > [!NOTE]
     > Als u besluit de toestemming van de gebruiker uit te scha kelen, moet een beheerder toestemming geven voor een nieuwe toepassing die een gebruiker nodig heeft om te worden gebruikt.

* Om te voor komen dat gebruikers hun eigen toepassingen registreren:
  1. Ga in het Azure Portal naar de sectie [gebruikers instellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) onder Azure Active Directory
  2. Wijzigings **gebruikers kunnen toepassingen registreren** op **Nee**.

> [!NOTE]
> Micro soft zelf maakt gebruik van de standaard configuratie, waarbij gebruikers toepassingen kunnen registreren en op hun eigen naam mogen instemmen.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
