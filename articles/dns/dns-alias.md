---
title: Overzicht van alias records-Azure DNS
description: In dit artikel vindt u meer informatie over ondersteuning voor alias records in Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954474"
---
# <a name="azure-dns-alias-records-overview"></a>Overzicht van Azure DNS-aliasrecords

Azure DNS alias records zijn kwalificaties voor een DNS-Recordset. Ze kunnen verwijzen naar andere Azure-resources vanuit uw DNS-zone. U kunt bijvoorbeeld een aliasset maken die verwijst naar een openbaar IP-adres van Azure in plaats van een record. De set met alias records verwijst dynamisch naar een service-exemplaar van het open bare IP-adres van Azure. Als gevolg hiervan wordt de alias record naadloos bijgewerkt tijdens de DNS-omzetting.

Een aliasset wordt ondersteund voor de volgende record typen in een Azure DNS zone: 

- A
- AAAA
- CNAME

> [!NOTE]
> Als u van plan bent een alias record te gebruiken voor de A-of AAAA-record typen om te verwijzen naar een [Azure Traffic Manager-profiel](../traffic-manager/quickstart-create-traffic-manager-profile.md) , moet u ervoor zorgen dat het Traffic Manager profiel alleen [externe eind punten](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints)heeft. U moet het IPv4-of IPv6-adres opgeven voor externe eind punten in Traffic Manager. U kunt geen volledig gekwalificeerde domein namen (FQDN) in eind punten gebruiken. In het ideale geval gebruikt u statische IP-adressen.

## <a name="capabilities"></a>Functies

- **Wijs naar een open bare IP-bron van een DNS A/AAAA-Recordset.** U kunt een A/AAAA-recordset maken en hiervan een alias instellen om te verwijzen naar een open bare IP-resource (standaard of basis). De DNS-record sets worden automatisch gewijzigd als het open bare IP-adres wordt gewijzigd of verwijderd. Dangling DNS-records die verwijzen naar onjuiste IP-adressen, worden vermeden.

   Er is een huidige limiet van 20 alias record sets per resource.

- **Ga naar een Traffic Manager profiel van een DNS A/AAAA/CNAME-Recordset.** U kunt een A/AAAA-of CNAME-recordset maken en alias records gebruiken om deze naar een Traffic Manager profiel te wijzen. Het is vooral handig wanneer u verkeer moet routeren op een zone Apex, omdat traditionele CNAME-records niet worden ondersteund voor een zone Apex. Stel bijvoorbeeld dat uw Traffic Manager profiel myprofile.trafficmanager.net is en dat uw bedrijfs-DNS-zone contoso.com is. U kunt een alias verzameling van het type A/AAAA voor contoso.com (de zone Apex) maken en naar myprofile.trafficmanager.net verwijzen.
- **Ga naar een Azure Content Delivery Network-eind punt (CDN)**. Dit is handig wanneer u statische websites maakt met behulp van Azure Storage en Azure CDN.
- **Wijs naar een andere DNS-record die binnen dezelfde zone is ingesteld.** Aliasrecords kunnen naar andere recordsets van hetzelfde type verwijzen. Een DNS CNAME-recordset kan bijvoorbeeld een alias zijn voor een andere CNAME-Recordset. Deze indeling is handig als u wilt dat sommige record sets aliassen en bepaalde niet-aliassen hebben.

## <a name="scenarios"></a>Scenario's

Er zijn enkele algemene scenario's voor alias records.

### <a name="prevent-dangling-dns-records"></a>Dangling DNS-records voor komen

Een veelvoorkomend probleem met traditionele DNS-records is Dangling records. Bijvoorbeeld DNS-records die niet zijn bijgewerkt met wijzigingen in IP-adressen. Het probleem treedt met name op met een/AAAA-of CNAME-record type.

Als het doel-IP-adres of de CNAME-record niet meer bestaat, moet de DNS-record die is gekoppeld aan de zone, hand matig worden bijgewerkt. In sommige organisaties kan een hand matige update mogelijk niet in de tijd plaatsvinden vanwege proces problemen of de schei ding van rollen en de bijbehorende machtigings niveaus. Een rol kan bijvoorbeeld de bevoegdheid hebben om een CNAME-of IP-adres te verwijderen dat bij een toepassing hoort. Maar er is niet voldoende autoriteit voor het bijwerken van de DNS-record die verwijst naar die doelen. Een vertraging bij het bijwerken van de DNS-record kan leiden tot een onderbreking van de gebruikers.

