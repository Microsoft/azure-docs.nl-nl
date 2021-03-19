---
title: Implementatie overzicht-avere vFXT voor Azure
description: Meer informatie over het implementeren van een avere vFXT voor Azure-cluster in dit overzicht. Gerelateerde artikelen hebben specifieke implementatie-instructies.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4c63fdf2164dd4dce12912669eec29c79755cc2a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88271223"
---
<!-- filename is linked to in the marketplace template, make sure it gets a redirect if we rename it -->

# <a name="avere-vfxt-for-azure---deployment-overview"></a>Overzicht van AVERE vFXT voor Azure-implementatie

In dit artikel vindt u een overzicht van de stappen die nodig zijn om een avere vFXT voor Azure-cluster actief te krijgen.

Er zijn verschillende taken nodig voor en nadat u het vFXT-cluster hebt gemaakt op de Azure Marketplace. Een duidelijke indruk van het begin-to-finish-proces helpt u de inspanningen te bereiken die nodig zijn.

## <a name="deployment-steps"></a>Implementatiestappen

Nadat u [uw systeem hebt gepland](avere-vfxt-deploy-plan.md), kunt u beginnen met het maken van uw avere vFXT-cluster.

Een Azure Resource Manager sjabloon in de Azure Marketplace verzamelt de benodigde gegevens en implementeert automatisch het hele cluster.

Nadat het vFXT-cluster actief is, zijn er nog enkele configuratie stappen die u moet uitvoeren voordat u deze kunt gebruiken. Als u een nieuwe Blob Storage-container hebt gemaakt, wilt u uw gegevens ernaar verplaatsen. Als u een NAS-opslag systeem gebruikt, moet u dit toevoegen nadat het cluster is gemaakt. U moet clients verbinden met het cluster.

Hier volgt een overzicht van alle stappen.

1. Vereisten configureren

   Voordat u een VM maakt, moet u een nieuw abonnement maken voor het avere vFXT-project, het eigendom van het abonnement configureren, quota's controleren en zo nodig een verhoging aanvragen en voor waarden voor het gebruik van de avere vFXT-software accepteren. Lees [de voor bereiding voor het maken van de avere-vFXT](avere-vfxt-prereqs.md) voor gedetailleerde instructies.

1. Het avere vFXT-cluster maken

   Gebruik Azure Marketplace om de avere vFXT voor Azure-cluster te maken. Een sjabloon verzamelt de vereiste informatie en voert scripts uit om het eind product te maken.

   Het maken van een cluster omvat de volgende stappen, die allemaal worden uitgevoerd door de Marketplace-sjabloon:

   * Nieuwe netwerk infrastructuur en-resource groepen maken, indien nodig
   * Een cluster controller maken

     De cluster controller is een eenvoudige VM die zich in hetzelfde virtuele netwerk bevindt als het avere vFXT-cluster en beschikt over de aangepaste software die nodig is voor het maken en beheren van het cluster. De controller maakt de vFXT-knoop punten en vormt het cluster en maakt ook een opdracht regel interface voor het beheren van het cluster tijdens de levens duur.

     Als u tijdens de implementatie een nieuw virtueel netwerk of subnet maakt, heeft uw controller een openbaar IP-adres. Dit betekent dat de controller als een Jump host kan fungeren voor het maken van verbinding met het cluster van buiten het virtuele netwerk.

   * De Vm's van het cluster knooppunt maken

   * Het cluster maken op basis van de afzonderlijke knoop punten

   * Maak eventueel een nieuwe BLOB-container en configureer deze als back-end-opslag voor het cluster

   Het maken van een cluster wordt gedetailleerd beschreven in [Deploy the vFXT cluster](avere-vfxt-deploy.md).

1. Het cluster configureren

   Maak verbinding met de configuratie-interface van AVERE vFXT (avere in het configuratie scherm) om de instellingen van het cluster aan te passen. Meld u aan voor ondersteunings bewaking en voeg uw opslag systeem toe als u hardware-opslag of extra BLOB-containers gebruikt.

   * [Access the vFXT cluster (Toegang tot het vFXT-cluster)](avere-vfxt-cluster-gui.md)
   * [Ondersteuning inschakelen](avere-vfxt-enable-support.md)
   * [Opslag configureren](avere-vfxt-add-storage.md) (indien nodig)

1. Clients koppelen

   Volg de richt lijnen in [mount the avere vFXT cluster](avere-vfxt-mount-clients.md) voor meer informatie over taak verdeling en hoe client computers het cluster moeten koppelen.

1. Gegevens toevoegen (indien nodig)

   Omdat de avere vFXT een schaal bare cache voor meerdere clients is, is de beste manier om gegevens te verplaatsen naar een nieuwe back-end-opslag container met een multi-client, multi-threaded File Copy-strategie.

   Als u werksetgegevens wilt verplaatsen naar een nieuwe BLOB-container of een ander back-end-opslag systeem, volgt u de instructies in [gegevens verplaatsen naar het vFXT-cluster](avere-vfxt-data-ingest.md).

## <a name="next-steps"></a>Volgende stappen

Ga door met [de voor bereiding voor het maken van de avere vFXT](avere-vfxt-prereqs.md) voor het volt ooien van de vereiste taken.
