---
title: Beschik baarheid van SAP HANA binnen een Azure-regio | Microsoft Docs
description: Beschrijft SAP HANA bewerkingen op Azure native Vm's in één Azure-regio.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83c56c337e2b07175dec28cfefa5da75dab7b4f0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667967"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Beschik baarheid van SAP HANA binnen een Azure-regio
In dit artikel worden verschillende beschikbaarheids scenario's binnen één Azure-regio beschreven. Azure heeft veel regio's, verspreid over de hele wereld. Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor de lijst met Azure-regio's. Voor de implementatie van SAP HANA op Vm's binnen één Azure-regio, biedt micro soft de implementatie van één virtuele machine met een HANA-exemplaar. Voor een verhoogde Beschik baarheid kunt u twee virtuele machines implementeren met twee HANA-instanties binnen een [Azure-beschikbaarheidsset](../../windows/tutorial-availability-sets.md) die gebruikmaakt van Hana-systeem replicatie voor Beschik baarheid. 

Op dit moment biedt Azure [Azure-beschikbaarheidszones](../../../availability-zones/az-overview.md). In dit artikel wordt Beschikbaarheidszones niet beschreven. Maar bevat een algemene discussie over het gebruik van beschikbaarheids sets versus Beschikbaarheidszones.

Azure-regio's waar Beschikbaarheidszones worden aangeboden, hebben meerdere data centers. De data centers zijn onafhankelijk van de voeding van voedings bronnen, koeling en netwerk. De reden voor het bieden van verschillende zones binnen één Azure-regio is het implementeren van toepassingen op twee of drie Beschikbaarheidszones die worden aangeboden. Implementaties in verschillende zones, problemen in de stroom en netwerken die van invloed zijn op één Azure-beschikbaarheids zone-infra structuur, de implementatie van uw toepassing binnen een Azure-regio nog steeds functioneel is. Er kan enige gereduceerde capaciteit optreden. Zo kunnen Vm's in de ene zone verloren gaan, maar worden de Vm's in de andere twee zones nog steeds uitgevoerd. 
 
Een Azure-Beschikbaarheidsset is een logische groeperings functie waarmee u ervoor kunt zorgen dat de VM-resources die u in de Beschikbaarheidsset plaatst, niet van elkaar zijn geïsoleerd wanneer ze worden geïmplementeerd in een Azure-Data Center. Azure zorgt ervoor dat de VM's die u in een beschikbaarheidsset plaatst, op meerdere fysieke servers, rekenrekken, opslageenheden en netwerkswitches worden uitgevoerd. In sommige Azure-documentatie wordt deze configuratie plaatsingen genoemd in verschillende update- [en fout domeinen](../../manage-availability.md). Deze plaatsingen bevinden zich doorgaans in een Azure-Data Center. Ervan uitgaande dat energie bronnen en netwerk problemen van invloed zijn op het Data Center dat u implementeert, is dit van invloed op uw capaciteit in één Azure-regio.

De plaatsing van data centers die Azure-beschikbaarheidszones vertegenwoordigen, is een inbreuk tussen het leveren van een acceptabele netwerk latentie tussen services die zijn geïmplementeerd in verschillende zones en een afstand tussen data centers. Natuurlijk rampen is in het ideale geval niet van invloed op de kracht, het netwerk aanbod en de infra structuur voor alle Beschikbaarheidszones in deze regio. Als Monumental Natural rampen zijn weer gegeven, levert Beschikbaarheidszones mogelijk niet altijd de beschik baarheid die u binnen één regio wilt. U kunt nadenken over orkaan Maria dat op 20 september 2017 op het eiland Puerto Rico raakt. De orkaan veroorzaakt een bijna 100 procente bedrukbaar op het eiland 90-km breed.

## <a name="single-vm-scenario"></a>Scenario met één VM

In een scenario met één VM maakt u een virtuele Azure-machine voor het SAP HANA-exemplaar. U gebruikt Azure Premium Storage voor het hosten van de besturingssysteem schijf en al uw gegevens schijven. De SLA voor Azure-uptime van 99,9 procent en de Sla's van andere Azure-onderdelen is voldoende om u te laten voldoen aan uw beschikbaarheids overeenkomsten voor uw klanten. In dit scenario hoeft u geen gebruik te maken van een Azure-Beschikbaarheidsset voor Vm's die de DBMS-laag uitvoeren. In dit scenario vertrouwt u op twee verschillende functies:

