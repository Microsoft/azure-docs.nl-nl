---
title: Pacemaker instellen voor RHEL in azure | Microsoft Docs
description: Pacemaker instellen voor Red Hat Enterprise Linux in azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2021
ms.author: radeltch
ms.openlocfilehash: 631ebcd41e50a6b8f9e049a646394e572c0b15f7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549317"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Pacemaker instellen voor Red Hat Enterprise Linux in azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Lees eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533], die:
  * De lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belang rijke informatie over de capaciteit van Azure VM-grootten.
  * De ondersteunde SAP-software en besturings systemen (OS) en database combinaties.
  * De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure.
* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA systeem replicatie in pacemaker-cluster](https://access.redhat.com/articles/3004101)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-SBD en fence_sbd](https://access.redhat.com/articles/2800691)
* Documentatie voor Azure-specifieke RHEL:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Overwegingen bij het aannemen van RHEL 8-hoge Beschik baarheid en clusters](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/considerations_in_adopting_rhel_8/high-availability-and-clusters_considerations-in-adopting-rhel-8)
  * [SAP S/4HANA ASCS/ERS met zelfstandige server 2 (ENSA2) configureren in pacemaker op RHEL 7,6](https://access.redhat.com/articles/3974941)
  * [RHEL voor SAP-aanbiedingen op Azure](https://access.redhat.com/articles/5456301)

## <a name="cluster-installation"></a>Cluster installatie

![Overzicht van pacemaker op RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat biedt geen ondersteuning voor door software geëmuleerde watchdog. Red Hat biedt geen ondersteuning voor SBD op Cloud platforms. Zie [het ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-SBD en fence_sbd](https://access.redhat.com/articles/2800691)voor meer informatie.
> Het enige ondersteunde afomheinings mechanisme voor pacemaker Red Hat Enterprise Linux clusters op Azure is Azure Fence agent.  

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** registreren. Deze stap is niet vereist als u gebruikmaakt van RHEL SAP HA-installatie kopieën.  

   Registreer uw virtuele machines en koppel deze aan een groep die opslag plaatsen voor RHEL 7 bevat.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Door een pool aan een Azure Marketplace PAYG RHEL-installatie kopie te koppelen, wordt u in feite gefactureerd voor uw RHEL-gebruik: eenmaal voor de PAYG-installatie kopie en eenmaal voor het RHEL recht van de groep die u koppelt. Azure biedt nu BYOS RHEL-installatie kopieën om dit te verhelpen. Meer informatie vindt u [hier](../redhat/byos.md).  

1. **[A]** Schakel RHEL in voor SAP opslag plaatsen. Deze stap is niet vereist als u gebruikmaakt van RHEL SAP HA-installatie kopieën.  

   Schakel de volgende opslag plaatsen in om de vereiste pakketten te installeren.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL HA installeren Add-On

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > We raden u aan de volgende versies van de Azure Fence-agent (of hoger) voor klanten te laten profiteren van een snellere failover, als het stoppen van een resource mislukt of als de cluster knooppunten niet meer kunnen communiceren.  
   > RHEL 7,7 of hoger gebruiken de nieuwste beschik bare versie van het pakket omheining-agents  
   > RHEL 7,6: omheining-agents-4.2.1-11.el7_6.8  
   > RHEL 7,5: omheining-agents-4.0.11-86.el7_5.8  
   > RHEL 7,4: omheining-agents-4.0.11-66.el7_4.12  
   > Zie voor meer informatie [Azure VM die wordt uitgevoerd als een RHEL-lid van een cluster met hoge Beschik baarheid neemt erg veel tijd in beslag om te worden geomheiningd of mislukt het uitzetten van een storing of een time-out voordat de virtuele machine wordt afgesloten](https://access.redhat.com/solutions/3408711).

   Controleer de versie van de Azure Fence-agent. Indien nodig moet u deze bijwerken naar een versie die gelijk is aan of hoger dan de hierboven vermelde.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Als u de Azure Fence-agent wilt bijwerken, en als u aangepaste rol gebruikt, moet u ervoor zorgen dat u de aangepaste rol bijwerkt om actie **uitgeschakeld** op te nemen. Zie [een aangepaste rol maken voor de Fence-agent](#1-create-a-custom-role-for-the-fence-agent)voor meer informatie.  

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten.  

   >[!IMPORTANT]
   > Als u hostnamen in de cluster configuratie gebruikt, is het essentieel dat u een betrouw bare naam omzetting voor hostnamen hebt. De cluster communicatie mislukt als de namen niet beschikbaar zijn en die kunnen leiden tot vertragingen in de failover van het cluster.
   > Het voor deel van het gebruik van bestand/etc/hosts is dat uw cluster onafhankelijk van DNS wordt. Dit kan ook een enkel storings punt zijn.  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster wacht woord wijzigen in hetzelfde wacht woord

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** firewall regels voor pacemaker toevoegen

   Voeg de volgende firewall regels toe aan alle cluster communicatie tussen de cluster knooppunten.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** basis Cluster Services inschakelen

   Voer de volgende opdrachten uit om de pacemaker-service in te scha kelen en te starten.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** pacemaker-cluster maken

   Voer de volgende opdrachten uit om de knoop punten te verifiëren en het cluster te maken. Stel het token in op 30000 om onderhoud van het geheugen mogelijk te maken. Zie [dit artikel voor Linux][virtual-machines-linux-maintenance]voor meer informatie.  
   
   Als u een cluster op **RHEL 7. x** maakt, gebruikt u de volgende opdrachten:  
   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all
   </code></pre>

   Als u een cluster op **RHEL 8. X** maakt, gebruikt u de volgende opdrachten:  
   <pre><code>sudo pcs host auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> totem token=30000
   sudo pcs cluster start --all
   </code></pre>

   Controleer de cluster status door de volgende opdracht uit te voeren:  
   <pre><code> # Run the following command until the status of both nodes is online
   sudo pcs status
   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** verwachte stemmen instellen. 
   
   <pre><code># Check the quorum votes 
    pcs quorum status
    # If the quorum votes are not set to 2, execute the next command
    sudo pcs quorum expected-votes 2
   </code></pre>

   >[!TIP]
   > Als u een cluster met meerdere knoop punten wilt bouwen, dat cluster met meer dan twee knooppunten is, stelt u de stemmen niet in op 2.    

1. **[1]** gelijktijdige omheinings acties toestaan

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>STONITH-apparaat maken

Het STONITH-apparaat gebruikt een Service-Principal om te autoriseren bij Microsoft Azure. Volg deze stappen om een service-principal te maken.

1. Ga naar <https://portal.azure.com>
1. Open de Blade Azure Active Directory  
   Ga naar eigenschappen en noteer de map-ID. Dit is de **Tenant-id**.
1. Klik op App-registraties
1. Klik op nieuwe registratie
1. Voer een naam in, selecteer alleen accounts in deze organisatie Directory 
2. Selecteer het toepassings type ' Web ', voer een aanmeldings-URL in (bijvoorbeeld http: \/ /localhost) en klik op toevoegen.  
   De aanmeldings-URL wordt niet gebruikt en kan een geldige URL zijn
1. Selecteer certificaten en geheimen en klik vervolgens op nieuw client geheim
1. Voer een beschrijving in voor een nieuwe sleutel, selecteer nooit verloopt en klik op toevoegen
1. Maak een knoop punt van de waarde. Dit wordt gebruikt als het **wacht woord** voor de Service-Principal
1. Selecteer overzicht. Noteer de toepassings-ID. Deze wordt gebruikt als de gebruikers naam (**aanmeldings-id** in de onderstaande stappen) van de Service-Principal

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** een aangepaste rol maken voor de Fence-agent

De service-principal heeft standaard geen machtigingen voor toegang tot uw Azure-resources. U moet de Service-Principal machtigingen geven om alle virtuele machines van het cluster te starten en te stoppen. Als u de aangepaste rol nog niet hebt gemaakt, kunt u deze maken met behulp van [Power shell](../../../role-based-access-control/role-assignments-powershell.md) of [Azure cli](../../../role-based-access-control/role-assignments-cli.md)

Gebruik de volgende inhoud voor het invoer bestand. U moet de inhoud aanpassen aan uw abonnementen, door c276fc76-9cd4-44c9-99a7-4fd71546436e en e91d47c4-76f3-4271-a796-21b4ecfe3624 te vervangen door de Id's van uw abonnement. Als u slechts één abonnement hebt, verwijdert u de tweede vermelding in AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** de aangepaste rol toewijzen aan de Service-Principal

Wijs de aangepaste rol Linux Fence-agent rol toe die in het laatste hoofd stuk is gemaakt voor de Service-Principal. Gebruik de rol van Eigenaar niet meer!

1. Ga naar https://portal.azure.com
1. Open de blade Alle resources
1. De virtuele machine van het eerste clusterknooppunt selecteren
1. Klik op Toegangsbeheer (IAM)
1. Klik op roltoewijzing toevoegen
1. Selecteer de rol ' Linux Fence-agent functie '
1. Voer de naam in van de toepassing die u hierboven hebt gemaakt
1. Klik op Opslaan

Herhaal de bovenstaande stappen voor het tweede cluster knooppunt.

### <a name="1-create-the-stonith-devices"></a>**[1]** de STONITH-apparaten maken

Nadat u de machtigingen voor de virtuele machines hebt bewerkt, kunt u de STONITH-apparaten in het cluster configureren.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

> [!NOTE]
> De optie pcmk_host_map is alleen vereist in de opdracht als de RHEL-hostnamen en de namen van de Azure-VM'S niet identiek zijn. Geef de toewijzing op in de indeling **hostname: VM-naam**.
> Raadpleeg de sectie vet in de opdracht. Zie voor meer informatie [welke indeling ik moet gebruiken om knooppunt toewijzingen op te geven voor stonith-apparaten in pcmk_host_map](https://access.redhat.com/solutions/2619961)

Voor RHEL **7. X** gebruikt u de volgende opdracht om het Fence-apparaat te configureren:    
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

Voor RHEL **8. X** gebruikt u de volgende opdracht om het Fence-apparaat te configureren:  
<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm username="<b>login ID</b>" password="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> De bewakings-en omheinings bewerkingen worden gedeserialiseerd. Als er een langere bewakings bewerking en gelijktijdige Afleidings gebeurtenis optreedt, is er dus geen vertraging voor de failover van het cluster vanwege de bewakings bewerking die al wordt uitgevoerd.  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** het gebruik van een STONITH-apparaat inschakelen

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>De Azure Fence-agent vereist uitgaande connectiviteit met open bare eind punten zoals gedocumenteerd, samen met mogelijke oplossingen, in [open bare-eindpunt connectiviteit voor vm's met behulp van standaard ILB](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
