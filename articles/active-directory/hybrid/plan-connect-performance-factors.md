---
title: Factoren die invloed hebben op de prestaties van Azure AD Connect
description: In dit document wordt uitgelegd hoe verschillende factoren van invloed zijn op de Azure AD Connect Provisioning engine. Met deze factoren kunnen organisaties hun Azure AD Connect-implementatie plannen om ervoor te zorgen dat ze voldoen aan hun synchronisatie vereisten.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15bcb0f7ca30c343072da396abeac8d08dee03a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90087006"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Factoren die invloed hebben op de prestaties van Azure AD Connect

Azure AD Connect uw Active Directory synchroniseert met Azure AD. Deze server is een essentieel onderdeel van het verplaatsen van uw gebruikers identiteiten naar de Cloud. De belangrijkste factoren die van invloed zijn op de prestaties van een Azure AD Connect zijn:

| **Ontwerp factor**| **Definition** |
|:-|-|
| Topologie| De distributie van de eind punten en onderdelen Azure AD Connect moet worden beheerd in het netwerk. |
| Schalen| Het aantal objecten zoals de gebruikers, groepen en organisatie-eenheden die worden beheerd door Azure AD Connect. |
| Hardware| De hardware (fysiek of virtueel) voor de Azure AD Connect en de afhankelijke prestatie capaciteit van elk hardwareonderdeel, inclusief CPU, geheugen, netwerk en configuratie van harde schijven. |
| Configuratie| Hoe Azure AD Connect de directory's en informatie verwerkt. |
| Laden| De frequentie van object wijzigingen. De belasting kan variëren tijdens een uur, dag of week. Afhankelijk van het onderdeel, moet u mogelijk worden ontworpen voor piek belasting of gemiddelde belasting. |

Het doel van dit document is het beschrijven van de factoren die van invloed zijn op de prestaties van de Azure AD Connect Provisioning engine. Grote of complexe organisaties (organisaties die meer dan 100.000 objecten inrichten) kunnen gebruikmaken van de aanbevelingen voor het optimaliseren van hun Azure AD Connect-implementatie, als ze problemen ondervinden met de prestaties die hier worden beschreven. De andere onderdelen van Azure AD Connect, zoals [Azure AD Connect status](how-to-connect-health-agent-install.md) en agents, worden hier niet behandeld.

> [!IMPORTANT]
> Micro soft biedt geen ondersteuning voor het wijzigen of bewerk Azure AD Connect buiten de acties die formeel zijn gedocumenteerd. Een van deze acties kan leiden tot een inconsistente of niet-ondersteunde status van Azure AD Connect synchronisatie. Als gevolg hiervan kan micro soft geen technische ondersteuning bieden voor dergelijke implementaties.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect onderdeel factoren

In het volgende diagram ziet u een architectuur op hoog niveau van het inrichten van de engine die verbinding maakt met één forest, hoewel er meerdere forests worden ondersteund. Deze architectuur laat zien hoe de verschillende onderdelen met elkaar communiceren.

