---
title: Gedetailleerde SSH-probleem oplossing voor een Azure-VM | Microsoft Docs
description: Meer gedetailleerde stappen voor het oplossen van problemen met een virtuele machine van Azure
keywords: SSH-verbinding geweigerd, SSH-fout, Azure SSH, SSH-verbinding is mislukt
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c117f9697299b94a54d9184093e65c56822b8bd2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002626"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Gedetailleerde stappen om SSH-problemen op te lossen bij verbindingsproblemen van een Linux-VM in Azure
Er zijn veel mogelijke redenen waarom de SSH-client de SSH-service mogelijk niet kan bereiken op de virtuele machine. Als u de meer [algemene stappen](troubleshoot-ssh-connection.md)voor het oplossen van SSH-problemen hebt gevolgd, moet u het verbindings probleem verder oplossen. In dit artikel vindt u gedetailleerde stappen voor probleem oplossing om te bepalen waar de SSH-verbinding is mislukt en hoe u deze kunt oplossen.

## <a name="take-preliminary-steps"></a>Voorbereidende stappen uitvoeren
In het volgende diagram ziet u de onderdelen die betrokken zijn.

![Diagram waarin de onderdelen van de SSH-service worden weer gegeven](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

De volgende stappen helpen u bij het isoleren van de oorzaak van de fout en het afnemen van oplossingen en oplossingen.

1. Controleer de status van de virtuele machine in de portal.
   Selecteer **virtuele machines** VM-naam in het [Azure Portal](https://portal.azure.com)  >  *VM name*.

   Het status venster voor de virtuele machine moet worden **uitgevoerd** weer gegeven. Schuif omlaag om recente activiteiten voor compute-, opslag-en netwerk bronnen weer te geven.

2. Selecteer **instellingen** om eind punten, IP-adressen, netwerk beveiligings groepen en andere instellingen te onderzoeken.

   De virtuele machine moet een eind punt hebben dat is gedefinieerd voor SSH-verkeer dat u kunt weer geven in **eind punten** of **[netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md)**. Eind punten in Vm's die zijn gemaakt met behulp van Resource Manager, worden opgeslagen in een netwerk beveiligings groep. Controleer of de regels zijn toegepast op de netwerk beveiligings groep en waarnaar wordt verwezen in het subnet.

Controleer de geconfigureerde eind punten om de netwerk verbinding te controleren en te controleren of u verbinding kunt maken met de virtuele machine via een ander protocol, zoals HTTP of een andere service.

Probeer na deze stappen de SSH-verbinding opnieuw uit te voeren.

## <a name="find-the-source-of-the-issue"></a>De bron van het probleem zoeken
De SSH-client op uw computer kan mogelijk geen verbinding maken met de SSH-service op de Azure-VM vanwege problemen of onjuiste configuratie op de volgende gebieden:

* [SSH-client computer](#source-1-ssh-client-computer)
* [Apparaat voor organisatie rand](#source-2-organization-edge-device)
* [Cloud service-eind punt en toegangs beheer lijst (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netwerkbeveiligingsgroepen](#source-4-network-security-groups)
* [Azure VM op basis van Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Bron 1: SSH-client computer
Als u wilt voor komen dat uw computer als bron van de fout optreedt, controleert u of deze SSH-verbindingen kan maken met een andere on-premises, Linux-computer.

![Diagram dat de SSH-client computer onderdelen markeert](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Als de verbinding mislukt, controleert u op de volgende problemen op uw computer:

* Een lokale firewall instelling die binnenkomend of uitgaand SSH-verkeer blokkeert (TCP 22)
* Lokaal geïnstalleerde client proxy software die SSH-verbindingen verhindert
* Lokaal geïnstalleerde netwerk bewakings software waardoor SSH-verbindingen worden voor komen
* Andere typen beveiligings software die verkeer bewaken of specifieke typen verkeer toestaan of weigeren

Als een van deze voor waarden van toepassing is, schakelt u de software tijdelijk uit en probeert u een SSH-verbinding met een on-premises computer om te achterhalen waarom de verbinding op uw computer wordt geblokkeerd. Werk vervolgens met de netwerk beheerder om de software-instellingen te corrigeren om SSH-verbindingen toe te staan.

Als u verificatie via certificaat gebruikt, controleert u of u deze machtigingen hebt voor de map. ssh in uw basismap:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/ \* . pub
* Chmod 600 ~/.ssh/id_rsa (of andere bestanden waarin uw persoonlijke sleutels zijn opgeslagen)
* Chmod 644 ~/.ssh/known_hosts (bevat hosts die u via SSH hebt verbonden)

## <a name="source-2-organization-edge-device"></a>Bron 2: apparaat voor organisatie rand
Als u wilt voor komen dat uw organisatie apparaat als bron van de fout wordt veroorzaakt, controleert u of een computer die rechtstreeks is verbonden met internet, SSH-verbindingen kan maken met uw Azure-VM. Als u de virtuele machine via een site-naar-site-VPN of een Azure ExpressRoute-verbinding opent, gaat u naar [bron 4: netwerk beveiligings groepen](#nsg).

![Diagram voor het markeren van het organisatie apparaat](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Als u geen computer hebt die rechtstreeks met internet is verbonden, maakt u een nieuwe Azure-VM in een eigen resource groep of Cloud service en gebruikt u deze nieuwe VM. Zie [een virtuele machine met Linux maken in azure](../linux/quick-create-cli.md)voor meer informatie. Verwijder de resource groep, de VM en de Cloud service wanneer u klaar bent met het testen.

Als u een SSH-verbinding kunt maken met een computer die rechtstreeks is verbonden met internet, controleert u het apparaat van uw organisatie voor het volgende:

* Een interne firewall die SSH-verkeer met Internet blokkeert
* Een proxy server die SSH-verbindingen verhindert
* Indringings detectie of netwerk bewakings software die wordt uitgevoerd op apparaten in uw Edge-netwerk die SSH-verbindingen verhinderen

Werk samen met uw netwerk beheerder om de instellingen van uw organisatie rand apparaten te corrigeren om SSH-verkeer met Internet toe te staan.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Bron 3: eind punt van Cloud service en ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Deze bron is alleen van toepassing op virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model. Ga door naar [bron 4: netwerk beveiligings groepen](#nsg)voor virtuele machines die zijn gemaakt met behulp van Resource Manager.

Als u het eind punt van de Cloud service en ACL als bron van de fout wilt elimineren, controleert u of een andere virtuele machine van Azure in hetzelfde virtueel netwerk verbinding kan maken via SSH.

![Diagram dat het Cloud service-eind punt en ACL markeert](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Als u nog geen VM in hetzelfde virtuele netwerk hebt, kunt u er eenvoudig een maken. Zie [een virtuele Linux-machine maken in azure met behulp van de CLI](../linux/quick-create-cli.md)voor meer informatie. Verwijder de extra virtuele machine wanneer u klaar bent met het testen.

Als u een SSH-verbinding met een VM in hetzelfde virtuele netwerk kunt maken, controleert u de volgende gebieden:

* **De eindpunt configuratie voor SSH-verkeer op de doel-VM.** De particuliere TCP-poort van het eind punt moet overeenkomen met de TCP-poort waarop de SSH-service op de VM luistert. (De standaard poort is 22). Controleer het SSH TCP-poort nummer in de Azure portal door **virtuele machines**  >  *VM naam*  >  **instellingen**  >  **eind punten** te selecteren.
* **De ACL voor het SSH-verkeer eindpunt op de virtuele doel machine.** U kunt met een ACL het toegestane of geweigerde binnenkomende verkeer van Internet opgeven, op basis van het bron-IP-adres. Onjuist geconfigureerde Acl's kunnen binnenkomend SSH-verkeer naar het eind punt verhinderen. Controleer uw Acl's om ervoor te zorgen dat binnenkomend verkeer van de open bare IP-adressen van uw proxy of een andere Edge-Server wordt toegestaan. Zie [about Network Access Control Lists (acl's)](/previous-versions/azure/virtual-network/virtual-networks-acl)voor meer informatie.

Als u het eind punt als een bron van het probleem wilt elimineren, verwijdert u het huidige eind punt, maakt u een ander eind punt en geeft u de SSH-naam op (TCP-poort 22 voor het open bare en particuliere poort nummer). Zie [eind punten instellen op een virtuele machine in azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json)voor meer informatie.

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Bron 4: netwerk beveiligings groepen
Met netwerk beveiligings groepen kunt u meer nauw keurige controle hebben over toegestaan binnenkomend en uitgaand verkeer. U kunt regels maken die subnetten en Cloud Services omvatten in een virtueel Azure-netwerk. Controleer de regels voor de netwerk beveiligings groep om ervoor te zorgen dat SSH-verkeer van en naar Internet wordt toegestaan.
Zie [over netwerk beveiligings groepen](../../virtual-network/network-security-groups-overview.md)voor meer informatie.

U kunt ook IP-verificatie gebruiken om de NSG-configuratie te valideren. Zie overzicht van Azure- [netwerk bewaking](../../network-watcher/network-watcher-monitoring-overview.md)voor meer informatie. 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Bron 5: Azure virtual machine op basis van Linux
De laatste bron van mogelijke problemen is de virtuele Azure-machine zelf.

![Diagram voor het markeren van Azure virtual machine op basis van Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Als u dit nog niet hebt gedaan, volgt u de instructies [voor het opnieuw instellen van een op Linux gebaseerde virtuele machines op basis van een wacht woord](./reset-password.md).

Probeer opnieuw verbinding te maken vanaf uw computer. Als de service nog steeds mislukt, zijn dit enkele van de mogelijke problemen:

* De SSH-service wordt niet uitgevoerd op de virtuele doel machine.
* De SSH-service luistert niet op TCP-poort 22. Als u wilt testen, installeert u een Telnet-client op uw lokale computer en voert u ' Telnet *cloudServiceName*. cloudapp.net 22 ' uit. Met deze stap wordt bepaald of de virtuele machine binnenkomende en uitgaande communicatie met het SSH-eind punt toestaat.
* De lokale firewall op de virtuele doel machine bevat regels die inkomend of uitgaand SSH-verkeer verhinderen.
* De indringings detectie of de netwerk bewakings software die wordt uitgevoerd op de virtuele machine van Azure, verhindert SSH-verbindingen.

## <a name="additional-resources"></a>Aanvullende bronnen
Zie [problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op een virtuele machine van Azure](./troubleshoot-app-connection.md) voor meer informatie over het oplossen van toepassings toegang