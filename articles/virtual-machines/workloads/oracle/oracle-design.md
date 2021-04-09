---
title: Een Oracle-data base ontwerpen en implementeren in azure | Microsoft Docs
description: Ontwerp en implementeer een Oracle-data base in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669990"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Een Oracle-data base ontwerpen en implementeren in azure

## <a name="assumptions"></a>Aannames

- U bent van plan om een Oracle-data base van on-premises naar Azure te migreren.
- U hebt het [Diagnostische pakket](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) of de [automatische werk belasting](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) voor de Oracle database die u wilt migreren
- U hebt een goed idee van de diverse metrische gegevens in Oracle.
- U hebt een basis informatie over de prestaties en het platform gebruik van de toepassing.

## <a name="goals"></a>Doelstellingen

- Meer informatie over het optimaliseren van uw Oracle-implementatie in Azure.
- Verken opties voor het afstemmen van prestaties voor een Oracle-data base in een Azure-omgeving.
- Hebben duidelijke verwachtingen tussen de limieten van fysieke afstemming via architectuur en voor delen of een logische afstemming van database code, (SQL) en het algehele database ontwerp.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>De verschillen tussen een on-premises en Azure-implementatie 

Hieronder volgen enkele belang rijke zaken die u moet overwegen wanneer u on-premises toepassingen naar Azure migreert. 

Een belang rijk verschil is dat in een Azure-implementatie resources, zoals Vm's, schijven en virtuele netwerken, worden gedeeld tussen andere clients. Bovendien kunnen resources worden beperkt op basis van de vereisten. In plaats van zich te richten op het voor komen van een fout (MBTF), is Azure beter gericht op het naleven van de fout (MTTR).

De volgende tabel bevat enkele van de verschillen tussen een on-premises implementatie en een Azure-implementatie van een Oracle-data base.


