---
title: Verbindings-en netwerkproblemen
description: In dit artikel vindt u de veelgestelde vragen over connectiviteit en netwerken voor Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c7b83c615e4ac19e10b5c4f6cc1a102206b1a39a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742417"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Problemen met de verbinding en het netwerk voor Azure Cloud Services (klassiek): veelgestelde vragen (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel vindt u veelgestelde vragen over verbindings-en netwerk problemen voor [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Zie de [pagina Cloud Services VM-grootte](cloud-services-sizes-specs.md)voor informatie over de grootte.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Ik kan geen IP-adres reserveren in een Cloud service met meerdere VIP'S.
Zorg er eerst voor dat het exemplaar van de virtuele machine waarvoor u het IP-adres wilt reserveren, is ingeschakeld. Ten tweede moet u ervoor zorgen dat u gereserveerde Ip's gebruikt voor zowel de fase ring als de productie-implementatie. Wijzig de instellingen *niet* wanneer de implementatie wordt bijgewerkt.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hoe kan ik gebruiken Extern bureaublad wanneer ik een NSG heb?
Voeg regels toe aan de NSG die verkeer toestaan op de poorten **3389** en **20000**. Extern bureaublad maakt gebruik van poort **3389**. Cloud service-exemplaren worden gelijkmatig verdeeld, zodat u niet rechtstreeks kunt bepalen met welk exemplaar verbinding moet worden gemaakt. De *RemoteForwarder* -en *remoteaccess* -agents beheren Remote Desktop Protocol verkeer (RDP) en toestaan dat de client een RDP-cookie verzendt en een afzonderlijk exemplaar opgeeft waarmee verbinding moet worden gemaakt. Voor de *RemoteForwarder* -en *remoteaccess* -agents moet het poort **20000** open zijn. Dit kan worden geblokkeerd als u een NSG hebt.

## <a name="can-i-ping-a-cloud-service"></a>Kan ik een Cloud service pingen?

Nee, niet met behulp van het normale ' ping '/ICMP-protocol. Het ICMP-protocol is niet toegestaan via de Azure-load balancer.

Als u de connectiviteit wilt testen, raden we u aan een poort ping uit te voeren. Hoewel Ping.exe ICMP gebruikt, kunt u andere hulpprogram ma's, zoals PSPing, nmap en Telnet, gebruiken om de connectiviteit met een specifieke TCP-poort te testen.

Zie voor meer informatie [poort pingen gebruiken in plaats van ICMP om Azure VM-connectiviteit te testen](/archive/blogs/mast/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hoe kan ik geen duizenden treffers ontvangen van onbekende IP-adressen die kunnen wijzen op een schadelijke aanval van de Cloud service?
Azure implementeert een netwerk beveiliging met meerdere lagen om de platform services te beschermen tegen gedistribueerde Denial-of-service-aanvallen (DDoS). Het Azure DDoS-verdedigings systeem maakt deel uit van het doorlopende bewakings proces van Azure, dat voortdurend wordt verbeterd door indringings tests. Dit DDoS-verdedigings systeem is zodanig ontworpen dat het niet alleen aanvallen van de buiten kant is, maar ook van andere Azure-tenants. Zie [Azure-netwerk beveiliging](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)voor meer informatie.

U kunt ook een opstart taak maken om specifieke IP-adressen selectief te blok keren. Zie [een specifiek IP-adres blok keren](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)voor meer informatie.

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Wanneer ik RDP naar mijn Cloud service-exemplaar probeer, krijg ik het bericht ' de gebruikers account is verlopen '.
Mogelijk wordt het fout bericht ' dit gebruikers account is verlopen ' weer gegeven wanneer u de verloop datum die in uw RDP-instellingen is geconfigureerd, omzeilt. U kunt de verval datum wijzigen via de portal door de volgende stappen uit te voeren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com), ga naar uw Cloud service en selecteer het tabblad **extern bureaublad** .

2. Selecteer de implementatie site voor **productie** of **fase ring** .

3. Wijzig de **verloopt op** datum en sla de configuratie vervolgens op.

U moet nu RDP-verbinding met uw computer kunnen maken.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Waarom wordt het verkeer niet gelijkmatig verdeeld met Azure Load Balancer?
Zie [Azure Load Balancer nieuwe distributie modus](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)voor meer informatie over de werking van een interne Load Balancer.

Het gebruikte distributie algoritme is een hash van 5-tuple (bron-IP, bron poort, doel-IP, doel poort en protocol type) om verkeer toe te wijzen aan beschik bare servers. Het biedt persistentie alleen binnen een transport sessie. Pakketten in dezelfde TCP-of UDP-sessie worden omgeleid naar dezelfde instantie van het Data Center IP (DIP) achter het eind punt met gelijke taak verdeling. Wanneer de client de verbinding sluit en opnieuw opent of een nieuwe sessie van hetzelfde bron-IP-adres start, wordt de bron poort gewijzigd en wordt het verkeer naar een ander DIP-eind punt gegaan.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hoe kan ik binnenkomend verkeer naar de standaard-URL van mijn Cloud service omleiden naar een aangepaste URL?

