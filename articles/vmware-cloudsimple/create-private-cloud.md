---
title: 'Azure VMware-oplossing op CloudSimple: CloudSimple-Privécloud maken'
description: Hierin wordt beschreven hoe u een CloudSimple-Privécloud maakt om VMware-workloads uit te breiden naar de Cloud met operationele flexibiliteit en continuïteit
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97896274"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Een CloudSimple-Privécloud maken

Een Privécloud is een geïsoleerde VMware-stack die ondersteuning biedt voor ESXi-hosts, vCenter, vSAN en NSX. Persoonlijke Clouds worden beheerd via de CloudSimple-Portal. Ze hebben hun eigen vCenter-Server in een eigen beheer domein. De stack wordt uitgevoerd op toegewezen knoop punten en geïsoleerde bare metal hardware-knoop punten.

Het maken van een Privécloud helpt u bij het oplossen van een aantal gemeen schappelijke behoeften voor de netwerk infrastructuur:

* **Groei**. Als u een hardware-vernieuwings punt hebt bereikt voor uw bestaande infra structuur, kunt u een Privécloud uitbreiden zonder dat er nieuwe hardware-investering vereist is.

* **Snelle uitbrei ding**. Als er sprake is van tijdelijke of niet-geplande capaciteit, kunt u met een Privécloud de extra capaciteit zonder vertraging maken.

* **Betere beveiliging**. Met een Privécloud van drie of meer knoop punten krijgt u automatische redundantie en bescherming met hoge Beschik baarheid.

* **Vereisten voor de infra structuur op lange termijn**. Als uw data centers een capaciteit hebben of u zich wilt herstructureren om uw kosten te verlagen, kunt u met een Privécloud data centers buiten gebruik stellen en migreren naar een Cloud oplossing terwijl deze compatibel zijn met uw bedrijfs activiteiten.

Wanneer u een Privécloud maakt, krijgt u één vSphere-cluster en alle beheer-Vm's die in het cluster zijn gemaakt.

## <a name="before-you-begin"></a>Voordat u begint

Knoop punten moeten worden ingericht voordat u de Privécloud kunt maken. Zie voor meer informatie over het inrichten van knoop punten [richt knooppunten inrichten voor Azure VMware-oplossing door CloudSimple](create-nodes.md).

Wijs een CIDR-bereik toe voor vSphere/vSAN-subnetten voor de Privécloud. Een Privécloud wordt gemaakt als een geïsoleerde VMware-stack omgeving (met ESXi-hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-Server. Beheer onderdelen worden geïmplementeerd in het netwerk dat is geselecteerd voor vSphere/vSAN-subnets CIDR. Het netwerk-CIDR-bereik is tijdens de implementatie onderverdeeld in verschillende subnetten. De adres ruimte van het vSphere/vSAN-subnet moet uniek zijn. Het mag niet overlappen met een netwerk dat communiceert met de CloudSimple-omgeving. De netwerken die met CloudSimple communiceren, zijn onder andere on-premises netwerken en Azure Virtual Networks. Zie voor meer informatie over vSphere-en vSAN-subnetten VLAN'S en subnetten-overzicht.

* Minimum aantal vSphere/vSAN-subnetten CIDR-bereik voor voegsel:/24
* Maximum aantal vSphere/vSAN-subnetten voor voegsel van CIDR-bereik:/21


## <a name="access-the-cloudsimple-portal"></a>Toegang tot de CloudSimple-portal

Toegang tot de [CloudSimple-Portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Een nieuwe Privécloud maken

1. Selecteer **Alle services**.
2. Zoek naar **CloudSimple Services**.
3. Selecteer de CloudSimple-service waarop u uw Privécloud wilt maken.
4. Klik in **overzicht** op **privécloud maken** om een nieuw browser tabblad voor CloudSimple portal te openen. Meld u aan met uw aanmeldings referenties voor Azure als u hierom wordt gevraagd.

    ![Een Privécloud maken van Azure](media/create-private-cloud-from-azure.png)

5. Geef in de CloudSimple-Portal een naam op voor de Privécloud.
6. Selecteer een **locatie** voor de privécloud.
7. Selecteer het **knooppunt type** in overeenstemming met wat u in azure hebt ingericht.
8. Geef het **aantal knoop punten** op.  Er zijn ten minste drie knoop punten vereist voor het maken van een Privécloud.

    ![Een persoonlijke Cloud maken-basis gegevens](media/create-private-cloud-basic-info.png)

9. Klik op **volgende: geavanceerde opties**.
10. Voer het CIDR-bereik in voor vSphere/vSAN-subnetten. Zorg ervoor dat het CIDR-bereik niet overlapt met een van uw on-premises of andere Azure-subnetten (virtuele netwerken) of met het gateway-subnet.

    **Opties voor het CIDR-bereik:** /24,/23,/22 of/21. Een/24 CIDR-bereik ondersteunt Maxi maal negen knoop punten, een/23 CIDR-bereik ondersteunt Maxi maal 41 knoop punten en een/22-en/21 CIDR-bereik ondersteunt Maxi maal 64 knoop punten (het maximum aantal knoop punten in een Privécloud).

    > [!IMPORTANT]
    > IP-adressen in het vSphere/vSAN CIDR-bereik zijn gereserveerd voor gebruik door de infra structuur van de Privécloud.  Gebruik het IP-adres in dit bereik op geen enkele virtuele machine.

11. Klik op **volgende: controleren en maken**.
12. Controleer de instellingen. Als u instellingen wilt wijzigen, klikt u op **vorige**.
13. Klik op **Create**.

Het inrichtings proces voor de privécloud wordt gestart. Het kan tot twee uur duren voordat de Privécloud is ingericht.

Zie [een privécloud uitvouwen](expand-private-cloud.md)voor instructies over het uitbreiden van een bestaande privécloud.
