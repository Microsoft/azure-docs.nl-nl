---
title: Herstel na nood geval instellen voor een IIS-Web-app met behulp van Azure Site Recovery
description: Meer informatie over het repliceren van virtuele IIS-webfarms met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 56ac58e47bffc73c7079af043ad567a77e8f3323
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735502"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Herstel na noodgeval instellen voor een IIS-webtoepassing met meerdere lagen

Toepassings software is de bedrijfs productiviteit in een organisatie. Verschillende webtoepassingen kunnen verschillende doel einden hebben in een organisatie. Sommige toepassingen, zoals toepassingen die worden gebruikt voor salaris verwerking, financiële toepassingen en klant gerichte websites, kunnen van cruciaal belang zijn voor een organisatie. Om het verlies van productiviteit te voor komen, is het belang rijk dat de organisatie deze toepassingen continu actief en werkend uitvoert. Belang rijker is dat deze toepassingen consistent kunnen worden voor komen dat het merk of de installatie kopie van de organisatie wordt beschadigd.

Essentiële webtoepassingen worden doorgaans ingesteld als toepassingen met meerdere lagen: de web-, data base-en toepassings toepassing bevinden zich op verschillende lagen. Naast het verdelen over verschillende lagen, kunnen de toepassingen ook meerdere servers in elke laag gebruiken om het verkeer te verdelen. Bovendien kunnen de toewijzingen tussen de verschillende lagen en op de webserver worden gebaseerd op vaste IP-adressen. Bij een failover moeten sommige van deze toewijzingen worden bijgewerkt, met name als er meerdere websites zijn geconfigureerd op de webserver. Als voor webtoepassingen TLS wordt gebruikt, moet u certificaat bindingen bijwerken.

Traditionele herstel methoden die niet zijn gebaseerd op replicatie, moeten back-ups maken van verschillende configuratie bestanden, register instellingen, bindingen, aangepaste onderdelen (COM of .NET), inhoud en certificaten. Bestanden worden hersteld met behulp van een reeks hand matige stappen. De traditionele herstel methoden van back-ups maken en hand matig herstellen van bestanden zijn lastig, fout gevoelig en niet schaalbaar. U kunt bijvoorbeeld eenvoudig een back-up van certificaten verg eten. Na een failover hebt u geen keuze, maar u kunt ook nieuwe certificaten kopen voor de-server.

Een goede oplossing voor herstel na nood gevallen ondersteunt model herstel plannen voor complexe toepassings architecturen. U moet ook aangepaste stappen kunnen toevoegen aan het herstel plan voor het afhandelen van toepassings toewijzingen tussen lagen. Als er sprake is van een ramp, biedt toepassings toewijzingen een oplossing met één muis klik, waarmee u een lagere RTO kunt aangaan.

In dit artikel wordt beschreven hoe u een webtoepassing op basis van Internet Information Services (IIS) kunt beveiligen met behulp van [Azure site Recovery](site-recovery-overview.md). In het artikel worden aanbevolen procedures beschreven voor het repliceren van een op drie tier, IIS gebaseerde webtoepassing naar Azure, het uitvoeren van een nood herstel analyse en het uitvoeren van een failover van de toepassing naar Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u weet hoe u de volgende taken moet uitvoeren:

