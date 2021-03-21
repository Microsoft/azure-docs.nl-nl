---
title: Overzicht van de Microsoft Azure StorSimple virtuele matrix
description: Beschrijft de virtuele StorSimple-matrix, een geïntegreerde opslag oplossing voor het beheer van opslag taken tussen een on-premises virtuele matrix en Microsoft Azure Cloud opslag.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 6d7d8c37d0e7ffacb3221a4f30a96ef6cccda819
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104674441"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Inleiding tot de virtuele StorSimple-matrix

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Overzicht

De Microsoft Azure StorSimple virtuele matrix is een geïntegreerde opslag oplossing voor het beheer van opslag taken tussen een on-premises virtuele matrix die wordt uitgevoerd in een Hyper Visor en Microsoft Azure Cloud opslag. De virtuele matrix is een efficiënte, rendabele en eenvoudig beheerde Bestands server-of iSCSI-server oplossing waarmee veel van de problemen en kosten die zijn gekoppeld aan ondernemings opslag en gegevens bescherming, worden geëlimineerd. De virtuele matrix is met name geschikt voor de opslag van zelden gebruikte archiverings gegevens.

In dit artikel vindt u een overzicht van de virtuele matrix. Dit zijn enkele andere bronnen:

* Zie [StorSimple Virtual Array Best practices](storsimple-ova-best-practices.md)(Engelstalig) voor aanbevolen procedures.
* Ga voor een overzicht van de StorSimple 8000 Series-apparaten naar [StorSimple 8000-serie: een hybride Cloud oplossing](storsimple-overview.md).
* Ga voor meer informatie over de StorSimple 5000/7000-serie apparaten naar de [online-Help van StorSimple](http://onlinehelp.storsimple.com/).

De virtuele matrix ondersteunt het iSCSI-of SMB-protocol (Server Message Block). Het wordt uitgevoerd op uw bestaande Hyper Visor infrastructuur en biedt lagen voor de Cloud, back-ups in de Cloud, snel terugzetten, herstel op item niveau en functies voor herstel na nood gevallen.

De volgende tabel bevat een overzicht van de belangrijkste functies van de virtuele StorSimple-matrix.

| Functie | StorSimple Virtual Array |
| --- | --- |
| Installatie vereisten |Maakt gebruik van virtualisatie-infra structuur (Hyper-V of VMware) |
| Beschikbaarheid |Eén knooppunt |
| Totale capaciteit (inclusief Cloud) |Tot 64 TB aan bruikbare capaciteit per virtuele matrix |
| Lokale capaciteit |390 GB tot 6,4 TB aan bruikbare capaciteit per virtuele matrix (moet 500 GB inrichten tot 8 TB aan schijf ruimte) |
| Systeem eigen protocollen |iSCSI of SMB |
| Beoogde hersteltijd (RTO) |iSCSI: minder dan 2 minuten, ongeacht de grootte |
| Recovery Point Objective (RPO) |Dagelijkse back-ups en back-ups op aanvraag |
| Opslag lagen |Maakt gebruik van warmte toewijzing om te bepalen welke gegevens moeten worden getierd of uitgesteld |
| Ondersteuning |Virtualisatie-infra structuur die wordt ondersteund door de leverancier |
| Prestaties |Varieert afhankelijk van de onderliggende infra structuur |
| Gegevens mobiliteit |Kan herstellen op hetzelfde apparaat of herstel op item niveau (bestands server) |
| Opslaglagen |Lokale Hyper Visor-opslag en Cloud |
| Share grootte |Gelaagd: Maxi maal 20 TB; lokaal vastgemaakt: Maxi maal 2 TB |
| Volume grootte |Gelaagd gelaagd: 500 GB tot 5 TB; lokaal vastgemaakt: 50 GB tot 200 GB <br> De maximale lokale reserve ring voor gelaagde volumes is 200 GB. |
| Momentopnamen |Crashconsistent |
| Herstel op itemniveau |Klikt gebruikers kunnen herstellen vanaf shares |

## <a name="why-use-storsimple"></a>Waarom StorSimple gebruiken?

StorSimple verbindt gebruikers en servers binnen enkele minuten met Azure Storage, zonder dat er wijzigingen in de toepassing worden aangebracht.

In de volgende tabel worden enkele van de belangrijkste voor delen beschreven die de StorSimple Virtual array-oplossing biedt.

| Functie | Voordeel |
| --- | --- |
| Transparante integratie |De virtuele matrix ondersteunt iSCSI of het SMB-protocol. De gegevens verplaatsing tussen de lokale laag en de Cloud laag is naadloos en transparant voor de gebruiker. |
| Lagere opslag kosten |Met StorSimple voorziet u voldoende lokale opslag om te voldoen aan de huidige vereisten voor de meest gebruikte warme gegevens. Naarmate opslag behoeften groeien, StorSimple lagen koud gegevens naar rendabele Cloud opslag. De gegevens worden ontdubbeld en gecomprimeerd voordat ze naar de cloud worden verzonden om de opslag vereisten en-kosten verder te verlagen. |
| Vereenvoudigd opslag beheer |StorSimple biedt gecentraliseerd beheer in de Cloud met behulp van StorSimple Apparaatbeheer om meerdere apparaten te beheren. |
| Verbeterd herstel na nood gevallen en naleving |StorSimple vereenvoudigt snellere herstel na nood gevallen door de meta gegevens onmiddellijk te herstellen en de gegevens naar behoefte te herstellen. Normale bewerkingen kunnen worden voortgezet met minimale onderbrekingen. |
| Gegevens mobiliteit |Gegevens die in de Cloud zijn gelaagd, kunnen worden geopend vanaf andere sites voor herstel en migratie doeleinden. U kunt alleen gegevens herstellen naar de oorspronkelijke virtuele matrix. U gebruikt echter herstel functies voor nood gevallen om de volledige virtuele matrix terug te zetten naar een andere virtuele matrix. |



## <a name="storsimple-workload-summary"></a>Samen vatting van StorSimple-werk belasting

Hieronder vindt u een overzicht van de ondersteunde StorSimple-werk belastingen.

|Scenario     |Workload     |Ondersteund      |Beperkingen               | Versies van toepassing|
|-------------|-------------|---------------|---------------------------|--------------------|
|Extern kantoor/filiaal (ROBO)  |Bestanden delen     |Ja      |Zie [maximum aantal limieten voor bestands server](storsimple-ova-limits.md).<br></br>Zie [systeem vereisten voor ondersteunde SMB-versies](storsimple-ova-system-requirements.md).| Alle versies     |
|Cloud archivering  |Archiveren van bestands deling     |Ja      |Zie [maximum aantal limieten voor bestands server](storsimple-ova-limits.md).<br></br>Zie [systeem vereisten voor ondersteunde SMB-versies](storsimple-ova-system-requirements.md).| Alle versies     |

De virtuele StorSimple-matrix is het meest geschikt voor zelden gebruikte gegevens. Hoewel de virtuele matrix een lokale cache heeft om prestaties te verbeteren, moeten gebruikers aannemen dat de services van het apparaat op de laagste laag van opslag (de Cloud). Elke virtuele array kan op ongeveer 100 Mbps schrijven en lezen naar Azure Storage. Deze koppeling wordt gedeeld met alle aanvragen die binnenkomen op het apparaat en kan een knel punt worden, zoals wordt weer gegeven in het onderstaande diagram.

![Cloud archivering](./media/storsimple-ova-overview/cloud-archiving.png)

Wanneer meerdere gelijktijdige gebruikers toegang hebben tot de virtuele matrix, delen ze allemaal de verbinding met Azure, waardoor de prestaties lager zijn. Er zijn geen gegarandeerde prestaties per gebruiker en het apparaat verwerkt afzonderlijke aanvragen wanneer ze binnenkomen.

De virtuele StorSimple-matrix is niet geschikt voor werk belastingen waarvoor hoge Beschik baarheid is vereist. De virtuele matrix is een apparaat met één knoop punt dat downtime ondervindt tijdens de installatie van software-updates. Beheerders moeten een onderhouds venster van 30 minuten 3-4 keer per jaar plannen.

## <a name="workflows"></a>Werkstromen

De virtuele StorSimple-matrix is met name geschikt voor de volgende werk stromen:

* [Opslag beheer op basis van de Cloud](#cloud-based-storage-management)
* [Locatie-onafhankelijke back-up](#location-independent-backup)
* [Gegevens bescherming en herstel na nood gevallen](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Opslag beheer op basis van de Cloud
U kunt de StorSimple Apparaatbeheer-service die wordt uitgevoerd in de Azure Portal gebruiken voor het beheren van gegevens die zijn opgeslagen op meerdere apparaten en op meerdere locaties. Dit is met name handig in gedistribueerde Branch-scenario's. U moet afzonderlijke instanties van de StorSimple-Apparaatbeheer-service maken om virtuele matrices en fysieke StorSimple-apparaten te beheren. De virtuele matrix maakt nu gebruik van de nieuwe Azure Portal in plaats van de klassieke Azure-Portal.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![opslag beheer op basis van de Cloud](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Locatie-onafhankelijke back-up
Met de virtuele matrix bieden cloud momentopnamen een locatie onafhankelijke, tijdgebonden kopie van een volume of share. Cloud momentopnamen zijn standaard ingeschakeld en kunnen niet worden uitgeschakeld. Voor alle volumes en shares wordt tegelijkertijd een back-up gemaakt via één dagelijks back-upbeleid en u kunt indien nodig aanvullende ad hoc back-ups maken.

### <a name="data-protection-and-disaster-recovery"></a>Gegevens bescherming en herstel na nood gevallen
De virtuele matrix ondersteunt de volgende scenario's voor gegevens bescherming en herstel na nood gevallen:

* **Volume of share herstellen** : gebruik de herstel bewerking als nieuwe werk stroom om een volume of share te herstellen. Gebruik deze methode om het hele volume of de share te herstellen.
* **Herstel op item niveau** : shares bieden vereenvoudigde toegang tot recente back-ups. U kunt eenvoudig een afzonderlijk bestand herstellen vanuit een speciale *. Backup-* map die beschikbaar is in de Cloud. Deze herstel mogelijkheid is door de gebruiker gestuurd en er is geen administratieve tussen komst vereist.
* **Herstel na nood** geval: gebruik de failover-functionaliteit om alle volumes of shares te herstellen naar een nieuwe virtuele matrix. U maakt de nieuwe virtuele matrix en registreert deze bij de StorSimple-Apparaatbeheer service, waarna failover wordt uitgevoerd voor de oorspronkelijke virtuele matrix. De nieuwe virtuele matrix gaat dan uit van de ingerichte resources.

## <a name="storsimple-virtual-array-components"></a>Onderdelen van de virtuele matrix van StorSimple

De virtuele matrix bevat de volgende onderdelen:

* [Virtuele matrix](#virtual-array) : een Hybrid Cloud Storage apparaat op basis van een virtuele machine die is ingericht in uw gevirtualiseerde omgeving of Hyper Visor.
* [StorSimple Apparaatbeheer service](#storsimple-device-manager-service) : een uitbrei ding van de Azure Portal waarmee u een of meer StorSimple-apparaten kunt beheren vanuit één webinterface die u vanaf verschillende geografische locaties kunt openen. U kunt de StorSimple Apparaatbeheer-service gebruiken voor het maken en beheren van services, het weer geven en beheren van apparaten en waarschuwingen en het beheren van volumes, shares en bestaande moment opnamen.
* [Lokale web gebruikers interface](#local-web-user-interface) : een webinterface die wordt gebruikt om het apparaat te configureren, zodat het verbinding kan maken met het lokale netwerk en het apparaat vervolgens kan registreren bij de StorSimple-Apparaatbeheer service. 
* [Opdracht regel interface](#command-line-interface) : een Windows Power shell-interface die u kunt gebruiken om een ondersteunings sessie te starten op de virtuele matrix.
  In de volgende secties wordt elk onderdeel uitvoeriger beschreven en wordt uitgelegd hoe de oplossing gegevens rangschikt, opslag toewijst en opslag beheer en gegevens beveiliging vereenvoudigt.

### <a name="virtual-array"></a>Virtuele matrix

De virtuele matrix is een opslag oplossing met één knoop punt dat primaire opslag biedt, communicatie beheert met Cloud opslag en helpt de beveiliging en vertrouwelijkheid te garanderen van alle gegevens die op het apparaat zijn opgeslagen.

De virtuele matrix is beschikbaar in één model dat kan worden gedownload. De virtuele matrix heeft een maximale capaciteit van 6,4 TB op het apparaat (met een onderliggende opslag vereiste van 8 TB) en 64 TB, inclusief Cloud opslag.

De virtuele matrix heeft de volgende functies:

* Het is rendabel. Het maakt gebruik van uw bestaande virtualisatie-infra structuur en kan worden geïmplementeerd op uw bestaande Hyper-V-of VMware-Hyper Visor.
* Het bevindt zich in het Data Center en kan worden geconfigureerd als een iSCSI-server of een bestands server.
* Het is geïntegreerd met de Cloud.
* Back-ups worden opgeslagen in de Cloud, waardoor herstel na nood gevallen en herstel op item niveau (ILR) kan worden vereenvoudigd.
* U kunt updates Toep assen op de virtuele matrix, op dezelfde manier als u deze toepast op een fysiek apparaat.

> [!NOTE]
> Een virtuele matrix kan niet worden uitgevouwen. Daarom is het belang rijk om voldoende opslag ruimte in te richten bij het maken van de virtuele matrix.

### <a name="storsimple-device-manager-service"></a>StorSimple-apparaatbeheerfunctie

Microsoft Azure StorSimple biedt een webgebaseerde gebruikers interface, de StorSimple Apparaatbeheer-service, waarmee u StorSimple-opslag centraal kunt beheren. U kunt de StorSimple Apparaatbeheer-service gebruiken om de volgende taken uit te voeren:

* Meerdere virtuele StorSimple-matrices beheren vanuit één service.
* Beveiligings instellingen voor StorSimple virtuele matrices configureren en beheren. (Versleuteling in de Cloud is afhankelijk van Microsoft Azure Api's.)
* Configureer referenties en eigenschappen van het opslag account.
* Volumes of shares configureren en beheren.
* Back-ups maken van gegevens op volumes of shares en deze herstellen.
* Prestaties bewaken.
* Controleer de systeem instellingen en Identificeer mogelijke problemen.

U gebruikt de StorSimple Apparaatbeheer-service voor het dagelijks beheer van uw virtuele matrix.

Ga voor meer informatie naar [Gebruik de StorSimple Apparaatbeheer-service om uw StorSimple-apparaat te beheren](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokale web-gebruikers interface

De virtuele matrix bevat een op het web gebaseerde gebruikers interface die wordt gebruikt voor eenmalige configuratie en registratie van het apparaat met de StorSimple-Apparaatbeheer service. U kunt deze gebruiken om de virtuele matrix af te sluiten en opnieuw op te starten, diagnostische tests uit te voeren, software bij te werken, het beheerders wachtwoord van het apparaat te wijzigen, systeem logboeken weer te geven en contact op te nemen Microsoft Ondersteuning om een service aanvraag te doen.

Voor informatie over het gebruik van de Web-UI, gaat u naar [de webgebaseerde gebruikers interface gebruiken om uw virtuele StorSimple-matrix te beheren](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Opdracht regel interface

Met de meegeleverde Windows Power shell-interface kunt u een ondersteunings sessie initiëren met Microsoft Ondersteuning zodat u problemen kunt oplossen en oplossen die kunnen optreden op uw virtuele array.

## <a name="storage-management-technologies"></a>Technologieën voor opslag beheer

Naast de virtuele matrix en andere onderdelen, gebruikt de StorSimple-oplossing de volgende software technologieën om snelle toegang tot belang rijke gegevens te bieden, het opslag gebruik te verminderen en gegevens te beveiligen die zijn opgeslagen in uw virtuele array:

* [Automatische opslag lagen](#automatic-storage-tiering) 
* [Lokaal vastgemaakte shares en volumes](#locally-pinned-shares-and-volumes)
* Ontdubbeling en compressie voor een gegevenslaag of een back-up naar de Cloud 
* [Geplande en on-demand back-ups](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatische opslag lagen
De virtuele matrix maakt gebruik van een nieuwe methode om de opgeslagen gegevens in de virtuele matrix en de cloud te beheren. Er zijn slechts twee lagen: de lokale virtuele matrix en Azure-Cloud opslag. De StorSimple-virtuele matrix rangschikt gegevens automatisch in de lagen op basis van een heatmap, waarmee het huidige gebruik, de leeftijd en de relaties met andere gegevens worden bijgehouden. Gegevens die het meest actief zijn (nieuwste) worden lokaal opgeslagen, terwijl minder actieve en inactieve gegevens automatisch naar de cloud worden gemigreerd. (Alle back-ups worden opgeslagen in de Cloud.) StorSimple past de gegevens-en opslag toewijzingen aan en herordent de wijzigingen in de gebruiks patronen. Zo kan een deel van de gegevens in de loop van de tijd minder actief worden. Omdat deze geleidelijk minder actief wordt, wordt deze in de Cloud gelaagd. Als dezelfde gegevens weer actief worden, wordt deze in de opslag matrix gelaagd.

Gegevens voor een bepaalde gelaagde share of volume worden gegarandeerd een eigen lokale laag ruimte (ongeveer 10 procent van de totale ingerichte ruimte voor die share of dit volume). Hoewel hierdoor de beschik bare opslag ruimte in de virtuele matrix voor die share of dit volume wordt gereduceerd, wordt voor komen dat het trapsgewijs scha kelen van een share of volume wordt beïnvloed door de behoeften van andere shares of volumes. Een zeer drukke werk belasting op één share of volume kan dus niet alle andere werk belastingen naar de Cloud afdwingen.

Gelaagde volumes die zijn gemaakt voor iSCSI, hebben een maximale lokale reserve ring van 200 GB, ongeacht de grootte van het volume.

![automatische opslag lagen](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> U kunt een lokaal vastgemaakt volume opgeven. in dat geval blijven de gegevens op de virtuele matrix en worden ze nooit op de Cloud gelaagd. Ga voor meer informatie naar [lokaal vastgemaakte shares en volumes](#locally-pinned-shares-and-volumes).

> [!IMPORTANT]
> Wanneer u StorSimple gebruikt, kunt u geen blobs naar archivering converteren, zelfs niet als uw apparaat is uitgevallen. Als u gegevens wilt ophalen van het apparaat, moet u de blobs van het archief opnieuw inpluggen tot het warme of coole type. Dit resulteert in aanzienlijke kosten.


### <a name="locally-pinned-shares-and-volumes"></a>Lokaal vastgemaakte shares en volumes

U kunt de juiste shares en volumes maken die lokaal zijn vastgemaakt. Op deze manier zorgt u ervoor dat de gegevens die nodig zijn voor kritieke toepassingen in de virtuele matrix blijven en nooit in de cloud worden gelaagd. Lokaal vastgemaakte shares en volumes hebben de volgende kenmerken:

* Ze zijn niet onderhevig aan Cloud latenties of verbindings problemen.
* Ze kunnen nog steeds profiteren van StorSimple-functies voor Cloud back-ups en herstel na nood gevallen.

U kunt een lokaal vastgemaakte share of volume herstellen als een gelaagde of gelaagde share of gedeeld volume. 

Voor meer informatie over lokaal vastgemaakte volumes gaat u naar [de StorSimple Apparaatbeheer-service gebruiken voor het beheren van volumes](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Ontdubbeling en compressie voor een gegevenslaag of een back-up naar de Cloud

StorSimple maakt gebruik van ontdubbeling en gegevens compressie om de opslag vereisten in de Cloud verder te reduceren. Ontdubbeling vermindert de totale hoeveelheid gegevens die is opgeslagen door redundantie in de opgeslagen gegevensset te elimineren. Als de gegevens worden gewijzigd, worden de ongewijzigde gegevens door StorSimple genegeerd en worden alleen de wijzigingen vastgelegd. Daarnaast vermindert StorSimple de hoeveelheid opgeslagen gegevens door dubbele gegevens te identificeren en te verwijderen.

> [!NOTE]
> Gegevens die zijn opgeslagen op de virtuele matrix, worden niet ontdubbeld of gecomprimeerd. Alle ontdubbeling en compressie vindt plaats net voordat de gegevens naar de cloud worden verzonden.


### <a name="scheduled-and-on-demand-backups"></a>Geplande en on-demand back-ups

Met StorSimple-functies voor gegevens bescherming kunt u back-ups op aanvraag maken. Daarnaast zorgt een standaard back-upschema ervoor dat er dagelijks een back-up van gegevens wordt gemaakt. Back-ups worden gemaakt in de vorm van incrementele moment opnamen, die worden opgeslagen in de Cloud. Moment opnamen, waarin alleen de wijzigingen sinds de laatste back-up worden vastgelegd, kunnen snel worden gemaakt en hersteld. Deze moment opnamen kunnen kritiek zijn in nood herstel scenario's, omdat ze secundaire opslag systemen vervangen (zoals tape back-ups) en u zo nodig gegevens kunt herstellen naar uw Data Center of naar andere sites.

## <a name="managing-personal-information"></a>Persoonlijke gegevens beheren

Met de StorSimple-Apparaatbeheer voor de virtuele reeks worden persoonlijke gegevens in twee sleutel instanties verzameld:
 - Gebruikers instellingen voor waarschuwingen waarbij e-mail adressen van gebruikers zijn geconfigureerd. Deze informatie kan worden gewist door de beheerder. 
 - Gebruikers die toegang hebben tot de gegevens op de shares. Een lijst met gebruikers die toegang hebben tot de gegevens van de share, worden weer gegeven en kan worden geëxporteerd. Deze lijst wordt verwijderd wanneer de share wordt verwijderd.

Raadpleeg het [privacybeleid van micro soft in het vertrouwens centrum](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het voorbereiden van de virtuele matrix Portal](storsimple-virtual-array-deploy1-portal-prep.md).
