---
title: Nieuwe functies in Windows Virtual Desktop - Azure
description: Nieuwe functies en productupdates voor Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 03/20/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: acb9b6d9d2080c63243ec4b924d07a927d6ccdfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774436"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Nieuwe functies in Windows Virtual Desktop

Windows Virtual Desktop wordt regelmatig bijgewerkt. In dit artikel vindt u meer informatie over:

- De meest recente updates
- Nieuwe functies
- Verbeteringen in bestaande functies
- Opgeloste fouten

Dit artikel wordt maandelijks bijgewerkt. Controleer hier regelmatig of er nieuwe updates beschikbaar zijn.

## <a name="client-updates"></a>Clientupdates

Bekijk deze artikelen voor meer informatie over updates voor onze clients voor Windows Virtual Desktop en Extern bureaublad-services:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [MacOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows Virtual Desktop agent-updates

De virtuele Windows-bureau blad-agent werkt ten minste één keer per maand.

Dit is what's gewijzigd in Windows Virtual Desktop agent:

- Versie 1.0.2800.2800: deze update is uitgebracht in maart 2021 en heeft een probleem met omgekeerde verbinding opgelost.
- Versie 1.0.2800.2700: deze update is uitgebracht in februari 2021 en heeft een probleem met het delen van de toegang geweigerd.

## <a name="fslogix-updates"></a>FSLogix updates

Nieuws gierig over de nieuwste updates voor FSLogix? Bekijk [wat er nieuw is op FSLogix](/fslogix/whats-new).

## <a name="february-2021"></a>Februari 2021

Dit is gewijzigd in februari 2021.

### <a name="portal-experience"></a>Portalervaring

De Azure Portal-ervaring is op de volgende manieren verbeterd:

- Bulk afvoer modus op hosts op het tabblad raster van sessie-host. 
- MSIX app attach is nu beschikbaar voor open bare preview.
- Overzicht van de informatie over de vaste hostgroep voor de donkere modus.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Opslag van EU-meta gegevens nu in open bare preview

We hosten nu een open bare preview van de Geografie van Europa (EU) als een opslag optie voor meta gegevens van de service in het virtuele bureau blad van Windows. Klanten kunnen kiezen tussen West of Europa-noord wanneer ze hun service objecten maken. De service objecten en meta gegevens voor de hostgroepen worden opgeslagen in de Azure-geografie die aan elke regio is gekoppeld. Lees voor meer informatie [onze blog post die de open bare preview aankondigt](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Teams in Windows-invoeg toepassing voor Virtual Desktop-updates

We hebben de video gespreks kwaliteit verbeterd op de Windows-invoeg toepassing voor virtueel bureau blad door de meest voorkomende problemen op te lossen, zoals wanneer het scherm plotseling donker gaat of de video en het geluid zijn gesynchroniseerd. Deze verbeteringen moeten de prestaties van de weer gave met één video met de actieve luidspreker wisseling verhogen. Er is ook een probleem opgelost waarbij hardwareapparaten met speciale tekens niet beschikbaar zijn in teams.

## <a name="january-2021"></a>Januari 2021

Dit is gewijzigd in januari 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nieuwe Windows virtueel bureau blad-aanbieding

Nieuwe klanten besparen 30 procent op virtuele Windows Desktop-kosten voor de virtuele machines uit de D-serie en de BS-serie tot 90 dagen bij gebruik van de systeem eigen micro soft-oplossing. U kunt de aanbieding in de Azure Portal inwisselen vóór 31 maart 2021. Meer informatie vindt u op onze [pagina met virtueel bureau blad voor Windows](https://azure.microsoft.com/services/virtual-desktop/offer/).

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules waarde wijzigen 

In de geneste sjabloon Azure Resource Manager is de standaard waarde voor networkSecurityGroupRules van een object gewijzigd in een matrix. Hierdoor voor komt u fouten als u managedDisks-customimagevm.jsgebruikt zonder een waarde voor networkSecurityGroupRules op te geven. Dit was geen belang rijke wijziging en is achterwaarts compatibel.

### <a name="fslogix-hotfix-update"></a>FSLogix hotfix update

We hebben FSLogix, versie 2009 HF_01 (2.9.7654.46150) uitgebracht voor het oplossen van problemen in de vorige release (2.9.7621.30127). Het is raadzaam om de vorige versie niet meer te gebruiken en FSLogix zo snel mogelijk bij te werken.

Zie de release opmerkingen in [what's New in FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)voor meer informatie.

### <a name="azure-portal-experience-improvements"></a>Verbeteringen in Azure Portal-ervaring

We hebben de volgende verbeteringen aangebracht in de Azure Portal-ervaring:

- U kunt nu rechtstreeks referenties voor de lokale VM-beheerder toevoegen in plaats van een lokaal account toe te voegen dat is gemaakt met de referenties van het Active Directory-account voor het lid van het domein.
- Gebruikers kunnen nu zowel afzonderlijke als groeps toewijzingen weer geven op afzonderlijke tabbladen voor afzonderlijke gebruikers en groepen.
- Het versie nummer van de Windows Virtual Desktop-agent is nu zichtbaar in het overzicht van virtuele machines voor hostgroepen.
- Bulksgewijs verwijderen voor hostgroepen en toepassings groepen is toegevoegd.
- U kunt nu de verwerkings modus voor meerdere sessie-hosts in een hostgroep in-of uitschakelen.
- Het veld openbaar IP-adres is verwijderd van de pagina met gegevens over de virtuele machine.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Problemen oplossen met Windows Virtual Desktop agent

We hebben onlangs de [Windows Virtual Desktop agent-probleemoplossings gids](troubleshoot-agent.md) ingesteld om klanten te helpen die veelvoorkomende problemen hebben aangetroffen.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender for Endpoint-integratie

Integratie van micro soft Defender voor endpoint is nu algemeen beschikbaar. Deze functie geeft uw virtuele Windows-Desktop-Vm's dezelfde onderzoek ervaring als een lokale Windows 10-computer. Als u Windows 10 Enter prise multi-session gebruikt, ondersteunt micro soft Defender voor eind punten Maxi maal 50 gelijktijdige gebruikers verbindingen, waardoor u de kosten besparingen voor Windows 10 Enter prise Multi-Session en het vertrouwen van micro soft Defender voor eind punt biedt. Bekijk onze [blog post](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)voor meer informatie.

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure-beveiligings basislijn voor Windows virtueel bureau blad

We hebben onlangs [een artikel gepubliceerd over de Azure Security-basis lijn](security-baseline.md) voor virtueel bureau blad van Windows, die we graag willen aankijken. Deze richt lijnen bevatten informatie over het Toep assen van de Azure Security-benchmark versie 2,0 naar het virtuele bureau blad van Windows. In de beveiligings benchmark van Azure worden de instellingen en procedures beschreven die u kunt gebruiken om uw cloud oplossingen op Azure te beveiligen.

## <a name="december-2020"></a>December 2020

Dit is er gewijzigd in december 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor voor Windows Virtual Desktop

De openbare preview voor Azure Monitor voor Windows Virtual Desktop is nu beschikbaar. Deze nieuwe functie omvat een robuust dashboard boven op Azure Monitor Workbooks, zodat IT-professionals meer inzicht hebben in hun Windows Virtual Desktop-omgeving. Bekijk [de aankondiging op onze blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) (Engelstalig) voor meer informatie. 

### <a name="azure-resource-manager-template-change"></a>Wijziging in de Azure Resource Manager-sjabloon 

In de meest recente update zijn alle parameters voor openbare IP-adressen uit de Azure Resource Manager-sjabloon verwijderd om hostgroepen te maken en in te richten. Als u uw implementatie veilig wilt houden, wordt u sterk aangeraden geen gebruik te maken van openbare IP-adressen voor Windows Virtual Desktop. Als uw implementatie afhankelijk is van openbare IP-adressen, moet u de implementatie opnieuw configureren voor het gebruik van privé-IP-adressen, anders werkt de implementatie niet goed.

### <a name="msix-app-attach-public-preview"></a>Openbare preview van MSIX app attach 

MSIX app attach is een andere service waarvan de openbare preview die deze maand is ingegaan. MSIX app attach is een service waarmee MSIX-toepassingen dynamisch worden gepresenteerd aan de sessiehost-VM's van Windows Virtual Desktop. Bekijk [de aankondiging op onze blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) (Engelstalig) voor meer informatie. 

### <a name="screen-capture-protection"></a>Beveiliging van schermopnamen 

Deze maand was ook de openbare preview voor beveiliging van schermopnamen voor het eerst te zien. U kunt deze functie gebruiken om te voorkomen dat gevoelige informatie wordt vastgelegd op de clienteindpunten. Ga naar [deze pagina](https://aka.ms/WVDScreenCaptureProtection) om de beveiliging van schermopnamen uit te proberen.  

### <a name="built-in-roles"></a>Ingebouwde rollen

Er zijn nieuwe ingebouwde rollen voor Windows Virtual Desktop voor beheerdersmachtigingen toegevoegd. Zie [Ingebouwde rollen voor Windows Virtual Desktop](rbac.md) voor meer informatie. 

### <a name="application-group-limit-increase"></a>Verhoging van de limiet voor toepassingsgroepen

De standaardlimiet voor toepassingsgroepen per Azure Active Directory-tenant is verhoogd naar 200 groepen.

### <a name="client-updates-for-december-2020"></a>Clientupdates van december 2020

Er zijn nieuwe versies van de volgende clients uitgebracht: 

- Android
- macOS
- Windows

Zie [Clientupdates](whats-new.md#client-updates) voor meer informatie over clientupdates.

## <a name="november-2020"></a>November 2020

### <a name="azure-portal-experience"></a>Ervaring met Azure Portal

Er zijn twee bugs in de gebruikerservaring van Azure Portal opgelost:

- De beschrijvende naam van de Desktop-toepassing wordt niet meer overschreven in de werkstroom 'VM toevoegen'.
- Het tabblad sessie-host wordt nu geladen als de sessie-hosts deel uitmaken van schaalsets.

### <a name="fslogix-client-version-2009"></a>FSLogix-client, versie 2009 

We hebben een nieuwe versie van de FSLogix-client uitgebracht met veel oplossingen en verbeteringen. Lees voor meer informatie [onze blogpost](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Openbare preview van RDP Shortpath

RDP Shortpath introduceert een directe verbinding met uw Windows Virtual Desktop-sessie-host met behulp van punt-naar-site- en site-naar-site-VPN's en ExpressRoute. Ook wordt het URCP-transportprotocol geïntroduceerd. RDP Shortpath is ontworpen om latentie en netwerk-hops te verlagen om de gebruikerservaring te verbeteren. Meer informatie vindt u op [Windows Virtual Desktop RDP Shortpath](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, versie 2.0.1

Versie 2.0.1 van de Windows Virtual Desktop-cmdlets is uitgebracht. Deze update bevat cmdlets waarmee u een MSIX App Attach kunt beheren. U kunt de nieuwe versie downloaden op [de PowerShell-galerie](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Updates voor Azure Advisor

Azure Advisor biedt nu een nieuwe aanbeveling voor nabijheidsrichtlijnen in Windows Virtual Desktop, en een nieuwe aanbeveling voor het optimaliseren van prestaties in hostpools met diepte-eerst taakverdeling. Meer informatie vindt u op [de website van Azure](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Oktober 2020

Dit is gewijzigd in oktober 2020:

### <a name="improved-performance"></a>Verbeterde prestaties

- De prestaties zijn geoptimaliseerd door de latentie van de verbinding in de volgende Azure-geografieën te verlagen:
    - Zwitserland
    - Canada

U kunt nu de [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) gebruiken om de kwaliteit van de gebruikerservaring in deze gebieden te schatten.

### <a name="azure-government-cloud-availability"></a>Beschikbaarheid van Azure Government Cloud

De Azure Government Cloud is nu algemeen beschikbaar. Lees voor meer informatie [onze blogpost](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Updates van de Azure Portal binnen Windows Virtual Desktop

Er zijn enkele updates voor de Azure Portal binnen Windows Virtual Desktop:

- Er is een resourceID-fout opgelost waardoor gebruikers het tabblad 'Sessies' niet konden openen.
- De gebruikersinterface is gestroomlijnd op het tabblad 'Sessiehosts'.
- De instellingen 'Standaardinstellingen', 'Bruikbaarheid' en 'Standaardinstellingen herstellen' onder 'RDP-eigenschappen' zijn gerepareerd.
- De verwijderfuncties zijn op alle tabbladen consistent gemaakt.
- De portal valideert nu app-namen in de werkstroom 'Een app toevoegen'.
- Er is een probleem opgelost waarbij het exporteren van gegevens van de sessie-host niet werd uitgelijnd in de kolommen.
- Er is een probleem opgelost waarbij de portal geen gebruikerssessies kon ophalen.
- Er is een probleem opgelost met het ophalen van de sessiehost. Dit probleem kwam voor wanneer de virtuele machine gemaakt was in een andere resourcegroep.
- Het tabblad 'Sessiehost' is bijgewerkt en bevat nu een lijst met zowel actieve sessies als sessies waarvan de verbinding verbroken is.
- Het tabblad 'Toepassingen' bevat nu pagina's.
- Er is een probleem opgelost waarbij de tekst 'Vereist opdrachtregel' niet correct werd weergegeven op het tabblad 'Toepassingslijst'.
- Er is een probleem opgelost waarbij de portal geen hostgroepen of virtuele machines kon implementeren als de Duitse taalversie van de Shared Image Gallery werd gebruikt.

### <a name="client-updates-for-october-2020"></a>Clientupdates van oktober 2020

We hebben nieuwe versies van de clients uitgebracht. Zie voor meer informatie de volgende artikelen:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Zie voor meer informatie over de andere clients [Clientupdates](#client-updates).

## <a name="september-2020"></a>September 2020

Dit is gewijzigd in september 2020:

- De prestaties zijn geoptimaliseerd door de latentie van de verbinding in de volgende Azure-geografieën te verlagen:
    - Duitsland
    - Zuid-Afrika (alleen voor validatie-omgevingen)

U kunt nu de [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) gebruiken om de kwaliteit van de gebruikerservaring in deze gebieden te schatten.

- Versie 1.2.1364 van de Windows Desktop-client voor Windows Virtual Desktop is uitgebracht. In deze update hebben we de volgende wijzigingen aangebracht:
    - Er is een probleem opgelost waarbij eenmalige aanmelding (SSO) niet werkte in Windows 7.
    - Er is een probleem opgelost waarbij de verbinding met de client werd verbroken wanneer een gebruiker die mediaoptimalisatie voor Teams had ingeschakeld, wilde bellen of wilde deelnemen aan een Teamsvergadering terwijl in een andere app een audiostream was geopend in de exclusieve modus.
    - Er is een probleem opgelost waarbij in Teams geen audio- of videoapparaten werden opgesomd, wanneer mediaoptimalisatie voor Teams was ingeschakeld.
    - De koppeling naar Hulp nodig bij het instellen van waarschuwingen? is toegevoegd op de pagina Bureaubladinstellingen.
    - Er is een probleem opgelost met de knop Abonneren. Dit probleem vond plaats wanneer donkere thema’s met hoog contrast werden gebruikt.
    
- Dankzij de enorme hulp van onze gebruikers hebben we twee kritieke problemen opgelost voor de Extern bureaublad-client van Microsoft Store. We blijven feedback beoordelen en problemen oplossen terwijl we de gefaseerde release van onze client wereldwijd beschikbaar maken voor meer gebruikers.
    
- We hebben een nieuwe functie toegevoegd waarmee u de VM-locatie, installatiekopie, resourcegroep, voorvoegselnaam, en netwerkconfiguratie kunt wijzigen als onderdeel van de werkstroom voor het toevoegen van een VM aan de implementatie in de Azure-portal.

- IT-professionals kunnen nu hybride, aan Azure Active Directory gekoppelde Windows 10 Enterprise-VM's beheren met Microsoft Endpoint Manager. Lees [onze blogpost](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048) voor meer informatie.

## <a name="august-2020"></a>Augustus 2020

Dit is gewijzigd in augustus 2020:

- De prestaties zijn verbeterd om de latentie van de verbinding in de volgende Azure-regio's te verminderen: 

    - Verenigd Koninkrijk
    - Frankrijk
    - Noorwegen
    - Zuid-Korea

   U kunt de [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) gebruiken om een algemeen beeld te krijgen van de manier waarop deze wijzigingen van invloed zijn op uw gebruikers.

- De Microsoft Store Extern bureaublad-client (v10.2.1522+) is nu overal beschikbaar. Deze versie van de Microsoft Store Extern bureaublad-client is compatibel met Windows Virtual Desktop. Daarnaast hebben we vernieuwde UI-stromen geïntroduceerd voor een betere gebruikerservaring. Deze update bevat een fluent-ontwerp, lichte en donkere modi en veel andere interessante wijzigingen. We hebben de client ook herschreven om dezelfde onderliggende RDP-Engine (Remote Desktop Protocol) te gebruiken als iOS-, Mac OS-en Android-clients. Zo kunnen we nieuwe functies sneller leveren op alle platforms. [Download de client](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) en probeer het uit!

- Er is een probleem opgelost in de Teams- bureaublad-client (versie 1.3.00.21759), waarbij de client alleen de UTC-tijdzone toonde in de chat, kanalen en agenda. De bijgewerkte client toont nu de tijdzone van de externe sessie.

- Azure Advisor maakt nu deel uit van Windows Virtual Desktop. Wanneer u via de Azure-portal toegang hebt tot Windows Virtual Desktop, ziet u aanbevelingen voor het optimaliseren van uw Windows Virtual Desktop-omgeving. Zie [Azure Advisor](azure-advisor.md) voor meer informatie.

- Azure CLI ondersteunt nu Windows Virtual Desktop (`az desktopvirtualization`) om u te helpen bij het automatiseren van Windows Virtual Destkop-implementaties. Bekijk [desktopvirtualization](/cli/azure/ext/desktopvirtualization/) voor een lijst met extensie-opdrachten.

- We hebben onze implementatiesjablonen bijgewerkt zodat ze volledig compatibel zijn met de Azure Resource Manager-interfaces voor Windows Virtual Desktop. U kunt de sjablonen vinden op [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- De Windows Virtual Desktop US Gov-portal is nu beschikbaar als openbare preview. Zie [onze aankondiging](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/) voor meer informatie.

## <a name="july-2020"></a>Juli 2020  

In juli werd de integratie van Windows Virtual Desktop met Azure Resource Management algemeen beschikbaar gesteld.

Met deze nieuwe release is het volgende gewijzigd: 

- De 'release najaar 2019' is nu bekend als 'Windows Virtual Desktop (klassiek)', terwijl 'release voorjaar 2020' nu gewoon 'Windows Virtual Desktop' heet. Lees [deze blogpost](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/) voor meer informatie. 

Lees [deze blogpost](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) voor meer informatie over de nieuwe functies. 

### <a name="autoscaling-tool-update"></a>Update voor hulpprogramma voor automatisch schaal aanpassen

De meest recente versie van het hulpprogramma voor automatisch schaal aanpassen dat in preview was, is nu algemeen beschikbaar. Dit hulpprogramma maakt gebruik van een Azure Automation-account en de Azure Logic-app voor het automatisch afsluiten en opnieuw opstarten van virtual machines (VM's) binnen een hostgroep, waardoor de infrastructuurkosten worden verminderd. Meer informatie over [Schaalsessiehosts die gebruikmaken van Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

U kunt nu met Azure Portal het volgende doen in Windows Virtual Desktop: 

- Gebruikers rechtstreeks toewijzen aan persoonlijke desktopsessiehosts  
- De instelling voor de validatieomgeving voor hostgroepen wijzigen 

### <a name="diagnostics"></a>Diagnostiek

We hebben een aantal nieuwe vooraf samengestelde query's uitgebracht voor de Log Analytics-werkruimte. Als u toegang wilt krijgen tot de query's, gaat u naar **Logboeken** en selecteert u onder **Categorie** de optie **Windows Virtual Desktop**. Meer informatie vindt u onder [Log Analytics gebruiken voor de diagnostische functie](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Update voor Extern bureaublad-client voor Android

De [Extern bureaublad-client voor Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) ondersteunt nu virtuele Windows Virtual Desktop-verbindingen. Vanaf versie 10.0.7 bevat de Android-client een nieuwe gebruikersinterface voor een verbeterde gebruikerservaring. De client integreert ook met Microsoft Authenticator op Android-apparaten om voorwaardelijke toegang in te schakelen wanneer u zich abonneert op Windows Virtual Desktop-werkruimten.  

De vorige versie van Extern bureaublad-client heet nu 'Extern bureaublad 8'. Alle bestaande verbindingen uit de eerdere versie van de client, worden naadloos overgebracht naar de nieuwe client. De nieuwe client is herschreven met dezelfde onderliggende RDP-kernengine als de iOS- en macOS-clients, met een snellere release van nieuwe functies op alle platforms. 

### <a name="teams-update"></a>Update van teams

We hebben verbeteringen aangebracht in Microsoft Teams voor Windows Virtual Desktop. Het belangrijkste is dat Windows Virtual Desktop nu ondersteuning biedt voor optimalisatie van audio en visualisatie voor de Windows Virtual Desktop-client. Omleiding verbetert de latentie door directe paden te maken tussen gebruikers wanneer ze audio of video in telefoongesprekken en vergaderingen gebruiken. Minder afstand betekent ook minder hops, waardoor oproepen een helderder beeld en beter geluid hebben. Zie [Teams gebruiken in Windows Virtual Desktop](teams-on-wvd.md) voor meer informatie.

## <a name="june-2020"></a>Juni 2020

Vorige maand hebben we Windows Virtual Desktop met Azure Resource Manager-integratie geïntroduceerd in de preview-versie. Deze update bevat veel geweldige nieuwe functies waar we graag meer over vertellen. Hieronder ziet u wat nieuw is in deze versie van Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop is nu geïntegreerd met Azure Resource Manager

Windows Virtual Desktop is nu geïntegreerd in Azure Resource Manager. In de meest recente update zijn alle Windows Virtual Desktop-objecten omgezet naar Azure Resource Manager-resources. Deze update is ook geïntegreerd met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Raadpleeg [Wat is Azure Resource Manager?](../azure-resource-manager/management/overview.md) voor meer informatie.

Dit is wat deze wijziging betekent voor u:

- Windows Virtual Desktop is nu geïntegreerd met de Azure-portal. Dit betekent dat u alles rechtstreeks kunt beheren in de portal. U hebt hiervoor geen PowerShell, web-apps of hulpprogramma's van derden nodig. Bekijk onze zelfstudie op [Een hostpool maken met behulp van de Azure-portal](create-host-pools-azure-marketplace.md) om aan de slag te gaan.

- Vóór deze update kon u RemoteApps en Desktops alleen publiceren voor individuele gebruikers. Met Azure Resource Manager kunt u resources nu publiceren in Azure Active Directory-groepen.

- De eerdere versie van Windows Virtual Desktop beschikte over vier ingebouwde beheerdersrollen die u kon toewijzen aan een tenant of hostpool. Deze rollen vallen in Azure nu onder [op rollen gebaseerd toegangsbeheer (Azure RBAC)](../role-based-access-control/overview.md). U kunt deze rollen toepassen op elk Azure Resource Manager-object in Windows Virtual Desktop, waardoor u beschikt over een compleet en uitgebreid delegatiemodel.

- In deze update hoeft u Azure Marketplace of de GitHub-sjabloon niet meer herhaaldelijk uit te voeren om een hostpool uit te breiden. Het enige wat u hoeft te doen om een hostpool uit te breiden, is naar de hostpool gaan in de Azure-portal en **+ Toevoegen** selecteren om extra sessiehosts te implementeren.

- Implementatie van hostpools is nu volledig geïntegreerd met de [Gedeelde installatiekopiegalerie van Azure](../virtual-machines/shared-image-galleries.md). De Gedeelde installatiekopiegalerie is een afzonderlijke Azure-service waarin installatiekopiedefinities van VM’s (virtuele machines) worden opgeslagen, waaronder versiebeheer voor installatiekopieën. U kunt ook globale replicatie gebruiken om uw installatiekopieën te kopiëren en te verzenden naar andere Azure-regio’s voor lokale implementatie.

- Bewakingsfuncties die voorheen werden uitgevoerd via PowerShell of met de web-app voor de Diagnostische service, zijn nu verplaatst naar Log Analytics in de Azure-portal. U hebt nu ook twee opties om uw rapporten te visualiseren. U kunt Kusto-query's uitvoeren en werkmappen gebruiken om visuele rapporten te maken.

- U hebt geen Azure AD-toestemming (Azure Active Directory) meer nodig om Windows Virtual Desktop te gebruiken. In deze update worden met de Azure AD-tenant in uw Azure-abonnement uw gebruikers geverifieerd en wordt Azure RBAC geboden voor de beheerders.

### <a name="powershell-support"></a>PowerShell-ondersteuning

In deze update zijn nieuwe AzWvd-cmdlets toegevoegd aan de Azure PowerShell AZ-module. Deze nieuwe module wordt ondersteund in PowerShell Core, wat wordt uitgevoerd in .NET Core.

Volg de instructies in [De PowerShell-module instellen voor Windows Virtual Desktop](powershell-module.md) om de module te installeren.

U kunt ook een lijst met beschikbare opdrachten bekijken in de [AzWvd PowerShell-referentie](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Lees [onze blogpost](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) voor meer informatie over de nieuwe functies.

### <a name="additional-gateways"></a>Aanvullende gateways

We hebben een nieuw gatewaycluster toegevoegd in Zuid-Afrika om de latentie van de verbinding te verminderen.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Windows Virtual Desktop (preview)

We hebben enkele verbeteringen aangebracht in Microsoft Teams voor Windows Virtual Desktop. Het belangrijkste is dat Windows Virtual Desktop nu ondersteuning biedt voor de omleiding van audio en visualisatie bij oproepen. Omleiding verbetert de latentie door directe paden te maken tussen gebruikers wanneer ze audio of video gebruiken om te bellen. Minder afstand betekent ook minder hops, waardoor oproepen een helderder beeld en beter geluid hebben.

Lees [onze blogpost](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees meer over toekomstige plannen in de [roadmap voor Microsoft 365 Windows Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
