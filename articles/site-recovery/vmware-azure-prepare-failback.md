---
title: VMware-Vm's voorbereiden voor herbescherming en failback met Azure Site Recovery
description: Voor bereidingen voor failback van virtuele VMware-machines na een failover met Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85847733"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Voorbereiden op opnieuw beveiligen en uitvoeren van failback van virtuele VMware-machines

Na een [failover](site-recovery-failover.md) van on-premises virtuele VMware-machines of fysieke servers naar Azure, moet u de virtuele Azure-machines die na een failover zijn gemaakt, opnieuw beveiligen, zodat deze worden gerepliceerd naar de on-premises site. Wanneer replicatie van Azure naar on-premises wordt uitgevoerd, kunt u failback uitvoeren door een failover van Azure naar on-premises uit te voeren wanneer u klaar bent.

Voordat u doorgaat, krijgt u een kort overzicht van deze video over hoe u een failback van Azure naar een on-premises site kunt uitvoeren.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Onderdelen voor opnieuw beveiligen en failback

U hebt een aantal onderdelen en instellingen nodig voor u kunt opnieuw beveiligen en failback uitvoeren vanuit Azure.

**Onderdeel**| **Details**
--- | ---
**On-premises configuratieserver** | De on-premises configuratie server moet actief zijn en verbonden zijn met Azure.<br/><br/> De virtuele machine waarvoor u een failover wilt uitvoeren, moet aanwezig zijn in de data base van de configuratie server. Als een nood geval de configuratie server beïnvloedt, herstelt u deze met hetzelfde IP-adres om er zeker van te zijn dat failback werkt.<br/><br/>  Als IP-adressen van gerepliceerde machines worden bewaard op failover, moet de site-naar-site-verbinding (of ExpressRoute-connectiviteit) tot stand worden gebracht tussen virtuele machines van Azure en de netwerk interface voor failback van de configuratie server. Voor het behouden van IP-adressen heeft de configuratie server twee Nic's nodig: een voor de verbinding van de bron machine en één voor Azure-failback-connectiviteit. Dit voor komt dat de adresbereiken voor de bron overlappen en failover-Vm's worden uitgevoerd.
**Proces server in azure** | U hebt een proces server in azure nodig om een failback naar uw on-premises site mogelijk te maken.<br/><br/> De proces server ontvangt gegevens van de beveiligde Azure-VM en verzendt deze naar de on-premises site.<br/><br/> U hebt een netwerk met lage latentie nodig tussen de proces server en de beveiligde virtuele machine. Daarom raden we u aan de proces server in azure te implementeren voor hogere replicatie prestaties.<br/><br/> Voor het testen van concepten kunt u de on-premises proces server gebruiken en ExpressRoute met privé-peering.<br/><br/> De proces server moet zich in het Azure-netwerk bevinden waarin de virtuele machine waarvoor een failover is uitgevoerd, zich bevindt. De proces server moet ook kunnen communiceren met de on-premises configuratie server en de hoofddoel server.
**Afzonderlijke hoofddoel server** | De hoofddoel server ontvangt failbackgegevens en standaard wordt een Windows-hoofddoel server uitgevoerd op de on-premises configuratie server.<br/><br/> Aan een hoofddoel server kunnen Maxi maal 60 schijven zijn gekoppeld. Vm's waarvoor een failover is uitgevoerd, hebben meer dan een gezamenlijk totaal van 60 schijven, of als u geen grote hoeveel heden verkeer meer wilt maken, maakt u een afzonderlijke hoofddoel server voor failback.<br/><br/> Als machines worden verzameld in een replicatie groep voor consistentie tussen meerdere VM'S, moeten de Vm's allemaal Windows zijn of moeten ze allemaal Linux zijn. Hoe komt dat? Omdat alle virtuele machines in een replicatie groep dezelfde hoofddoel server moeten gebruiken, en de hoofddoel server moet hetzelfde besturings systeem hebben (met dezelfde of een hogere versie) dan die van de gerepliceerde computers.<br/><br/> De Master doel server mag geen moment opnamen op de schijven hebben, anders wordt de beveiliging en failback niet uitgevoerd.<br/><br/> Het hoofd doel kan geen Pvscsi SCSI-controller hebben. De controller kan alleen een LSI Logic-Controller zijn. Zonder een LSI Logic-Controller mislukt het opnieuw beveiligen.
**Failback-replicatie beleid** | Als u terug wilt repliceren naar een on-premises site, hebt u een beleid voor failback nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatie beleid maakt in Azure.<br/><br/> Het beleid wordt automatisch gekoppeld aan de configuratieserver. Het is ingesteld op een RPO-drempel van 15 minuten, retentie van het herstel punt van 24 uur en de frequentie van de app-consistente moment opname is 60 minuten. Het beleid kan niet worden bewerkt. 
**Site-naar-site VPN/ExpressRoute privé-peering** | Voor het opnieuw beveiligen en failback hebt u een site-naar-site-VPN-verbinding nodig of ExpressRoute privé-peering om gegevens te repliceren. 


