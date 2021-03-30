---
title: AVERE vFXT voor Azure implementeren
description: Meer informatie over het gebruik van de implementatie wizard die beschikbaar is via Azure Marketplace voor het implementeren van een cluster met avere vFXT voor Azure.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 110fc658ffc3b72de91bbf1c9562f3efeecaf55b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342413"
---
# <a name="deploy-the-vfxt-cluster"></a>Het vFXT-cluster implementeren

Deze procedure begeleidt u bij het gebruik van de implementatie wizard die beschikbaar is via Azure Marketplace. De wizard implementeert automatisch het cluster met behulp van een Azure Resource Manager sjabloon. Nadat u de para meters in het formulier hebt ingevoerd en op **maken** hebt geklikt, worden deze taken automatisch door Azure voltooid:

* Maakt de cluster controller, een basis-VM die de software bevat die nodig is voor het implementeren en beheren van het cluster.
* Hiermee stelt u de resource groep en de infra structuur van het virtuele netwerk in, inclusief het maken van nieuwe elementen.
* Maakt de cluster knooppunt-Vm's en configureert deze als het avere-cluster.
* Hiermee wordt indien aangevraagd een nieuwe Azure Blob-container gemaakt en geconfigureerd als een kern Bestands server van het cluster.

Nadat u de instructies in dit document hebt gevolgd, hebt u een virtueel netwerk, een subnet, een cluster controller en een vFXT-cluster zoals in het volgende diagram wordt weer gegeven. Dit diagram toont de optionele Azure Blob core-bestands extensie, die een nieuwe Blob Storage-container (in een nieuw opslag account, niet weer gegeven) en een service-eind punt voor micro soft Storage in het subnet bevat.

![diagram met drie concentrische rechthoeken met avere-cluster onderdelen. De buitenste rechthoek heet ' resource groep ' en bevat een zeshoek met de naam ' Blob Storage (optional) '. De volgende rechthoek in heeft het label virtueel netwerk: 10.0.0.0/16 en bevat geen unieke onderdelen. De binnenste rechthoek heeft het label subnet: 10.0.0.0/24 en bevat een virtuele machine met de naam ' cluster controller ', een stapel van drie virtuele machines met het label ' vFXT nodes (vFXT cluster) ' en een zeshoek met het label ' service-eind punt '. Er is een pijl verbonden met het service-eind punt (dat zich binnen het subnet bevindt) en de Blob-opslag (die zich buiten het subnet en vnet bevindt in de resource groep). De pijl geeft de grenzen van het subnet en het virtuele netwerk door.](media/avere-vfxt-deployment.png)

Voordat u de sjabloon voor maken gebruikt, moet u ervoor zorgen dat u deze vereisten hebt aangepakt:  

* [Nieuw abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Machtigingen voor abonnements eigenaar](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Quota voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Opslag service-eind punt (indien nodig)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) : vereist voor implementaties die gebruikmaken van een bestaand virtueel netwerk en Blob-opslag maken

Lees voor meer informatie over de stappen voor het implementeren van clusters en het plannen van het [overzicht](avere-vfxt-deploy-overview.md)van [uw avere vFXT-systeem](avere-vfxt-deploy-plan.md) en-implementatie.

## <a name="create-the-avere-vfxt-for-azure"></a>De avere-vFXT voor Azure maken

Open de sjabloon maken in de Azure Portal door te zoeken naar avere en de optie ' avere vFXT for Azure ARM Temp late ' te selecteren.

![Browser venster met de Azure Portal met brood crumbs ' New > Marketplace > alles '. Op de pagina alles heeft het zoek veld de term ' avere ' en het tweede resultaat ' avere vFXT for Azure ARM Temp late ' wordt in rood beschreven om deze te markeren.](media/avere-vfxt-template-choose.png)

Nadat u de details op de pagina avere vFXT voor Azure ARM-sjabloon hebt gelezen, klikt u op de knop **maken** om te beginnen.

![Azure Marketplace met de eerste pagina van de implementatie sjabloon met](media/avere-vfxt-deploy-first.png)

De sjabloon is onderverdeeld in vier stappen: twee pagina's voor het verzamelen van gegevens, plus validatie-en bevestigings stappen.

* De pagina Eén verzamelt instellingen voor de virtuele machine van de cluster controller.
* Op de pagina twee worden para meters verzameld voor het maken van het cluster en aanvullende resources, zoals subnetten en opslag.
* Pagina drie bevat een overzicht van uw keuzes en valideert de configuratie.
* In pagina vier worden de software voorwaarden en-voor waarden uitgelegd en kunt u het proces voor het maken van het cluster starten.

