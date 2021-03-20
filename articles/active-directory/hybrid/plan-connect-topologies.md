---
title: 'Azure AD Connect: ondersteunde topologieën | Microsoft Docs'
description: In dit onderwerp vindt u informatie over ondersteunde en niet-ondersteunde topologieën voor Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359829"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologieën voor Azure AD Connect
In dit artikel worden verschillende topologieën voor on-premises en Azure Active Directory (Azure AD) beschreven die gebruikmaken van Azure AD Connect Sync als de oplossing voor sleutel integratie. Dit artikel bevat zowel ondersteunde als niet-ondersteunde configuraties.


Hier ziet u de legenda voor afbeeldingen in het artikel:

| Beschrijving | Symbool |
| --- | --- |
| On-premises Active Directory-forest |![On-premises Active Directory-forest](./media/plan-connect-topologies/legendad1.png) |
| On-premises Active Directory met gefilterde import |![Active Directory met gefilterde import](./media/plan-connect-topologies/legendad2.png) |
| Azure AD Connect synchronisatie server |![Azure AD Connect synchronisatie server](./media/plan-connect-topologies/legendsync1.png) |
| "Faserings modus van Azure AD Connect-server" |!["Faserings modus van Azure AD Connect-server"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync met Forefront Identity Manager (FIM) 2010 of Microsoft Identity Manager (MIM) 2016 |![GALSync met FIM 2010 of MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Azure AD Connect-synchronisatie server, gedetailleerd |![Azure AD Connect-synchronisatie server, gedetailleerd](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Niet-ondersteund scenario |![Niet-ondersteund scenario](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> Micro soft biedt geen ondersteuning voor het wijzigen of uitvoeren van Azure AD Connect synchronisatie buiten de configuraties of acties die formeel zijn gedocumenteerd. Een van deze configuraties of acties kan leiden tot een inconsistente of niet-ondersteunde status van Azure AD Connect synchronisatie. Als gevolg hiervan kan micro soft geen technische ondersteuning bieden voor dergelijke implementaties.


## <a name="single-forest-single-azure-ad-tenant"></a>Eén forest, één Azure AD-Tenant
![Topologie voor één forest en één Tenant](./media/plan-connect-topologies/singleforestsingledirectory.png)

De meest voorkomende topologie is één on-premises forest met een of meer domeinen en één Azure AD-Tenant. Voor Azure AD-verificatie wordt wachtwoord hash-synchronisatie gebruikt. De snelle installatie van Azure AD Connect ondersteunt alleen deze topologie.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Eén forest, meerdere synchronisatie servers naar één Azure AD-Tenant
![Niet-ondersteunde, gefilterde topologie voor één forest](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Meerdere Azure AD Connect-synchronisatie servers die zijn verbonden met dezelfde Azure AD-Tenant, worden niet ondersteund, met uitzonde ring van een [staging-server](#staging-server). Het wordt niet ondersteund, zelfs niet als deze servers zijn geconfigureerd om te synchroniseren met een groep objecten die elkaar uitsluiten. Mogelijk hebt u deze topologie gezien als u niet alle domeinen in het forest kunt bereiken vanaf één server, of als u de belasting over meerdere servers wilt verdelen.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Meerdere forests, één Azure AD-Tenant
![Topologie voor meerdere forests en één Tenant](./media/plan-connect-topologies/multiforestsingledirectory.png)

Veel organisaties hebben omgevingen met meerdere on-premises Active Directory forests. Er zijn verschillende redenen om meer dan één on-premises Active Directory forest te hebben. Typische voor beelden zijn ontwerpen met account-resource bossen en het resultaat van een fusie of overname.

Wanneer u meerdere forests hebt, moeten alle forests bereikbaar zijn voor een enkele Azure AD Connect-synchronisatie server. De server moet lid zijn van een domein. Indien nodig om alle forests te bereiken, kunt u de server in een perimeter netwerk (ook wel bekend als DMZ, gedemilitariseerde zone en gescreend subnet) plaatsen.

De installatie wizard van Azure AD Connect biedt verschillende opties voor het consolideren van gebruikers die in meerdere forests worden weer gegeven. Het doel is dat een gebruiker slechts één keer in azure AD wordt weer gegeven. Er zijn enkele algemene topologieën die u kunt configureren in het pad voor aangepaste installatie in de installatie wizard. Op de pagina **unieke identificatie van uw gebruikers** selecteert u de optie die overeenkomt met uw topologie. De samen voeging is alleen geconfigureerd voor gebruikers. Dubbele groepen worden niet geconsolideerd met de standaard configuratie.

Algemene topologieën worden besproken in de secties over afzonderlijke topologieën, [volledige mesh](#multiple-forests-full-mesh-with-optional-galsync)en [de account-resource topologie](#multiple-forests-account-resource-forest).

Bij de standaard configuratie in Azure AD Connect Sync wordt uitgegaan van het volgende:

* Elke gebruiker heeft slechts één ingeschakeld account en het forest waar dit account zich bevindt, wordt gebruikt om de gebruiker te verifiëren. Deze veronderstelling geldt voor wacht woord-hash-synchronisatie, Pass Through-verificatie en Federatie. UserPrincipalName en source Anchor/immutableID zijn afkomstig uit dit forest.
* Elke gebruiker heeft slechts één postvak.
* De forest die als host fungeert voor het postvak voor een gebruiker heeft de beste gegevens kwaliteit voor kenmerken die zichtbaar zijn in de algemene adres lijst van Exchange (GAL). Als er geen postvak is voor de gebruiker, kan elk forest worden gebruikt om deze kenmerk waarden bij te dragen.
* Als u een gekoppeld postvak hebt, is er ook een account in een ander forest dat wordt gebruikt om u aan te melden.

Als uw omgeving niet met deze hypo Thesen overeenkomt, gebeurt het volgende:

* Als u meer dan één actief account of meer dan één postvak hebt, kiest de synchronisatie-engine er een en negeert het andere.
* Een gekoppeld postvak zonder ander actief account wordt niet geëxporteerd naar Azure AD. Het gebruikers account wordt niet weer gegeven als lid van een groep. Een gekoppeld postvak in DirSync wordt altijd weer gegeven als een normaal postvak. Deze wijziging is opzettelijk een ander gedrag voor betere ondersteuning van scenario's met meerdere forests.

U kunt meer informatie vinden over [de standaard configuratie](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Meerdere forests, meerdere synchronisatie servers naar één Azure AD-Tenant
![Niet-ondersteunde topologie voor meerdere forests en meerdere synchronisatie servers](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Het is niet mogelijk om meer dan één Azure AD Connect synchronisatie server die is verbonden met één Azure AD-Tenant. De uitzonde ring is het gebruik van een [staging-server](#staging-server).

Deze topologie wijkt af van de volgende in dat **meerdere synchronisatie servers** die zijn verbonden met één Azure AD-Tenant, niet worden ondersteund.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Meerdere forests, één synchronisatie server, gebruikers worden in slechts één map weer gegeven
![Optie voor het weer geven van gebruikers in alle directory's](./media/plan-connect-topologies/multiforestusersonce.png)

![Voors telling van meerdere forests en afzonderlijke topologieën](./media/plan-connect-topologies/multiforestseparatetopologies.png)

In deze omgeving worden alle on-premises forests beschouwd als afzonderlijke entiteiten. Er is geen gebruiker aanwezig in een ander forest. Elk forest heeft een eigen Exchange-organisatie en er is geen GALSync tussen de forests. Deze topologie kan de situatie zijn na een fusie/aanschaf of in een organisatie waar elke bedrijfs eenheid onafhankelijk van elkaar werkt. Deze forests bevinden zich in dezelfde organisatie in azure AD en worden weer gegeven met een uniforme GAL. In de voor gaande afbeelding wordt elk object in elk forest één keer weer gegeven in het omgekeerde en samengevoegd in de doel-Azure AD-Tenant.

### <a name="multiple-forests-match-users"></a>Meerdere forests: overeenkomende gebruikers
Algemene voor al deze scenario's is dat distributie-en beveiligings groepen een combi natie van gebruikers, contact personen en refererende beveiligings-principals (FSPs) kunnen bevatten. FSPs worden in Active Directory Domain Services (AD DS) gebruikt om leden van andere forests in een beveiligings groep weer te geven. Alle FSPs worden omgezet in het echte object in azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Meerdere forests: volledig net met optionele GALSync
![Optie voor het gebruik van het kenmerk mail voor het vergelijken wanneer gebruikers identiteiten in meerdere directory's bestaan](./media/plan-connect-topologies/multiforestusersmail.png)

![Volledige mesh-topologie voor meerdere forests](./media/plan-connect-topologies/multiforestfullmesh.png)

Met een volledige mesh-topologie kunnen gebruikers en bronnen in elk forest worden geplaatst. Doorgaans zijn er twee richtings vertrouwensrelaties tussen de forests.

Als Exchange aanwezig is in meer dan één forest, is er mogelijk een on-premises GALSync-oplossing (optioneel). Elke gebruiker wordt vervolgens als contact persoon weer gegeven in alle andere forests. GALSync wordt doorgaans geïmplementeerd via FIM 2010 of MIM 2016. Azure AD Connect kan niet worden gebruikt voor on-premises GALSync.

In dit scenario worden identiteits objecten gekoppeld via het kenmerk mail. Een gebruiker die een postvak in het ene forest heeft, wordt gekoppeld aan de contact personen in de andere forests.

### <a name="multiple-forests-account-resource-forest"></a>Meerdere forests: account-bron-forest
![Optie voor het gebruik van de ObjectSID-en msExchMasterAccountSID-kenmerken voor het vergelijken van identiteiten in meerdere directory's](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Account-resource forest-topologie voor meerdere forests](./media/plan-connect-topologies/multiforestaccountresource.png)

In een account-resource forest-topologie hebt u een of meer *account* -forests met actieve gebruikers accounts. U hebt ook een of meer *bron* -forests met uitgeschakelde accounts.

In dit scenario vertrouwt een (of meer) resource forest alle account forests. De resource-forest heeft doorgaans een uitgebreid Active Directory schema met Exchange en Lync. Alle Exchange-en Lync-Services, samen met andere gedeelde services, bevinden zich in dit forest. Gebruikers hebben een uitgeschakeld gebruikers account in dit forest en het postvak is gekoppeld aan het account-forest.

## <a name="microsoft-365-and-topology-considerations"></a>Microsoft 365 en topologie overwegingen
Sommige Microsoft 365 werk belastingen hebben bepaalde beperkingen op ondersteunde topologieën:

| Workload | Beperkingen |
| --------- | --------- |
| Exchange Online | Zie [hybride implementaties met meerdere Active Directory-forests](/Exchange/hybrid-deployment/hybrid-with-multiple-forests)voor meer informatie over hybride topologieën die door Exchange Online worden ondersteund. |
| Skype voor Bedrijven | Wanneer u meerdere on-premises forests gebruikt, wordt alleen de topologie van het account-resource forest ondersteund. Zie voor meer informatie [milieu vereisten voor Skype voor bedrijven Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Als u een grotere organisatie bent, kunt u overwegen om de [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) -functie te gebruiken. Hiermee kunt u definiëren in welke Data Center-regio de resources van de gebruiker zich bevinden.

## <a name="staging-server"></a>Staging-server
![Faserings server in een topologie](./media/plan-connect-topologies/multiforeststaging.png)

Azure AD Connect ondersteunt het installeren van een tweede server in de *faserings modus*. Een server in deze modus leest gegevens van alle verbonden directory's, maar schrijft niets naar verbonden directory's. Er wordt gebruikgemaakt van de normale synchronisatie cyclus en daarom is er een bijgewerkte kopie van de identiteits gegevens.

In een nood situatie waarbij de primaire server uitvalt, kunt u een failover uitvoeren naar de staging-server. U kunt dit doen in de Azure AD Connect wizard. Deze tweede server kan zich in een ander Data Center bevinden, omdat er geen infra structuur wordt gedeeld met de primaire server. U moet alle configuratie wijzigingen die zijn aangebracht op de primaire server hand matig naar de tweede server kopiëren.

U kunt een staging-server gebruiken om een nieuwe aangepaste configuratie en het effect ervan op uw gegevens te testen. U kunt een voor beeld van de wijzigingen bekijken en de configuratie aanpassen. Wanneer u tevreden bent met de nieuwe configuratie, kunt u de staging-server de actieve server maken en de oude actieve server instellen op de faserings modus.

U kunt deze methode ook gebruiken om de actieve synchronisatie server te vervangen. Bereid de nieuwe server voor en stel deze in op de faserings modus. Zorg ervoor dat de status in orde is, schakel de faserings modus uit (waardoor deze actief is) en sluit de huidige actieve server af.

Het is mogelijk om meer dan één staging-server te hebben wanneer u meerdere back-ups wilt maken in verschillende data centers.

## <a name="multiple-azure-ad-tenants"></a>Meerdere Azure AD-tenants
We raden u aan één Tenant in azure AD te hebben voor een organisatie.
Zie het artikel beheer [eenheden in azure AD](../roles/administrative-units.md)voordat u meerdere Azure AD-tenants wilt gebruiken. Het behandelt veelvoorkomende scenario's waarin u één Tenant kunt gebruiken.

![Topologie voor meerdere forests en meerdere tenants](./media/plan-connect-topologies/multiforestmultidirectory.png)

Er is een 1:1-relatie tussen een Azure AD Connect synchronisatie server en een Azure AD-Tenant. Voor elke Azure AD-Tenant hebt u een Azure AD Connect synchronisatie server geïnstalleerd. De Azure AD-Tenant instanties worden door het ontwerp geïsoleerd. Dat wil zeggen dat gebruikers in een Tenant geen gebruikers kunnen zien in de andere Tenant. Als u deze schei ding wilt, is dit een ondersteunde configuratie. Als dat niet het geval is, moet u één Azure AD-Tenant model gebruiken.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Elk object slechts eenmaal in een Azure AD-Tenant
![Gefilterde topologie voor één forest](./media/plan-connect-topologies/singleforestfiltered.png)

In deze topologie is een Azure AD Connect synchronisatie server verbonden met elke Azure AD-Tenant. De Azure AD Connect-synchronisatie servers moeten zodanig worden geconfigureerd dat ze worden gefilterd, zodat elk een set objecten bevat die gelijktijdig kunnen worden gebruikt. U kunt bijvoorbeeld elke server voor een bepaald domein of een bepaalde organisatie-eenheid bereiken.

Een DNS-domein kan in slechts één Azure AD-Tenant worden geregistreerd. De Upn's van de gebruikers in het on-premises Active Directory-exemplaar moeten ook afzonderlijke naam ruimten gebruiken. Zo worden in de voor gaande afbeelding drie afzonderlijke UPN-achtervoegsels geregistreerd in het on-premises Active Directory-exemplaar: contoso.com, fabrikam.com en wingtiptoys.com. De gebruikers in elke on-premises Active Directory domein gebruiken een andere naam ruimte.

>[!NOTE]
>GalSync (Global Address List synchronization) wordt niet automatisch uitgevoerd in deze topologie en vereist een extra aangepaste MIM-implementatie om ervoor te zorgen dat elke Tenant een volledige algemene adres lijst (GAL) in Exchange Online en Skype voor bedrijven online heeft.


Deze topologie heeft de volgende beperkingen op andere ondersteunde scenario's:

* Voor een maximum van 5 Azure Active Directory tenants kan Exchange Hybrid zijn met het on-premises Active Directory exemplaar. Dit scenario wordt beschreven in [Update van de wizard hybride configuratie van September 2020](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* De Exchange-server die de hybride configuratie wizard uitvoert, moet 2016 CU18 of 2019 CU7 of hoger zijn.
* Elk Azure AD Connect exemplaar moet worden uitgevoerd op een computer die lid is van een domein.
* Azure AD Connect moet worden geconfigureerd met behulp van de optie voor filteren van domein/OE om gebruikers uit uw on-premises adres lijst te filteren. Met deze optie zorgt u ervoor dat gebruikers alleen in één online Exchange-Tenant worden weer gegeven.
* Windows 10-apparaten kunnen worden gekoppeld aan één Azure AD-Tenant.
* De optie voor eenmalige aanmelding (SSO) voor wachtwoord hash-synchronisatie en Pass Through-verificatie kan worden gebruikt met slechts één Azure AD-Tenant.

De vereiste voor een wederzijds exclusieve verzameling objecten is ook van toepassing op write-back. Sommige write-functies worden niet ondersteund met deze topologie omdat ze uitgaan van één on-premises configuratie. Deze functies zijn onder andere:

* Terugschrijven van groep met standaard configuratie.
* Write-back van apparaat.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Elk object meerdere keren in een Azure AD-Tenant
![Niet-ondersteunde topologie voor één forest en meerdere tenants](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Niet-ondersteunde topologie voor één forest en meerdere connectors](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Deze taken worden niet ondersteund:

* Synchroniseer dezelfde gebruiker met meerdere Azure AD-tenants.
* Breng een configuratie wijziging aan zodat gebruikers in een Azure AD-Tenant als contact personen worden weer gegeven in een andere Azure AD-Tenant.
* Wijzig Azure AD Connect synchronisatie om verbinding te maken met meerdere Azure AD-tenants.

### <a name="galsync-by-using-writeback"></a>GALSync met behulp van write-back
![Niet-ondersteunde topologie voor meerdere forests en meerdere directory's, met GALSync focussen op Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Niet-ondersteunde topologie voor meerdere forests en meerdere directory's, waarbij GALSync zich richt op on-premises Active Directory](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Azure AD-tenants worden door het ontwerp geïsoleerd. Deze taken worden niet ondersteund:

* Wijzig de configuratie van Azure AD Connect Sync om gegevens te lezen van een andere Azure AD-Tenant.
* Exporteer gebruikers als contact personen naar een andere on-premises Active Directory instantie met behulp van Azure AD Connect-synchronisatie.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync met on-premises synchronisatie server
![GALSync in een topologie voor meerdere forests en meerdere directory's](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

U kunt FIM 2010 of MIM 2016 on-premises gebruiken voor het synchroniseren van gebruikers (via GALSync) tussen twee Exchange-organisaties. De gebruikers in de ene organisatie worden weer gegeven als vreemde gebruikers/contact personen in de andere organisatie. Deze verschillende on-premises Active Directory instanties kunnen vervolgens worden gesynchroniseerd met hun eigen Azure AD-tenants.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Onbevoegde clients gebruiken om toegang te krijgen tot de Azure AD Connect back-end
![Onbevoegde clients gebruiken om toegang te krijgen tot de Azure AD Connect back-end](./media/plan-connect-topologies/other-client-unsupported.png)

De Azure Active Directory Connect-server communiceert met Azure Active Directory via de Azure Active Directory Connect back-end. De enige software die kan worden gebruikt om met deze back-end te communiceren, is Azure Active Directory Connect. Het is niet mogelijk om te communiceren met de Azure Active Directory Connect back-end met andere software of methode. 

## <a name="next-steps"></a>Volgende stappen
Zie [aangepaste installatie van Azure AD Connect](how-to-connect-install-custom.md)voor meer informatie over het installeren van Azure AD Connect voor deze scenario's.

Meer informatie over de [Azure AD Connect synchronisatie](how-to-connect-sync-whatis.md) configuratie.

Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
