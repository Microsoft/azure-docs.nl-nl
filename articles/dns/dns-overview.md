---
title: Wat is Azure DNS?
description: Overzicht van DNS-hostingservice op Microsoft Azure. Host uw domein op Microsoft Azure.
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 3/21/2019
ms.author: rohink
ms.openlocfilehash: 5df2c2f686a1993dcd25d5391d5b553f2b42d937
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965643"
---
# <a name="what-is-azure-dns"></a>Wat is Azure DNS?

Azure DNS is een hostingservice voor DNS-domeinen die naamomzetting biedt met behulp van Microsoft Azure-infrastructuur. Door uw domeinen in Azure te hosten, kunt u uw DNS-records met dezelfde referenties, API's, hulpprogramma's en facturering beheren als voor uw andere Azure-services.

U kunt Azure DNS niet gebruiken om een ​​domeinnaam te kopen. Tegen een jaarlijkse vergoeding kunt u een domeinnaam kopen met behulp van [App Service-domeinen](../app-service/manage-custom-dns-buy-domain.md#buy-the-domain) of bij een externe domeinnaamregistrar. Uw domeinen kunnen vervolgens worden gehost in Azure DNS voor recordbeheer. Zie [Delegate a domain to Azure DNS](dns-domain-delegation.md) (Een domein aan Azure DNS overdragen) voor meer informatie.

De volgende functies zijn opgenomen in Azure DNS.

## <a name="reliability-and-performance"></a>Betrouwbaarheid en prestaties

DNS-domeinen in Azure DNS worden gehost op het wereldwijde netwerk van Azure DNS-naamservers. Azure DNS maakt gebruik van anycast-netwerken. Elke DNS-query wordt beantwoord door de dichtstbijzijnde DNS-server voor het leveren van snelle prestaties en hoge beschikbaarheid voor uw domein.

## <a name="security"></a>Beveiliging

 Azure DNS is gebaseerd op Azure Resource Manager, die onder meer de volgende functies biedt:

* [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../azure-resource-manager/management/overview.md) om te bepalen wie er toegang heeft tot bepaalde acties voor uw organisatie.

* [Activiteitenlogboeken](../azure-resource-manager/management/overview.md) om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd, of te gebruiken bij het opsporen van fouten.

* [vergrendelen van resources](../azure-resource-manager/management/lock-resources.md) om een abonnement, een resourcegroep of een resource te vergrendelen. Met vergrendeling voorkomt u dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen wijzigen of verwijderen.

Zie voor meer informatie [DNS-zones en -records beschermen](dns-protect-zones-recordsets.md). 

## <a name="dnssec"></a>DNSSEC

Azure DNS ondersteunt momenteel geen DNSSEC. In de meeste gevallen kunt u de behoefte aan DNSSEC verminderen door steeds HTTPS/TLS te gebruiken in uw toepassingen. Als DNSSEC een essentiële vereiste is voor uw DNS-zones, kunt u deze zones hosten met externe DNS-hostingproviders.

## <a name="ease-of-use"></a>Gebruiksgemak

 Azure DNS kan DNS-records voor uw Azure-services beheren en ook DNS leveren aan uw externe resources. Azure DNS is geïntegreerd in Azure Portal en gebruikt dezelfde referenties, hetzelfde ondersteuningscontract en dezelfde facturering als uw andere Azure-services. 

De facturering van DNS vindt plaats op basis van het aantal DNS-zones dat in Azure wordt gehost en het aantal DNS-query's dat wordt ontvangen. Zie [Prijzen voor Azure DNS](https://azure.microsoft.com/pricing/details/dns/) voor meer informatie over prijzen.

Uw domeinen en records kunnen worden beheerd met behulp van de Azure-portal, Azure PowerShell-cmdlets en de platformoverschrijdende Azure CLI. Toepassingen waarvoor geautomatiseerd DNS-beheer is vereist, kunnen met de-service worden geïntegreerd met behulp van de REST API en SDK's.

## <a name="customizable-virtual-networks-with-private-domains"></a>Aanpasbare virtuele netwerken met privédomeinen

Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen. Met deze functie kunt u uw eigen aangepaste domeinnamen gebruiken in uw virtuele privénetwerken in plaats van de door Azure geleverde namen die momenteel beschikbaar zijn.

Zie [Azure DNS gebruiken voor privédomeinen](private-dns-overview.md) voor meer informatie.

## <a name="alias-records"></a>Aliasrecords

Azure DNS ondersteunt alias-recordsets. U kunt een alias-recordset gebruiken om te verwijzen naar een Azure-resource, zoals een openbaar IP-adres van Azure, een Azure Traffic Manager-profiel of een Azure CDN-eindpunt (CDN: Content Delivery Network). Als het IP-adres van de onderliggende resource verandert, wordt de alias-recordset automatisch bijgewerkt tijdens DNS-omzetting. De alias-record set verwijst naar het service-exemplaar en het service-exemplaar is gekoppeld aan een IP-adres.

U kunt nu ook uw apex of domein zonder voorvoegsel laten verwijzen naar een Traffic Manager-profiel of CDN-eindpunt met behulp van een alias-record. Bijvoorbeeld: contoso.com.

Zie [Overzicht van Azure DNS-aliasrecords](dns-alias.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-zones en records.

* Zie [Een DNS-zone maken](./dns-getstarted-portal.md) voor meer informatie over het maken van een zone in Azure DNS.

* Zie voor veelgestelde vragen over Azure DNS de [Veelgestelde vragen over Azure DNS](dns-faq.md).