Alias records verhinderen Dangling-verwijzingen door de levens cyclus van een DNS-record met een Azure-resource nauw keurig te koppelen. Denk bijvoorbeeld aan een DNS-record die is gekwalificeerd als alias record om te verwijzen naar een openbaar IP-adres of een Traffic Manager profiel. Als u deze onderliggende bronnen verwijdert, wordt de DNS-alias record een lege recordset. Er wordt niet langer verwezen naar de verwijderde resource.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>DNS-record bijwerken: automatisch instellen wanneer IP-adressen van toepassingen worden gewijzigd

Dit scenario is vergelijkbaar met het voor gaande. Mogelijk wordt een toepassing verplaatst of wordt de onderliggende virtuele machine opnieuw opgestart. Een alias record wordt vervolgens automatisch bijgewerkt wanneer het IP-adres wordt gewijzigd voor de onderliggende open bare IP-resource. Dit voor komt mogelijke beveiligings Risico's van het omleiden van de gebruikers naar een andere toepassing waaraan het oude open bare IP-adres is toegewezen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Toepassingen met taak verdeling hosten op de zone Apex

Het DNS-protocol voor komt dat de toewijzing van CNAME-records op de zone Apex. Als uw domein bijvoorbeeld contoso.com is; u kunt CNAME-records maken voor somelabel.contoso.com; maar u kunt geen CNAME maken voor contoso.com zelf.
Deze beperking geeft een probleem met de eigen aren van toepassingen die toepassingen met taak verdeling achter [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)hebben. Omdat het gebruik van een Traffic Manager profiel het maken van een CNAME-record vereist, is het niet mogelijk om naar het Traffic Manager profiel van de zone Apex te verwijzen.

Dit probleem wordt opgelost met behulp van alias records. In tegens telling tot CNAME-records worden alias records gemaakt op de zone Apex en de eigen aren van toepassingen kunnen deze gebruiken om hun zone Apex-record te laten verwijzen naar een Traffic Manager profiel met externe eind punten. Toepassings eigenaren verwijzen naar hetzelfde Traffic Manager-profiel dat wordt gebruikt voor elk ander domein binnen hun DNS-zone.

Contoso.com en www \. contoso.com kunnen bijvoorbeeld verwijzen naar hetzelfde Traffic Manager-profiel. Zie de sectie volgende stappen voor meer informatie over het gebruik van alias records met Azure Traffic Manager-profielen.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Punt zone Apex naar Azure CDN-eind punten

Net als bij een Traffic Manager profiel kunt u ook alias records gebruiken om uw DNS-zone te laten verwijzen naar Azure CDN-eind punten. Dit is handig wanneer u statische websites maakt met behulp van Azure Storage en Azure CDN. U kunt vervolgens toegang krijgen tot de website zonder ' www ' in behandeling te nemen in uw DNS-naam.

Als uw statische website bijvoorbeeld een naam heeft `www.contoso.com` , hebben uw gebruikers toegang tot uw site met behulp van `contoso.com` zonder de nood zaak om www-laten voorafgaan door te maken met de DNS-naam.

Zoals eerder beschreven, worden CNAME-records niet ondersteund op de zone Apex. U kunt dus geen CNAME-record gebruiken om contoso.com te verwijzen naar uw CDN-eind punt. In plaats daarvan kunt u een alias record gebruiken om de zone rechtstreeks naar een CDN-eind punt te wijzen.

> [!NOTE]
> Het is niet mogelijk om een zone toe te wijzen aan CDN-eind punten voor Azure CDN van Akamai.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over alias records:

- [Zelfstudie: Een aliasrecord zo configureren dat deze naar een openbaar Azure-IP-adres verwijst](tutorial-alias-pip.md)
- [Zelfstudie: een Azure DNS-aliasrecord configureren om het gebruik van hoofddomeinnaam met Traffic Manager te ondersteunen](tutorial-alias-tm.md)
- [Veelgestelde vragen over DNS](./dns-faq.md#alias-records)