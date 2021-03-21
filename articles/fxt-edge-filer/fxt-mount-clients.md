---
title: Clients koppelen aan het Azure FXT Edge Filer-cluster van Microsoft
description: Hoe NFS-clientcomputers aan de Azure FXT Edge Filer hybride opslagcache kunnen koppelen
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: fa1f9da2c60aaf4c552916d16c266e984bf08892
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92340509"
---
# <a name="tutorial-mount-the-cluster"></a>Zelfstudie: Het cluster koppelen

In deze zelfstudie leert u hoe u NFS-clients kunt koppelen aan het Azure FXT Edge Filer-cluster. Clients koppelen de paden van de virtuele naamruimte die u hebt toegewezen bij het toevoegen van back-end-opslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Strategieën voor taakverdeling-clients over het bereik van client-gerichte IP-adressen
> * Hoe een koppelingspad maken op basis van een client-gericht IP-adres en naamruimteverbinding
> * Welke argumenten moeten worden gebruikt in een koppelingsopdracht

Het voltooien van deze zelfstudie duurt ongeveer 45 minuten.

## <a name="steps-to-mount-the-cluster"></a>Stappen om het cluster te koppelen

Volg deze stappen om clientcomputers te verbinden met uw Azure FXT Edge Filer-cluster.

1. Bepaal hoe u het clientverkeer wilt verdelen over de clusterknooppunten. Lees [Clientbelasting verdelen](#balance-client-load)hieronder voor meer informatie.
1. Identificeer het IP-adres en het pad van de cluster om te koppelen.
1. Bepaal het op de client-gerichte pad voor de koppeling.
1. Verzend de [Koppelingsopdracht](#use-recommended-mount-command-options)met de juiste argumenten.

## <a name="balance-client-load"></a>Verdeling van clientbelasting

Als u clientaanvragen wilt verdelen over alle knooppunten in het cluster, moet u clients koppelen aan het volledige bereik aan client-gerichte IP-adressen. Er zijn verschillende manieren om deze taak te automatiseren.

Lees [DNS configureren voor de Azure FXT Edge Filer-cluster](fxt-configure-network.md#configure-dns-for-load-balancing)voor meer informatie over round robin DNS-taakverdeling voor het cluster. Als u deze methode wilt gebruiken, moet u een DNS-server onderhouden, die niet wordt uitgelegd in deze artikelen.

Een eenvoudigere methode voor kleine installaties is het gebruik van een script om IP-adressen toe te wijzen binnen het bereik van de client koppelingstijd.

Andere methoden voor taakverdeling kunnen geschikt zijn voor grote of gecompliceerde systemen. Neem contact op met uw Microsoft-vertegenwoordiger of open een [Ondersteuningsaanvraag](fxt-support-ticket.md) voor hulp. (Azure Load Balancer wordt momenteel *niet ondersteund* met de Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>De koppelingsopdracht maken

Vanaf uw client, ``mount``wijst de opdracht de virtuele server (vserver) toe aan het Azure FXT Edge Filer-cluster naar een pad op het lokale bestandssysteem.

De indeling is ``mount <FXT cluster path> <local path> {options}``

Er zijn drie elementen voor de koppelingsopdracht:

* Clusterpad – een combinatie van het pad voor IP-adres en naamruimtekoppeling dat hieronder wordt beschreven
* Lokaal pad – het pad op de client
* Opties koppelingsopdracht – (weer gegeven in [Aanbevolen opties voor koppelingsopdracht gebruiken](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Het clusterpad maken

Het clusterpad is een combinatie van het *IP-adres* van de vserver plus het pad naar een *naamruimtekoppeling*. De naamruimtekoppeling is een virtueel pad dat u hebt gedefinieerd wanneer u [het opslagsysteem hebt toegevoegd](fxt-add-storage.md#create-a-junction).

Als u bijvoorbeeld ``/fxt/files`` hebt gebruikt als het pad naar de naamruimte, zouden uw clients *IP_address*:/fxt/files te koppelen aan hun lokale koppelpunt.

![Dialoogvenster "Nieuwe koppeling toevoegen" met /avere/files in het veld naamruimtepad](media/fxt-mount/fxt-junction-example.png)

Het IP-adres is één van de client-gerichte IP-adressen die zijn gedefinieerd voor de vserver. U kunt het bereik van client-gerichte IP's vinden op twee plaatsen in het cluster Configuratiescherm:

* **VServers** tabel (tabblad Dashboard) –

  ![Tabblad Dashboard van het Configuratiescherm met het tabblad VServer geselecteerd in de gegevenstabel onder de grafiek, en de sectie IP-adres omcirkeld](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Instellingenpagina **Client-gericht netwerk** –

  ![Instellingen > VServer > Client-gerichte netwerk configuratiepagina met een cirkel rond de sectie Adresbereik van de tabel voor een bepaalde vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Combineer het IP-adres en het pad van de naamruimte om het pad van het cluster voor de koppelingsopdracht te maken.

Voor beeld van client koppelingsopdracht: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Het lokale pad maken

Het lokale pad voor de koppelingsopdracht hangt af van u. U kunt elke gewenste padstructuur instellen als onderdeel van de virtuele naamruimte. Ontwerp een naamruimte en een lokaal pad dat handig is voor uw client werkstroom.

Lees voor meer informatie over de client-gerichte naamruimte het [Naamruimte overzicht](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html) van de Hulplijn Clusterconfiguratie.

Naast de paden, neemt u de [opties voor de koppelingsopdracht](#use-recommended-mount-command-options) op die hieronder worden beschreven bij het koppelen van elke client.

### <a name="use-recommended-mount-command-options"></a>Aanbevolen opties voor de koppelingsopdracht gebruiken

Om ervoor te zorgen dat een naadloze clientkoppeling, geeft u deze instellingen en argument door in uw koppelingsopdracht:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Verplichte instellingen | Beschrijving |
--- | ---
``hard`` | Zachte koppelingen naar het Azure FXT Edge Filer-cluster zijn gekoppeld aan toepassingsfouten en mogelijk gegevensverlies.
``proto=netid`` | Deze optie ondersteunt de juiste verwerking van NFS-netwerkfouten.
``mountproto=netid`` | Deze optie ondersteunt de juiste verwerking van netwerkfouten voor koppelingsbewerkingen.
``retry=n`` | Stel ``retry=30`` in om tijdelijke koppelingsfouten te voorkomen. (Een andere waarde wordt aanbevolen in voorgrondkoppelingen.)

| Voorkeursinstellingen  | Beschrijving |
--- | ---
``nointr``            | Als uw clients oudere OS-kernels gebruiken (vóór april 2008) die deze optie ondersteunen, gebruikt u deze. De optie "intr" is de standaardinstelling.

## <a name="next-steps"></a>Volgende stappen

Nadat u clients hebt gekoppeld, kunt u uw werkstroom testen en aan de slag gaan met uw cluster.

Als u gegevens naar een nieuwe cloud kernfiler wilt verplaatsen, kunt u profiteren van de cache structuur door gebruik te maken van parallelle gegevensopname. Sommige strategieën worden beschreven in [Verplaatsen van gegevens naar een vFXT-cluster](../avere-vfxt/avere-vfxt-data-ingest.md). (Avere vFXT voor Azure is een cloud-product dat gebruikmaakt van technologie voor opslaan in cache die zeer vergelijkbaar is met de Azure FXT Edge Filer.)

Lees [De hardware-status van de Azure FXT Edge Filer bewaken](fxt-monitor.md) als u problemen met hardware moet oplossen.