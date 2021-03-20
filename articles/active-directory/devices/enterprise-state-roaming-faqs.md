---
title: Enterprise State Roaming Veelgestelde vragen-Azure Active Directory
description: Veelgestelde vragen over ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96173121"
---
# <a name="settings-and-data-roaming-faq"></a>Veelgestelde vragen over instellingen en gegevensroaming

In dit artikel vindt u antwoorden op enkele vragen die IT-beheerders mogelijk hebben over instellingen en synchronisatie van app-gegevens.

## <a name="what-data-roams"></a>Welke gegevens worden er geroamd?

**Windows-instellingen**: de PC-instellingen die zijn ingebouwd in het Windows-besturings systeem. Over het algemeen zijn dit instellingen die uw PC personaliseren en bevatten de volgende algemene categorieën:

* *Thema*, met functies zoals het bureaubladthema en taakbalkinstellingen.
* *Instellingen voor Internet Explorer*, inclusief onlangs geopende tabbladen en favorieten.
* *Micro soft Edge-browser instellingen*, zoals Favorieten en de Lees lijst.
* *Wacht woorden*, waaronder Internet wachtwoorden, Wi-Fi profielen en anderen.
* *Taalvoorkeuren*, met onder meer instellingen voor toetsenbordindelingen, systeemtaal en datum en tijd.
* *Toegankelijkheidsfuncties*, zoals thema met een hoog contrast, Verteller en vergrootglas.
* *Andere Windows-instellingen*, zoals muisinstellingen.

> [!NOTE]
> Dit artikel is van toepassing op de op HTML gebaseerde browser van micro soft Edge die is geïntroduceerd in Windows 10 in juli 2015. Het artikel is niet van toepassing op de nieuwe micro soft Edge-browser op basis van chroom, uitgebracht op 15 januari 2020. Zie het artikel [micro soft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)(Engelstalig) voor meer informatie over het synchronisatie gedrag voor de nieuwe micro soft Edge.