De module voor het herschrijven van URL'S van IIS kan worden gebruikt om verkeer dat wordt omgeleid naar de standaard-URL voor de Cloud service (bijvoorbeeld \* . cloudapp.net), om te leiden naar een aangepaste naam/URL. Omdat de module voor het herschrijven van URL'S standaard is ingeschakeld op webrollen en de bijbehorende regels worden geconfigureerd in de web.config van de toepassing, is deze altijd beschikbaar op de virtuele machine, ongeacht het opnieuw opstarten/terugzetten van installatie kopieën. Zie voor meer informatie:

- [Herschrijf regels maken voor de module voor het herschrijven van URL'S](/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Een standaard koppeling verwijderen](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hoe kan ik binnenkomend verkeer naar de standaard-URL van mijn Cloud service blok keren/uitschakelen?

U kunt voor komen dat binnenkomend verkeer naar de standaard-URL/naam van uw Cloud service (bijvoorbeeld \* . cloudapp.net). Stel de host-header in op een aangepaste DNS-naam (bijvoorbeeld www- \. MyCloudService.com) onder configuratie van site binding in het bestand met de Cloud service definition (*. csdef), zoals aangegeven:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Omdat deze host-header binding wordt afgedwongen via het csdef-bestand, is de service alleen toegankelijk via de aangepaste naam ' www.MyCloudService.com '. Alle binnenkomende aanvragen naar het domein ' *. cloudapp.net ' zijn altijd mislukt. Als u een aangepaste SLB-test of een interne load balancer in de service gebruikt, kan het blok keren van de standaard-URL/naam van de service leiden tot problemen met het gedrag van de bewerking.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hoe kan ik ervoor zorgen dat het open bare IP-adres van een Cloud service nooit verandert?

Om ervoor te zorgen dat het open bare IP-adres van uw Cloud service (ook wel een VIP genoemd) nooit wordt gewijzigd, zodat het op een manier kan worden goedgekeurd door enkele specifieke clients, wordt u aangeraden een gereserveerde IP te koppelen. Anders wordt de toewijzing van het virtuele IP-adres van Azure ongedaan gemaakt voor uw abonnement als u de implementatie verwijdert. Voor een geslaagde VIP-wissel bewerking hebt u afzonderlijke gereserveerde IP-adressen nodig voor productie-en staging-sleuven. Zonder deze, mislukt de wissel bewerking. Als u een IP-adres wilt reserveren en dit aan uw Cloud service wilt koppelen, raadpleegt u de volgende artikelen:

- [Het IP-adres van een bestaande Cloud service reserveren](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Een gereserveerd IP-adres koppelen aan een Cloud service met behulp van een service configuratie bestand](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Als u meer dan één exemplaar voor uw rollen hebt, kan het koppelen van RIP met uw Cloud service geen uitval tijd veroorzaken. U kunt ook het IP-adres bereik van uw Azure-Data Center toevoegen aan een acceptatie lijst. U kunt alle Azure IP-bereiken vinden in het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Dit bestand bevat de IP-adresbereiken (met inbegrip van compute-, SQL-en opslag bereik) die worden gebruikt in azure-data centers. Er wordt wekelijks een bijgewerkt bestand geplaatst met de huidige geïmplementeerde bereiken en eventuele toekomstige wijzigingen in de IP-bereiken. Nieuwe bereiken die in het bestand worden weer gegeven, worden gedurende ten minste één week niet gebruikt in de data centers. Down load elke week het nieuwe. XML-bestand en voer de benodigde wijzigingen op uw site uit om de services die in Azure worden uitgevoerd correct te identificeren. Gebruikers van Azure ExpressRoute kunnen merken dat dit bestand wordt gebruikt om de BGP-advertisement van Azure Space bij te werken in de eerste week van elke maand.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hoe kan ik Azure Resource Manager virtuele netwerken met Cloud Services gebruiken?

Cloud Services kunnen niet in Azure Resource Manager virtuele netwerken worden geplaatst. Virtuele netwerken van Resource Manager en klassieke implementatie kunnen worden verbonden via peering. Zie [peering van virtuele netwerken](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hoe krijg ik de lijst met open bare IP-adressen die worden gebruikt door mijn Cloud Services?

U kunt het volgende PS-script gebruiken om de lijst met open bare IP-adressen voor Cloud Services te verkrijgen onder uw abonnement

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```