* [Een virtuele machine repliceren naar Azure](vmware-azure-tutorial.md)
* [Een herstel netwerk ontwerpen](./concepts-on-premises-to-azure-networking.md)
* [Een testfailover naar Azure uitvoeren](site-recovery-test-failover-to-azure.md)
* [Een failover naar Azure uitvoeren](site-recovery-failover.md)
* [Een domein controller repliceren](site-recovery-active-directory.md)
* [SQL Server repliceren](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Implementatie patronen
Een IIS-webtoepassing volgt doorgaans een van de volgende implementatie patronen:

**Implementatie patroon 1**

Een IIS-webfarm met Application Request Routing (ARR), een IIS-server en SQL Server.

![Diagram van een IIS-webfarm met drie lagen](./media/site-recovery-iis/deployment-pattern1.png)

**Implementatie patroon 2**

Een IIS-webfarm met ARR, een IIS-server, een toepassings server en SQL Server.

![Diagram van een IIS-webfarm met vier lagen](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Voor de voor beelden in dit artikel gebruiken we virtuele VMware-machines met IIS 7,5 op Windows Server 2012 R2 Enter prise. Omdat Site Recovery replicatie niet specifiek is voor een toepassing, worden de aanbevelingen in dit artikel naar verwachting toegepast in de scenario's die in de volgende tabel worden vermeld en voor verschillende versies van IIS.

### <a name="source-and-target"></a>Bron en doel

Scenario | Op een secundaire site | In Azure
--- | --- | ---
Hyper-V | Ja | Ja
VMware | Ja | Ja
Fysieke server | Nee | Ja
Azure|N.v.t.|Ja

## <a name="replicate-virtual-machines"></a>Virtuele machines repliceren

Als u alle virtuele IIS-webfarms wilt repliceren naar Azure, volgt u de instructies in [testfailover naar Azure in site Recovery](site-recovery-test-failover-to-azure.md).

Als u een statisch IP-adres gebruikt, kunt u het IP-adres opgeven dat u wilt dat de virtuele machine moet overnemen. Als u het IP-adres wilt instellen, gaat u naar **reken-en netwerk instellingen**  >  **doel-IP**.

![Scherm afbeelding die laat zien hoe u het doel-IP kunt instellen in het deel venster Site Recovery Compute en netwerk](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Een herstelplan maken
Een herstel plan ondersteunt het sequentiëren van verschillende lagen in een toepassing met meerdere lagen tijdens een failover. Met sequentiëren kunt u de consistentie van toepassingen onderhouden. Wanneer u een herstel plan voor een webtoepassing met meerdere lagen maakt, voert u de stappen uit die worden beschreven in [een herstel plan maken met behulp van site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failover-groepen
Een typische IIS-webtoepassing met meerdere lagen bestaat uit de volgende onderdelen:
* Een Data Base-laag met virtuele SQL-machines.
* De weblaag, die bestaat uit een IIS-server en een toepassings laag. 

Virtuele machines toevoegen aan verschillende groepen op basis van de laag:

1. Een herstel plan maken. Voeg de virtuele machines voor de data base-laag toe onder groep 1. Dit zorgt ervoor dat virtuele machines in de data base-laag de laatste keer worden afgesloten en eerst worden geladen.
1. Voeg de virtuele machines van de toepassingslaag toe onder groep 2. Dit zorgt ervoor dat de virtuele machines van de toepassingslaag worden geopend nadat de data base-laag is geopend.
1. Voeg de virtuele machines voor de weblaag in groep 3 toe. Dit zorgt ervoor dat de virtuele machines van de weblaag worden geopend nadat de toepassingslaag is geactiveerd.
1. Voeg virtuele machines met taak verdeling in groep 4 toe. Dit zorgt ervoor dat de taak verdeling van virtuele machines wordt geactiveerd nadat de weblaag is bijgewerkt.

Zie [het herstel plan aanpassen](site-recovery-runbook-automation.md#customize-the-recovery-plan)voor meer informatie.


### <a name="add-a-script-to-the-recovery-plan"></a>Een script toevoegen aan het herstel plan
Voor een juiste werking van de IIS-webfarm moet u mogelijk bepaalde bewerkingen uitvoeren op de virtuele machines van Azure na failover of tijdens een testfailover. U kunt bewerkingen na de failover automatiseren. U kunt de DNS-vermelding bijvoorbeeld bijwerken, een site binding wijzigen of een connection string wijzigen door de bijbehorende scripts toe te voegen aan het herstel plan. [Een VMM-script toevoegen aan een herstel plan](./hyper-v-vmm-recovery-script.md) wordt beschreven hoe u geautomatiseerde taken instelt met behulp van een script.

#### <a name="dns-update"></a>DNS-update
Als DNS is geconfigureerd voor dynamische DNS-updates, werkt virtuele machines de DNS doorgaans bij met het nieuwe IP-adres wanneer ze worden gestart. Als u een expliciete stap wilt toevoegen om DNS bij te werken met de nieuwe IP-adressen van de virtuele machines, voegt u een [script toe om IP in DNS bij te werken](https://aka.ms/asr-dns-update) als een actie na een failover voor herstel plan groepen.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Verbindings reeks in de web.config van een toepassing
Met de connection string geeft u de Data Base op waarmee de website communiceert. Als de connection string de naam van de virtuele machine van de Data Base bevat, zijn er geen verdere stappen nodig na de failover. De toepassing kan automatisch communiceren met de data base. Als het IP-adres voor de virtuele machine van de data base wordt bewaard, hoeft de connection string ook niet te worden bijgewerkt. 

Als de connection string verwijst naar de virtuele machine van de data base met behulp van een IP-adres, moet deze na de failover worden bijgewerkt. De volgende connection string verwijst bijvoorbeeld naar de data base met het IP-adres 127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

Als u de connection string in de weblaag wilt bijwerken, voegt u een [update script voor IIS-verbindingen](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) toe na groep 3 in het herstel plan.

#### <a name="site-bindings-for-the-application"></a>Site bindingen voor de toepassing
Elke site bestaat uit bindings informatie. De bindings informatie omvat het type binding, het IP-adres waarop de IIS-server luistert naar de aanvragen voor de site, het poort nummer en de hostnamen voor de site. Tijdens de failover moet u deze bindingen mogelijk bijwerken als er een wijziging is in het IP-adres dat eraan is gekoppeld.

> [!NOTE]
>
> Als u de site binding instelt op **alle** niet-toegewezen, hoeft u deze binding na failover niet bij te werken. Als het IP-adres dat is gekoppeld aan een site na de failover niet is gewijzigd, hoeft u de site binding niet bij te werken. (Het bewaren van het IP-adres is afhankelijk van de netwerk architectuur en subnetten die zijn toegewezen aan de primaire en herstel sites. Het bijwerken hiervan is mogelijk niet haalbaar voor uw organisatie.)

![Scherm opname van de instelling van de TLS/SSL-binding](./media/site-recovery-iis/sslbinding.png)

Als u het IP-adres met een site hebt gekoppeld, werkt u alle site bindingen bij met het nieuwe IP-adres. Als u de site bindingen wilt wijzigen, moet u een script voor het bijwerken van de [IIS-weblaag](/samples/browse/?redirectedfrom=TechNet-Gallery) toevoegen na groep 3 in het herstel plan.

#### <a name="update-the-load-balancer-ip-address"></a>Het load balancer IP-adres bijwerken
Als u een virtuele ARR-machine hebt, moet u een [IIS ARR-failover-script](/samples/browse/?redirectedfrom=TechNet-Gallery) toevoegen na groep 4 om het IP-adres bij te werken.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>TLS/SSL-certificaat binding voor een HTTPS-verbinding
Een website heeft mogelijk een bijbehorend TLS/SSL-certificaat waarmee een beveiligde communicatie tussen de webserver en de browser van de gebruiker kan worden gegarandeerd. Als de website een HTTPS-verbinding heeft en er ook een gekoppelde HTTPS-site is gekoppeld aan het IP-adres van de IIS-server met een TLS/SSL-certificaat binding, moet u een nieuwe site binding voor het certificaat toevoegen met het IP-adres van de virtuele IIS-machine na de failover.

Het TLS/SSL-certificaat kan worden verleend aan de hand van de volgende onderdelen:

* De Fully Qualified Domain Name van de website.
* De naam van de server.
* Een Joker teken certificaat voor de domein naam.  
* Een IP-adres. Als het TLS/SSL-certificaat wordt uitgegeven op basis van het IP-adres van de IIS-server, moet er een ander TLS/SSL-certificaat worden verleend aan het IP-adres van de IIS-server op de Azure-site. Er moet een aanvullende TLS-binding voor dit certificaat worden gemaakt. Daarom wordt u aangeraden geen TLS/SSL-certificaat te gebruiken dat is uitgegeven op basis van het IP-adres. Deze optie is minder vaak gebruikt en wordt binnenkort afgeschaft in overeenstemming met de nieuwe certificerings instantie/browser forum wijzigingen.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>De afhankelijkheid tussen de weblaag en de toepassingslaag bijwerken
Als u een toepassingsspecifiek afhankelijkheid hebt die is gebaseerd op het IP-adres van de virtuele machines, moet u deze afhankelijkheid na failover bijwerken.

## <a name="run-a-test-failover"></a>Een testfailover uitvoeren

1. Selecteer uw Recovery Services kluis in de Azure Portal.
2. Selecteer het herstel plan dat u hebt gemaakt voor de IIS-webfarm.
3. Selecteer **Failover testen**.
4. Als u het proces testfailover wilt starten, selecteert u het herstel punt en het virtuele Azure-netwerk.
5. Wanneer de secundaire omgeving actief is, kunt u validaties uitvoeren.
6. Wanneer validaties zijn voltooid, selecteert u op de testfailover de optie **validaties voltooid**.

Zie [failover testen naar Azure in site Recovery](site-recovery-test-failover-to-azure.md)voor meer informatie.

## <a name="run-a-failover"></a>Een failover uitvoeren

1. Selecteer uw Recovery Services kluis in de Azure Portal.
1. Selecteer het herstel plan dat u hebt gemaakt voor de IIS-webfarm.
1. Selecteer **Failover**.
1. Als u het failoverproces wilt starten, selecteert u het herstel punt.

Zie [failover in site Recovery](site-recovery-failover.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [repliceren van andere toepassingen](site-recovery-workload.md) met behulp van site Recovery.