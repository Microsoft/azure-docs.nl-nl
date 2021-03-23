---
title: Instellingen voor de Azure HPC-cache configureren
description: In dit artikel wordt uitgelegd hoe u aanvullende instellingen voor de cache kunt configureren, zoals MTU, aangepaste NTP-en DNS-configuratie en hoe u toegang krijgt tot de Express-moment opnamen vanuit Azure Blob-opslag doelen.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/17/2021
ms.author: v-erkel
ms.openlocfilehash: 6e1e1283cb82dcb900da6473de65ef087a5cea82
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773229"
---
# <a name="configure-additional-azure-hpc-cache-settings"></a>Aanvullende instellingen voor de Azure HPC-cache configureren

De pagina **netwerk** in de Azure Portal bevat opties voor het aanpassen van verschillende instellingen. De meeste gebruikers hoeven deze instellingen niet te wijzigen van hun standaard waarden.

In dit artikel wordt ook beschreven hoe u de functie snap shot gebruikt voor Azure Blob Storage-doelen. De momentopname functie heeft geen Configureer bare instellingen.

Als u de instellingen wilt zien, opent u de pagina **netwerk toegang** van de cache in de Azure Portal.

![scherm afbeelding van de pagina netwerk in Azure Portal](media/networking-page.png)

> [!NOTE]
> Een vorige versie van deze pagina bevatte een Squash-instelling op cache niveau, maar deze instelling is verplaatst naar het [beleid voor client toegang](access-policies.md).

<!-- >> [!TIP]
> The [Managing Azure HPC Cache video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) shows the networking page and its settings. -->

## <a name="adjust-mtu-value"></a>MTU-waarde aanpassen
<!-- linked from troubleshoot-nas article -->

U kunt de maximale grootte van de verzend eenheid voor de cache selecteren met behulp van de vervolg keuzelijst met de naam **MTU-grootte**.

De standaard waarde is 1500 bytes, maar u kunt deze wijzigen in 1400.

> [!NOTE]
> Als u de MTU-grootte van de cache verlaagt, moet u ervoor zorgen dat de clients en opslag systemen die met de cache communiceren dezelfde MTU-instelling of een lagere waarde hebben.

Het verlagen van de cache-MTU-waarde kan u helpen de beperkingen voor pakket grootte te omzeilen in de rest van het netwerk van de cache. Sommige Vpn's kunnen bijvoorbeeld geen 1500-byte-pakketten van de volledige grootte verzenden. Het verminderen van de grootte van pakketten die via de VPN-verbinding worden verzonden, kan dat probleem mogelijk niet oplossen. Houd er echter rekening mee dat een lagere MTU-instelling voor de cache betekent dat elk ander onderdeel dat communiceert met de cache-inclusief-clients en-opslag systemen, ook een lagere MTU-instelling moet hebben om communicatie problemen te voor komen.

Als u de MTU-instellingen op andere systeem onderdelen niet wilt wijzigen, moet u de MTU-instelling van de cache niet verlagen. Er zijn andere oplossingen om beperkingen voor de grootte van VPN-pakketten te omzeilen. Lees de beperkingen voor de [grootte van VPN-pakketten aanpassen](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) in het artikel over het oplossen van problemen met NAS voor meer informatie over het diagnosticeren en oplossen van dit probleem.