## <a name="ports-for-reprotectionfailback"></a>Poorten voor rebeveiliging/failback

Er moet een aantal poorten zijn geopend voor het opnieuw beveiligen en failback. In de volgende afbeelding ziet u de poorten en de stroom voor opnieuw beveiligen en failback.

![Poorten voor failover en failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Een proces server in azure implementeren

1. [Stel een proces server](vmware-azure-set-up-process-server-azure.md) in Azure in voor failback.
2. Zorg ervoor dat virtuele machines van Azure de proces server kunnen bereiken. 
3. Zorg ervoor dat de site-naar-site VPN-verbinding of het ExpressRoute particuliere peering-netwerk voldoende band breedte heeft om gegevens van de proces server te verzenden naar de on-premises site.

## <a name="deploy-a-separate-master-target-server"></a>Een afzonderlijke hoofddoel server implementeren

1. Noteer de [vereisten en beperkingen](#reprotectionfailback-components)van de hoofddoel server.
2. Maak een [Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) -of [Linux](vmware-azure-install-linux-master-target.md) -hoofddoel server, zodat deze overeenkomt met het besturings systeem van de vm's die u opnieuw wilt beveiligen en failback wilt uitvoeren.
3. Zorg ervoor dat u geen gebruik maakt van Storage vMotion voor de hoofddoel server of failback kan mislukken. De VM-machine kan niet worden gestart omdat de schijven niet beschikbaar zijn.
    - Als u dit wilt voor komen, sluit u de hoofddoel server uit vanuit uw lijst van vMotion.
    - Als een Master doel een Storage vMotion-taak ondergaat na het opnieuw beveiligen, worden de beveiligde VM-schijven die zijn gekoppeld aan de hoofddoel server gemigreerd naar het doel van de vMotion-taak. Als u na dit wilt failback, mislukt het ontkoppelen van de schijf omdat de schijven niet zijn gevonden. Het is dan lastig om de schijven in uw opslag accounts te vinden. Als dit het geval is, zoekt u deze hand matig en koppelt u deze aan de VM. Daarna kan de on-premises VM worden opgestart.

4. Een Bewaar station toevoegen aan de bestaande Windows-hoofddoel server. Voeg een nieuwe schijf toe en Format teer het station. Het Bewaar station wordt gebruikt om de tijdstippen te stoppen waarop de virtuele machine weer wordt gerepliceerd naar de on-premises site. Let op deze criteria. Als ze niet worden voldaan, wordt het station niet vermeld voor de hoofddoel server:
    - Het volume wordt niet gebruikt voor andere doel einden, zoals een replicatie doel, en bevindt zich niet in de vergrendelings modus.
    - Het volume is geen cache volume. Het aangepaste installatie volume voor de proces server en het hoofd doel komt niet in aanmerking voor een Bewaar volume. Wanneer de proces server en het hoofd doel op een volume zijn geïnstalleerd, is het volume een cache volume van het hoofd doel.
    - Het bestandssysteem type van het volume is niet FAT of FAT32.
    - De volume capaciteit is niet nul.
    - Het standaard retentie volume voor Windows is het R-volume.
    - Het standaard Bewaar volume voor Linux is/mnt/retention.

5. Voeg een station toe als u een bestaande proces server gebruikt. Het nieuwe station moet voldoen aan de vereisten in de laatste stap. Als het Bewaar station niet aanwezig is, wordt het niet weer gegeven in de vervolg keuzelijst selectie in de portal. Nadat u een station aan het on-premises hoofd doel hebt toegevoegd, duurt het Maxi maal 15 minuten voordat het station wordt weer gegeven in de selectie in de portal. U kunt de configuratie server vernieuwen als het station niet meer dan 15 minuten wordt weer gegeven.
6. Installeer VMware-hulpprogram ma's of open-vm-hulpprogram ma's op de hoofddoel server. Zonder de hulpprogram ma's kunnen de gegevens opslag op de ESXi-host van het hoofd doel niet worden gedetecteerd.
7. Stel de schijf in. Instelling EnableUUID = True in de configuratie parameters van de Master doel-VM in VMware. Als deze rij niet bestaat, voegt u deze toe. Deze instelling is vereist om een consistente UUID naar de VMDK te geven zodat deze correct wordt gekoppeld.
8. Controleer vCenter Server toegangs vereisten:
    - Als de virtuele machine waarvoor u een failover uitvoert, zich op een ESXi-host bevindt die wordt beheerd door VMware vCenter Server, moet de hoofddoel server toegang hebben tot de on-premises VM-schijf van de virtuele machine, om de gerepliceerde gegevens naar de schijven van de virtuele machine te schrijven. Zorg ervoor dat de on-premises VM-gegevens opslag is gekoppeld aan de hoofddoel host met lees-en schrijf toegang.
    - Als de virtuele machine zich niet op een ESXi-host bevindt die wordt beheerd door een VMware vCenter Server, maakt Site Recovery een nieuwe VM tijdens het opnieuw beveiligen. Deze VM wordt gemaakt op de ESXi-host waarop u de virtuele machine van de hoofddoel server maakt. Kies zorgvuldig de ESXi-host om de virtuele machine op de gewenste host te maken. De harde schijf van de VM moet zich in een gegevensopslag bevinden die toegankelijk is voor de host waarop de hoofddoelserver wordt uitgevoerd.
    - Een andere optie, als de on-premises VM al bestaat voor failback, is deze te verwijderen voordat u een failback gaat uitvoeren. Met failback wordt vervolgens een nieuwe virtuele machine gemaakt op dezelfde host als de ESXi van het hoofd doel. Wanneer u een failback naar een alternatieve locatie aanmeldt, worden de gegevens hersteld naar dezelfde gegevens opslag en dezelfde ESXi-host als die welke wordt gebruikt door de on-premises hoofddoel server.
9. Voor fysieke computers die niet kunnen worden gebruikt voor virtuele VMware-machines, moet u de detectie volt ooien van de host waarop de hoofddoel server wordt uitgevoerd, voordat u de machine opnieuw kunt beveiligen.
10. Controleer of de ESXi-host waaraan de hoofddoel-VM ten minste één VMFS-gegevens archief (Virtual Machine File System) heeft gekoppeld. Als er geen VMFS-gegevens opslag zijn gekoppeld, is de invoer gegevens opslag in de instellingen voor opnieuw beveiligen leeg en kunt u niet door gaan.


## <a name="next-steps"></a>Volgende stappen

Een virtuele machine opnieuw [beveiligen](vmware-azure-reprotect.md) .
