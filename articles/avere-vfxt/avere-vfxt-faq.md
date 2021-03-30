---
title: Veelgestelde vragen-avere vFXT voor Azure
description: Gebruik deze veelgestelde vragen om te bepalen of avere vFXT voor Azure aan uw behoeften voldoet. Meer informatie over hoe avere vFXT voor Azure werkt met andere Azure-onderdelen.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.custom: references_regions
ms.openlocfilehash: 647a9792384a21dfc191371ef444cbdc098bc819
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92342362"
---
# <a name="avere-vfxt-for-azure-faq"></a>Veelgestelde vragen over Avere vFXT for Azure

In dit artikel vindt u antwoorden op vragen die u kunnen helpen bij het bepalen of avere vFXT voor Azure geschikt is voor uw behoeften. Het bevat algemene informatie over avere vFXT en legt uit hoe het werkt met andere Azure-onderdelen en met producten van externe leveranciers.

## <a name="general"></a>Algemeen

### <a name="what-is-avere-vfxt-for-azure"></a>Wat is Avere vFXT for Azure?

AVERE vFXT voor Azure is een bestands systeem met hoge prestaties waarmee actieve gegevens in azure Compute worden opgeslagen voor efficiënte verwerking van kritieke workloads.

### <a name="is-avere-vfxt-a-storage-solution"></a>Is avere vFXT een opslag oplossing?

Nee. AVERE vFXT voor Azure is een bestandssysteem *cache* die is gekoppeld aan opslag omgevingen, zoals uw EMC-of NetApp NAS of een Azure Blob-container. AVERE vFXT stroomlijnt gegevens aanvragen van clients en slaat de gegevens op in de cache om de prestaties op schaal en na verloop van tijd te verbeteren. AVERE vFXT zelf slaat geen gegevens op. Het bevat geen informatie over de hoeveelheid gegevens die achter het bestand is opgeslagen.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Is avere vFXT een laag oplossing?

AVERE vFXT voor Azure belaagt niet automatisch gegevens tussen warme en coole lagen.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hoe kan ik weet of een omgeving geschikt is voor avere vFXT?

De beste manier om te denken over deze vraag is het volgende te vragen: "is de werk belasting in de cache?" Dat wil zeggen dat de workload een hoge lees-naar-schrijf verhouding heeft? Een voor beeld is 80/20 of 70/30 leest/schrijft.

Overweeg avere vFXT voor Azure als u een analytische pijp lijn hebt die wordt uitgevoerd op een groot aantal virtuele machines van Azure en voldoet aan een of meer van de volgende voor waarden:

* De algehele prestaties zijn traag of inconsistent vanwege lange bestands toegangs tijden (tien tallen milliseconden of seconden), afhankelijk van de vereisten. Deze latentie is onaanvaardbaar voor de klant.

* De gegevens die nodig zijn voor de verwerking bevinden zich helemaal aan het einde van een WAN-omgeving en het is niet praktisch om die gegevens permanent te verplaatsen. De gegevens kunnen zich in een andere Azure-regio of in een klant centrum bevinden.

* Een groot aantal clients vraagt de gegevens bijvoorbeeld op in een HPC-cluster (High-Performance Computing). Het grote aantal gelijktijdige aanvragen kan de latentie verhogen.

* De klant wil de huidige pijp lijn uitvoeren "zoals is" in azure virtual machines en heeft een op POSIX gebaseerde, gedeelde opslag (of caching)-oplossing nodig voor schaal baarheid. Door avere vFXT voor Azure te gebruiken, hoeft u de werk pijplijn niet opnieuw te architecten om systeem eigen aanroepen naar Azure Blob-opslag te maken.

* Uw HPC-toepassing is gebaseerd op NFSv3-clients. (In sommige gevallen kan het SMB 2,1-clients gebruiken, maar de prestaties zijn beperkt.)

Het volgende diagram kan u helpen bij het beantwoorden van deze vraag. Hoe dichter uw werk stroom zich helemaal rechtsboven bevindt, des te waarschijnlijker is dat de oplossing voor het opslaan van de avere vFXT voor Azure geschikt is voor uw omgeving.