## <a name="page-one-parameters---cluster-controller-information"></a>Pagina één para meters-cluster controller gegevens

De eerste pagina van de implementatie sjabloon is gericht op de cluster controller.

![Eerste pagina van de implementatie sjabloon](media/avere-vfxt-deploy-1.png)

Vul de volgende informatie in:

* **Naam van de cluster controller** : Stel de naam in voor de virtuele machine van de cluster controller.

* **Controller gebruikers naam** : Stel de hoofdmap in voor de cluster controller-VM.

* **Verificatie type** : Kies voor het maken van verbinding met de controller een wacht woord of een open bare SSH-sleutel verificatie. De open bare SSH-sleutel methode wordt aanbevolen; meer informatie [over het maken en gebruiken van SSH-sleutels](../virtual-machines/linux/ssh-from-windows.md) als u hulp nodig hebt.

* **Wacht woord** of **open bare SSH-sleutel** : afhankelijk van het verificatie type dat u hebt geselecteerd, moet u een open bare RSA-sleutel of een wacht woord in de volgende velden opgeven. Deze referentie wordt gebruikt met de gebruikers naam die u eerder hebt ingesteld.

* **Abonnement** : Selecteer het abonnement voor de avere vFXT.

* **Resource groep** : Selecteer een bestaande lege resource groep voor het avere vFXT-cluster of klik op nieuwe maken en voer een nieuwe naam voor de resource groep in.

* **Locatie** : Selecteer de Azure-locatie voor uw cluster en bronnen.

Klik op **OK** wanneer u klaar bent.

> [!NOTE]
> Als u wilt dat de cluster controller een openbaar IP-adres heeft, maakt u een nieuw virtueel netwerk voor het cluster in plaats van een bestaand netwerk te selecteren. Deze instelling is op pagina twee.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Pagina twee para meters-vFXT-cluster informatie

Op de tweede pagina van de implementatie sjabloon kunt u de cluster grootte, het type knoop punt, de cache grootte en de opslag parameters instellen, onder andere instellingen.

![Tweede pagina van de implementatie sjabloon](media/avere-vfxt-deploy-2.png)

* **Avere vFXT aantal cluster knooppunten** : Kies het aantal knoop punten in het cluster. Het minimum aantal is drie knoop punten en het maximum is twaalf.

* **Wacht woord voor cluster beheer** : het wacht woord voor cluster beheer maken. Dit wacht woord wordt met de gebruikers naam gebruikt ```admin``` om u aan te melden bij het configuratie scherm van het cluster, waar u het cluster kunt bewaken en de cluster instellingen configureren.

* **Avere vFXT-cluster naam** : Geef het cluster een unieke naam.

* **Grootte** : in deze sectie wordt het VM-type weer gegeven dat wordt gebruikt voor de cluster knooppunten. Hoewel er slechts één aanbevolen optie is, opent de koppeling **wijzigings grootte** een tabel met details over dit type exemplaar en een koppeling naar een prijs calculator.

* **Cache grootte per knoop punt** -de cluster cache wordt verspreid over de cluster knooppunten, zodat de totale cache grootte van uw avere vFXT-cluster deze grootte vermenigvuldigt met het aantal knoop punten.

  Aanbevolen configuratie: gebruik 4 TB per knoop punt voor Standard_E32s_v3 knooppunten.

* **Virtueel netwerk** : Definieer een nieuw virtueel netwerk voor het gebruik van het cluster of selecteer een bestaand netwerk dat voldoet aan de vereisten die worden beschreven in [uw avere VFXT-systeem plannen](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure).

  > [!NOTE]
  > Als u een nieuw virtueel netwerk maakt, heeft de cluster controller een openbaar IP-adres, zodat u toegang hebt tot het nieuwe particuliere netwerk. Als u een bestaand virtueel netwerk kiest, wordt de cluster controller geconfigureerd zonder een openbaar IP-adres.
  >
  > Een openbaar zichtbaar IP-adres op de cluster controller biedt eenvoudiger toegang tot het vFXT-cluster, maar maakt een klein beveiligings risico.
  >* Met een openbaar IP-adres op de cluster controller kunt u deze gebruiken als een Jump-host om verbinding te maken met het avere vFXT-cluster van buiten het particuliere subnet.
  >* Als u geen openbaar IP-adres op de controller hebt, hebt u een andere Jump host, een VPN-verbinding of een ExpressRoute nodig om toegang te krijgen tot het cluster. Gebruik bijvoorbeeld een bestaand virtueel netwerk waarop al een VPN-verbinding is geconfigureerd.
  >* Als u een controller met een openbaar IP-adres maakt, moet u de controller-VM beveiligen met een netwerk beveiligings groep. De avere vFXT voor Azure-implementatie maakt standaard een netwerk beveiligings groep waarmee de toegang tot alleen poort 22 wordt beperkt voor controllers met open bare IP-adressen. U kunt het systeem verder beveiligen door de toegang tot uw bereik van IP-bron adressen te vergren delen, dat wil zeggen, alleen verbindingen toestaan van computers die u wilt gebruiken voor toegang tot het cluster.

  Er wordt ook een nieuw virtueel netwerk geconfigureerd met een opslag service-eind punt voor Azure Blob-opslag en met netwerk toegangs beheer vergrendeld zodat alleen IP-adressen van het subnet van het cluster worden toegestaan.