- Azure VM automatisch opnieuw starten (ook wel Azure service Retoucheer genoemd)
- SAP HANA automatisch opnieuw opstarten

Azure VM automatisch opnieuw opstarten of service retoucheert, is een functionaliteit in azure die op twee niveaus werkt:

- De Azure server-host controleert de status van een virtuele machine die wordt gehost op de server host.
- De Azure Fabric-controller bewaakt de status en beschik baarheid van de server host.

Een status controle functie controleert de status van elke virtuele machine die wordt gehost op een Azure server-host. Als een virtuele machine zich in een niet-goede staat bevindt, kan de virtuele machine opnieuw worden opgestart door de Azure Hosta Gent waarmee de status van de virtuele machine wordt gecontroleerd. De infrastructuur controller controleert de status van de host door een aantal verschillende para meters te controleren die kunnen wijzen op problemen met de hardware van de host. Ook wordt gecontroleerd op de toegankelijkheid van de host via het netwerk. Een indicatie van problemen met de host kan leiden tot de volgende gebeurtenissen:

- Als de host een slechte status signaleert, wordt het opnieuw opstarten van de host en het opnieuw opstarten van de Vm's die worden uitgevoerd op de host geactiveerd.
- Als de host niet in orde is nadat deze opnieuw is opgestart, wordt de virtuele machines die oorspronkelijk op het huidige beschadigde knoop punt waren, op een gezonde hostserver, opnieuw geïmplementeerd. In dit geval is de oorspronkelijke host gemarkeerd als niet in orde. Het wordt niet gebruikt voor verdere implementaties totdat deze is gewist of vervangen.
- Als de beschadigde host problemen ondervindt tijdens het opnieuw opstarten, wordt direct opnieuw opstarten van de Vm's op een gezonde host geactiveerd. 

Met de host-en VM-bewaking die door Azure wordt verstrekt, worden Azure-Vm's die problemen met de host ondervinden, automatisch opnieuw gestart op een goede Azure-host. 

>[!IMPORTANT]
>Azure service retoucheert geen Linux-Vm's waar het gast besturingssysteem zich in de status van de kernel bevindt. De standaard instellingen van de meest gebruikte Linux-releases, worden niet automatisch opnieuw opgestart op Vm's of servers waarop de Linux-kernel zich in de stand-by staat bevindt. In plaats daarvan wordt de standaard instelling gebruikt om te zorgen dat het besturings systeem in de status van de kernel in staat is om een kerneldebugger toe te voegen om te analyseren. Azure respecteert dat gedrag door een virtuele machine niet automatisch opnieuw te starten met het gast besturingssysteem in een dergelijke status. Veronderstelling is dat dergelijke gevallen extreem zeldzaam zijn. U kunt het standaard gedrag overschrijven om het opnieuw opstarten van de virtuele machine in te scha kelen. Als u het standaard gedrag wilt wijzigen, schakelt u de para meter ' kernel. paniek ' in/etc/sysctl.conf. De tijd die u instelt voor deze para meter is in enkele seconden. Gemeen schappelijke aanbevolen waarden moeten 20-30 seconden wachten voordat de herstart via deze para meter wordt geactiveerd. Zie ook <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf> .

De tweede functie die u in dit scenario vertrouwt, is het feit dat de HANA-service die wordt uitgevoerd in een opnieuw opgestart VM automatisch wordt gestart nadat de VM opnieuw is opgestart. U kunt [Hana-service automatisch opnieuw opstarten](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) via de watchdog-services van de verschillende Hana-Services.

U kunt dit scenario met één virtuele machine verbeteren door een koud failover-knoop punt toe te voegen aan een SAP HANA configuratie. In de SAP HANA-documentatie wordt deze instelling [automatische failover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)van de host genoemd. Deze configuratie kan zinvol zijn in een on-premises implementatie situatie waarbij de serverhardware beperkt is en u een knoop punt met één server toekent als het knoop punt voor automatische failover van de host voor een set productie-hosts. In azure, waarbij de onderliggende infra structuur van Azure een gezonde doel server biedt voor een geslaagde VM-herstart, is het niet logisch om SAP HANA automatische failover van de host te implementeren. Vanwege het herstellen van de Azure-service is er geen referentie architectuur die een stand-by-knoop punt voor de automatische failover van HANA-hosts voorkent. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Speciaal geval van SAP HANA scale-out configuraties in azure
Hoge Beschik baarheid voor SAP HANA scale-out-configuraties is afhankelijk van de service retoucheert van virtuele machines van Azure en het opnieuw opstarten van de SAP HANA instantie omdat de VM weer actief is. Architecturen met hoge Beschik baarheid op basis van HANA-systeem replicatie zullen op een later tijdstip worden geïntroduceerd. 