Lees meer over MTU-instellingen in virtuele netwerken van Azure door het [afstemmen van TCP/IP-prestaties voor Azure-vm's](../virtual-network/virtual-network-tcpip-performance-tuning.md)te lezen.

## <a name="customize-ntp"></a>NTP aanpassen

Uw cache maakt standaard gebruik van de tijd server-time.microsoft.com op basis van Azure. Als u wilt dat uw cache een andere NTP-server gebruikt, geeft u deze op in de sectie **NTP-configuratie** . Gebruik een Fully Qualified Domain Name of een IP-adres.

## <a name="set-a-custom-dns-configuration"></a>Een aangepaste DNS-configuratie instellen

> [!CAUTION]
> Wijzig de DNS-configuratie van uw cache niet als u dit niet nodig hebt. Configuratie fouten kunnen ernstige gevolgen hebben. Als uw configuratie geen Azure-service namen kan omzetten, wordt het HPC-cache-exemplaar permanent onbereikbaar.

De Azure HPC-cache wordt automatisch geconfigureerd voor het gebruik van het veilige en handige Azure DNS systeem. Voor een paar ongebruikelijke configuraties moet de cache echter gebruikmaken van een afzonderlijk, lokaal DNS-systeem in plaats van het Azure-systeem. De sectie **DNS-configuratie** van de pagina **netwerk** wordt gebruikt om dit type systeem op te geven.

Neem contact op met uw Azure-vertegenwoordigers of Raadpleeg de service en ondersteuning van micro soft om te bepalen of u een aangepaste cache-DNS-configuratie moet gebruiken.

Als u uw eigen on-premises DNS-systeem configureert voor gebruik in de Azure HPC-cache, moet u ervoor zorgen dat de configuratie Azure-eindpunt namen voor Azure-Services kan omzetten. U moet uw aangepaste DNS-omgeving configureren om bepaalde aanvragen voor naam omzetting door te sturen naar Azure DNS of naar een andere server als dat nodig is.

Controleer of de DNS-configuratie deze items kan omzetten voordat u deze gebruikt voor een Azure HPC-cache:

* ``*.core.windows.net``
* Certificaat intrekkings lijst (CRL) downloaden en verificatie services voor OCSP (Online Certificate Status Protocol). Aan het einde van dit [Azure TLS-artikel](../security/fundamentals/tls-certificate-changes.md)is een gedeeltelijke lijst opgenomen in het [item firewall regels](../security/fundamentals/tls-certificate-changes.md#will-this-change-affect-me) , maar u moet een technische mede werker van micro soft raadplegen om alle vereisten te begrijpen.
* De Fully Qualified Domain Name van uw NTP-server (time.microsoft.com of een aangepaste server)

Als u een aangepaste DNS-server voor uw cache wilt instellen, gebruikt u de opgegeven velden:

* **DNS-Zoek domein** (optioneel): Voer uw zoek domein in, bijvoorbeeld ``contoso.com`` . Er is één waarde toegestaan of u kunt deze leeg laten.
* **DNS-server (s)** : Voer Maxi maal drie DNS-servers in. Geef ze op als IP-adres.

<!-- 
  > [!NOTE]
  > The cache will use only the first DNS server it successfully finds. -->

Overweeg het gebruik van een test cache om uw DNS-installatie te controleren en te verfijnen voordat u deze in een productie omgeving gebruikt.

### <a name="refresh-storage-target-dns"></a>DNS voor opslag doel vernieuwen

Als de DNS-server IP-adressen bijwerkt, worden de gekoppelde NFS-opslag doelen tijdelijk niet beschikbaar. Lees hoe u de IP-adressen van uw aangepaste DNS-systeem bijwerkt in [opslag doelen bewerken](hpc-cache-edit-storage.md#update-ip-address-custom-dns-configurations-only).

## <a name="view-snapshots-for-blob-storage-targets"></a>Moment opnamen voor Blob-opslag doelen weer geven

Met Azure HPC cache worden opslag momentopnamen voor Azure Blob-opslag doelen automatisch opgeslagen. Moment opnamen bieden een snel referentie punt voor de inhoud van de back-end-opslag container.

Moment opnamen zijn geen vervanging voor back-ups van gegevens en ze bevatten geen informatie over de status van gegevens in de cache.

> [!NOTE]
> Deze functie voor moment opnamen wijkt af van de momentopname functie die is opgenomen in de opslag software van NetApp of Isilon. Deze moment opname-implementaties nemen wijzigingen van de cache over naar het back-end-opslag systeem voordat de moment opname wordt gemaakt.
>
> Voor efficiëntie worden de wijzigingen in de Azure HPC-cache momentopname niet eerst leeg gemaakt en worden alleen gegevens vastgelegd die naar de BLOB-container zijn geschreven. Deze moment opname vertegenwoordigt niet de status van de gegevens in de cache, waardoor er mogelijk geen recente wijzigingen zijn.

Deze functie is alleen beschikbaar voor Azure Blob-opslag doelen en de configuratie kan niet worden gewijzigd.

Moment opnamen worden elke acht uur genomen, op UTC 0:00, 08:00 en 16:00.

Met de Azure HPC-cache worden dagelijkse, wekelijkse en maandelijkse moment opnamen opgeslagen, totdat deze worden vervangen door nieuwe sjablonen. De limieten voor het bewaren van de moment opname zijn:

* Maxi maal 20 dagelijkse moment opnamen
* Maxi maal 8 wekelijkse moment opnamen
* Maxi maal 3 maandelijkse moment opnamen

Open de moment opnamen van de `.snapshot` map in de hoofdmap van het gekoppelde Blob Storage-doel.
