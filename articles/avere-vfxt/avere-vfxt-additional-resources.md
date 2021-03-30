---
title: Aanvullende koppelingen over avere vFXT voor Azure
description: Gebruik deze bronnen voor aanvullende informatie over avere vFXT voor Azure, waaronder avere-cluster documentatie en documentatie voor vFXT-beheer.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 6f9a9f8e6d2a279871ded3dff614be99dd2901df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271087"
---
# <a name="additional-documentation"></a>Aanvullende documentatie

Dit artikel bevat koppelingen naar aanvullende documentatie over de beheer interface voor avere in het configuratie scherm en verwante onderwerpen.

## <a name="avere-cluster-documentation"></a>Documentatie voor avere-cluster

Meer documentatie over avere-clusters vindt u op de website op <https://azure.github.io/Avere/> . Deze documenten kunnen u helpen inzicht te krijgen in de mogelijkheden van het cluster en het configureren van de instellingen.

* De [FXT-hand leiding](<https://azure.github.io/Avere/#fxt_cluster>) voor het maken van clusters is ontworpen voor clusters die bestaan uit fysieke hardwareconfiguraties, maar sommige informatie in het document is ook relevant voor vFXT-clusters. Met name nieuwe vFXT-cluster beheerders kunnen de volgende secties profiteren:
  * Door de [instellingen voor ondersteuning en controle](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_support.html#config-support>) aan te passen wordt uitgelegd hoe u de instellingen voor het uploaden van ondersteuning kunt aanpassen en externe bewaking kunt inschakelen
  * Het configureren van de [VServers en de globale naam ruimte](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_vserver.html#config-vserver>) bevat informatie over het maken van een client gerichte naam ruimte.
  * Bij [het configureren van DNS voor het avere-cluster](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_network.html#dns-overview>) wordt uitgelegd hoe Round-Robin DNS moet worden geconfigureerd.
  * [Opslag documenten voor back-end toevoegen een](<https://azure.github.io/Avere/legacy/create_cluster/4_8/html/config_core_filer.html#add-core-filer>) kern bestand toevoegen.

* De [cluster configuratie handleiding](<https://azure.github.io/Avere/#operations>) is een volledige verwijzing naar instellingen en opties voor een avere-cluster. Een vFXT-cluster maakt gebruik van een subset van deze opties, maar de meeste van dezelfde configuratie pagina's zijn van toepassing.

* In de [dash board-hand leiding](<https://azure.github.io/Avere/#operations>) wordt uitgelegd hoe u de cluster controle functies van het configuratie scherm avere gebruikt.

## <a name="vfxt-creation-and-management-documentation"></a>documentatie voor het maken en beheren van vFXT

Een volledige hand leiding voor het gebruik van vfxt.py, een script op basis van het maken en beheren van Cloud clusters, wordt gegeven op GitHub: [Cloud cluster Management met vfxt.py](https://github.com/Azure/AvereSDK/blob/master/docs/README.md).
