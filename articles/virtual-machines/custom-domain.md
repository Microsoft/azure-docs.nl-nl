---
title: Een aangepast domein maken en gebruiken
description: Een aangepast domein verbinden met een virtuele machine in Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: c4797420904b6dc03550f658c2aa950a4de99c9c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250982"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Aangepast domein toevoegen aan Azure VM of resource

In azure zijn er meerdere manieren om een aangepast domein te koppelen aan uw VM of resource. Voor elke resource met een openbaar IP-adres (virtuele machine, Load Balancer, Application Gateway) is de meest rechtse manier om een recordset te maken in de bijbehorende domein registratie groep. 

## <a name="prerequisites"></a>Vereisten 
- U hebt een virtuele machine nodig waarop een webserver wordt uitgevoerd. U kunt de [Snelstartgids](./linux/quick-create-cli.md) gebruiken om een virtuele machine te maken en NGINX toe te voegen.

- De virtuele machine moet toegankelijk zijn voor het web (open poort 80 of 443). Voor een veiligere implementatie plaatst u uw virtuele machine achter een load balancer of Application Gateway eerst. Zie [Quick Start: Load Balancer](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal?tabs=option-1-create-load-balancer-standard)voor meer informatie.

- Een bestaand domein en toegang tot de DNS-instellingen hebben. Zie [een aangepast domein voor Azure app service kopen](../app-service/manage-custom-dns-buy-domain.md)voor meer informatie.


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Een aangepast domein toevoegen aan het open bare IP-adres van de VM

Wanneer u een virtuele machine in de Azure Portal maakt, wordt automatisch een open bare IP-resource voor de virtuele machine gemaakt. Uw open bare IP-adres wordt weer gegeven in de overzichts pagina van de virtuele machine. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Het open bare IP-adres wordt weer gegeven in de sectie Essentials van de pagina overzicht van de virtuele machine.":::

Als u het IP-adres selecteert, kunt u meer informatie weer geven. Controleer of uw **IP-toewijzing** is ingesteld op **statisch**. Een statisch IP-adres wordt niet gewijzigd als de virtuele machine of bron opnieuw wordt opgestart of afgesloten.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Hier wordt de open bare IP-configuratie weer gegeven, zodat u kunt zien of het IP-adres statisch is.":::

Als uw IP-adres niet statisch is, moet u een FQDN maken. 

1. Selecteer uw virtuele machine in de portal. 
1. Selecteer in het menu links **Eigenschappen**
1. Selecteer bij **label voor open bare IP-naam address\DNS** uw IP-adres.
2. Onder **DNS-naam label** voert u het voor voegsel in dat u wilt gebruiken.
3. Selecteer **Opslaan** bovenaan de pagina.
4. Selecteer **overzicht** in het menu links om terug te keren naar de BLADe VM-overzicht.
5. Controleer of de *DNS-naam* correct wordt weer gegeven. 

Open een browser en voer uw IP-adres of een FQDN-naam in en controleer of de webinhoud wordt weer gegeven die op uw virtuele machine wordt uitgevoerd.
 
Nadat u het statische IP-adres of de FQDN hebt gecontroleerd, gaat u naar uw domein provider en navigeert u naar DNS-instellingen.

Zodra u een *record* hebt toegevoegd die verwijst naar uw open bare IP-adres of FQDN. Bijvoorbeeld: de procedure voor de registrar van een GoDaddy-domein is als volgt:
1. Meld u aan en selecteer het aangepaste domein dat u wilt gebruiken.
2. Selecteer in de sectie **domeinen** de optie **Alles beheren** en selecteer vervolgens **DNS | Zones beheren**.
3. Voer bij **Domeinnaam** uw aangepaste domein in en selecteer **Zoeken**.
4. Selecteer op de pagina DNS-beheer de optie toevoegen en selecteer een in de lijst Type.
5. Vul de velden van de vermelding in:
    - Type: Geef **een** geselecteerd.
    - Host: Voer **@**
    - Verwijst naar: Voer het open bare IP-adres of de FQDN van de virtuele machine in. 
    - TTL: Eén uur geselecteerd laten.
6. Selecteer **Opslaan**.

De A-record vermelding wordt toegevoegd aan de tabel DNS-records.
 
Nadat de record is gemaakt, duurt het ongeveer een uur voor DNS-doorgifte, maar het kan soms Maxi maal 48 uur duren. 


 
## <a name="next-steps"></a>Volgende stappen
[Overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway](../application-gateway/ssl-overview.md).

 