|  | Lokale implementatie | Azure-implementatie |
| --- | --- | --- |
| **Netwerken** |LAN/WAN  |SDN (door software gedefinieerde netwerken)|
| **Beveiligings groep** |Hulpprogram ma's voor IP-en poort beperking |[Netwerk beveiligings groep (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Tolerantie** |MBTF (gemiddelde tijd tussen storingen) |MTTR (gemiddelde tijd voor herstel)|
| **Gepland onderhoud** |Patches/upgrades|[Beschikbaarheids sets](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (patches/upgrades die worden beheerd door Azure) |
| **Resource** |Toegewezen  |Gedeeld met andere clients|
| **Regio's** |Datacenters |[Regioparen](../../regions.md#region-pairs)|
| **Storage** |SAN/fysieke schijven |[Door Azure beheerde opslag](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Schalen** |Verticale schaal |Horizontaal schalen|


### <a name="requirements"></a>Vereisten

- Bepaal het werkelijke CPU-gebruik, omdat Oracle wordt gelicentieerd door core, het aanpassen van de behoeften aan vCPU kan een belang rijke stap zijn voor de kosten besparingen. 
- Bepaal de grootte van de data base, de back-upopslag en het groei tempo.
- Bepaal de i/o-vereisten, die u kunt schatten op basis van Oracle Statspack-en AWR-rapporten of van hulpprogram ma's voor opslag bewaking op besturingssysteem niveau.

## <a name="configuration-options"></a>Configuratie-opties

Er zijn vier mogelijke gebieden die u kunt afstemmen om de prestaties in een Azure-omgeving te verbeteren:

- Grootte van de virtuele machine
- Netwerk doorvoer
- Schijf typen en configuraties
- Instellingen voor schijf cache

### <a name="generate-an-awr-report"></a>Een AWR-rapport genereren

Als u een bestaande Oracle Enter prise Edition-Data Base hebt en wilt migreren naar Azure, hebt u verschillende opties. Als u het [Diagnostische pakket](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) voor uw Oracle-exemplaren hebt, kunt u het rapport Oracle AWR uitvoeren om de metrische gegevens (IOPS, Mbps, GiBs, enzovoort) op te halen. Voor deze data bases zonder de Diagnostics Pack-licentie of voor een Standard Edition-data base kunnen dezelfde belang rijke metrische gegevens worden verzameld met een Statspack-rapport nadat er hand matige moment opnamen zijn verzameld.  Het belangrijkste verschil tussen deze twee rapportage methoden is dat AWR automatisch wordt verzameld en meer informatie biedt over de data base dan de voorafgaande rapportage optie van Statspack.

U kunt overwegen uw AWR-rapport uit te voeren tijdens zowel normale als piek werk belastingen. Als u de nauw keurigere werk belasting wilt verzamelen, kunt u een uitgebreid venster rapport van één week, versus een 24-uursnotatie, gebruiken en realiseert u zich dat AWR gemiddelden levert als onderdeel van de berekeningen in het rapport.  Voor een Data Center-migratie raden wij aan om rapporten te verzamelen voor de grootte van de productie systemen en de resterende database kopieën te schatten die worden gebruikt voor het testen van gebruikers, testen, ontwikkelen, enz. (bedoeld gelijk zijn aan productie, test en ontwikkeling 50% van de productie grootte, enz.)

De AWR-opslag plaats behoudt standaard 8 dagen aan gegevens en maakt moment opnamen op basis van intervallen.  Als u een AWR-rapport wilt uitvoeren vanaf de opdracht regel, kunt u het volgende uitvoeren vanaf een Terminal:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Belangrijke metrische gegevens

In het rapport wordt gevraagd naar de volgende informatie:
- Rapport type: HTML of tekst, (HTML in 12,1 en biedt aanvullende informatie dan de tekst indeling.)
- Het aantal dagen van moment opnamen dat moet worden weer gegeven (gedurende één uur wordt een rapport van één week een 168 anders in moment opname-Id's)
- Het begin SnapshotID voor het rapport venster.
- De laatste SnapshotId voor het rapport venster.
- De naam van het rapport dat moet worden gemaakt door het AWR-script.

Als de AWR op een echt toepassings cluster wordt uitgevoerd, is het opdracht regel rapport de awrgrpt. SQL in plaats van awrrpt. SQL.  Het rapport "g" maakt een rapport voor alle knoop punten in de RAC-data base in één rapport en er moet één worden uitgevoerd op elk RAC-knoop punt.

Hieronder vindt u de metrische gegevens die u kunt ophalen uit het AWR-rapport:

- Database naam, exemplaar naam en hostnaam
- Database versie, (ondersteunings service van Oracle)
- CPU/kernen
- SGA/PGA, (en advisoren om u te laten weten of ondermaatse)
- Totaal geheugen in GB
- CPU% bezet
- DB-Cpu's
- IOPs (lezen/schrijven)
- MBPs (lezen/schrijven)
- Netwerk doorvoer
- Netwerk latentie (laag/hoog)
- Meest gewacht gebeurtenissen 
- Parameter instellingen voor de data base
- Is data base RAC, Exadata, met behulp van geavanceerde functies of configuraties

### <a name="virtual-machine-size"></a>Grootte van de virtuele machine

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. de grootte van de virtuele machine schatten op basis van CPU, geheugen en I/O-gebruik van het AWR-rapport

Een ding waarnaar u kunt kijken, is de vijf getimede voorgrond gebeurtenissen die aangeven waar de systeem knelpunten zich bevinden.

In het volgende diagram bevindt zich bijvoorbeeld bovenaan het logboek bestand. Hiermee wordt het aantal wacht tijden aangegeven dat vereist is voordat de LGWR de logboek buffer naar het logboek bestand voor opnieuw uitvoeren schrijft. Deze resultaten geven aan dat er meer opslag of schijven moeten worden uitgevoerd. Daarnaast toont het diagram ook het aantal CPU (kernen) en de hoeveelheid geheugen.

![Scherm opname van het logboek bestand dat boven aan de tabel wordt weer gegeven.](./media/oracle-design/cpu_memory_info.png)

In het volgende diagram ziet u de totale I/O van lezen en schrijven. Er zijn 59 GB gelezen en 247,3 GB geschreven tijdens de periode van het rapport.

![Scherm afbeelding met het totale I/O-lees-en schrijf bewerkingen.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Kies een virtuele machine

Op basis van de informatie die u hebt verzameld uit het AWR-rapport, is de volgende stap het kiezen van een virtuele machine met een vergelijk bare grootte die voldoet aan uw vereisten. U kunt een lijst met beschik bare virtuele machines vinden in het artikel [geoptimaliseerd voor geheugen](../../sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Verfijn de VM-grootte met een soort gelijke VM-serie op basis van de ACU

Nadat u de virtuele machine hebt gekozen, moet u rekening best Eden aan de ACU voor de virtuele machine. U kunt een andere VM kiezen op basis van de ACU-waarde die beter aansluit bij uw vereisten. Zie [Azure Compute unit](../../acu.md)voor meer informatie.

![Scherm afbeelding van de pagina ACU units](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Netwerk doorvoer

In het volgende diagram ziet u de relatie tussen door Voer en IOPS:

![Scherm afbeelding van door Voer](./media/oracle-design/throughput.png)

De totale netwerk doorvoer wordt geschat op basis van de volgende gegevens:
- SQL * net-verkeer
- MBps x aantal servers (uitgaande stroom zoals Oracle Data Guard)
- Andere factoren, zoals toepassings replicatie

![Scherm afbeelding van de SQL * netto-door Voer](./media/oracle-design/sqlnet_info.png)

Op basis van de vereisten voor de netwerk bandbreedte zijn er verschillende gateway typen waaruit u kunt kiezen. Dit zijn onder andere Basic, VpnGw en Azure ExpressRoute. Zie de pagina met prijzen voor [VPN-gateways](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)voor meer informatie.

**Aanbevelingen**

- De netwerk latentie is hoger vergeleken met een on-premises implementatie. Het verminderen van netwerk round trips kan de prestaties aanzienlijk verbeteren.
- Consolidatie van toepassingen die hoge trans acties of ' intensieve-apps hebben op dezelfde virtuele machine om retouren te verminderen.
- Gebruik Virtual Machines met [versneld netwerken](../../../virtual-network/create-vm-accelerated-networking-cli.md) voor betere netwerk prestaties.
- Voor bepaalde Linux-distributies kunt u [ondersteuning voor knippen/](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)ontkoppelen inschakelen.
- Installeer [Oracle Enter prise Manager](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) op een afzonderlijke virtuele machine.
- Zeer grote pagina's zijn standaard niet ingeschakeld op Linux. Overweeg om zeer grote pagina's in te scha kelen en in te stellen `use_large_pages = ONLY` op de Oracle DB. Dit kan helpen om de prestaties te verbeteren. Meer informatie vindt u [hier](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390).

### <a name="disk-types-and-configurations"></a>Schijf typen en configuraties

- *Standaard besturingssysteem schijven*: deze schijf typen bieden permanente gegevens en caching. Ze zijn geoptimaliseerd voor toegang tot het besturings systeem bij het opstarten en zijn niet bedoeld voor trans acties die zijn gebaseerd op transactionele of Data Warehouse-workloads.

- *Beheerde schijven*: Azure beheert de opslag accounts die u gebruikt voor uw VM-schijven. U geeft het schijf type op (meestal Premium SSD voor Oracle-workloads) en de grootte van de schijf die u nodig hebt. Azure maakt en beheert de schijf voor u.  Premium-SSD Managed disk is alleen beschikbaar voor door het geheugen geoptimaliseerde en speciaal ontworpen VM-serie. Nadat u een bepaalde VM-grootte hebt gekozen, worden in het menu alleen de beschik bare Sku's voor Premium-opslag weer gegeven die zijn gebaseerd op die VM-grootte.

![Scherm afbeelding van de pagina Managed Disk](./media/oracle-design/premium_disk01.png)

Nadat u uw opslag op een VM hebt geconfigureerd, wilt u de schijven wellicht testen voordat u een Data Base maakt. Als u de I/O-snelheid in termen van zowel latentie als door Voer kent, kunt u bepalen of de Vm's de verwachte door Voer ondersteunen met latentie doelen.

Er zijn een aantal hulpprogram ma's voor het testen van de toepassings belasting, zoals Oracle Orion, sysbench, SLOB en Fio.

Voer de belasting test opnieuw uit nadat u een Oracle-Data Base hebt geïmplementeerd. Start uw normale en piek werkbelastingen en de resultaten tonen de basis lijn van uw omgeving.  Wees realistisch in de test van de werk belasting. het is niet zinvol een werk belasting uit te voeren die niets bevalt, zoals wat u in werkelijkheid op de virtuele machine uitvoert.

Omdat Oracle voor veel een Data Base van IO is, is het belang rijk om de opslag grootte te wijzigen op basis van het aantal IOPS in plaats van de opslag grootte. Als de vereiste IOPS bijvoorbeeld 5.000 is, maar u alleen 200 GB nodig hebt, kunt u nog steeds de Premium-schijf van de P30-klasse krijgen, ook al is er meer dan 200 GB opslag ruimte beschikbaar.

U kunt het aantal IOPS ophalen uit het AWR-rapport. Dit wordt bepaald door het logboek voor opnieuw uitvoeren, de fysieke Lees-en schrijf snelheden.  Controleer altijd of de gekozen VM-reeks de mogelijkheid heeft om de i/o-vraag van de werk belasting te verwerken.  Als de virtuele machine een lagere IO-limiet heeft dan de opslag, wordt de limiet Maxi maal ingesteld door de virtuele machine.

![Scherm afbeelding van de rapport pagina AWR](./media/oracle-design/awr_report.png)

De grootte voor opnieuw uitvoeren is bijvoorbeeld 12.200.000 bytes per seconde, die gelijk is aan 11,63 MBPs.
De IOPS is 12.200.000/2.358 = 5.174.

Nadat u een duidelijke afbeelding van de I/O-vereisten hebt, kunt u een combi natie van stations kiezen die het meest geschikt zijn om aan deze vereisten te voldoen.

**Aanbevelingen**

- Voor gegevens tabel ruimte kunt u de I/O-werk belasting over een aantal schijven verdelen met behulp van beheerde opslag of Oracle ASM.
- Gebruik geavanceerde compressie van Oracle om I/O (voor gegevens en indexen) te verminderen.
- Afzonderlijke logboeken voor opnieuw uitvoeren, tijdelijke tablespaces en ongedaan maken op afzonderlijke gegevens schijven.
- Plaats geen toepassings bestanden op de standaard besturingssysteem schijven (/dev/sda). Deze schijven zijn niet geoptimaliseerd voor snelle opstart tijden voor de virtuele machines en ze bieden mogelijk geen goede prestaties voor uw toepassing.
- Wanneer u virtuele machines uit de M-serie in Premium Storage gebruikt, schakelt u [Write Accelerator](../../how-to-enable-write-accelerator.md) in op de schijf voor opnieuw uitvoeren van Logboeken.
- Overweeg om de logboeken opnieuw te verplaatsen met een hoge latentie tot ultra disk.

### <a name="disk-cache-settings"></a>Instellingen voor schijf cache

Er zijn drie opties voor het opslaan van hosts, maar voor een Oracle-data base, wordt alleen de alleen-lezen cache aanbevolen voor een Data Base-workload.  Met ReadWrite kunnen aanzienlijke beveiligings problemen worden geïntroduceerd in een gegevens bestand, waarbij het doel van een Data Base is om het te schrijven naar het gegevens bestand, en de informatie niet in de cache op te slaan.

In tegens telling tot een bestands systeem of toepassing, voor een Data Base, is de aanbeveling voor de caching van hosts *alleen-lezen*: alle aanvragen worden in de cache opgeslagen voor toekomstige Lees bewerkingen. Alle schrijf bewerkingen blijven naar de schijf worden geschreven.

**Aanbevelingen**

Om de door voer te maximaliseren, wordt u aangeraden om waar mogelijk te beginnen met **alleen-lezen** voor opslag in de host. Bedenk voor Premium Storage dat u de ' obstakels ' moet uitschakelen wanneer u het bestands systeem koppelt aan de opties voor **alleen-lezen** . Werk het bestand/etc/fstab-bestand met de UUID bij naar de schijven.

![Scherm afbeelding van de pagina Managed Disk waarop de opties ReadOnly en none worden weer gegeven.](./media/oracle-design/premium_disk02.png)

- Gebruik voor besturingssysteem schijven de standaard cache voor **lezen/schrijven** en gebruik Premium SSD voor virtuele machines van Oracle-werk belastingen.  Zorg er ook voor dat het volume dat wordt gebruikt voor wisselen ook op Premium SSD is.
- Gebruik voor alle DATAFILES **alleen-lezen** voor caching. Alleen-lezen cache is alleen beschikbaar voor Premium Managed disk, P30 en hoger.  Er geldt een limiet voor een 4095GiB-volume dat kan worden gebruikt met ReadOnly-caching.  Wanneer een toewijzing groter is, wordt de host-caching standaard uitgeschakeld.

Als de workloads aanzienlijk verschillen tussen de dag en de avond en de i/o-werk belasting deze kan ondersteunen, kan P1-P20-Premium-SSD met bursting de prestaties bieden die nodig zijn tijdens het laden van de nacht periode en de beperkte IO-vereisten.  

## <a name="security"></a>Beveiliging

Nadat u uw Azure-omgeving hebt ingesteld en geconfigureerd, is de volgende stap het beveiligen van uw netwerk. Hier volgen enkele aanbevelingen:

- *NSG-beleid*: NSG kan worden gedefinieerd door een SUBNET of NIC. Het is eenvoudiger om de toegang op het subnetniveau te beheren, zowel voor beveiliging als voor het afdwingen van route ring voor zaken als toepassings firewalls.

- *JumpBox*: voor meer veilige toegang moeten beheerders niet rechtstreeks verbinding maken met de toepassings service of-Data Base. Een JumpBox wordt gebruikt als een medium tussen de computer van de beheerder en Azure-resources.
![Scherm afbeelding van de pagina met de JumpBox-topologie](./media/oracle-design/jumpbox.png)

    De beheerder computer moet alleen IP-beperkte toegang bieden tot de JumpBox. De JumpBox moet toegang hebben tot de toepassing en de data base.

- *Particulier netwerk* (subnetten): we raden u aan de toepassings service en-Data Base op afzonderlijke subnetten te hebben, zodat u beter beheer kunt instellen met NSG-beleid.


## <a name="additional-reading"></a>Meer artikelen

- [Oracle ASM configureren](configure-oracle-asm.md)
- [Oracle Data Guard configureren](configure-oracle-dataguard.md)
- [Een Oracle Golden-Gate configureren](configure-oracle-golden-gate.md)
- [Oracle-back-up en-herstel](./oracle-overview.md)

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: Maxi maal beschik bare Vm's maken](../../linux/create-cli-complete.md)
- [Azure CLI-voor beelden van VM-implementatie verkennen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