* **Subnet** : Kies een subnet of maak een nieuwe.

* **Blob Storage maken en gebruiken** : Kies **waar** om een nieuwe Azure Blob-container te maken en configureer deze als back-end-opslag voor het nieuwe avere vFXT-cluster. Met deze optie maakt u ook een nieuw opslag account in de resource groep van het cluster en maakt u een micro soft Storage service-eind punt in het subnet van het cluster.
  
  Als u een bestaand virtueel netwerk opgeeft, moet het een opslag service-eind punt hebben voordat u het cluster maakt. (Zie [uw avere vFXT-systeem plannen](avere-vfxt-deploy-plan.md)voor meer informatie.)

  Stel dit veld in op **Onwaar** als u geen nieuwe container wilt maken. In dit geval moet u de opslag koppelen en configureren nadat u het cluster hebt gemaakt. Lees [opslag configureren](avere-vfxt-add-storage.md) voor instructies.

* **(Nieuw) opslag account** : als u een nieuwe Azure Blob-container maakt, voert u een naam in voor het nieuwe opslag account.

## <a name="validation-and-purchase"></a>Validatie en aankoop

Pagina drie geeft een overzicht van de configuratie en valideert de para meters. Nadat de validatie is voltooid, controleert u de samen vatting en klikt u op de knop **OK** .

> [!TIP]
> U kunt de instellingen voor het maken van het cluster opslaan door te klikken op de koppeling **sjabloon en para meters downloaden** naast de knop **OK** . Deze informatie kan nuttig zijn als u later een vergelijkbaar cluster moet maken, bijvoorbeeld om een vervangend cluster in een nood herstel scenario te maken. (Lees de [richt lijnen voor herstel na nood gevallen](disaster-recovery.md) voor meer informatie.)

![Derde pagina van de implementatie sjabloon-validatie](media/avere-vfxt-deploy-3.png)

Op pagina 4 vindt u de gebruiks voorwaarden en koppelingen naar informatie over privacy en prijzen.

Voer eventuele ontbrekende contact gegevens in en klik op de knop **maken** om de voor waarden te accepteren en de avere VFXT voor Azure-cluster te maken.

![Vierde pagina van de implementatie sjabloon-voor waarden, knop maken](media/avere-vfxt-deploy-4.png)

De cluster implementatie neemt 15-20 minuten in beslag.

## <a name="gather-template-output"></a>Sjabloon uitvoer verzamelen

Wanneer de avere vFXT-sjabloon het maken van het cluster voltooit, wordt er een belang rijke informatie over het nieuwe cluster uitgevoerd.

> [!TIP]
> Zorg ervoor dat u het **IP-adres** van het beheer kopieert vanuit de sjabloon uitvoer. U hebt dit adres nodig voor het beheren van het cluster.

De informatie zoeken:

1. Ga naar de resource groep voor de cluster controller.

1. Klik aan de linkerkant op **implementaties** en vervolgens op **micro soft-avere. vfxt-Temp late**.

   ![De portal pagina van de resource groep met implementaties geselecteerd aan de linkerkant en micro soft-avere. vfxt: de sjabloon die wordt weer gegeven in een tabel onder implementatie naam](media/avere-vfxt-outputs-deployments.png)

1. Klik aan de linkerkant op **uitvoer**. Kopieer de waarden in elk van de velden.

   ![uitvoer pagina met SSHSTRING, RESOURCE_GROUP, locatie, NETWORK_RESOURCE_GROUP, netwerk, SUBNET, SUBNET_ID, VSERVER_IPs en MGMT_IP waarden in velden rechts van de labels](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Volgende stappen

Nu het cluster actief is en u weet wat het beheer-IP-adres is, [maakt u verbinding met het hulp programma voor cluster configuratie](avere-vfxt-cluster-gui.md).

Met de configuratie-interface kunt u uw cluster aanpassen, met inbegrip van de volgende instellings taken:

* [Ondersteuning inschakelen](avere-vfxt-enable-support.md)
* [Opslag toevoegen](avere-vfxt-add-storage.md) (indien nodig)