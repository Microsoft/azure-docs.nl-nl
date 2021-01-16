---
title: Veelgestelde vragen over privé-DNS in Azure
description: In dit artikel vindt u veelgestelde vragen over Azure Privé-DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 01/15/2021
ms.author: rohink
ms.openlocfilehash: 3272f4fea4169fd5e91b1ba2b97ad72dfbb49080
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247383"
---
# <a name="azure-private-dns-faq"></a>Veelgestelde vragen over privé-DNS in Azure

Hieronder vindt u veelgestelde vragen over Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>Ondersteunt Azure DNS persoonlijke domeinen?

Privé domeinen worden ondersteund met behulp van de functie Azure Privé-DNS-zones. Privé-DNS zones kunnen alleen worden omgezet vanuit opgegeven virtuele netwerken. Zie het [overzicht](private-dns-overview.md)voor meer informatie.

Zie [naam omzetting voor vm's en rolinstanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)voor informatie over andere interne DNS-opties in Azure.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Werken Azure Privé-DNS zones over Azure-regio's?

Ja. Persoonlijke zones worden ondersteund voor DNS-omzetting tussen virtuele netwerken in azure-regio's. Persoonlijke zones werken zelfs zonder expliciet peering van de virtuele netwerken. Alle virtuele netwerken moeten zijn gekoppeld aan de privé-DNS-zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Is er verbinding met Internet via virtuele netwerken die vereist zijn voor persoonlijke zones?

Nee. Persoonlijke zones werken samen met virtuele netwerken. U kunt ze gebruiken om domeinen te beheren voor virtuele machines of andere resources binnen en tussen virtuele netwerken. Internet connectiviteit is niet vereist voor naam omzetting.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Kan dezelfde privé zone worden gebruikt voor verschillende virtuele netwerken voor oplossing?

Ja. U kunt een privé-DNS-zone koppelen aan duizenden virtuele netwerken. Zie [Azure DNS limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) voor meer informatie

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Kan een virtueel netwerk dat tot een ander abonnement behoort, worden gekoppeld aan een privé zone?

Ja. U moet beschikken over de machtiging schrijf bewerking voor de virtuele netwerken en de particuliere DNS-zone. De schrijf machtiging kan worden toegekend aan verschillende Azure-rollen. Zo heeft de Azure-rol klassiek Network Inzender schrijf machtigingen voor virtuele netwerken en de rol Inzender voor Privé-DNS zones heeft schrijf machtigingen voor de privé-DNS-zones. Zie voor meer informatie over Azure-rollen [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Worden de automatisch geregistreerde DNS-records van virtuele machines in een privé zone automatisch verwijderd wanneer u de virtuele machine verwijdert?

Ja. Als u een virtuele machine in een gekoppeld virtueel netwerk verwijdert waarvoor automatische registratie is ingeschakeld, worden de geregistreerde records automatisch verwijderd.

## <a name="ive-reconfigured-the-os-in-my-virtual-machine-to-have-a-new-host-name-or-static-ip-address-why-dont-i-see-that-change-reflected-in-the-private-zone"></a>Ik heb het besturings systeem in mijn virtuele machine opnieuw geconfigureerd voor een nieuwe hostnaam of een statisch IP-adres. Waarom kan ik niet zien dat de wijziging wordt doorgevoerd in de privé zone?

De records van de privé zone worden ingevuld door de Azure DHCP-service. client registratie berichten worden genegeerd. Als u DHCP-client ondersteuning in de virtuele machine hebt uitgeschakeld door een statisch IP-adres te configureren, worden wijzigingen aan de hostnaam of het statische IP in de virtuele machine niet weer gegeven in de zone.

## <a name="i-have-configured-a-preferred-dns-suffix-in-my-windows-virtual-machine-why-are-my-records-still-registered-in-the-zone-linked-to-the-virtual-network"></a>Ik heb een voor Keurs-DNS-achtervoegsel geconfigureerd in mijn virtuele Windows-machine. Waarom worden mijn records nog steeds geregistreerd in de zone die is gekoppeld aan het virtuele netwerk?

De Azure DHCP-service negeert eventuele DNS-achtervoegsel wanneer het de privé-DNS-zone registreert. Als uw virtuele machine bijvoorbeeld is geconfigureerd `contoso.com` als het primaire DNS-achtervoegsel, maar het virtuele netwerk is gekoppeld aan de `fabrikam.com` privé-DNS-zone, wordt de registratie van de virtuele machine weer gegeven in de `fabrikam.com` privé-DNS-zone.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Kan een automatisch geregistreerde virtuele-machine record in een privé zone van een gekoppeld virtueel netwerk hand matig worden verwijderd?

Ja. U kunt de automatisch geregistreerde DNS-records overschrijven met een hand matig gemaakte DNS-record in de zone. In dit onderwerp worden de volgende vraag en dit antwoord adres beschreven.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Wat gebeurt er wanneer ik hand matig een nieuwe DNS-record wil maken in een persoonlijke zone die dezelfde hostnaam heeft als een automatisch geregistreerde bestaande virtuele machine in een gekoppeld virtueel netwerk?

U probeert hand matig een nieuwe DNS-record te maken in een persoonlijke zone die dezelfde hostnaam heeft als een bestaande, automatisch geregistreerde virtuele machine in een gekoppeld virtueel netwerk. Wanneer u dit doet, overschrijft de nieuwe DNS-record de automatisch geregistreerde virtuele-machine record. Als u probeert deze hand matig gemaakte DNS-record opnieuw uit de zone te verwijderen, slaagt de verwijdering. De automatische registratie gebeurt opnieuw zolang de virtuele machine nog bestaat en waaraan een persoonlijk IP-adres is gekoppeld. De DNS-record wordt automatisch opnieuw gemaakt in de zone.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Wat gebeurt er wanneer we een gekoppeld virtueel netwerk ontkoppelen van een privé zone? Worden de automatisch geregistreerde virtuele-machine records uit het virtuele netwerk ook uit de zone verwijderd?

Ja. Als u een gekoppeld virtueel netwerk wilt ontkoppelen van een privé zone, werkt u de DNS-zone bij om de gekoppelde virtuele netwerk koppeling te verwijderen. In dit proces worden de records van virtuele machines die automatisch zijn geregistreerd, uit de zone verwijderd.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Wat gebeurt er wanneer we een gekoppeld virtueel netwerk verwijderen dat is gekoppeld aan een privé zone? Moeten we de privé zone hand matig bijwerken om het virtuele netwerk als een gekoppeld virtueel netwerk van de zone te ontkoppelen?

Nee. Wanneer u een gekoppeld virtueel netwerk verwijdert zonder het eerst van een privé zone te ontkoppelen, wordt uw verwijderings bewerking voltooid en worden de koppelingen naar de DNS-zone automatisch gewist.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Werkt de DNS-omzetting met behulp van de standaard-FQDN (internal.cloudapp.net) nog steeds, zelfs wanneer een privé zone (bijvoorbeeld private.contoso.com) is gekoppeld aan een virtueel netwerk?

Ja. Persoonlijke zones vervangen de standaard internal.cloudapp.net-zone van Azure niet. Of u nu afhankelijk bent van de door Azure verschafte internal.cloudapp.net of uw eigen privé zone, gebruikt u de FQDN van de zone die u wilt oplossen.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Wordt het DNS-achtervoegsel op virtuele machines in een gekoppeld virtueel netwerk gewijzigd in dat van de privé zone?

Nee. Het DNS-achtervoegsel op de virtuele machines in het gekoppelde virtuele netwerk blijft als standaard achtervoegsel van Azure (' *. internal.cloudapp.net '). U kunt dit DNS-achtervoegsel hand matig wijzigen op uw virtuele machines naar dat van de privé zone.
Zie voor meer informatie over het wijzigen van dit achtervoegsel [dynamische DNS gebruiken om hostnamen te registreren in uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-ddns.md#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Wat zijn de gebruiks limieten voor Azure DNS privé zones?

Raadpleeg [Azure DNS limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits) voor meer informatie over de gebruiks limieten voor Azure DNS persoonlijke zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Waarom worden mijn bestaande privé-DNS-zones niet weer gegeven in de nieuwe portal?

Als uw bestaande privé-DNS-zone is gemaakt met behulp van preview-API, moet u deze zones migreren naar een nieuw resource model. Privé-DNS zones die zijn gemaakt met de preview-API, worden niet weer gegeven in de nieuwe portal-ervaring. Hieronder vindt u instructies voor het migreren naar een nieuw resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hoe kan ik mijn bestaande privé-DNS-zones naar het nieuwe model migreren?

We raden u ten zeerste aan om zo snel mogelijk naar het nieuwe resource model te migreren. Verouderd resource model wordt ondersteund, maar er worden echter geen verdere functies op dit model ontwikkeld. In de toekomst nemen we de bedoeling af om het nieuwe resource model voor te nemen. Zie[migratie handleiding voor Azure DNS particuliere zones](private-dns-migration-guide.md)voor meer informatie over het migreren van uw bestaande privé-DNS-zones naar een nieuw resource model.

### <a name="does-azure-dns-private-zones-store-any-customer-content"></a>Slaat Azure DNS privé zones eventuele klant inhoud op?

Nee, Azure DNS persoonlijke zones slaan geen klant inhoud op.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Privé-DNS](private-dns-overview.md)