**Toepassings gegevens**: universele Windows-apps kunnen instellingen gegevens schrijven naar een zwervende map en alle gegevens die naar deze map worden geschreven, worden automatisch gesynchroniseerd. Het is aan te melden dat een app door de ontwikkelaar van de app kan worden ontworpen om gebruik te maken van deze mogelijkheid. Voor meer informatie over het ontwikkelen van een universele Windows-app die gebruikmaakt van roaming, raadpleegt u de [Opslag-API van AppData](/windows/uwp/design/app-settings/store-and-retrieve-app-data) en de [Windows 8 AppData-blog voor ontwikkel aars](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Welk account wordt gebruikt voor de synchronisatie van instellingen?

In Windows 8,1 wordt met instellingen synchronisatie altijd micro soft-accounts voor consumenten gebruikt. Zakelijke gebruikers kunnen een Microsoft-account koppelen aan hun Active Directory domein account om toegang te krijgen tot instellingen synchronisatie. In Windows 10 wordt deze verbonden Microsoft-account functionaliteit vervangen door een primair/secundair account-Framework.

Het primaire account is gedefinieerd als het account dat wordt gebruikt om u aan te melden bij Windows. Dit kan een Microsoft-account, een Azure Active Directory (Azure AD)-account, een on-premises Active Directory-account of een lokaal account zijn. Naast het primaire account kunnen gebruikers van Windows 10 een of meer secundaire Cloud accounts toevoegen aan hun apparaat. Een secundair account is doorgaans een Microsoft-account, een Azure AD-account of een ander account zoals Gmail of Facebook. Deze secundaire accounts bieden toegang tot extra services zoals eenmalige aanmelding en de Windows Store, maar ze kunnen niet worden ingesteld op de synchronisatie van energie-instellingen.

In Windows 10 kan alleen het primaire account voor het apparaat worden gebruikt voor de synchronisatie van instellingen (Zie [Hoe kan ik upgrade uitvoeren van Microsoft-account instellingen synchronisatie in Windows 8 naar Azure AD-instellingen synchroniseren in Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

De gegevens worden nooit gemengd tussen de verschillende gebruikers accounts op het apparaat. Er zijn twee regels voor het synchroniseren van instellingen:

* Windows-instellingen worden altijd geroamd met het primaire account.
* App-gegevens worden gelabeld met het account dat wordt gebruikt om de app te verkrijgen. Alleen apps die zijn gelabeld met het primaire account, worden gesynchroniseerd. Het coderen van de app-eigendom wordt bepaald wanneer een app aan het werk is zijde van de Windows Store of Mobile Device Management (MDM).

Als de eigenaar van een app niet kan worden geïdentificeerd, wordt deze geroamd met het primaire account. Als een apparaat wordt bijgewerkt van Windows 8 of Windows 8,1 naar Windows 10, worden alle apps gelabeld als aangeschaft door de Microsoft-account. Dit is omdat de meeste gebruikers apps aanschaffen via de Windows Store en er geen Windows Store-ondersteuning is voor Azure AD-accounts vóór Windows 10. Als een app wordt geïnstalleerd via een offline licentie, wordt de app gelabeld met het primaire account op het apparaat.

> [!NOTE]
> Windows 10-apparaten die eigendom zijn van het bedrijf en die zijn verbonden met Azure AD, kunnen hun micro soft-accounts niet langer verbinden met een domein account. De mogelijkheid om een Microsoft-account te verbinden met een domein account en alle gegevens van de gebruiker te synchroniseren met de Microsoft-account (dat wil zeggen, de Microsoft-account roaming via de verbonden Microsoft-account en Active Directory functionaliteit) wordt verwijderd van Windows 10-apparaten die zijn gekoppeld aan een verbonden Active Directory of Azure AD-omgeving.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Hoe kan ik upgrade van Microsoft-account instellingen synchronisatie in Windows 8 naar Azure AD-instellingen synchroniseren in Windows 10?

Als u lid bent van het Active Directory domein met Windows 8,1 met een verbonden Microsoft-account, synchroniseert u de instellingen via uw Microsoft-account. Nadat u een upgrade hebt uitgevoerd naar Windows 10, blijft u gebruikers instellingen synchroniseren via Microsoft-account, zolang u een gebruiker bent die lid is van een domein en het Active Directory domein geen verbinding maakt met Azure AD.

Als het on-premises Active Directory domein verbinding maakt met Azure AD, probeert uw apparaat instellingen te synchroniseren met het verbonden Azure AD-account. Als de Azure AD-beheerder Enterprise State Roaming niet inschakelt, worden de synchronisatie-instellingen voor uw verbonden Azure AD-account gestopt. Als u een Windows 10-gebruiker bent en u zich aanmeldt met een Azure AD-identiteit, start u de Windows-instellingen synchroniseren zodra uw beheerder instellingen synchronisatie via Azure AD inschakelt.

Als u persoonlijke gegevens op uw bedrijfs apparaat hebt opgeslagen, moet u er rekening mee houden dat Windows-besturings systeem en toepassings gegevens worden gesynchroniseerd met Azure AD. Dit heeft de volgende gevolgen:

* Uw persoonlijke Microsoft-account-instellingen worden van elkaar gescheiden van de instellingen op uw werk-of school-Azure AD-accounts. Dit komt doordat de Microsoft-account-en Azure AD-instellingen synchronisatie nu gebruikmaken van afzonderlijke accounts.
* Persoonlijke gegevens, zoals Wi-Fi wacht woorden, Webreferenties en Internet Explorer-favorieten die eerder zijn gesynchroniseerd via een verbonden Microsoft-account, worden gesynchroniseerd via Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Hoe werkt Microsoft-account-en Azure AD Enterprise State Roaming-interoperabiliteit?

In de releases van Windows 10 november 2015 of hoger wordt Enterprise State Roaming alleen ondersteund voor één account tegelijk. Als u zich met een werk-of school-Azure AD-account aanmeldt bij Windows, worden alle gegevens gesynchroniseerd via Azure AD. Als u zich aanmeldt bij Windows met behulp van een persoonlijke Microsoft-account, worden alle gegevens gesynchroniseerd via de Microsoft-account. Universal AppData roamt alleen met het primaire aanmeldings account op het apparaat, en wordt alleen geroamd als de licentie van de app het primaire account is. Universele AppData voor de apps die eigendom zijn van een of meer secundaire accounts, worden niet gesynchroniseerd.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Moeten instellingen voor Azure AD-accounts worden gesynchroniseerd vanuit meerdere tenants?

Wanneer meerdere Azure AD-accounts van verschillende Azure AD-tenants zich op hetzelfde apparaat bevinden, moet u het REGI ster van het apparaat bijwerken om te communiceren met de Azure Rights Management-service voor elke Azure AD-Tenant.  

1. Zoek de GUID voor elke Azure AD-Tenant. Open de Azure Portal en selecteer een Azure AD-Tenant. De GUID voor de Tenant bevindt zich op de pagina eigenschappen voor de geselecteerde Tenant ( https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) , label **Directory-id**). 
2. Nadat u de GUID hebt, moet u de register sleutel toevoegen **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   Maak in de GUID-sleutel van de **Tenant-id** een nieuwe waarde met meerdere teken reeksen (reg-multi-SZ) met de naam **AllowedRMSServerUrls**. Geef voor de gegevens de Url's van het Licensing-distributie punt op van de andere Azure-tenants die het apparaat heeft geopend.
3. U kunt de Url's van het Licensing-distributie punt vinden door de cmdlet **Get-AadrmConfiguration** uit te voeren vanuit de AADRM-module. Als de waarden voor **LicensingIntranetDistributionPointUrl** en **LicensingExtranetDistributionPointUrl** verschillend zijn, moet u beide waarden opgeven. Als de waarden gelijk zijn, geeft u de waarde slechts eenmaal op.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Wat zijn de opties voor zwervende instellingen voor bestaande Windows Desktop-toepassingen?

Roaming werkt alleen voor universele Windows-apps. Er zijn twee opties beschikbaar voor het inschakelen van roaming op een bestaande Windows Desktop-toepassing:

* De [bureau blad-brug](/windows/msix/desktop/source-code-overview) helpt u uw bestaande Windows-bureau blad-apps naar de universeel Windows-platform te brengen. Hier worden minimale code wijzigingen vereist om te profiteren van Azure AD App data roaming. De bureaublad brug biedt uw apps een app-identiteit, die nodig is om app data roaming in te scha kelen voor bestaande bureau blad-apps.
* [Gebruikers ervaring-virtualisatie (UE-V)](/previous-versions//dn458947(v=vs.85)) helpt u bij het maken van een sjabloon voor aangepaste instellingen voor bestaande Windows-bureau blad-apps en voor het inschakelen van roaming voor Win32-apps. Voor deze optie is het niet vereist dat de ontwikkelaar van de app de code van de app wijzigt. UE-V is beperkt tot on-premises Active Directory roaming voor klanten die het micro soft Desktop Optimization Pack hebben aangeschaft.

Beheerders kunnen UE-V configureren om gegevens van Windows Desktop-apps te roamen door roaming van instellingen voor Windows-besturings systemen en universele app-gegevens te wijzigen via [het groeps beleid van UE-V](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), waaronder:

* Groeps beleid voor Windows-instellingen roamen
* Groeps beleid Windows-apps niet synchroniseren
* Internet Explorer roaming in het gedeelte toepassingen

In de toekomst kan micro soft onderzoeken hoe een diep gaande versie van UE-V in Windows kan worden geïntegreerd en hoe u UE-V kunt uitbreiden om de instellingen via de Azure AD-Cloud te roamen.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Kan ik synchronisatie-instellingen en-gegevens on-premises opslaan?

Enterprise State Roaming worden alle gesynchroniseerde gegevens opgeslagen in de micro soft-Cloud. UE-V biedt een on-premises zwervende oplossing.

## <a name="who-owns-the-data-thats-being-roamed"></a>Wie is eigenaar van de gegevens die worden geroamd?

De ondernemingen zijn eigenaar van de gegevens die via Enterprise State Roaming worden geroamd. Gegevens worden opgeslagen in een Azure-Data Center. Alle gebruikers gegevens worden zowel in transit als in de Cloud versleuteld met behulp van de Azure Rights Management-service van Azure Information Protection. Dit is een verbetering ten opzichte van Microsoft-account instellingen synchroniseren, waarmee alleen bepaalde gevoelige gegevens, zoals gebruikers referenties, worden versleuteld voordat het apparaat wordt verlaten.

Micro soft hecht veel belang aan het beveiligen van klant gegevens. De gegevens van de gebruikers instellingen van een onderneming worden automatisch versleuteld door de Azure Rights Management-service voordat ze een Windows 10-apparaat verlaten, zodat geen enkele andere gebruiker deze gegevens kan lezen. Als uw organisatie een betaald abonnement heeft voor de Azure Rights Management-service, kunt u andere beveiligings functies gebruiken, zoals het bijhouden en intrekken van documenten, het automatisch beveiligen van e-mail berichten die gevoelige informatie bevatten en het beheren van uw eigen sleutels (ook wel bekend als BYOK). Zie [Wat is Azure Rights Management](/azure/information-protection/what-is-information-protection)voor meer informatie over deze functies en over de werking van deze beveiligings service.

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Kan ik de synchronisatie voor een specifieke app of instelling beheren?

In Windows 10 is er geen MDM-of groepsbeleid-instelling om roaming voor een afzonderlijke toepassing uit te scha kelen. Tenant beheerders kunnen de synchronisatie van AppData uitschakelen voor alle apps op een beheerd apparaat, maar er is geen nauw keurige controle per app of binnen-app-niveau.

## <a name="how-can-i-enable-or-disable-roaming"></a>Hoe kan ik roaming in-of uitschakelen?

Ga in de **instellingen** -app naar **accounts** om  >  **uw instellingen te synchroniseren**. Op deze pagina kunt u zien welk account wordt gebruikt om instellingen te roamen, en kunt u afzonderlijke groeps instellingen inschakelen of uitschakelen.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Wat is de aanbeveling van micro soft voor het inschakelen van roaming in Windows 10?

Micro soft heeft een aantal verschillende instellingen voor zwervende oplossingen, waaronder zwervende gebruikers profielen, UE-V en Enterprise State Roaming.  Micro soft streeft ernaar een investering in Enterprise State Roaming te nemen in toekomstige versies van Windows. Als uw organisatie niet gereed is of vertrouwd is met het verplaatsen van gegevens naar de Cloud, raden we u aan om UE-V als uw primaire zwervende technologie te gebruiken. Als uw organisatie ondersteuning voor roaming nodig heeft voor bestaande Windows Desktop-toepassingen, maar u wilt overstappen op de Cloud, raden we u aan om zowel Enterprise State Roaming als UE-V te gebruiken. Hoewel UE-V en Enterprise State Roaming zeer soort gelijke technologieën zijn, zijn ze niet wederzijds exclusief. Ze vormen een aanvulling op elkaar om ervoor te zorgen dat uw organisatie de zwervende services levert die uw gebruikers nodig hebben.  

Bij het gebruik van zowel Enterprise State Roaming als UE-V gelden de volgende regels:

* Enterprise State Roaming is de primaire zwervende agent op het apparaat. UE-V wordt gebruikt om de ' Win32-hiaat ' aan te vullen.
* UE-V-roaming voor Windows-instellingen en moderne UWP app-gegevens moeten worden uitgeschakeld wanneer u het UE-V-groeps beleid gebruikt. Deze worden al gedekt door Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Hoe ondersteunt Enterprise State Roaming VDI (Virtual Desktop Infrastructure)?

Enterprise State Roaming wordt ondersteund op Windows 10 client-Sku's, maar niet op server-Sku's. Als een client-VM wordt gehost op een Hyper Visor machine en u zich extern aanmeldt bij de virtuele machine, worden uw gegevens geroamd. Als meerdere gebruikers hetzelfde besturings systeem delen en gebruikers op afstand aanmelden bij een server voor een volledig bureaublad ervaring, werkt roaming mogelijk niet. Het laatste scenario op basis van een sessie wordt niet officieel ondersteund.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Wat gebeurt er wanneer mijn organisatie een abonnement aanschaft dat Azure Rights Management bevat na het gebruik van roaming?

Als uw organisatie al gebruikmaakt van roaming in Windows 10 met het gratis abonnement van Azure Rights Management beperkt gebruik, heeft het kopen van een [betaald abonnement](https://azure.microsoft.com/pricing/details/information-protection/) met de Azure Rights Management Protection-Service geen invloed op de functionaliteit van de functie roaming en zijn er geen wijzigingen in de configuratie vereist voor de IT-beheerder.

## <a name="known-issues"></a>Bekende problemen

Zie de documentatie in de sectie [probleem oplossing](enterprise-state-roaming-troubleshooting.md) voor een lijst met bekende problemen. 

## <a name="next-steps"></a>Volgende stappen 

Zie [Enter prise State roaming Overview](enterprise-state-roaming-overview.md) (Engelstalig) voor een overzicht