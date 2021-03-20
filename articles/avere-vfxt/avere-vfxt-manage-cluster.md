---
title: Het avere vFXT-cluster beheren-Azure
description: 'AVERE-cluster beheren: knoop punten toevoegen of verwijderen, het vFXT-cluster opnieuw opstarten, stoppen of vernietigen'
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85505491"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Het Avere vFXT-cluster beheren

Op een bepaald moment in de levens cyclus van uw avere vFXT voor Azure-cluster moet u mogelijk cluster knooppunten toevoegen of het cluster starten of opnieuw opstarten. Wanneer het project is voltooid, moet u weten hoe u het cluster stopt en het permanent verwijdert.

In dit artikel wordt uitgelegd hoe u cluster knooppunten en andere basis cluster bewerkingen toevoegt of verwijdert. Als u cluster instellingen wilt wijzigen of als u het werk wilt controleren, gebruikt u het [avere-configuratie scherm](avere-vfxt-cluster-gui.md).

Afhankelijk van de beheer taak moet u mogelijk een van drie verschillende hulpprogram ma's gebruiken: avere configuratie scherm, het vfxt.py-opdracht regel script en de Azure Portal.

Deze tabel bevat een overzicht van de hulpprogram ma's die voor elke taak kunnen worden gebruikt.

| Bewerking | Configuratie scherm avere | vfxt.py  | Azure Portal |
| --- | --- | --- | --- |
| Clusterknooppunten toevoegen | nee | ja | nee |
| Cluster knooppunten verwijderen | ja | nee | nee |
| Een cluster knooppunt stoppen | Ja (kan ook services opnieuw starten of opnieuw opstarten) | nee | het uitschakelen van een knooppunt-VM vanuit de portal wordt geïnterpreteerd als een knooppunt fout |
| Een gestopt knoop punt starten | nee | nee | ja |
| Eén cluster knooppunt vernietigen | nee | nee | ja |
| Het cluster opnieuw opstarten |  |  |  |
| Het cluster veilig afsluiten of stoppen | ja | ja | nee |
| Het cluster vernietigen  | nee | ja | Ja, maar gegevens integriteit is niet gegarandeerd |

Hieronder vindt u gedetailleerde instructies voor elk hulp programma.

## <a name="about-stopped-instances-in-azure"></a>Over gestopte instanties in azure

Wanneer u een virtuele machine van Azure afsluit of stopt, worden er geen reken kosten meer in rekening gebracht, maar u moet wel betalen voor de opslag. Als u een vFXT-knoop punt of het hele vFXT-cluster afsluit en u niet van plan bent om het te starten, moet u de Azure Portal gebruiken om de gerelateerde Vm's te verwijderen.

In de Azure Portal, een *gestopt* knoop punt (dat opnieuw kan worden gestart), wordt de status **gestopt** weer gegeven in de Azure Portal. Een *verwijderd* knoop punt geeft aan dat de status is **gestopt (toewijzing** ongedaan gemaakt) en dat er geen reken-of opslag kosten meer in rekening worden gebracht.

Voordat u de virtuele machine verwijdert, moet u ervoor zorgen dat alle gewijzigde gegevens vanuit de cache naar een back-end-opslag zijn geschreven met behulp van het configuratie scherm avere of de vfxt.py opties om het cluster te stoppen of af te sluiten.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Het cluster beheren met het configuratie scherm avere

Het configuratie scherm avere kan worden gebruikt voor de volgende taken:

* Afzonderlijke knoop punten stoppen of opnieuw opstarten
* Een knooppunt uit het cluster verwijderen
* Het hele cluster stoppen of opnieuw opstarten

In het configuratie scherm van AVERE wordt een prioriteit gegeven voor de gegevens integriteit, zodat de gewijzigde gegevens worden geschreven naar de back-end-opslag voordat een mogelijk destructieve bewerking wordt uitgevoerd. Dit maakt het een veiliger optie dan de Azure Portal.

Open het configuratie scherm van AVERE via een webbrowser. Volg de instructies in [toegang tot het vFXT-cluster](avere-vfxt-cluster-gui.md) als u hulp nodig hebt.

### <a name="manage-nodes-with-avere-control-panel"></a>Knoop punten beheren met het configuratie scherm avere

De pagina instellingen van **FXT-knoop punten** bevat besturings elementen voor het beheren van afzonderlijke knoop punten.

Als u een knoop punt wilt afsluiten, opnieuw wilt opstarten of verwijderen, gaat u naar het knoop punt in de lijst op de pagina **FXT-knoop punten** en klikt u op de desbetreffende knop in de kolom **acties** .

