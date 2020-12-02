---
title: Een Oracle-data base ontwerpen en implementeren in azure | Microsoft Docs
description: Ontwerp en implementeer een Oracle-data base in uw Azure-omgeving.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 5e9ddecd694a9051e746d07cbc1bee4d98bf5829
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484427"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Een Oracle-data base ontwerpen en implementeren in azure

## <a name="assumptions"></a>Aannames

- U bent van plan om een Oracle-data base van on-premises naar Azure te migreren.
- U hebt het [Diagnostische pakket](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) voor de Oracle database u wilt migreren
- U hebt een goed idee van de diverse metrische gegevens in Oracle AWR-rapporten.
- U hebt een basis informatie over de prestaties en het platform gebruik van de toepassing.

## <a name="goals"></a>Doelstellingen

- Meer informatie over het optimaliseren van uw Oracle-implementatie in Azure.
- Verken opties voor het afstemmen van prestaties voor een Oracle-data base in een Azure-omgeving.

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

- Bepaal de grootte en het groei tempo van de data base.
- Bepaal de IOPS-vereisten, die u kunt schatten op basis van Oracle AWR-rapporten of andere hulpprogram ma's voor netwerk controle.

## <a name="configuration-options"></a>Configuratie-opties

Er zijn vier mogelijke gebieden die u kunt afstemmen om de prestaties in een Azure-omgeving te verbeteren:

- Grootte van de virtuele machine
- Netwerk doorvoer
- Schijf typen en configuraties
- Instellingen voor schijf cache

### <a name="generate-an-awr-report"></a>Een AWR-rapport genereren

Als u een bestaande Oracle-Data Base hebt en wilt migreren naar Azure, hebt u verschillende opties. Als u het [Diagnostische pakket](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) voor uw Oracle-exemplaren hebt, kunt u het rapport Oracle AWR uitvoeren om de metrische gegevens (IOPS, Mbps, GiBs, enzovoort) op te halen. Kies vervolgens de virtuele machine op basis van de metrische gegevens die u hebt verzameld. U kunt ook contact opnemen met uw infrastructuur team om Vergelijk bare informatie te verkrijgen.

U kunt overwegen uw AWR-rapport uit te voeren tijdens zowel normale als piek werk belastingen. Op basis van deze rapporten kunt u de grootte van de virtuele machines aanpassen op basis van de gemiddelde werk belasting of de maximale werk belasting.

Hieronder volgt een voor beeld van het genereren van een AWR-rapport (uw AWR-rapporten genereren met behulp van uw Oracle Enter prise Manager als uw huidige installatie een van de volgende is):

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Belangrijke metrische gegevens

Hieronder vindt u de metrische gegevens die u kunt ophalen uit het AWR-rapport:

- Totaal aantal kernen
- CPU-klok snelheid
- Totaal geheugen in GB
- CPU-gebruik
- Piek waarde voor gegevens overdracht
- Aantal I/O-wijzigingen (lezen/schrijven)
- Logboek frequentie opnieuw uitvoeren (MBPs)
- Netwerk doorvoer
- Netwerk latentie (laag/hoog)
- Database grootte in GB
- Bytes ontvangen via SQL * net van/naar client

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

- Niet- *beheerde schijven*: met deze schijf typen beheert u de opslag accounts waarin de VHD-bestanden (virtuele harde schijf) worden opgeslagen die overeenkomen met uw VM-schijven. VHD-bestanden worden opgeslagen als pagina-blobs in azure-opslag accounts.

- *Beheerde schijven*: Azure beheert de opslag accounts die u gebruikt voor uw VM-schijven. U geeft het schijf type (Premium of Standard) en de grootte van de schijf op die u nodig hebt. Azure maakt en beheert de schijf voor u.

- *Premium-opslag schijven*: deze schijf typen zijn het meest geschikt voor productie werkbelastingen. Premium-opslag ondersteunt VM-schijven die kunnen worden gekoppeld aan specifieke Vm's met een grootte van virtuele machines, zoals DS-, DSv2-, GS-en F-serie-Vm's. De Premium-schijf wordt geleverd met verschillende grootten en u kunt kiezen tussen schijven van 32 GB tot 4.096 GB. Elke schijf grootte heeft eigen prestatie specificaties. Afhankelijk van de toepassings vereisten kunt u een of meer schijven aan uw virtuele machine koppelen.

Wanneer u een nieuwe beheerde schijf maakt vanuit de portal, kunt u het **account type** kiezen voor het type schijf dat u wilt gebruiken. Niet alle beschik bare schijven worden weer gegeven in de vervolg keuzelijst. Nadat u een bepaalde VM-grootte hebt gekozen, worden in het menu alleen de beschik bare Sku's voor Premium-opslag weer gegeven die zijn gebaseerd op die VM-grootte.

![Scherm afbeelding van de pagina Managed Disk](./media/oracle-design/premium_disk01.png)

Nadat u uw opslag op een VM hebt geconfigureerd, wilt u de schijven wellicht testen voordat u een Data Base maakt. Als u de I/O-snelheid in termen van zowel latentie als door Voer kent, kunt u bepalen of de Vm's de verwachte door Voer ondersteunen met latentie doelen.

