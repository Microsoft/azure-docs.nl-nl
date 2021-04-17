---
title: Functies voor hoge beschikbaarheid Oracle op Azure BareMetal
description: Meer informatie over de functies die beschikbaar zijn in BareMetal voor een Oracle-database.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 75032cc6351504a8400be43d05091d2b47484229
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590429"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Functies voor hoge beschikbaarheid Oracle op Azure BareMetal

In dit artikel kijken we naar de belangrijkste functies voor hoge beschikbaarheid en herstel na noodherstel van Oracle.

Oracle biedt veel functies voor het bouwen van een robuust platform voor het uitvoeren van Oracle-databases. Hoewel geen enkele functie dekking biedt voor elk type fout, zorgt het combineren van technologieën op een gelaagde manier voor een zeer beschikbaar systeem. Niet elke functie is vereist om beschikbaarheid te behouden. Maar het combineren van strategieën biedt u de beste bescherming tegen het assortiment fouten dat zich voordoet. 

## <a name="flashback-database"></a>Flashback-database

De [functie Flashback-database](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) is Oracle Database Enterprise Edition. De database wordt terugspoelen naar een bepaald tijdstip. Deze functie verschilt van een herstelpunt van [Recovery Manager (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) in die zin dat deze terugstroomt van het huidige tijdstip, in plaats van dat er na een herstel naar voren wordt geschoven. Dit resulteert in veel snellere voltooiingstijden.
 
U kunt deze functie naast [Oracle Data Guard gebruiken.](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590) Met Flashback Database kan een databasebeheerder een mislukte database opnieuw in een Data Guard-configuratie herstellen zonder een volledige RMAN-herstel en -herstel. Met deze functie kunt u de mogelijkheid voor herstel na noodherstel (en eventuele offloaded rapportage- en back-upvoordelen met Active Data Guard) veel sneller herstellen.
 
U kunt deze functie gebruiken in plaats van een vertraagde redo op de stand-bydatabase. Een stand-bydatabase kan worden teruggelicht naar een punt vóór een probleem.
 
De Oracle Database houdt flashbacklogboeken bij in het gebied voor snel herstel (FRA). Deze logboeken zijn gescheiden van de redo-logboeken en vereisen meer ruimte in de FRA. Standaard worden er 24 uur aan flashback-logboeken bewaard, maar u kunt deze instelling wijzigen volgens uw vereisten.

## <a name="oracle-real-application-clusters"></a>Echte Oracle-toepassingsclusters

[Met Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) kunnen meerdere onderling verbonden servers worden weergegeven als één databaseservice voor eindgebruikers en toepassingen. Deze functie verwijdert veel storingspunten en is een herkende actieve/actieve oplossing met hoge beschikbaarheid voor Oracle-databases.

Zoals u kunt zien in de volgende afbeelding van overzicht van hoge beschikbaarheid en [best practices](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)van Oracle, wordt één RAC-database weergegeven in de toepassingslaag. De toepassingen maken verbinding met de SCAN-listener, die het verkeer naar een specifiek database-exemplaar stuurt. RAC beheert de toegang van meerdere exemplaren om gegevensconsistentie te behouden tussen afzonderlijke rekenknooppunten.

![Diagram met een overzicht van de architectuur van Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Als het ene exemplaar mislukt, wordt de service voortgezet op alle andere resterende exemplaren. Elke database die op de oplossing is geïmplementeerd, heeft een RAC-configuratie van n+1, waarbij n de minimale verwerkingskracht is die nodig is om de service te ondersteunen.

Oracle Database-services worden gebruikt om verbindingen toe te staan een fail over te brengen tussen knooppunten wanneer een exemplaar transparant uitvalt. Dergelijke fouten kunnen gepland of ongepland zijn. Als u met de toepassing werkt (snelle toepassingsmeldingsgebeurtenissen), wordt de service verplaatst naar een overlevend knooppunt wanneer een exemplaar niet beschikbaar wordt gemaakt. De service wordt verplaatst naar een knooppunt dat is opgegeven in de serviceconfiguratie als voorkeur of beschikbaar.

Een andere belangrijke functie van Oracle Database services is alleen het starten van een service, afhankelijk van de rol ervan. Deze functie wordt gebruikt wanneer er een Data Guard-failover is. Alle patronen die zijn geïmplementeerd met Data Guard zijn vereist om een databaseservice te koppelen aan een Data Guard-rol.

Er kunnen bijvoorbeeld twee services worden gemaakt: MIJN \_ \_ DB-APP en MIJN \_ DB \_ AS. De MY \_ DB \_ APP-service wordt alleen gestart wanneer het database-exemplaar wordt gestart met de Data Guard-rol van PRIMARY. MY \_ DB AS wordt alleen gestart wanneer de Data \_ Guard-rol FYSIEK \_ STAND-BY is. Met deze configuratie kunnen toepassingen naar de APP-service wijzen, terwijl ze ook rapporteren. Deze kunnen worden ge-offload naar active stand-by en naar de \_ \_ AS-service worden gewezen.

## <a name="oracle-data-guard"></a>Oracle Data Guard

Met Data Guard kunt u een identieke kopie van een database onderhouden op afzonderlijke fysieke hardware. In het ideale moment moet de hardware geografisch worden gescheiden. Data Guard plaatst geen limiet op de afstand, hoewel afstand invloed heeft op de beschermingsmodi. Een grotere afstand voegt latentie toe tussen sites, waardoor sommige opties (zoals synchrone replicatie) niet langer haalbaar zijn.

Data Guard biedt voordelen ten opzichte van replicatie op opslagniveau:

- Omdat de replicatie databasebewust is, wordt alleen relevant verkeer gerepliceerd.
- Bepaalde werkbelastingen kunnen hoge invoer/uitvoer genereren voor tijdelijke tabelruimten, die niet op de stand-by zijn vereist en dus niet worden gerepliceerd.
- Validatie van de gerepliceerde blokken vindt plaats in de stand-bydatabase, zodat fysieke beschadigingen die zijn geïntroduceerd in de primaire database, niet worden gerepliceerd naar de stand-bydatabase.
- Voorkomt logische beschadigingen binnen blokken en beschadigingen van verloren schrijfingen. Het elimineert ook het risico dat opslagbeheerders fouten maken van replicatie naar de stand-by.
Opnieuw doen kan een vooraf bepaalde periode worden uitgesteld, zodat gebruikersfouten niet onmiddellijk naar de stand-by worden gerepliceerd.

## <a name="azure-netapp-files-snapshots"></a>Azure NetApp Files momentopnamen maken

Met de NetApp Files-opslagoplossing die in BareMetal wordt gebruikt, kunt u momentopnamen van volumes maken. Met momentopnamen kunt u een bestandssysteem snel terugdraaien naar een bepaald tijdstip. Momentopnametechnologieën maken RTO-tijden (Recovery Time Objective) mogelijk die slechts een fractie van de tijd zijn die is gekoppeld aan het herstellen van een back-up van de database.

Momentopnamefunctionaliteit voor Oracle-databases is beschikbaar via Azure NetApp SnapCenter. Met SnapCenter kunt u het maken en herstellen van volumemomentopnamen plannen en automatiseren.

## <a name="recovery-manager"></a>Recovery Manager

Recovery Manager (RMAN) is het voorkeursprogramma voor het maken van back-ups van fysieke databases. RMAN communiceert met het databasebeheerbestand (of een gecentraliseerde herstelcatalogus) om de verschillende kernonderdelen van de database te beveiligen, waaronder:

- Databasegegevensbestanden
- Gearchiveerde redo-logboeken
- Databasebeheerbestanden
- Database-initialisatiebestanden (spfile)

Met RMAN kunt u back-ups van hot of cold databases maken. U kunt deze back-ups gebruiken om stand-bydatabases te maken of om databases te dupliceren om omgevingen te klonen. RMAN heeft ook een herstelvalidatiefunctie. Deze functie leest een back-upset en bepaalt of u deze kunt gebruiken om de database te herstellen naar een bepaald tijdstip.

Omdat RMAN een door Oracle geleverd hulpprogramma is, kan het de interne structuur van databasebestanden lezen. Hiermee kunt u fysieke en logische beschadigingscontroles uitvoeren tijdens back-up- en herstelbewerkingen. U kunt ook databasegegevensbestanden herstellen en afzonderlijke gegevensbestanden en tabelruimten herstellen naar een bepaald tijdstip. Dit zijn voordelen van RMAN ten opzichte van opslagmomentopnamen. RMAN-back-ups bieden een laatste verdedigingslinie tegen volledig gegevensverlies wanneer u geen momentopnamen kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over opties en aanbevelingen voor het optimaliseren van de beveiliging en prestaties van Oracle op BareMetal Infrastructure:

> [!div class="nextstepaction"]
> [Opties voor Oracle BareMetal Infrastructure-servers](options-considerations-high-availability.md)
