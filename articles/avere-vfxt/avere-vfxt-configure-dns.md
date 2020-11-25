---
title: AVERE vFXT DNS-Azure
description: Een DNS-server configureren voor Round-Robin taak verdeling met avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009021"
---
# <a name="avere-cluster-dns-configuration"></a>DNS-configuratie voor Avere-cluster

In deze sectie worden de basis beginselen uitgelegd van het configureren van een DNS-systeem voor taak verdeling van uw avere vFXT-cluster.

Dit document bevat *geen* instructies voor het instellen en beheren van een DNS-server in de Azure-omgeving.

In plaats van Round-Robin DNS te gebruiken om een vFXT-cluster in azure te verdelen, kunt u hand matige methoden gebruiken om IP-adressen gelijkmatig toe te wijzen aan clients wanneer deze zijn gekoppeld. Er worden verschillende methoden beschreven in [Koppel het avere-cluster](avere-vfxt-mount-clients.md).

Houd rekening met het volgende bij het bepalen of u wel of geen DNS-server wilt gebruiken:

* Als uw systeem alleen door NFS-clients wordt gebruikt, is het gebruik van DNS niet vereist. het is mogelijk om alle netwerk adressen op te geven met behulp van numerieke IP-adressen.

* Als uw systeem ondersteuning biedt voor toegang via SMB (CIFS), is DNS wel vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

## <a name="load-balancing"></a>Taakverdeling

Als u de algehele belasting wilt distribueren, moet u uw DNS-domein configureren voor het gebruik van Round-Robin load distributie voor client gerichte IP-adressen.

## <a name="configuration-details"></a>Configuratie Details

Wanneer clients toegang hebben tot het cluster, worden de aanvragen via RRDNS gelijkmatig verdeeld over alle beschikbare interfaces.

Voor de beste prestaties moet u de DNS-server configureren voor het afhandelen van clientgerichte clusteradressen, zoals wordt weergegeven in het volgende diagram.

Een cluster-vserver wordt aan de linkerkant weergegeven, en IP-adressen worden in het midden en aan de rechterkant weergegeven. Configureer elk clienttoegangspunt met A-records en pointers zoals geïllustreerd.

![AVERE Cluster Round-Robin DNS-diagram](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Elk clientgericht IP-adres moet een unieke naam hebben voor intern gebruik door het cluster. (In dit diagram hebben de IP-adressen van de client de naam vs1-client-IP-* voor de duidelijkheid, maar in een productieomgeving moet u waarschijnlijk een beknoptere naam gebruiken, zoals client*.)

Clients koppelen het cluster met de naam van de vserver als het serverargument.

Wijzig het bestand ``named.conf`` van uw DNS-server om de cyclische volgorde voor query's naar uw vserver in te stellen. Deze optie zorgt ervoor dat alle beschikbare waarden de cyclus doorlopen. Voeg een instructie zoals deze toe:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

De volgende ``nsupdate``-opdrachten zijn voorbeelden voor het correct configureren van DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>DNS-instellingen van cluster

Geef de DNS-server op die het vFXT-cluster gebruikt op de pagina **cluster**  >  **beheer netwerk** instellingen. Instellingen op die pagina zijn onder andere:

* DNS-serveradres
* DNS-domeinnaam
* DNS-zoekdomeinen

Lees de [DNS-instellingen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de avere-cluster configuratie handleiding voor meer informatie over het gebruik van deze pagina.
