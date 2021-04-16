---
title: Hoe en waarom apps worden toegevoegd aan Azure AD
titleSuffix: Microsoft identity platform
description: Wat betekent het dat een toepassing wordt toegevoegd aan Azure AD en hoe komen ze daar?
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
ms.openlocfilehash: ac02638dfdef4867e93e277175df82be18be66a7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530099"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Hoe en waarom toepassingen worden toegevoegd aan Azure AD

Er zijn twee weergaven van toepassingen in Azure AD:

* [Toepassingsobjecten:](app-objects-and-service-principals.md#application-object) hoewel er [uitzonderingen zijn,](#notes-and-exceptions)kunnen toepassingsobjecten worden beschouwd als de definitie van een toepassing.
* [Service-principals:](app-objects-and-service-principals.md#service-principal-object) kunnen worden beschouwd als een exemplaar van een toepassing. Service-principals verwijzen doorgaans naar een toepassingsobject en er kan naar één toepassingsobject worden verwezen door meerdere service-principals in meerdere directories.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Wat zijn toepassingsobjecten en waar komen ze vandaan?

U kunt [toepassingsobjecten](app-objects-and-service-principals.md#application-object) in de Azure Portal via de [ervaring App-registraties.](https://aka.ms/appregistrations) Toepassingsobjecten beschrijven de toepassing voor Azure AD en kunnen worden beschouwd als de definitie van de toepassing, zodat de service weet hoe tokens aan de toepassing moeten worden uitgeven op basis van de instellingen. Het toepassingsobject bestaat alleen in de basismap, zelfs als het een toepassing met meerdere tenants is die service-principals in andere directory's ondersteunt. Het toepassingsobject kan een van de volgende bevatten (evenals aanvullende informatie die hier niet wordt vermeld):

* Naam, logo en uitgever
* Omleidings-URI's
* Geheimen (symmetrische en/of asymmetrische sleutels die worden gebruikt om de toepassing te verifiëren)
* API-afhankelijkheden (OAuth)
* Gepubliceerde API's/resources/scopes (OAuth)
* App-rollen (RBAC)
* Metagegevens en configuratie voor eenmalige aanmelding
* Metagegevens en configuratie van gebruikers inrichten
* Proxymetagegevens en -configuratie

Toepassingsobjecten kunnen worden gemaakt via meerdere paden, waaronder:

* Toepassingsregistraties in de Azure Portal
* Een nieuwe toepassing maken met behulp Visual Studio configureren voor het gebruik van Azure AD-verificatie
* Wanneer een beheerder een toepassing toevoegt vanuit de app-galerie (waarmee ook een service-principal wordt gemaakt)
* De api Microsoft Graph PowerShell gebruiken om een nieuwe toepassing te maken
* Veel andere, waaronder verschillende ontwikkelaarservaringen in Azure en in API Explorer-ervaringen in ontwikkelaarscentrums

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Wat zijn service-principals en waar komen ze vandaan?

U kunt [service-principals](app-objects-and-service-principals.md#service-principal-object) in de Azure Portal via de [ervaring bedrijfstoepassingen.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Service-principals zijn bepalend voor een toepassing die verbinding maakt met Azure AD en kan worden beschouwd als het exemplaar van de toepassing in uw directory. Voor elke toepassing kan deze ten zeerste één toepassingsobject hebben (dat is geregistreerd in een basismap) en een of meer service-principalobjecten die exemplaren van de toepassing in elke directory vertegenwoordigen waarin deze fungeert. 

De service-principal kan het volgende omvatten:

* Een verwijzing terug naar een toepassingsobject via de eigenschap toepassings-id
* Records van lokale gebruikers- en groepstoewijzingen voor toepassingsrol
* Records van lokale gebruikers- en beheerdersmachtigingen die aan de toepassing zijn verleend
  * Bijvoorbeeld: machtiging voor de toepassing voor toegang tot e-mail van een bepaalde gebruiker
* Records van lokaal beleid, waaronder beleid voor voorwaardelijke toegang
* Records met alternatieve lokale instellingen voor een toepassing
  * Claimtransformatieregels
  * Kenmerktoewijzingen (gebruikers inrichten)
  * Mapspecifieke app-rollen (als de toepassing aangepaste rollen ondersteunt)
  * Mapspecifieke naam of logo

Net als toepassingsobjecten kunnen service-principals ook worden gemaakt via meerdere paden, waaronder:

* Wanneer gebruikers zich aanmelden bij een toepassing van derden die is geïntegreerd met Azure AD
  * Tijdens het aanmelden wordt gebruikers gevraagd om de toepassing toestemming te geven voor toegang tot hun profiel en andere machtigingen. De eerste persoon die toestemming geeft, zorgt ervoor dat een service-principal die de toepassing vertegenwoordigt, wordt toegevoegd aan de directory.
* Wanneer gebruikers zich aanmelden bij Microsoft onlineservices zoals [Microsoft 365](https://products.office.com/)
  * Wanneer u zich abonneert op Microsoft 365 of een proefversie start, worden een of meer service-principals gemaakt in de map die de verschillende services vertegenwoordigt die worden gebruikt voor het leveren van alle functionaliteit die is gekoppeld aan Microsoft 365.
  * Sommige Microsoft 365 zoals SharePoint maken voortdurend service-principals om veilige communicatie tussen onderdelen, waaronder werkstromen, mogelijk te maken.
* Wanneer een beheerder een toepassing toevoegt vanuit de app-galerie (hiermee wordt ook een onderliggend app-object gemaakt)
* Een toepassing toevoegen voor het gebruik van [de Azure AD-toepassingsproxy](../manage-apps/application-proxy.md)
* Een toepassing verbinden voor eenmalige aanmelding met behulp van SAML of eenmalige aanmelding (SSO) voor wachtwoorden
* Programmatisch via de Microsoft Graph API of PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Hoe zijn toepassingsobjecten en service-principals aan elkaar gerelateerd?

Een toepassing heeft één toepassingsobject in de basismap waarnaar wordt verwezen door een of meer service-principals in elk van de mappen waarin deze wordt gebruikt (inclusief de basismap van de toepassing).

![Toont de relatie tussen app-objecten en service-principals][apps_service_principals_directory]

In het voorgaande diagram onderhoudt Microsoft intern twee directories (weergegeven aan de linkerkant) die worden gebruikt om toepassingen te publiceren:

* Een voor Microsoft Apps (Microsoft-services directory)
* Een voor vooraf geïntegreerde toepassingen van derden (app-galeriemap)

Uitgevers/leveranciers van toepassingen die integreren met Azure AD, moeten een publicatiemap hebben (aan de rechterkant weergegeven als 'Sommige SaaS-directory's').

Toepassingen die u zelf toevoegt (weergegeven als **App (van u)** in het diagram, zijn onder andere:

* Apps die u hebt ontwikkeld (geïntegreerd met Azure AD)
* Apps die u hebt verbonden voor een enkele aanmelding
* Apps die u hebt gepubliceerd met behulp van de Azure AD-toepassingsproxy

### <a name="notes-and-exceptions"></a>Opmerkingen en uitzonderingen

* Niet alle service-principals wijzen terug naar een toepassingsobject. Toen Azure AD werd gebouwd, waren de services voor toepassingen beperkter en was de service-principal voldoende om een toepassings-id tot stand te brengen. De oorspronkelijke service-principal was dichter bij het Windows Server Active Directory serviceaccount. Daarom is het nog steeds mogelijk om service-principals te maken via verschillende paden, zoals het gebruik van Azure AD PowerShell, zonder eerst een toepassingsobject te maken. De Microsoft Graph API vereist een toepassingsobject voordat u een service-principal maakt.
* Niet alle informatie die hierboven wordt beschreven, wordt momenteel programmatisch beschikbaar gemaakt. Het volgende is alleen beschikbaar in de gebruikersinterface:
  * Claimtransformatieregels
  * Kenmerktoewijzingen (gebruikers inrichten)
* Zie de naslagdocumentatie over Microsoft Graph API voor meer informatie over de service-principal en toepassingsobjecten:
  * [Toepassing](/graph/api/resources/application)
  * [Service-principal](/graph/api/resources/serviceprincipal)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Waarom kunnen toepassingen worden geïntegreerd met Azure AD?

Toepassingen worden toegevoegd aan Azure AD om gebruik te maken van een of meer van de services die het biedt, waaronder:

* Toepassingsverificatie en -autorisatie
* Gebruikersverificatie en -autorisatie
* Eenmalige aanmelding met federatie of wachtwoord
* Gebruikers inrichten en synchroniseren
* Op rollen gebaseerd toegangsbeheer: gebruik de map om toepassingsrollen te definiëren voor het uitvoeren van op rollen gebaseerde autorisatiecontroles in een toepassing
* OAuth-autorisatieservices: gebruikt door Microsoft 365 en andere Microsoft-toepassingen om toegang tot API's/resources te autorisatie
* Publicatie en proxy van toepassingen: een toepassing publiceren vanuit een particulier netwerk op internet
* Kenmerken van directoryschema-extensies: [breid het schema van de service-principal en gebruikersobjecten](active-directory-schema-extensions.md) uit om aanvullende gegevens op te slaan in Azure AD 

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Wie heeft toestemming om toepassingen toe te voegen aan mijn Azure AD-exemplaar?

Hoewel er enkele taken zijn die alleen globale beheerders kunnen uitvoeren (zoals het toevoegen van toepassingen uit de app-galerie en het configureren van een toepassing voor het gebruik van de toepassingsproxy), hebben alle gebruikers in uw directory standaard rechten om toepassingsobjecten te registreren die ze ontwikkelen en om te bepalen welke toepassingen ze delen/toegang verlenen tot hun organisatiegegevens via toestemming. Als een persoon de eerste gebruiker in uw directory is die zich bij een toepassing heeft aanmelden en toestemming verleent, wordt er een service-principal in uw tenant gemaakt; anders wordt de informatie over het verlenen van toestemming opgeslagen op de bestaande service-principal.

Gebruikers toestaan om toepassingen te registreren en er toestemming voor te geven, klinkt in eerste instantie misschien niet erg, maar houd rekening met het volgende:


* Toepassingen kunnen al jaren gebruikmaken van Windows Server Active Directory voor gebruikersverificatie zonder dat de toepassing moet worden geregistreerd of vastgelegd in de directory. De organisatie heeft nu een betere zichtbaarheid van het aantal toepassingen dat de directory gebruikt en voor welk doel.
* Als u deze verantwoordelijkheden delegeert aan gebruikers, wordt de noodzaak van registratie en publicatie van een toepassing op basis van beheerdersrechten over het hoofd zien. Met Active Directory Federation Services (ADFS) moest een beheerder waarschijnlijk een toepassing toevoegen als relying party namens hun ontwikkelaars. Ontwikkelaars kunnen nu selfservice gebruiken.
* Gebruikers die zich voor zakelijke doeleinden aanmelden bij toepassingen met behulp van hun organisatieaccounts, is een goede zaak. Als ze vervolgens de organisatie verlaten, verliezen ze automatisch de toegang tot hun account in de toepassing die ze gebruikte.
* Het is goed om een record te hebben van welke gegevens zijn gedeeld met welke toepassing. Gegevens zijn transporteerbaarder dan ooit en het is handig om een duidelijk beeld te hebben van wie welke gegevens heeft gedeeld met welke toepassingen.
* API-eigenaren die Azure AD voor OAuth gebruiken, bepalen precies welke machtigingen gebruikers aan toepassingen kunnen verlenen en met welke machtigingen een beheerder akkoord moet gaan. Alleen beheerders kunnen toestemming geven voor grotere scopes en meer significante machtigingen, terwijl toestemming van de gebruiker is beperkt tot de eigen gegevens en mogelijkheden van de gebruikers.
* Wanneer een gebruiker een toepassing toevoegt of toegang geeft tot de gegevens, kan de gebeurtenis worden gecontroleerd, zodat u de controlerapporten in de Azure Portal kunt bekijken om te bepalen hoe een toepassing aan de directory is toegevoegd.

Als u nog steeds wilt voorkomen dat gebruikers in uw directory toepassingen registreren en zich kunnen aanmelden bij toepassingen zonder goedkeuring van de beheerder, zijn er twee instellingen die u kunt wijzigen om deze mogelijkheden uit te schakelen:

* Om te voorkomen dat gebruikers namens hen toestemming geven voor toepassingen:
  1. Ga in Azure Portal naar de [sectie Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) onder Bedrijfstoepassingen.
  2. Wijzigen: gebruikers kunnen toestemming geven voor apps die namens hen toegang hebben tot **bedrijfsgegevens.** 
     
     > [!NOTE]
     > Als u besluit om toestemming van de gebruiker uit te schakelen, moet een beheerder toestemming geven voor elke nieuwe toepassing die een gebruiker moet gebruiken.

* Om te voorkomen dat gebruikers hun eigen toepassingen registreren:
  1. Ga in Azure Portal naar de [sectie Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) onder Azure Active Directory
  2. Wijzig **Gebruikers kunnen toepassingen registreren in** **Nee.**

> [!NOTE]
> Microsoft zelf gebruikt de standaardconfiguratie met gebruikers die toepassingen kunnen registreren en namens hen toestemming kunnen geven voor toepassingen.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