Er zijn een aantal hulpprogram ma's voor het testen van de toepassings belasting, zoals Oracle Orion, sysbench en Fio.

Voer de belasting test opnieuw uit nadat u een Oracle-Data Base hebt geïmplementeerd. Start uw normale en piek werkbelastingen en de resultaten tonen de basis lijn van uw omgeving.

Het is mogelijk belang rijker om de grootte van de opslag te wijzigen op basis van het aantal IOPS in plaats van de opslag grootte. Als de vereiste IOPS bijvoorbeeld 5.000 is, maar u alleen 200 GB nodig hebt, kunt u nog steeds de Premium-schijf van de P30-klasse krijgen, ook al is er meer dan 200 GB opslag ruimte beschikbaar.

U kunt het aantal IOPS ophalen uit het AWR-rapport. Dit wordt bepaald door het logboek voor opnieuw uitvoeren, de fysieke Lees-en schrijf snelheden.

![Scherm afbeelding van de rapport pagina AWR](./media/oracle-design/awr_report.png)

De grootte voor opnieuw uitvoeren is bijvoorbeeld 12.200.000 bytes per seconde, die gelijk is aan 11,63 MBPs.
De IOPS is 12.200.000/2.358 = 5.174.

Nadat u een duidelijke afbeelding van de I/O-vereisten hebt, kunt u een combi natie van stations kiezen die het meest geschikt zijn om aan deze vereisten te voldoen.

**Aanbevelingen**

- Voor gegevens tabel ruimte kunt u de I/O-werk belasting over een aantal schijven verdelen met behulp van beheerde opslag of Oracle ASM.
- Voeg meer gegevens schijven toe als de grootte van de I/O-blok kering toeneemt voor lees-en schrijf bewerkingen.
- Verg root de blok grootte voor grote sequentiële processen.
- Gebruik gegevens compressie om I/O (voor gegevens en indexen) te verminderen.
- Afzonderlijke logboeken, systeem en temps afzonderlijk opnieuw uitvoeren en TS ongedaan maken op afzonderlijke gegevens schijven.
- Plaats geen toepassings bestanden op de standaard besturingssysteem schijven (/dev/sda). Deze schijven zijn niet geoptimaliseerd voor snelle opstart tijden voor de virtuele machines en ze bieden mogelijk geen goede prestaties voor uw toepassing.
- Wanneer u virtuele machines uit de M-serie in Premium Storage gebruikt, schakelt u [Write Accelerator](../../how-to-enable-write-accelerator.md) in op de schijf voor opnieuw uitvoeren van Logboeken.

### <a name="disk-cache-settings"></a>Instellingen voor schijf cache

Er zijn drie opties voor het opslaan van hosts in de cache:

- *Alleen-lezen*: alle aanvragen worden in de cache opgeslagen voor toekomstige Lees bewerkingen. Alle schrijf bewerkingen worden direct opgeslagen in Azure Blob-opslag.

- *Readwrite*: dit is een ' read-ahead ' algoritme. De lees-en schrijf bewerkingen worden opgeslagen in de cache voor toekomstige Lees bewerkingen. Schrijf bewerkingen die niet write-through zijn, worden eerst opgeslagen in de lokale cache. Het biedt ook de laagste schijf latentie voor lichte werk belastingen. Het gebruik van de ReadWrite-cache met een toepassing die het persistent maken van de vereiste gegevens niet verwerkt, kan leiden tot verlies van gegevens, als de virtuele machine vastloopt.

- *Geen* (uitgeschakeld): met deze optie kunt u de cache overs Laan. Alle gegevens worden overgebracht naar de schijf en blijven bewaard voor Azure Storage. Deze methode biedt u de hoogste I/O-frequentie voor I/O-intensieve workloads. U moet ook rekening houden met transactie kosten.

**Aanbevelingen**

Om de door voer te maximaliseren, raden we u aan om te beginnen met **geen** voor host-caching. Bedenk voor Premium Storage dat u de ' obstakels ' moet uitschakelen wanneer u het bestands systeem koppelt aan de opties **alleen-lezen** of **geen** . Werk het bestand/etc/fstab-bestand met de UUID bij naar de schijven.

![Scherm afbeelding van de pagina Managed Disk waarop de opties ReadOnly en none worden weer gegeven.](./media/oracle-design/premium_disk02.png)

- Gebruik voor besturingssysteem schijven de standaard cache voor **lezen/schrijven** .
- Gebruik voor systeem, tijdelijk en ongedaan maken **geen** voor het opslaan in cache.
- Gebruik voor gegevens **geen** voor caching. Maar als uw data base alleen-lezen of lezen-intensief is, gebruikt u **alleen-lezen** cache.

Nadat de instelling van de gegevens schijf is opgeslagen, kunt u de instelling voor de cache van de host niet wijzigen, tenzij u het station ontkoppelt op het niveau van het besturings systeem en vervolgens opnieuw koppelt nadat u de wijziging hebt aangebracht.

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
- [Azure CLI-voor beelden van VM-implementatie verkennen](../../linux/cli-samples.md)