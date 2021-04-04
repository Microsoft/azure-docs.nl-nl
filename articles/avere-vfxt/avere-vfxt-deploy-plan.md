---
title: Uw avere vFXT-systeem plannen-Azure
description: Plan een avere-vFXT voor Azure-cluster dat geschikt is voor uw behoeften. U kunt vragen stellen voordat u naar de Azure Marketplace gaat of virtuele machines maakt.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: 9f852ec056f3252005bba0bc142c05a9cfbe6c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342396"
---
# <a name="plan-your-avere-vfxt-system"></a>Uw Avere vFXT-systeem plannen

In dit artikel wordt uitgelegd hoe u een nieuw avere vFXT voor Azure-cluster plant dat op de juiste wijze is gepositioneerd en aangepast aan uw behoeften.

Voordat u naar de Azure Marketplace gaat of een virtuele machine maakt, moet u rekening houden met de volgende details:

* Hoe werkt het cluster samen met andere Azure-bronnen?
* Waar moeten cluster elementen zich bevinden in particuliere netwerken en subnetten?
* Welk type back-end-opslag kunt u gebruiken en hoe krijgt het cluster toegang tot het?
* Hoe krachtig moeten uw cluster knooppunten uw werk stroom ondersteunen?

Lees verder voor meer informatie.

## <a name="learn-the-components-of-the-system"></a>Meer informatie over de onderdelen van het systeem

Het kan handig zijn om inzicht te krijgen in de onderdelen van het avere vFXT voor Azure-systeem wanneer u begint met plannen.

* Cluster knooppunten: het cluster bestaat uit drie of meer virtuele machines die zijn geconfigureerd als cluster knooppunten. Meer knoop punten bieden het systeem meer door Voer en een grotere cache.

* Cache-de cache capaciteit wordt gelijkmatig verdeeld over de cluster knooppunten. Stel de cache grootte per knoop punt in wanneer u het cluster maakt. de knooppunt grootten worden toegevoegd om de totale cache grootte te krijgen.

* Cluster controller: de cluster controller is een extra VM die zich in hetzelfde subnet bevindt als de cluster knooppunten. De controller is nodig voor het maken van het cluster en voor doorlopende beheer taken.

* Back-end-opslag: de gegevens die u in de cache wilt opslaan, worden op lange termijn opgeslagen in een hardware-opslag systeem of in een Azure Blob-container. U kunt opslag toevoegen nadat u de avere vFXT voor Azure-cluster hebt gemaakt, of als u Blob Storage gebruikt, kunt u de container toevoegen en configureren tijdens het maken van het cluster.

