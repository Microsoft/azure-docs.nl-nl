---
title: Problemen met de NFS-opslag doelen van Azure HPC cache oplossen
description: Tips voor het voor komen en oplossen van configuratie fouten en andere problemen die kunnen leiden tot fouten bij het maken van een NFS-opslag doel
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: bb17918774d23dbeb2747fa55eefc4956812e254
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775694"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>Problemen met de NAS-configuratie en NFS-opslag doelen oplossen

Dit artikel bevat oplossingen voor enkele veelvoorkomende configuratie fouten en andere problemen die kunnen voor komen dat Azure HPC-cache een NFS-opslag systeem kan toevoegen als opslag doel.

Dit artikel bevat informatie over het controleren van poorten en het inschakelen van toegang tot het hoofd niveau van een NAS-systeem. Het bevat ook gedetailleerde informatie over minder veelvoorkomende problemen die ertoe kunnen leiden dat het maken van NFS-opslag doel mislukt.

> [!TIP]
> Lees de [vereisten voor NFS-opslag doelen](hpc-cache-prerequisites.md#nfs-storage-requirements)voordat u deze hand leiding gebruikt.

Als de oplossing voor uw probleem hier niet is opgenomen, [opent u een ondersteunings ticket](hpc-cache-support-ticket.md) zodat micro soft-service en-ondersteuning met u kunnen samen werken om het probleem te onderzoeken en op te lossen.

## <a name="check-port-settings"></a>Poort instellingen controleren

Azure HPC-cache vereist lees-/schrijftoegang tot verschillende UDP/TCP-poorten op het back-end NAS-opslag systeem. Zorg ervoor dat deze poorten toegankelijk zijn op het NAS-systeem en dat verkeer wordt toegestaan aan deze poorten via alle firewalls tussen het opslag systeem en het cache-subnet. U moet mogelijk met de firewall en netwerk beheerder voor uw Data Center werken om deze configuratie te controleren.

De poorten zijn verschillend voor opslag systemen van verschillende leveranciers. Controleer daarom de vereisten van uw systeem bij het instellen van een opslag doel.

In het algemeen moet de cache toegang hebben tot deze poorten:

| Protocol | Poort  | Service  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | gekoppeld   |
| TCP/UDP  | 4047  | status   |

Gebruik de volgende opdracht voor meer informatie over de specifieke poorten die nodig zijn voor uw systeem ``rpcinfo`` . Met deze opdracht hieronder worden de poorten weer gegeven en worden de relevante resultaten in een tabel opgemaakt. (Gebruik het IP-adres van uw systeem in plaats van de *<storage_IP>* term.)

U kunt deze opdracht geven vanuit elke Linux-client waarop de NFS-infra structuur is geïnstalleerd. Als u een-client in het cluster-subnet gebruikt, kunt u hiermee ook de connectiviteit tussen het subnet en het opslag systeem controleren.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

Zorg ervoor dat alle poorten die door de query zijn geretourneerd, ``rpcinfo`` onbeperkt verkeer van het subnet van de Azure HPC-cache toestaan.

Controleer deze instellingen zowel op de NAS zelf als op alle firewalls tussen het opslag systeem en het cache-subnet.

## <a name="check-root-access"></a>Toegang tot hoofdmap controleren

De Azure HPC-cache moet toegang hebben tot de export van uw opslag systeem om het opslag doel te maken. Met name de exports worden gekoppeld als gebruiker-ID 0.

Verschillende opslag systemen gebruiken verschillende methoden om deze toegang in te scha kelen:

* Linux-servers voegen doorgaans ``no_root_squash`` toe aan het geëxporteerde pad in ``/etc/exports`` .
* NetApp-en EMC-systemen bepalen doorgaans de toegang tot de export regels die zijn gekoppeld aan specifieke IP-adressen of netwerken.

Als u regels voor exporteren gebruikt, moet u er rekening mee houden dat de cache meerdere verschillende IP-adressen van het cache-subnet kan gebruiken. Toegang vanaf het volledige bereik van mogelijke subnet-IP-adressen toestaan.

> [!NOTE]
> Hoewel de cache hoofd toegang heeft tot het back-end-opslag systeem, kunt u de toegang beperken tot clients die verbinding maken via de cache. Lees de [client toegang](access-policies.md#root-squash) voor meer informatie.

Werk samen met de leverancier van uw NAS-opslag om het juiste toegangs niveau voor de cache in te scha kelen.

### <a name="allow-root-access-on-directory-paths"></a>Toegang tot hoofdmap toestaan voor Directory paden
<!-- linked in prereqs article -->

Voor NAS-systemen die hiërarchische directory's exporteren, moet Azure HPC-cache toegang hebben tot de hoofdmap voor elk export niveau.

Een systeem kan bijvoorbeeld drie exports als volgt weer geven:

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

De export ``/ifs/accounting/payroll`` is een onderliggend element van ``/ifs/accounting`` en ``/ifs/accounting`` is zelf een onderliggend element van ``/ifs`` .

Als u het ``payroll`` exporteren als een HPC-cache-opslag doel toevoegt, koppelt de cache de map voor de ``/ifs/`` salaris administratie werkelijk. Azure HPC-cache heeft toegang tot de hoofdmap nodig om toegang te kunnen ``/ifs`` krijgen tot het ``/ifs/accounting/payroll`` exporteren.

Deze vereiste houdt verband met de manier waarop de cache bestanden indexeert en conflicten met bestanden voor komt, met behulp van bestands ingangen die het opslag systeem biedt.

Een NAS-systeem met hiërarchische uitvoer kan verschillende bestands ingangen geven voor hetzelfde bestand als het bestand wordt opgehaald uit verschillende exports. Een client kan bijvoorbeeld ``/ifs/accounting`` het bestand koppelen en openen ``payroll/2011.txt`` . Een andere client koppelt ``/ifs/accounting/payroll`` en opent het bestand ``2011.txt`` . Afhankelijk van hoe het opslag systeem bestands ingangen toewijst, kunnen deze twee clients hetzelfde bestand met verschillende bestands ingangen ontvangen (één voor ``<mount2>/payroll/2011.txt`` en een voor ``<mount3>/2011.txt`` ).

Het back-end-opslag systeem houdt interne aliassen voor bestands ingangen bij, maar de Azure HPC-cache kan niet bepalen welke bestands ingangen in de index naar hetzelfde item verwijzen. Het is dus mogelijk dat de cache verschillende schrijf bewerkingen voor hetzelfde bestand kan hebben en dat de wijzigingen onjuist worden toegepast omdat deze niet overeenkomen met het bestand.

Als u wilt voor komen dat er conflicten ontstaan met bestanden in meerdere uitvoer bewerkingen, koppelt Azure HPC cache automatisch de meest recente uitvoer in het pad ( ``/ifs`` in het voor beeld) en gebruikt de bestands ingang die van de export is opgegeven. Als meerdere exports hetzelfde basispad gebruiken, moet Azure HPC-cache toegang hebben tot het pad naar de hoofdmap.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>Beperkingen voor VPN-pakket grootte aanpassen
<!-- link in prereqs article and configuration article -->

Als u een VPN tussen de cache en uw NAS-apparaat hebt, kan het VPN de volledige 1500-bytes Ethernet-pakketten blok keren. Dit probleem kan optreden als grote uitwisselingen tussen de NAS en het Azure HPC-cache-exemplaar niet zijn voltooid, maar kleinere updates werken zoals verwacht.

Er is geen eenvoudige manier om te bepalen of uw systeem dit probleem heeft tenzij u de details van uw VPN-configuratie kent. Hier volgen enkele methoden die u kunnen helpen bij het controleren op dit probleem.

* Gebruik pakket-sniffs aan beide zijden van de VPN om te detecteren welke pakketten zijn overgedragen.
* Als uw VPN ping-opdrachten toestaat, kunt u de verzen ding van een pakket op volledige grootte testen.

  Voer een ping-opdracht via de VPN-verbinding met de NAS uit met deze opties. (Gebruik het IP-adres van uw opslag systeem in plaats van de *<storage_IP>* waarde.)

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  Dit zijn de opties in de opdracht:

  * ``-M do`` -Niet fragmenteren
  * ``-c 1`` -Slechts één pakket verzenden
  * ``-s 1472`` -Stel de grootte van de payload in op 1472 bytes. Dit is de maximale payload-waarde voor een 1500-byte-pakket na de accounting voor de Ethernet-overhead.

  Een geslaagd antwoord ziet er zo uit:

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  Als de ping mislukt met 1472 bytes, is er waarschijnlijk een probleem met de pakket grootte.

Om het probleem op te lossen, moet u de MSS-verbinding op het VPN configureren om het externe systeem de maximale frame grootte correct te laten detecteren. Lees de [documentatie van VPN gateway IPSec/IKE-para meters](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) voor meer informatie.

In sommige gevallen kunt u de MTU-instelling voor de Azure HPC-cache wijzigen in 1400. Als u echter de MTU van de cache beperkt, moet u ook de MTU-instellingen voor clients en back-end-opslag systemen die met de cache communiceren, beperken. Meer informatie over [extra Azure HPC-cache-instellingen configureren](configuration.md#adjust-mtu-value) .

## <a name="check-for-acl-security-style"></a>Controleren op ACL-beveiligings stijl

Sommige NAS-systemen gebruiken een hybride beveiligings stijl waarmee Acl's (Access Control Lists) worden gecombineerd met de traditionele POSIX-of UNIX-beveiliging.

Als uw systeem de beveiligings stijl rapporteert als UNIX of POSIX zonder het acroniem ' ACL'S ' op te nemen, heeft dit probleem geen invloed op u.

Voor systemen die gebruikmaken van Acl's, moet Azure HPC cache aanvullende gebruikersspecifieke waarden bijhouden om de toegang tot het bestand te beheren. Dit doet u door een toegangs cache in te scha kelen. Er is geen besturings element aan de gebruiker gericht om de toegangs cache in te scha kelen, maar u kunt een ondersteunings ticket openen om aan te vragen dat het wordt ingeschakeld voor de betrokken opslag doelen op uw cache systeem.

## <a name="next-steps"></a>Volgende stappen

Als u een probleem ondervindt dat niet in dit artikel is opgelost, [opent u een ondersteunings ticket](hpc-cache-support-ticket.md) om hulp te krijgen bij de deskundige.