## <a name="availability-scenarios-for-two-different-vms"></a>Beschikbaarheids scenario's voor twee verschillende Vm's

Als u twee Azure-Vm's in een Azure-Beschikbaarheidsset gebruikt, kunt u de uptime tussen deze twee Vm's verhogen als deze in een Azure-Beschikbaarheidsset binnen één Azure-regio worden geplaatst. De basis instelling in azure zou er als volgt uitzien:

![Diagram van twee virtuele machines met alle lagen](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Als u de verschillende beschikbaarheids scenario's wilt illustreren, worden een aantal lagen in het diagram wegge laten. In het diagram worden alleen de lagen weer gegeven van Vm's, hosts, beschikbaarheids sets en Azure-regio's. Azure Virtual Network-instanties, resource groepen en abonnementen spelen geen rol in de scenario's die in deze sectie worden beschreven.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Back-ups repliceren naar een tweede virtuele machine

Een van de meest elementaire-instellingen is het gebruik van back-ups. Met name kunnen er back-ups van transactie logboeken worden verzonden van de ene VM naar een andere virtuele machine van Azure. U kunt het type Azure Storage kiezen. In deze installatie moet u verantwoordelijk zijn voor het uitvoeren van scripts voor het kopiëren van geplande back-ups die worden uitgevoerd op de eerste VM naar de tweede VM. Als u de tweede VM-exemplaren wilt gebruiken, moet u de volledige, incrementele/differentiële en transactie logboek back-ups herstellen naar het punt dat u nodig hebt. 

De architectuur ziet er als volgt uit:

![Diagram waarin de architectuur van twee virtuele machines met opslag replicatie wordt weer gegeven.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Deze installatie is niet geschikt voor het bereiken van geweldige beoogde herstel punten (RPO) en RTO (Recovery Time objectief). RTO keer dat de volledige data base volledig moet worden hersteld met behulp van de gekopieerde back-ups. Deze installatie is echter handig voor het herstellen van onbedoelde gegevens verwijdering op de belangrijkste instanties. Met deze installatie kunt u op elk gewenst moment herstellen naar een bepaald tijdstip, de gegevens extra heren en de verwijderde gegevens importeren in uw hoofd instantie. Daarom kan het zinvol zijn om een methode voor het kopiëren van back-ups te gebruiken in combi natie met andere functionaliteit voor hoge Beschik baarheid. 

Terwijl back-ups worden gekopieerd, kunt u mogelijk een kleinere virtuele machine gebruiken dan de hoofd-VM waarop het SAP HANA-exemplaar wordt uitgevoerd. Houd er rekening mee dat u een kleiner aantal Vhd's kunt koppelen aan kleinere Vm's. Zie [grootten voor virtuele Linux-machines in azure](../../sizes.md)voor meer informatie over de limieten van afzonderlijke VM-typen.

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Systeem replicatie SAP HANA zonder automatische failover

De scenario's die in deze sectie worden beschreven, gebruiken SAP HANA systeem replicatie. Zie [System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)(Engelstalig) voor de SAP-documentatie. Scenario's zonder automatische failover zijn niet gebruikelijk voor configuraties binnen één Azure-regio. Een configuratie zonder automatische failover, maar bij het vermijden van een pacemaker-installatie, moet u ervoor zorgen dat u hand matig kunt controleren en failover. Omdat dit het duurt en er ook is, vertrouwen de meeste klanten in plaats daarvan op Azure-service herstel. Er zijn enkele rand gevallen waarin deze configuratie kan helpen bij fout scenario's. Of, in sommige gevallen, kan een klant meer efficiëntie bedenken.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>Systeem replicatie SAP HANA zonder automatische failover en zonder vooraf laden van gegevens

In dit scenario gebruikt u SAP HANA systeem replicatie om gegevens synchroon te verplaatsen om een RPO van 0 te krijgen. Aan de andere kant hebt u een lang genoeg RTO die u niet nodig hebt voor het vooraf laden van failover of gegevens in de cache van het HANA-exemplaar. In dit geval is het mogelijk om in uw configuratie verdere economie te behalen door de volgende acties uit te voeren:

- Voer een andere SAP HANA-exemplaar uit in de tweede VM. Het SAP HANA-exemplaar in de tweede VM neemt het grootste deel van het geheugen van de virtuele machine in beslag. Als een failover naar de tweede virtuele machine wordt uitgevoerd, moet u het actieve SAP HANA-exemplaar afsluiten dat de gegevens volledig in de tweede VM heeft geladen, zodat de gerepliceerde gegevens in de cache van het beoogde HANA-exemplaar in de tweede virtuele machine kunnen worden geladen.
- Gebruik een kleinere VM-grootte op de tweede VM. Als er een failover optreedt, hebt u een extra stap voor de hand matige failover. In deze stap wijzigt u het formaat van de virtuele machine naar de grootte van de bron-VM. 
 
Het scenario ziet er als volgt uit:

![Diagram van twee virtuele machines met opslag replicatie](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Zelfs als u geen vooraf laden van gegevens gebruikt in het doel van de HANA-systeem replicatie, hebt u ten minste 64 GB geheugen nodig. Daarnaast hebt u voldoende geheugen nodig naast 64 GB om de rowstore-gegevens in het geheugen van het doel exemplaar te laten staan.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>Systeem replicatie SAP HANA zonder automatische failover en met vooraf laden van gegevens

In dit scenario is het vooraf laden van gegevens die zijn gerepliceerd naar het HANA-exemplaar in de tweede virtuele machine. Dit elimineert de twee voor delen van het vooraf laden van gegevens. In dit geval kunt u geen ander SAP HANA-systeem op de tweede VM uitvoeren. U kunt ook geen kleinere VM-grootte gebruiken. Daarom implementeren klanten dit scenario zelden.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Systeem replicatie SAP HANA met automatische failover

In de standaard-en meest voorkomende beschikbaarheids configuratie binnen één Azure-regio, hebben twee Azure-Vm's met SLES Linux een failovercluster gedefinieerd. Het SLES Linux-cluster is gebaseerd op het [pacemaker](./high-availability-guide-suse-pacemaker.md) -Framework, in combi natie met een [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) -apparaat. 

Vanuit een SAP HANA perspectief wordt de gebruikte replicatie modus gesynchroniseerd en wordt een automatische failover geconfigureerd. In de tweede VM fungeert het SAP HANA-exemplaar als een hot standby-knoop punt. Het knoop punt stand-by ontvangt een synchrone stroom van wijzigings records van het primaire SAP HANA exemplaar. Omdat trans acties worden doorgevoerd door de toepassing op het primaire knoop punt HANA, wacht het primaire HANA-knoop punt op het bevestigen van het door voeren van de toepassing totdat het secundaire SAP HANA knoop punt bevestigt dat het de commit-record heeft ontvangen. SAP HANA biedt twee synchrone replicatie modi. Zie voor meer informatie en voor een beschrijving van de verschillen tussen deze twee synchrone replicatie modi de SAP-artikel [replicatie modi voor SAP Hana-systeem replicatie](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

De algehele configuratie ziet er als volgt uit:

![Diagram van twee virtuele machines met opslag replicatie en failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

U kunt deze oplossing kiezen omdat u hiermee een RPO = 0 en een lage RTO kunt bezorgen. Configureer de SAP HANA client connectiviteit zodat de SAP HANA-clients het virtuele IP-adres gebruiken om verbinding te maken met de configuratie van de HANA-systeem replicatie. Een dergelijke configuratie elimineert de nood zaak om de toepassing opnieuw te configureren als er een failover naar het secundaire knoop punt plaatsvindt. In dit scenario moeten de Sku's van de Azure-VM voor de primaire en secundaire Vm's hetzelfde zijn.

## <a name="next-steps"></a>Volgende stappen

Ga voor stapsgewijze instructies voor het instellen van deze configuraties in azure naar:

- [SAP HANA systeem replicatie in virtuele Azure-machines instellen](sap-hana-high-availability.md)
- [Hoge Beschik baarheid voor SAP HANA met behulp van systeem replicatie](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Zie voor meer informatie over de beschik baarheid van SAP HANA in azure-regio's:

- [Beschik baarheid van SAP HANA in azure-regio's](./sap-hana-availability-across-regions.md)