![Grafiek diagram waarin wordt getoond dat het lezen-zwaar laden met duizenden clients beter geschikt is voor avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Op welke schaal van clients is de avere vFXT-oplossing het meest zinnig?

De avere vFXT-cache oplossing is gebouwd om honderden, duizenden of tien tallen duizenden reken kernen af te handelen. Als er een paar computers met licht werk zijn, is avere vFXT niet de juiste oplossing.

Typische avere vFXT-klanten voeren veeleisende workloads uit met ongeveer 1.000 CPU-kernen. Deze omgevingen kunnen een grootte hebben van 50.000 kernen of meer. Omdat avere vFXT schaalbaar is, kunt u knoop punten toevoegen ter ondersteuning van deze werk belastingen wanneer ze groeien om meer door Voer of meer IOPS te vereisen.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hoeveel gegevens kan een avere vFXT-omgevings opslag?

AVERE vFXT voor Azure is een cache. De gegevens worden niet specifiek opgeslagen. Er wordt gebruikgemaakt van een combi natie van RAM en Ssd's voor het opslaan van de gegevens in de cache. De gegevens worden permanent opgeslagen op een back-end-opslag systeem (bijvoorbeeld een NetApp NAS-systeem of een BLOB-container). Het avere vFXT-systeem heeft geen informatie over de hoeveelheid gegevens die erop zijn opgeslagen. AVERE vFXT slaat alleen de subset van die gegevens op die door clients worden aangevraagd.  

### <a name="what-regions-are-supported"></a>Welke regio's worden ondersteund?

AVERE vFXT voor Azure wordt ondersteund in alle regio's, met uitzonde ring van soevereine regio's (China, Duitsland). Zorg ervoor dat de regio die u wilt gebruiken, ondersteuning biedt voor het grote aantal reken kernen en de VM-exemplaren die nodig zijn om het avere vFXT-cluster te maken.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hoe kan ik hulp krijgen bij avere vFXT?

Een gespecialiseerde groep ondersteunings medewerkers biedt hulp bij avere vFXT voor Azure. Volg de instructies in [hulp bij uw systeem verkrijgen](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) om een ondersteunings ticket te openen via de Azure Portal.

### <a name="is-avere-vfxt-highly-available"></a>Is avere vFXT Maxi maal beschikbaar?

Ja, avere vFXT wordt uitsluitend uitgevoerd als een HA-oplossing.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Ondersteunt avere vFXT voor Azure ook andere Cloud Services?

Ja, klanten kunnen meer dan één Cloud provider gebruiken met het avere vFXT-cluster. Het ondersteunt AWS S3 Standard buckets, Google Cloud Services Standard buckets en Azure Blob-containers.

> [!NOTE]
> Een software vergoeding is van toepassing op het gebruik van AVERE vFXT met AWS of Google Cloud Storage. Er zijn geen extra software kosten voor het gebruik van Azure Blob Storage.

## <a name="technical-compute"></a>Technisch: berekenen

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>Kunt u beschrijven wat een avere vFXT-omgeving lijkt?

AVERE vFXT is een geclusterd apparaat dat bestaat uit meerdere virtuele machines van Azure. Een python-bibliotheek verwerkt het maken, verwijderen en wijzigen van het cluster. Lees [Wat is avere vFXT voor Azure?](avere-vfxt-overview.md) voor meer informatie.

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Op welke soort virtuele Azure-machines wordt avere vFXT uitgevoerd?  

Een avere vFXT voor Azure-cluster maakt gebruik van Microsoft Azure E32s_v3 virtuele machines.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Wordt de omgeving van de avere-vFXT geschaald?

Het avere vFXT-cluster kan Maxi maal drie virtuele-machine knooppunten of Maxi maal 24 knoop punten zijn. Neem contact op met de technische ondersteuning van Azure voor hulp bij de planning als u denkt dat u een cluster van meer dan negen knoop punten nodig hebt. Het grotere aantal knoop punten vereist een grotere implementatie architectuur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Is de avere vFXT-omgeving "automatisch schalen"?

Nee. U kunt de cluster grootte omhoog en omlaag schalen, maar het toevoegen of verwijderen van cluster knooppunten is een hand matige stap.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan ik het avere vFXT-cluster uitvoeren als een schaalset voor virtuele machines?

AVERE vFXT biedt geen ondersteuning voor de implementatie van een schaalset met virtuele machines. Verschillende ingebouwde beschik bare ondersteunings mechanismen zijn alleen ontworpen voor atomische Vm's die deel uitmaken van een cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Kan ik het avere vFXT-cluster op virtuele machines met lage prioriteit uitvoeren?

Nee, het systeem vereist een onderliggende stabiele set virtuele machines.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Kan ik het avere vFXT-cluster in containers uitvoeren?

Nee, avere vFXT moet als een onafhankelijke toepassing worden geïmplementeerd.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Is het aantal avere vFXT-Vm's voor mijn reken quota?

Ja. Zorg ervoor dat de regio voldoende quota heeft voor de ondersteuning van het cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan ik de avere vFXT-cluster machines in verschillende beschikbaarheids zones uitvoeren?

Nee. Het model voor hoge Beschik baarheid in avere vFXT biedt momenteel geen ondersteuning voor afzonderlijke avere vFXT-cluster leden die zich in verschillende beschikbaarheids zones bevinden.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan ik virtuele machines van AVERE vFXT klonen?

Nee, u moet het ondersteunde python-script gebruiken om knoop punten toe te voegen aan of te verwijderen uit het avere vFXT-cluster. Lees [Manage the avere vFXT cluster](avere-vfxt-manage-cluster.md)voor meer informatie.

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Is er een VM-versie van de software die ik in mijn eigen lokale omgeving kan uitvoeren?

Nee, het systeem wordt aangeboden als een geclusterd apparaat en getest op specifieke typen virtuele machines. Deze beperking helpt klanten bij het maken van een systeem dat geen ondersteuning biedt voor de hoge prestatie vereisten van een typische avere vFXT-werk stroom.

## <a name="technical-disks"></a>Technisch: schijven

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Welke typen schijven worden ondersteund voor de virtuele Azure-machines?

AVERE vFXT voor Azure kan gebruikmaken van 1 TB of 4 TB Premium SSD-configuraties. De Premium SSD-configuratie kan worden geïmplementeerd als meerdere beheerde schijven.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Ondersteunt het cluster niet-beheerde schijven?

Nee, het cluster vereist beheerde schijven.

### <a name="does-the-system-support-local-attached-ssds"></a>Ondersteunt het systeem lokale (gekoppelde) Ssd's?

AVERE vFXT voor Azure biedt momenteel geen ondersteuning voor lokale Ssd's. Schijven die worden gebruikt voor avere vFXT moeten kunnen worden afgesloten en opnieuw worden opgestart, maar lokale gekoppelde Ssd's in deze configuratie kan alleen worden beëindigd.

### <a name="does-the-system-support-ultra-ssds"></a>Ondersteunt het systeem Ultra Ssd's?

Nee, het systeem ondersteunt alleen Premium SSD-configuraties.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan ik mijn Premium Ssd's ontkoppelen en later opnieuw koppelen om de cache-inhoud tussen het gebruik te bewaren?

Het loskoppelen en opnieuw koppelen van Ssd's wordt niet ondersteund. Meta gegevens of bestands inhoud op de bron zijn mogelijk gewijzigd tussen het gebruik, wat kan leiden tot problemen met de gegevens integriteit.

### <a name="does-the-system-encrypt-the-cache"></a>Wordt de cache door het systeem versleuteld?

De gegevens worden gestripd over de schijven, maar niet versleuteld. De schijven zelf kunnen echter worden versleuteld. Zie voor meer informatie [beleid voor veilig en gebruik van virtuele machines in azure](../virtual-machines/security-policy.md#encryption).

## <a name="technical-networking"></a>Technisch: netwerken

### <a name="what-network-is-recommended"></a>Welk netwerk wordt aanbevolen?

Als u gebruikmaakt van on-premises opslag met avere vFXT, hebt u een netwerk verbinding van 1 Gbps of beter tussen uw opslag en het cluster nodig. Als u een kleine hoeveelheid gegevens hebt en bereid bent om gegevens te kopiëren naar de Cloud voordat u taken uitvoert, kan de VPN-verbinding voldoende zijn.

> [!TIP]
> Hoe langzamer de netwerk koppeling is, hoe langzamer de eerste Lees bewerkingen zijn. Trage Lees bewerkingen verhogen de latentie van de werk pijplijn.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan ik avere vFXT uitvoeren in een ander virtueel netwerk dan mijn berekenings cluster?

Ja, u kunt uw avere vFXT-systeem maken in een ander virtueel netwerk. Raadpleeg [uw avere vFXT-systeem plannen](avere-vfxt-deploy-plan.md) voor meer informatie.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Is avere vFXT een eigen subnet nodig?

Ja. AVERE vFXT wordt uitsluitend als een cluster met hoge Beschik baarheid (HA) uitgevoerd en vereist dat meerdere IP-adressen actief zijn. Als het cluster zich in een eigen subnet bevindt, vermijdt u het risico op conflicten met IP-adressen. Dit kan leiden tot problemen met de installatie en de normale werking. Het subnet van het cluster kan zich in een virtueel netwerk bevinden dat door andere bronnen wordt gebruikt, zolang er geen IP-adressen zijn die elkaar overlappen.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kan ik avere vFXT op InfiniBand uitvoeren?

Nee, avere vFXT gebruikt alleen Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hoe kan ik toegang tot mijn on-premises NAS-omgeving vanuit avere vFXT?

De avere vFXT-omgeving is net als elke andere virtuele machine van Azure, omdat hiervoor gerouteerde toegang via een netwerk gateway of VPN naar het Data Center van de klant is vereist. Overweeg het gebruik van Azure ExpressRoute-connectiviteit als deze beschikbaar is in uw omgeving.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Wat zijn de bandbreedte vereisten voor avere vFXT?

De vereiste voor de totale band breedte is afhankelijk van twee factoren:

* De hoeveelheid gegevens die wordt aangevraagd vanuit de bron
* De tolerantie van het client systeem tijdens het laden van initiële gegevens  

Voor latentie gevoelige omgevingen moet u een glasvezel oplossing gebruiken met een minimale verbindings snelheid van 1 Gbps. Gebruik ExpressRoute als deze beschikbaar is.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kan ik avere vFXT uitvoeren met open bare IP-adressen?

Nee, avere vFXT is bedoeld om te worden gebruikt in een netwerk omgeving die wordt beveiligd met aanbevolen procedures.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kan ik Internet toegang vanuit het virtuele netwerk van mijn cluster beperken?

Over het algemeen kunt u zo nodig extra beveiliging op het virtuele netwerk configureren, maar sommige beperkingen kunnen de werking van het cluster belemmeren.

Als u bijvoorbeeld de uitgaande internet toegang van uw virtuele netwerk beperkt, worden er problemen voor het cluster veroorzaakt, tenzij u ook een regel toevoegt waarmee expliciet toegang tot Cloud wordt toegestaan. Deze situatie wordt beschreven in [aanvullende documentatie over github](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Neem contact op met de ondersteuning, zoals wordt beschreven in [hulp vragen bij uw systeem](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)voor hulp bij aangepaste beveiliging.

## <a name="technical-back-end-storage-core-filers"></a>Technisch: back-end-opslag (kern bestanden)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hoeveel kern bestanden ondersteunt één avere vFXT-omgeving?

Een avere vFXT-cluster ondersteunt Maxi maal 20 kern bestanden.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hoe worden gegevens opgeslagen in de omgeving van de avere-vFXT?

AVERE vFXT is geen opslag ruimte. Het is een cache die gegevens leest uit en schrijft van meerdere opslag doelen die kern bestanden worden genoemd. Gegevens die zijn opgeslagen op Premium SSD-schijven in avere vFXT, zijn tijdelijk en worden uiteindelijk leeg gemaakt naar de back-end-kern bestands opslag.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Welke kern bestanden ondersteunt avere vFXT?

In het algemeen worden in avere vFXT voor Azure de volgende systemen ondersteund als kern bestanden:

* Dell EMC Isilon (OneFS 7,1, 7,2, 8,0 en 8,1) 
* NetApp ONTAP (geclusterde modus 9,4, 9,3, 9,2, 9.1 P1, 8.0-8.3) en (7-modus 7. *, 8.0-8,3)

* Azure Blob-containers (alleen lokaal redundante opslag)
* AWS S3-buckets
* Google-Cloud-buckets

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Waarom biedt avere vFXT geen ondersteuning voor alle NFS-bestanden?

Hoewel alle NFS-platforms voldoen aan dezelfde IETF-normen, heeft elke implementatie in de praktijk een eigen quirks. Deze details beïnvloeden hoe avere vFXT communiceert met het opslag systeem. De ondersteunde systemen zijn de meest gebruikte platforms op Marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Biedt avere vFXT ondersteuning voor opslag van persoonlijke objecten (zoals SwiftStack)?

AVERE vFXT biedt geen ondersteuning voor opslag van privé-objecten.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hoe krijg ik een specifiek opslag product onder ondersteuning?

Ondersteuning is gebaseerd op de hoeveelheid vraag in het veld. Als er voldoende op omzet gebaseerde aanvragen zijn voor de ondersteuning van een NAS-oplossing, worden deze beschouwd. Aanvragen indienen via ondersteuning voor Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan ik Azure Blob-opslag als kern bestand gebruiken?

Ja, avere vFXT voor Azure kan gebruikmaken van een blok-BLOB-container als een Cloud core-bestand.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Wat zijn de vereisten voor het opslag account voor een BLOB core-bestand?

Uw opslag account moet een GPv2-account (General-Purpose v2) zijn en alleen worden geconfigureerd voor lokaal redundante opslag. Geografisch redundante opslag en zone-redundante opslag worden niet ondersteund.

Lees [Azure Blob Storage Cloud core filer](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) voor meer informatie over de vereisten voor het opslag account.

### <a name="can-i-use-archive-blob-storage"></a>Kan ik archief-Blob-opslag gebruiken?

Nee. De Service Level Agreement (SLA) voor archief opslag is niet compatibel met de real-time Directory-en bestands toegangs behoeften van het avere vFXT-systeem.

### <a name="can-i-use-cool-blob-storage"></a>Kan ik de cool Blob-opslag gebruiken?

Blob-opslag voor de cool-laag wordt doorgaans niet aanbevolen voor een avere vFXT voor Azure core-bestanden. Cool-laag biedt lagere opslag kosten, maar hogere bewerkings kosten. (Zie [prijzen voor blok-blobs](<https://azure.microsoft.com/pricing/details/storage/blobs/>) voor meer informatie.) Als gegevens regel matig worden geopend en gewijzigd of verwijderd, kunt u overwegen de warme laag te gebruiken.

[Toegangs lagen](../storage/blobs/storage-blob-storage-tiers.md#cool-access-tier) biedt meer informatie over wanneer het zinvol is om de cool-laag opslag te gebruiken als een vFXT-kern bestand.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hoe kan ik de BLOB-container versleutelen?

U kunt BLOB-versleuteling configureren in azure (bij voor keur) of op het niveau van de avere vFXT core-bestand.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan ik mijn eigen versleutelings sleutel gebruiken voor een BLOB-kern bestand?

Standaard worden gegevens versleuteld met door micro soft beheerde sleutels voor Azure Blob, Table en Queue Storage, plus Azure Files. U kunt uw eigen sleutel voor versleuteling van de Blob-opslag en Azure Files. Als u ervoor kiest om avere vFXT-versleuteling te gebruiken, moet u de door avere gegenereerde sleutel gebruiken en deze lokaal opslaan.

## <a name="purchasing"></a>Aanschaffen

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hoe kan ik avere vFXT voor Azure-licenties ophalen?

Het verkrijgen van een avere vFXT voor Azure-licentie is eenvoudig via Azure Marketplace. Meld u aan voor een Azure-account en volg de instructies in [Deploy the avere vFXT cluster](avere-vfxt-deploy.md) om een avere vFXT-cluster te maken.

### <a name="how-much-does-avere-vfxt-cost"></a>Wat kost avere vFXT?

In azure zijn er geen extra licentie kosten verbonden aan het gebruik van AVERE vFXT-clusters. Klanten zijn verantwoordelijk voor opslag en andere kosten voor Azure-verbruik.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kunnen avere vFXT-Vm's als lage prioriteit worden uitgevoerd?

Nee, avere vFXT-clusters vereisen de service ' always on '. De clusters kunnen worden uitgeschakeld wanneer ze niet nodig zijn.

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen voor meer informatie over het plannen en implementeren van uw eigen systeem om aan de slag te gaan met avere vFXT voor Azure:

* [Uw Avere vFXT-systeem plannen](avere-vfxt-deploy-plan.md)
* [Implementatie-overzicht](avere-vfxt-deploy-overview.md)
* [Voorbereiden op het maken van een avere vFXT-cluster](avere-vfxt-prereqs.md)
* [Het Avere vFXT-cluster implementeren](avere-vfxt-deploy.md)

Ga naar [avere vFXT voor Azure](https://azure.microsoft.com/services/storage/avere-vfxt/)voor meer informatie over mogelijkheden en use cases voor avere vFXT.