> [!NOTE]
> IP-adressen kunnen worden verplaatst tussen cluster knooppunten wanneer het aantal actieve knoop punten verandert.

Lees de [FXT-knoop punten van cluster >](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) in de avere-hand leiding voor cluster instellingen voor meer informatie.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Het cluster stoppen of opnieuw opstarten met het configuratie scherm van AVERE

De pagina instellingen voor **systeem onderhoud** bevat opdrachten voor het opnieuw starten van Cluster Services, het opnieuw opstarten van het cluster of het veilig uitschakelen van het cluster. Lees [beheer > systeem onderhoud](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (in de hand leiding voor avere-cluster instellingen) voor meer informatie.

Wanneer een cluster wordt afgesloten, worden status berichten naar het tabblad **dash board** gepost. Na enkele ogen blikken reageren de berichten niet meer en uiteindelijk wordt de avere-configuratie paneel sessie niet meer reageert, wat betekent dat het cluster is uitgeschakeld.

## <a name="manage-the-cluster-with-vfxtpy"></a>Het cluster beheren met vfxt.py

vfxt.py is een opdracht regel programma voor het maken en beheren van clusters.

vfxt.py is vooraf geïnstalleerd op de cluster controller-VM. Als u het op een ander systeem wilt installeren, raadpleegt u de documentatie op <https://github.com/Azure/AvereSDK> .

Het vfxt.py-script kan worden gebruikt voor deze cluster beheer taken:

* Nieuwe knoop punten toevoegen aan een cluster
* Een cluster stoppen of starten  
* Een cluster vernietigen

Net als avere configuratie scherm, proberen vfxt.py bewerkingen ervoor te zorgen dat gewijzigde gegevens permanent worden opgeslagen op de back-end-opslag voordat het cluster of het knoop punt wordt afgesloten of vernietigd. Dit maakt het een veiliger optie dan de Azure Portal.

Er is een volledige vfxt.py-gebruiks handleiding beschikbaar op GitHub: [Cloud cluster Management met vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Cluster knooppunten toevoegen met vfxt.py

Een voor beeld van een opdracht script voor het toevoegen van cluster knooppunten is opgenomen op de cluster controller. Zoek ``./add-nodes`` op de controller en open deze in een editor om deze aan te passen aan uw cluster informatie.

Het cluster moet worden uitgevoerd om deze opdracht te kunnen gebruiken.

Geef de volgende waarden op:

* De naam van de resource groep voor het cluster en ook voor netwerk-en opslag bronnen als deze zich niet in dezelfde resource groep bevinden als het cluster
* Cluster locatie
* Cluster netwerk en subnet
* Access-rol cluster knooppunt (gebruik de ingebouwde rol [avere operator](../role-based-access-control/built-in-roles.md#avere-operator))
* IP-adres en beheerders wachtwoord voor cluster beheer
* Aantal toe te voegen knoop punten (1, 2 of 3)
* Type knooppunt exemplaar en waarden voor cache grootte

Als u het prototype niet gebruikt, moet u een opdracht als volgt maken, inclusief alle hierboven beschreven informatie.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Lees voor meer informatie [knoop punten toevoegen aan een cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) in de hand leiding voor vfxt.py-gebruik.

### <a name="stop-a-cluster-with-vfxtpy"></a>Een cluster stoppen met vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Een gestopt cluster starten met vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Omdat het cluster is gestopt, moet u exemplaar-id's door geven om de cluster knooppunten op te geven. Lees het [cluster dat u wilt wijzigen](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) in de hand leiding voor vfxt.py-gebruik voor meer informatie.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Een cluster met vfxt.py vernietigen

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

``--quick-destroy``U kunt de optie gebruiken als u geen gewijzigde gegevens van de cluster cache wilt opslaan.

Lees de [hand leiding voor vfxt.py-gebruik](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) voor aanvullende informatie.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Cluster-Vm's beheren vanuit de Azure Portal

De Azure Portal kan worden gebruikt om cluster-Vm's afzonderlijk te vernietigen, maar de gegevens integriteit wordt niet gegarandeerd als het cluster eerst niet volledig wordt afgesloten.

De Azure Portal kan worden gebruikt voor deze cluster beheer taken:

* Een gestopt vFXT-knoop punt starten
* Een afzonderlijk vFXT-knoop punt stoppen (het cluster interpreteert dit als een knooppunt fout)
* Een vFXT-cluster vernietigen *als* u niet meer nodig hebt om ervoor te zorgen dat gewijzigde gegevens in de cluster cache worden geschreven naar de kern bestand
* VFXT-knoop punten en andere cluster bronnen permanent verwijderen nadat ze veilig zijn afgesloten

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>VFXT-exemplaren van de Azure Portal opnieuw starten

Als u een gestopt knoop punt opnieuw moet opstarten, moet u de Azure Portal gebruiken. Selecteer **virtuele machines** in het menu links en klik vervolgens op de naam van de virtuele machine in de lijst om de pagina overzicht te openen.

Klik boven aan de pagina overzicht op de knop **Start** om de virtuele machine opnieuw te activeren.

![Azure Portal scherm met de optie om een gestopt VM te starten](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Cluster knooppunten verwijderen

Als u één knoop punt uit het vFXT-cluster wilt verwijderen maar de rest van het cluster wilt houden, moet u eerst [het knoop punt verwijderen uit het cluster](#manage-nodes-with-avere-control-panel) met behulp van het configuratie scherm avere.

> [!CAUTION]
> Als u een knoop punt verwijdert zonder het eerst te verwijderen uit het vFXT-cluster, kunnen gegevens verloren gaan.

Als u een of meer instanties die als vFXT-knoop punt worden gebruikt permanent wilt vernietigen, gebruikt u de Azure Portal.
Selecteer **virtuele machines** in het menu links en klik vervolgens op de naam van de virtuele machine in de lijst om de pagina overzicht te openen.

Klik op de knop **verwijderen** boven aan de pagina overzicht om de virtuele machine permanent te vernietigen.

U kunt deze methode gebruiken om cluster knooppunten permanent te verwijderen nadat ze veilig zijn afgesloten.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Het cluster uit de Azure Portal vernietigen

> [!NOTE]
> Als u wilt dat eventuele resterende client wijzigingen in de cache naar back-end-opslag worden geschreven, gebruikt u de `--destroy` optie vfxt.py of gebruikt u het configuratie scherm van AVERE om het cluster op de juiste wijze af te sluiten voordat u de knooppunt exemplaren in de Azure Portal verwijdert.

U kunt knooppunt instanties permanent vernietigen door ze te verwijderen in de Azure Portal. U kunt ze een voor een verwijderen zoals hierboven wordt beschreven, of u kunt de pagina **virtual machines** gebruiken om alle cluster-vm's te zoeken. Selecteer deze met de selectie vakjes en klik op de knop **verwijderen** om ze allemaal in één actie te verwijderen.

![Lijst met virtuele machines in de portal, gefilterd op de term ' cluster ', waarbij drie van de vier ingeschakelde en gemarkeerde](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Extra cluster bronnen verwijderen uit de Azure Portal

Als u aanvullende bronnen voor het vFXT-cluster hebt gemaakt, wilt u deze mogelijk verwijderen als onderdeel van het afbreken van het cluster. Vernietig geen elementen die gegevens bevatten die u nodig hebt, of items die worden gedeeld met andere projecten.

Naast het verwijderen van de cluster knooppunten, kunt u deze onderdelen verwijderen:

* De cluster controller-VM
* Gegevens schijven die zijn gekoppeld aan cluster knooppunten
* Netwerk interfaces en open bare Ip's die zijn gekoppeld aan cluster onderdelen
* Virtuele netwerken
* Opslag containers en opslag accounts (**alleen** als ze geen belang rijke gegevens bevatten)
* Beschikbaarheidsset

![Azure Portal lijst ' alle resources ' toont de resources die zijn gemaakt voor een test cluster](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>De resource groep van een cluster verwijderen uit het Azure Portal

Als u een resource groep speciaal hebt gemaakt voor het maken van het cluster, kunt u alle gerelateerde resources voor het cluster vernietigen door de resource groep te vernietigen.

> [!Caution]
> Vernietig alleen de resource groep als u zeker weet dat niets in de groep is opgeslagen. Zorg er bijvoorbeeld voor dat u de benodigde gegevens hebt verplaatst van opslag containers in de resource groep.  

Als u een resource groep wilt verwijderen, klikt u op **resource groepen** in het menu links van de portal en filtert u de lijst met resource groepen om het account te zoeken dat u hebt gemaakt voor het vFXT-cluster. Selecteer de resource groep en klik op de drie puntjes aan de rechter kant van het paneel. Kies **Resourcegroep verwijderen**. U wordt gevraagd om het verwijderen te bevestigen. Dit is onomkeerbaar.

![Resource groep met de actie ' resource groep verwijderen '](media/avere-vfxt-delete-resource-group.png)