![Diagram toont hoe de verbonden Directory's en Azure AD Connect inrichtings engine communiceren, met inbegrip van connector ruimte en omgekeerde onderdelen in een SQL Database. ](media/plan-connect-performance-factors/AzureADConnentInternal.png)

De inrichtings Engine maakt verbinding met elk Active Directory forest en Azure AD. Het proces voor het lezen van informatie uit elke map wordt importeren genoemd. Exporteren verwijst naar het bijwerken van de directory's van de inrichtings engine. Met synchronisatie worden de regels geëvalueerd van de manier waarop de objecten in de inrichtings engine worden geplaatst. Voor een diep gaande kennis kunt u verwijzen naar [Azure AD Connect Sync: inzicht in de architectuur](./concept-azure-ad-connect-sync-architecture.md).

Azure AD Connect maakt gebruik van de volgende faserings gebieden, regels en processen om de synchronisatie van Active Directory naar Azure AD toe te staan:

* **Connector ruimte (CS)** : objecten van elke verbonden Directory (cd), de daad werkelijke directory's, worden hier eerst klaargezet voordat ze kunnen worden verwerkt door de inrichtings engine. Azure AD heeft een eigen CS en elk forest waarmee u verbinding maakt, heeft een eigen CS.
* **Omgekeerd (MV)** : de objecten die moeten worden gesynchroniseerd, worden hier gemaakt op basis van de synchronisatie regels. Objecten moeten aanwezig zijn in de MV voordat ze objecten en kenmerken kunnen vullen naar de andere verbonden directory's. Er is slechts één MV.
* **Synchronisatie regels** : ze bepalen welke objecten worden gemaakt (geprojecteerd) of verbonden (gekoppelde) aan objecten in de MV. De synchronisatie regels bepalen ook welke kenmerk waarden worden gekopieerd of getransformeerd naar en uit de directory's.
* **Profielen uitvoeren** : bundelt de proces stappen voor het kopiëren van objecten en hun kenmerk waarden volgens de synchronisatie regels tussen de faserings gebieden en verbonden directory's.

Er zijn verschillende uitvoerings profielen voor het optimaliseren van de prestaties van de inrichtings engine. De meeste organisaties gebruiken de standaard schema's en voeren profielen uit voor normale bewerkingen, maar sommige organisaties moeten mogelijk [het schema wijzigen](./how-to-connect-sync-feature-scheduler.md) of andere uitvoerings profielen activeren voor ongebruikelijke situaties. De volgende uitvoerings profielen zijn beschikbaar:

### <a name="initial-sync-profile"></a>Eerste synchronisatie profiel

Het eerste synchronisatie profiel is het proces van het voor de eerste keer lezen van de verbonden directory's, zoals een Active Directory-forest. Vervolgens voert het een analyse uit voor alle vermeldingen in de data base van de synchronisatie-engine. De eerste cyclus maakt nieuwe objecten in azure AD en neemt meer tijd in beslag als uw Active Directory-forests groot zijn. De eerste synchronisatie omvat de volgende stappen:

1. Volledige import op alle connectors
2. Volledige synchronisatie op alle connectors
3. Exporteren op alle connectors

### <a name="delta-sync-profile"></a>Delta synchronisatie profiel

Als u het synchronisatie proces wilt optimaliseren, worden de wijzigingen (maken, verwijderen en bijwerken) van objecten in uw verbonden directory's sinds de laatste synchronisatie procedure door uitgevoerd. Het Delta synchronisatie profiel wordt standaard elke 30 minuten uitgevoerd. Organisaties moeten ervoor zorgen dat het duurt minder dan 30 minuten om te controleren of de Azure AD up-to-date is. Als u de status van Azure AD Connect wilt controleren, gebruikt u de [Health Monitoring-Agent](how-to-connect-health-sync.md) om eventuele problemen met het proces te bekijken. Het Delta synchronisatie profiel bevat de volgende stappen:

1. Delta-import op alle connectors
2. Delta synchronisatie op alle connectors
3. Exporteren op alle connectors

Een typische Delta-synchronisatie scenario voor ondernemingen is:

- ~ 1% van de objecten zijn verwijderd
- ~ 1% van de objecten zijn gemaakt
- ~ 5% van de objecten zijn gewijzigd

De wijzigings hoeveelheid kan variëren, afhankelijk van hoe vaak uw organisatie gebruikers bijwerkt in uw Active Directory. Zo kan een hoger wijzigings tarief optreden bij het aannemen en verminderen van de werk kracht.

### <a name="full-sync-profile"></a>Profiel voor volledige synchronisatie

Een volledige synchronisatie cyclus is vereist als u een van de volgende configuratie wijzigingen hebt aangebracht:



- Verg root het bereik van de objecten of kenmerken die moeten worden geïmporteerd uit de verbonden directory's. Wanneer u bijvoorbeeld een domein of OE toevoegt aan uw import bereik.
- Wijzigingen aangebracht in de synchronisatie regels. Wanneer u bijvoorbeeld een nieuwe regel maakt om de titel van een gebruiker in azure AD in te vullen vanuit extension_attribute3 in Active Directory. Deze update vereist dat de inrichtings engine alle bestaande gebruikers opnieuw onderzoekt om hun titels bij te werken om de wijziging door te voeren.

De volgende bewerkingen zijn opgenomen in een volledige synchronisatie cyclus:

1. Volledige import op alle connectors
2. Volledige/Delta synchronisatie op alle connectors
3. Exporteren op alle connectors

> [!NOTE]
> Een zorgvuldige planning is vereist bij het bulksgewijs bijwerken van veel objecten in uw Active Directory of Azure AD. Bulksgewijs bijwerken zorgt ervoor dat het Delta synchronisatie proces tijdens het importeren langer duurt, omdat een groot aantal objecten is gewijzigd. Lange invoer kan gebeuren, zelfs als de bulk update geen invloed heeft op het synchronisatie proces. Als u bijvoorbeeld licenties toewijst aan een groot aantal gebruikers in azure AD, treedt er een lange Importeer cyclus op uit Azure AD, maar worden er geen kenmerk wijzigingen in Active Directory.

### <a name="synchronization"></a>Synchronisatie

De runtime van het synchronisatie proces heeft de volgende prestatie kenmerken:

* Synchronisatie is één thread, wat betekent dat de inrichtings engine geen parallelle verwerking van profielen met verbonden directory's, objecten of kenmerken uitvoert.
* De invoer tijd wordt lineair uitgebreid met het aantal objecten dat wordt gesynchroniseerd. Als 10.000-objecten bijvoorbeeld 10 minuten duren om te importeren, neemt 20.000 objecten ongeveer 20 minuten op dezelfde server in beslag.
* Exporteren is ook lineair.
* De synchronisatie neemt exponentieel toe op basis van het aantal objecten met verwijzingen naar andere objecten. Groepslid maatschappen en geneste groepen hebben de belangrijkste prestatie-impact, omdat hun leden naar gebruikers objecten of andere groepen verwijzen. Deze verwijzingen moeten worden gevonden en verwezen naar werkelijke objecten in de MV om de synchronisatie cyclus te volt ooien.

### <a name="filtering"></a>Filteren

De grootte van de Active Directory topologie die u wilt importeren, is het aantal factoren die van invloed zijn op de prestaties en de totale tijd dat de interne onderdelen van de inrichtings engine worden uitgevoerd.

[Filters](./how-to-connect-sync-configure-filtering.md) moeten worden gebruikt om de objecten te beperken tot de synchronisatie. Hiermee voor komt u dat onnodige objecten worden verwerkt en geëxporteerd naar Azure AD. In de volg orde van voor keur zijn de volgende technieken voor filteren beschikbaar:



- **Filteren op basis van een domein** : gebruik deze optie om specifieke domeinen te selecteren die u wilt synchroniseren met Azure AD. U moet domeinen toevoegen aan en verwijderen uit de configuratie van de synchronisatie-engine wanneer u wijzigingen aanbrengt in uw on-premises infra structuur nadat u Azure AD Connect synchronisatie hebt geïnstalleerd.
- **Filtering organisatie-eenheid (OE)** : maakt gebruik van organisatie-eenheden om specifieke objecten in Active Directory domeinen te richten op het inrichten van Azure AD. Filteren op OE is het tweede aanbevolen filter mechanisme, omdat het gebruik maakt van eenvoudige LDAP-Scope query's om een kleinere subset van objecten te importeren uit Active Directory.
- **Kenmerk filtering per object** : gebruikt de kenmerk waarden voor objecten om te bepalen of specifiek object in Active Directory is ingericht in azure AD. Kenmerk filters zijn handig voor het verfijnen van uw filters, wanneer het filteren van domeinen en organisatie-eenheden niet voldoet aan de specifieke filter vereisten. Kenmerk filtering beperkt het import tijdstip niet, maar kan de synchronisatie-en export tijden verminderen.
- **Filteren op basis van een groep** : gebruikt groepslid maatschap om te bepalen of objecten moeten worden ingericht in azure AD. Filteren op basis van een groep is alleen geschikt voor het testen van situaties en wordt niet aanbevolen voor productie, omdat er extra overhead nodig is om het groepslid maatschap tijdens de synchronisatie cyclus te controleren.

Veel permanente [disconnecters-objecten](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) in uw Active Directory-CS kunnen meer synchronisatie tijden veroorzaken, omdat de inrichtings engine elk losgekoppelde object opnieuw moet evalueren voor mogelijke verbinding in de synchronisatie cyclus. Als u dit probleem wilt verholpen, kunt u een van de volgende aanbevelingen overwegen:



- Plaats de objecten van de disconnecter buiten het bereik om te importeren met behulp van domein-of OE-filtering.
- Project/Voeg de objecten toe aan de MV en stel het kenmerk [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) in op True om het inrichten van deze objecten in de Azure AD CS te voor komen.

> [!NOTE]
> Gebruikers kunnen problemen met de compatibiliteit of toepassings machtigingen ondervinden wanneer te veel objecten worden gefilterd. In een hybride implementatie voor Exchange Online worden bijvoorbeeld gebruikers met on-premises post vakken meer gebruikers weer geven in de algemene adres lijst dan gebruikers met post vakken in Exchange Online. In andere gevallen is het mogelijk dat een gebruiker in een Cloud-app toegang verleent aan een andere gebruiker die geen deel uitmaakt van het bereik van de gefilterde set met objecten.

### <a name="attribute-flows"></a>Kenmerk stromen

Kenmerk stromen is het proces voor het kopiëren of transformeren van de kenmerk waarden van objecten van een verbonden map naar een andere verbonden Directory. Ze worden gedefinieerd als onderdeel van de synchronisatie regels. Als bijvoorbeeld het telefoon nummer van een gebruiker in uw Active Directory is gewijzigd, wordt het telefoon nummer in azure AD bijgewerkt. Organisaties kunnen [de kenmerk stromen wijzigen](./how-to-connect-sync-change-the-configuration.md) in suite-verschillende vereisten. U kunt het beste de bestaande kenmerk stromen kopiëren voordat u ze wijzigt.

Eenvoudige omleidingen, zoals het debiet van een kenmerk waarde naar een ander kenmerk, hebben geen invloed op de prestaties van materialen. Een voor beeld van een omleiding is het door lopen van een mobiel nummer in Active Directory naar het telefoon nummer van het kantoor in azure AD.

Het transformeren van kenmerk waarden kan invloed hebben op de prestaties van het synchronisatie proces. Het transformeren van kenmerk waarden omvat het wijzigen, opmaken, samen voegen of aftrekken van waarden van kenmerken.

Organisaties kunnen voor komen dat bepaalde kenmerken naar Azure AD stromen, maar dit heeft geen invloed op de prestaties van de inrichtings engine.

> [!NOTE]
> Verwijder geen ongewenste kenmerk stromen in uw synchronisatie regels. U wordt aangeraden deze in plaats daarvan uit te scha kelen, omdat verwijderde regels opnieuw worden gemaakt tijdens Azure AD Connect-upgrades.

## <a name="azure-ad-connect-dependency-factors"></a>Afhankelijkheids factoren Azure AD Connect

De prestaties van Azure AD Connect zijn afhankelijk van de prestaties van de verbonden directory's die worden geïmporteerd en geëxporteerd naar. Bijvoorbeeld de grootte van de Active Directory die moet worden geïmporteerd of de netwerk latentie naar de Azure AD-service. Het SQL database dat de inrichtings engine gebruikt, heeft ook invloed op de algehele prestaties van de synchronisatie cyclus.

### <a name="active-directory-factors"></a>Active Directory factoren

Zoals eerder vermeld, beïnvloedt het aantal te importeren objecten de prestaties aanzienlijk. De [hardware en vereisten voor Azure AD Connect](how-to-connect-install-prerequisites.md) een overzicht van specifieke hardware-lagen op basis van de grootte van uw implementatie. Azure AD Connect ondersteunen alleen specifieke topologieën zoals beschreven in [topologieën voor Azure AD Connect](plan-connect-topologies.md). Er zijn geen prestatie optimalisaties en aanbevelingen voor niet-ondersteunde topologieën.

Zorg ervoor dat uw Azure AD Connect-server voldoet aan de hardwarevereisten op basis van uw Active Directory grootte die u wilt importeren. Een onjuiste of langzame netwerk verbinding tussen de Azure AD Connect server en uw Active Directory domein controllers kunnen uw import vertragen.

### <a name="azure-ad-factors"></a>Azure AD-factoren

Azure AD maakt gebruik van beperking om de Cloud service te beschermen tegen DoS-aanvallen (Denial of service). Momenteel heeft Azure AD een beperkings limiet van 7.000 schrijf bewerkingen per 5 minuten (84.000 per uur). De volgende bewerkingen kunnen bijvoorbeeld worden beperkt:



- Azure AD Connect exporteren naar Azure AD.
- Power shell-scripts of-toepassingen die de Azure AD rechtstreeks op de achtergrond bijwerken, zoals lidmaatschappen van dynamische groepen.
- Gebruikers updaten hun eigen identiteits records, zoals registratie voor MFA of SSPR (self-service voor wachtwoord herstel).
- Bewerkingen binnen het Graphical User Interface.

Plan voor implementatie-en onderhouds taken om ervoor te zorgen dat uw Azure AD Connect synchronisatie cyclus niet wordt beïnvloed door beperkings limieten. Als u bijvoorbeeld een grote huur Golf hebt waarin u duizenden gebruikers identiteiten maakt, kan dit ertoe leiden dat updates voor dynamische groepslid maatschappen, licentie toewijzingen en selfservice voor wachtwoord herstel worden ingesteld. Het is beter om deze schrijf bewerkingen over enkele uren of enkele dagen uit te breiden.

### <a name="sql-database-factors"></a>SQL database factoren

De grootte van de bron Active Directory topologie heeft invloed op uw SQL database prestaties. Volg de [hardwarevereisten](how-to-connect-install-prerequisites.md) voor de SQL Server-Data Base en houd rekening met de volgende aanbevelingen:



- Organisaties met meer dan 100.000 gebruikers kunnen netwerk latentie verminderen door SQL database en de inrichtings engine te verplaatsen op dezelfde server.
- Als gevolg van de vereisten voor invoer en uitvoer (I/O) voor hoge schijven van het synchronisatie proces, gebruikt u een SSD-schijf (Solid-State Drive) voor de SQL database van de inrichtings engine voor optimale resultaten, als dit niet mogelijk is, overweeg dan RAID 0 of RAID 1-configuraties.
- Voer geen volledige synchronisatie preventief uit. Dit leidt tot onnodig verloop en tragere reactie tijden.

## <a name="conclusion"></a>Conclusie

Houd rekening met de volgende aanbevelingen om de prestaties van uw Azure AD Connect-implementatie te optimaliseren:



- Gebruik de [aanbevolen hardwareconfiguratie](how-to-connect-install-prerequisites.md) op basis van de grootte van uw implementatie voor de Azure AD Connect-server.
- Wanneer u Azure AD Connect in grootschalige implementaties bijwerkt, kunt u overwegen om een [Swing migratie methode](./how-to-upgrade-previous-version.md#swing-migration)te gebruiken om ervoor te zorgen dat u over de minste downtime en de beste betrouw baarheid beschikt. 
- Gebruik SSD voor de SQL database voor de beste schrijf prestaties.
- Filter het Active Directory bereik zodanig dat er alleen objecten worden opgenomen die moeten worden ingericht in azure AD, met behulp van domein, OE of kenmerk filtering.
- Als u de standaard regels voor kenmerk stroom wilt wijzigen, kopieert u eerst de regel, wijzigt u de kopie en schakelt u de oorspronkelijke regel uit. Vergeet niet om een volledige synchronisatie opnieuw uit te voeren.
- Plan voldoende tijd voor het eerste volledige profiel voor de synchronisatie.
- Streef naar het volt ooien van de Delta synchronisatie cyclus in 30 minuten. Als het Delta synchronisatie profiel niet binnen 30 minuten is voltooid, wijzigt u de standaard synchronisatie frequentie zodat deze een volledige Delta synchronisatie cyclus bevat.
- Controleer uw [Azure AD Connect synchronisatie status](how-to-connect-health-agent-install.md) in azure AD.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).