---
title: Azure Load Balancer distributie modi
description: Ga aan de slag met het leren over de verschillende distributie modi van Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99551253"
---
# <a name="azure-load-balancer-distribution-modes"></a>Azure Load Balancer distributie modi

Azure Load Balancer ondersteunt twee distributie modi voor het routeren van verbindingen met de toepassing met taak verdeling:

* Op hash gebaseerd
* Bron-IP-affiniteit

## <a name="hash-based"></a>Op hash gebaseerd

De standaarddistributiemodus van Azure Load Balancer is een hash met vijf tuples. 

De tuple bestaat uit het volgende:
* **Bron-IP**
* **Bronpoort**
* **Doel-IP**
* **Doelpoort**
* **Protocol type**

De hash wordt gebruikt om verkeer toe te wijzen aan de beschik bare servers. De algoritme biedt persistentie alleen binnen een transport sessie. Pakketten die zich in dezelfde sessie bevinden, worden omgeleid naar hetzelfde Data Center-IP achter het eind punt met gelijke taak verdeling. Wanneer de client een nieuwe sessie start vanuit hetzelfde bron-IP-adres, wordt de bron poort gewijzigd en wordt het verkeer naar een ander Data Center-eind punt getransporteerd.

![5-tuple hash-gebaseerde distributie modus](./media/distribution-mode-concepts/load-balancer-distribution.png)

Hash-gebaseerde modus heeft één configuratie type:

* **Geen (op hash gebaseerd)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde client door elke virtuele machine kunnen worden verwerkt.

## <a name="source-ip-affinity"></a>Bron-IP-affiniteit

Deze distributiemodus wordt ook wel sessieaffiniteit of client-IP-affiniteit genoemd. In de modus wordt gebruikgemaakt van een twee-tuple (bron-IP en doel-IP) of drie Tuples (bron-IP, doel-IP en type protocol) om verkeer toe te wijzen aan de beschik bare servers. 

Met behulp van bron-IP-affiniteit gaan verbindingen die vanaf dezelfde client computer worden gestart naar hetzelfde Data Center-eind punt.

In de volgende afbeelding ziet u een configuratie met twee Tuples. U ziet hoe de twee Tuples worden uitgevoerd via de load balancer naar Virtual Machine 1 (VM1). VM1 wordt vervolgens een back-up gemaakt door VM2 en VM3.

![Distributie modus voor twee tuple-sessie affiniteit](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Bron-IP-affiniteits modus heeft twee configuratie typen:

* **Client-IP (bron-IP-affiniteit 2-tuple)** -geeft aan dat opeenvolgende aanvragen van hetzelfde client-IP-adres worden verwerkt door dezelfde virtuele machine.
* **Client-IP en-protocol (bron-IP-affiniteit 3-tuple)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde combi natie van client-IP-adres en-protocol worden verwerkt door dezelfde virtuele machine.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Bron-IP-affiniteit met client-IP en-protocol (bron-IP-affiniteit 3-tuple), lost een incompatibiliteit tussen Azure Load Balancer en Extern bureaublad-gateway (RD-gateway) op. 

Een ander use-case-scenario is het uploaden van media. Het uploaden van gegevens gebeurt via UDP, maar het besturings element wordt bereikt via TCP:

* Een client start een TCP-sessie naar het open bare adres met taak verdeling en wordt omgeleid naar een specifieke DIP. Het kanaal blijft actief voor het bewaken van de verbindings status.
* Er wordt een nieuwe UDP-sessie van dezelfde client computer gestart voor hetzelfde open bare eind punt met gelijke taak verdeling. De verbinding wordt omgeleid naar hetzelfde DIP-eind punt als de vorige TCP-verbinding. Het uploaden van media kan worden uitgevoerd met een hoge door Voer terwijl een besturings kanaal wordt onderhouden via TCP.

> [!NOTE]
> Wanneer een set met gelijke taak verdeling wordt gewijzigd door een virtuele machine te verwijderen of toe te voegen, wordt de distributie van client aanvragen opnieuw berekend. U kunt niet afhankelijk zijn van nieuwe verbindingen van bestaande clients om op dezelfde server te eindigen. Bovendien kan het gebruik van de distributie modus voor bron-IP-affiniteit een ongelijke verdeling van verkeer veroorzaken. Clients die worden uitgevoerd achter proxy's, kunnen worden gezien als één unieke client toepassing.


## <a name="next-steps"></a>Volgende stappen

Zie [de distributie modus voor Azure Load Balancer configureren](load-balancer-distribution-mode.md)voor meer informatie over het configureren van de distributie modus van Azure Load Balancer.