* Clients: client machines die gebruikmaken van de cache bestanden maken verbinding met het cluster met behulp van een virtueel bestandspad in plaats van rechtstreeks toegang tot de opslag systemen. (Meer informatie over [het koppelen van het avere vFXT-cluster](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Abonnement, resource groep en netwerk infrastructuur

Denk na over waar de elementen van uw avere vFXT voor Azure-implementatie zullen worden. In het onderstaande diagram ziet u een mogelijke schema voor de avere vFXT voor Azure-onderdelen:

![Diagram waarin de cluster controller en de cluster-Vm's binnen één subnet worden weer gegeven. Rond de grens van het subnet bevindt zich een vnet-grens. Binnen het vnet is dit een zeshoek die het service-eind punt van de opslag aangeeft. deze is verbonden met een onderbroken pijl naar een Blob-opslag buiten het vnet.](media/avere-vfxt-components-option.png)

Volg deze richt lijnen bij het plannen van de netwerk infrastructuur van uw avere vFXT-cluster:

* Maak een nieuw abonnement voor elke avere-vFXT voor Azure-implementatie. Alle onderdelen in dit abonnement beheren.

  Voor delen van het gebruik van een nieuw abonnement voor elke implementatie zijn:
  * Eenvoudiger kosten bijhouden: Bekijk en controleer alle kosten van resources, infra structuur en reken cycli in één abonnement.
  * Eenvoudiger opschonen: u kunt het hele abonnement verwijderen wanneer u klaar bent met het project.
  * Handig partitioneren van resource quota: Isoleer de avere vFXT-clients en het cluster in één abonnement om andere kritieke werk belastingen te beschermen tegen mogelijke resource beperking. Deze schei ding voor komt conflicten bij het inbrengen van een groot aantal clients voor een werk stroom met hoge prestaties.

* Zoek uw client Compute-systemen dicht bij het vFXT-cluster. Back-end-opslag kan extern zijn.  

* Zoek het vFXT-cluster en de virtuele machine van de cluster controller samen, zodat ze het volgende moeten zijn:

  * In hetzelfde virtuele netwerk
  * In dezelfde resource groep
  * Hetzelfde opslag account gebruiken
  
  De sjabloon voor het maken van het cluster verwerkt deze configuratie voor de meeste situaties.

* Het cluster moet zich in een eigen subnet bevinden om IP-adres conflicten met clients of andere reken bronnen te voor komen.

* Gebruik de sjabloon voor het maken van het cluster om de meeste benodigde infrastructuur resources voor het cluster te maken, met inbegrip van resource groepen, virtuele netwerken, subnetten en opslag accounts.

  Als u resources wilt gebruiken die al bestaan, moet u ervoor zorgen dat deze voldoen aan de vereisten in deze tabel.

  | Resource | Bestaande gebruiken? | Vereisten |
  |----------|-----------|----------|
  | Resourcegroep | Ja, indien leeg | Moet leeg zijn|
  | Storage-account | **Ja** als er verbinding wordt gemaakt met een bestaande BLOB-container na het maken van het cluster <br/>  **Nee** , als u een nieuwe BLOB-container maakt tijdens het maken van het cluster | Bestaande BLOB-container moet leeg zijn <br/> &nbsp; |
  | Virtueel netwerk | Yes | Moet een service-eind punt voor opslag bevatten als er een nieuwe Azure Blob-container wordt gemaakt |
  | Subnet | Yes | Kan geen andere resources bevatten |

## <a name="ip-address-requirements"></a>Vereisten voor IP-adressen

Zorg ervoor dat het subnet van het cluster een groot voldoende IP-adres bereik heeft voor de ondersteuning van het cluster.

Het avere vFXT-cluster maakt gebruik van de volgende IP-adressen:

* Eén IP-adres voor cluster beheer. Dit adres kan van het ene naar het andere knoop punt in het cluster worden verplaatst, zodat deze altijd beschikbaar is. Gebruik dit adres om verbinding te maken met het configuratie hulpprogramma van het avere configuratie scherm.
* Voor elk cluster knooppunt:
  * Ten minste één client gerichte IP-adres. (Alle client adressen worden beheerd door de *vserver* van het cluster, waardoor de IP-adressen naar wens kunnen worden verplaatst tussen de knoop punten.)
  * Eén IP-adres voor cluster communicatie
  * Een IP-adres van een exemplaar (toegewezen aan de virtuele machine)

Als u Azure Blob Storage gebruikt, kan het ook IP-adressen van het virtuele netwerk van uw cluster vereisen:  

* Een Azure Blob-opslag account vereist ten minste vijf IP-adressen. Houd deze vereiste in acht als u Blob Storage vindt in hetzelfde virtuele netwerk als uw cluster.
* Als u Azure Blob Storage gebruikt die zich buiten het virtuele netwerk van het cluster bevindt, maakt u een opslag service-eind punt in het virtuele netwerk. Het eind punt maakt geen gebruik van een IP-adres.

U hebt de mogelijkheid om netwerk bronnen en Blob-opslag (indien gebruikt) in verschillende resource groepen van het cluster te vinden.

## <a name="vfxt-node-size"></a>grootte van vFXT-knoop punt

De virtuele machines die fungeren als cluster knooppunten bepalen de door Voer van de aanvraag en de opslag capaciteit van uw cache. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Elk vFXT-knoop punt is identiek. Dat wil zeggen, als u een cluster met drie knoop punten maakt, u drie Vm's hebt van hetzelfde type en dezelfde grootte.

| Type instantie | vCPUs | Geheugen  | Lokale SSD-opslag  | Max. aantal gegevensschijven | Niet in cache opgeslagen schijf doorvoer | NIC (aantal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51.200 IOPS <br/> 768 MBps | 16.000 MBps (8)  |

Schijf cache per knoop punt kan worden geconfigureerd en kan rage van 1000 GB tot 8000 GB. 4 TB per knoop punt is de aanbevolen cache grootte voor Standard_E32s_v3 knooppunten.

Lees de Microsoft Azure documentatie voor meer informatie over deze Vm's: [grootten van virtuele machines](../virtual-machines/sizes-memory.md) die zijn geoptimaliseerd voor geheugen

## <a name="account-quota"></a>Account quotum

Zorg ervoor dat uw abonnement de capaciteit heeft om het avere vFXT-cluster uit te voeren, evenals alle computers of client systemen die worden gebruikt. Lees [quotum voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) voor meer informatie.

## <a name="back-end-data-storage"></a>Back-end-gegevens opslag

Back-end-opslag systemen leveren beide bestanden aan de cache van het cluster en ontvangen ook gewijzigde gegevens van de cache. Bepaal of uw werkset lange termijn wordt opgeslagen in een nieuwe BLOB-container of in een bestaand opslag systeem (Cloud of hardware). Deze back-end-opslag systemen worden *kern bestanden* genoemd.

### <a name="hardware-core-filers"></a>Kern bestanden voor hardware

Voeg hardwarematige opslag systemen toe aan het vFXT-cluster nadat u het cluster hebt gemaakt. U kunt verschillende populaire hardwaresystemen gebruiken, waaronder on-premises systemen, zolang het opslag systeem kan worden bereikt vanuit het subnet van het cluster.

Lees [opslag configureren](avere-vfxt-add-storage.md) voor gedetailleerde instructies over het toevoegen van een bestaand opslag systeem aan het avere vFXT-cluster.

### <a name="cloud-core-filers"></a>Cloud-kern bestanden

In het avere vFXT voor Azure-systeem kunnen lege BLOB-containers worden gebruikt voor back-endservers. Containers moeten leeg zijn wanneer ze worden toegevoegd aan het cluster. het vFXT-systeem moet het object archief kunnen beheren zonder dat bestaande gegevens moeten worden bewaard.

> [!TIP]
> Als u Azure Blob Storage voor de back-end wilt gebruiken, maakt u een nieuwe container als onderdeel van het maken van het vFXT-cluster. De sjabloon voor het maken van het cluster kan een nieuwe BLOB-container maken en configureren, zodat deze gereed is om te worden gebruikt zodra het cluster beschikbaar is. Het toevoegen van een container is later gecompliceerder.
>
> Lees [de avere-vFXT voor Azure maken](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) voor meer informatie.

Nadat u de lege Blob Storage-container als een kern bestand hebt toegevoegd, kunt u er gegevens naar kopiëren via het cluster. Gebruik een parallel Kopieer mechanisme met meerdere threads. Lees [gegevens verplaatsen naar het vFXT-cluster](avere-vfxt-data-ingest.md) voor meer informatie over het efficiënt kopiëren van gegevens naar de nieuwe container van het cluster met behulp van client computers en de avere vFXT-cache.

## <a name="cluster-access"></a>Cluster toegang

Het avere vFXT voor Azure-cluster bevindt zich in een privé subnet en het cluster heeft geen openbaar IP-adres. U moet een van de volgende manieren hebben om toegang te krijgen tot het privé subnet voor cluster beheer en client verbindingen.

De toegangs opties zijn onder andere:

* Jump host: wijs een openbaar IP-adres toe aan een afzonderlijke virtuele machine in het particuliere netwerk en gebruik het om een TLS-tunnel te maken voor de cluster knooppunten.

  > [!TIP]
  > Als u een openbaar IP-adres op de cluster controller instelt, kunt u het gebruiken als de Jump host. Lees de [cluster controller als Jump host](#cluster-controller-as-jump-host) voor meer informatie.

* Virtueel particulier netwerk (VPN): Configureer een punt-naar-site-of site-naar-site-VPN tussen uw particuliere netwerk in Azure en bedrijfs netwerken.

* Azure-ExpressRoute: Configureer een particuliere verbinding via een ExpressRoute-partner.

Lees de [documentatie van Azure Virtual Network over Internet communicatie](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet)voor meer informatie over deze opties.

### <a name="cluster-controller-as-jump-host"></a>Cluster controller als Jump host

Als u een openbaar IP-adres op de cluster controller instelt, kunt u dit als een Jump host gebruiken om contact op te nemen met het avere vFXT-cluster van buiten het particuliere subnet. Omdat de controller echter toegangs rechten heeft om cluster knooppunten te wijzigen, maakt dit een klein beveiligings risico.

Ter verbetering van de beveiliging van een controller met een openbaar IP-adres, wordt door het implementatie script automatisch een netwerk beveiligings groep gemaakt waarmee de toegang tot alleen poort 22 wordt beperkt. U kunt het systeem verder beveiligen door de toegang tot uw bereik van IP-bron adressen te vergren delen, dat wil zeggen, alleen verbindingen toestaan van computers die u wilt gebruiken voor toegang tot het cluster.

Wanneer u het cluster maakt, kunt u kiezen of u een openbaar IP-adres op de cluster controller wilt maken.

* Als u een **nieuw virtueel netwerk** of een **Nieuw subnet** maakt, wordt aan de cluster controller een **openbaar** IP-adres toegewezen.
* Als u een bestaand virtueel netwerk en een subnet selecteert, heeft de cluster controller alleen **privé** -IP-adressen.

## <a name="vm-access-roles"></a>VM-toegangs rollen

Azure maakt gebruik [van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/index.yml) om de cluster-vm's te machtigen om bepaalde taken uit te voeren. Zo moet de cluster controller autorisatie hebben om de virtuele machines van het cluster knooppunt te maken en te configureren. Cluster knooppunten moeten IP-adressen toewijzen of opnieuw toewijzen aan andere cluster knooppunten.

Er worden twee ingebouwde Azure-rollen gebruikt voor de virtuele avere-machines van vFXT:

* De cluster controller maakt gebruik van de ingebouwde rol [Inzender voor avere](../role-based-access-control/built-in-roles.md#avere-contributor).
* Cluster knooppunten gebruiken de ingebouwde rol [avere operator](../role-based-access-control/built-in-roles.md#avere-operator).

Als u toegangs rollen voor avere vFXT-onderdelen wilt aanpassen, moet u uw eigen rol definiëren en deze vervolgens toewijzen aan de virtuele machines op het moment dat ze worden gemaakt. U kunt de implementatie sjabloon niet gebruiken in azure Marketplace. Neem contact op met de klanten service van micro soft door een ticket in de Azure Portal te openen, zoals wordt beschreven in [hulp vragen bij uw systeem](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Volgende stappen

[Implementatie overzicht](avere-vfxt-deploy-overview.md) biedt een grote weer gave van de stappen die nodig zijn voor het maken van een avere-VFXT voor Azure-systeem en het voorbereiden van de gegevens.