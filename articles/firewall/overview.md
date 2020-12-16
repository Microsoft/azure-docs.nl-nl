---
title: Wat is Azure Firewall?
description: Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc, contperf-fy21q1
ms.date: 12/03/2020
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: 3bfc8704919fc26db692701eaca526dd5c333b6f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033490"
---
# <a name="what-is-azure-firewall"></a>Wat is Azure Firewall?

![ICSA-certificering](media/overview/icsa-cert-firewall-small.png)

Azure Firewall is een beheerde, cloudgebaseerde netwerkbeveiligingsservice die uw Azure Virtual Network-resources beschermt. Het is een volledige stateful firewall als een service met ingebouwde hoge beschikbaarheid en onbeperkte cloudschaalbaarheid.

![Firewalloverzicht](media/overview/firewall-threat.png)

U kunt beleid voor toepassings- en netwerkconnectiviteit centraal maken, afdwingen en registreren voor abonnementen en virtuele netwerken. Azure Firewall maakt gebruik van een statisch openbaar IP-adres voor uw virtuele-netwerkresources zodat externe firewalls verkeer dat afkomstig is van uw virtuele netwerk kunnen identificeren.  De service is volledig geïntegreerd met Azure Monitor voor registratie en analyses.

## <a name="features"></a>Functies

Zie [Azure Firewall-functies](features.md) voor meer informatie over Azure Firewall-functies.

## <a name="pricing-and-sla"></a>Prijzen en SLA

Zie [Azure Firewall-prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/) voor informatie over Azure Firewall-prijzen.

Zie [SLA voor Azure Firewall](https://azure.microsoft.com/support/legal/sla/azure-firewall/) voor informatie over de SLA voor Azure Firewall.

## <a name="whats-new"></a>Nieuwe functies

Zie [Azure-updates](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Firewall) om te ontdekken wat er nieuw is in Azure Firewall.


## <a name="known-issues"></a>Bekende problemen

Azure Firewall heeft de volgende bekende problemen:

|Probleem  |Beschrijving  |Oplossing  |
|---------|---------|---------|
Netwerkfilterregels voor niet-TCP/UDP-protocollen (bijvoorbeeld ICMP) werken niet voor internetverkeer|Netwerkfilterregels voor niet-TCP/UDP-protocollen werken niet met SNAT naar uw openbare IP-adres. Niet-TCP/UDP-protocollen worden ondersteund tussen spoke-subnetten en VNets.|Azure Firewall maakt gebruik van de standaardversie van Standard Load Balancer, [die momenteel geen ondersteuning biedt voor SNAT voor IP-protocollen](../load-balancer/load-balancer-overview.md). We onderzoeken mogelijkheden om dit scenario in een toekomstige release te ondersteunen.|
|Ontbrekende PowerShell- en CLI-ondersteuning voor ICMP|Azure PowerShell en CLI bieden geen ondersteuning voor ICMP als een geldig protocol in netwerkregels.|Het is nog steeds mogelijk om ICMP als een protocol te gebruiken via de portal en de REST-API. Er wordt aan gewerkt om ICMP binnenkort toe te voegen in PowerShell en CLI.|
|FQDN-tags vereisen instelling van een protocol: poort|Voor toepassingsregels met FQDN-tags is definitie van poort: protocol vereist.|U kunt **https** gebruiken als de waarde voor poort:protocol. Er wordt aan gewerkt om dit veld optioneel te maken wanneer FQDN-tags worden gebruikt.|
|Het verplaatsen van een firewall naar een andere resourcegroep of een ander abonnement wordt niet ondersteund|Het verplaatsen van een firewall naar een andere resourcegroep of een ander abonnement wordt niet ondersteund.|Ondersteuning van deze functionaliteit staat op de planning. Om een firewall naar een andere resourcegroep of ander abonnement verplaatsen, moet u het huidige exemplaar verwijderen en deze vervolgens opnieuw maken in de nieuwe resourcegroep of het nieuwe abonnement.|
|Waarschuwingen met betrekking tot bedreigingsinformatie worden mogelijk gemaskeerd|Netwerkregels met bestemming 80/443 voor uitgaande filtering maskeren bedreigingsinformatiewaarschuwingen wanneer deze zijn geconfigureerd voor de modus alleen-waarschuwingen.|Maak uitgaande filters voor 80/443 met behulp van toepassingsregels. U kunt ook de modus voor bedreigingsinformatie wijzigen in **Waarschuwen en weigeren**.|
|Azure Firewall DNAT werkt niet voor privé-IP-doelen|DNAT-ondersteuning van Azure Firewall is beperkt tot inkomend/uitgaand internetverkeer. DNAT werkt momenteel niet voor privé-IP-doelen. Bijvoorbeeld, spoke naar spoke.|Dit is een huidige beperking.|
|Kan de eerste openbare IP-configuratie niet verwijderen|Elk openbaar IP-adres van Azure Firewall wordt toegewezen aan een *IP-configuratie*.  De eerste IP-configuratie wordt toegewezen tijdens de implementatie van de firewall en bevat doorgaans een verwijzing naar het subnet van de firewall (tenzij expliciet anders is geconfigureerd via een sjabloonimplementatie). U kunt deze IP-configuratie niet verwijderen omdat hiermee de toewijzing van de firewall ongedaan wordt gemaakt. U kunt het openbare IP-adres dat is gekoppeld aan deze IP-configuratie nog steeds wijzigen of verwijderen als de firewall over ten minste één ander openbaar IP-adres beschikt.|Dit is standaard.|
|Beschikbaarheidszones kunnen alleen worden geconfigureerd tijdens de implementatie.|Beschikbaarheidszones kunnen alleen worden geconfigureerd tijdens de implementatie. U kunt Beschikbaarheidszones niet configureren nadat er een firewall is geïmplementeerd.|Dit is standaard.|
|SNAT op inkomende verbindingen|Naast DNAT worden verbindingen via het openbare IP-adres van de firewall (inkomend) met SNAT omgezet naar een van de privé-IP's van de firewall. Deze vereiste is (ook voor Actieve/Actieve NVA's) om symmetrische routering te garanderen.|Als u de oorspronkelijke bron voor HTTP/S wilt behouden, kunt u [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For)-headers gebruiken. Gebruik bijvoorbeeld een service als [Azure Front Door](../frontdoor/front-door-http-headers-protocol.md#front-door-to-backend) of [Azure Application Gateway](../application-gateway/rewrite-http-headers.md) vóór de firewall. U kunt ook WAF toevoegen als onderdeel van Azure Front Door en ketenen aan de firewall.
|Ondersteuning voor SQL FQDN-filtering alleen in proxymodus (poort 1433)|Voor Azure SQL Database, Azure Synapse Analytics en Azure SQL Managed Instance:<br><br>Filteren met SQL FQDN wordt alleen ondersteund in de proxymodus (poort 1433).<br><br>Voor Azure SQL IaaS:<br><br>Als u werkt met niet-standaard poorten, kunt u die poorten opgeven in de toepassingsregels.|Voor SQL in de omleidingsmodus (de standaardinstelling als u verbinding maakt vanuit Azure), kunt u in plaats daarvan de toegang filteren met behulp van de SQL-servicetag als onderdeel van Azure Firewall-netwerkregels.
|Uitgaand verkeer op TCP-poort 25 is niet toegestaan| Uitgaande SMTP-verbindingen die gebruikmaken van TCP-poort 25 worden geblokkeerd. Poort 25 wordt hoofdzakelijk gebruikt voor niet-geverifieerde e-mailbezorging. Dit is het standaardplatformgedrag voor virtuele machines. Raadpleeg [Problemen met uitgaande SMTP-connectiviteit oplossen in Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md) voor meer informatie. Het is momenteel echter niet mogelijk om deze functionaliteit in te schakelen op Azure Firewall, in tegenstelling tot op virtuele machines. Opmerking: Als u geverifieerde SMTP (poort 587) of SMTP wilt toestaan via een andere poort dan 25, moet u een netwerkregel configureren en geen toepassingsregel, omdat SMTP-inspectie momenteel niet wordt ondersteund.|Volg de aanbevolen methode voor het verzenden van e-mailberichten, zoals beschreven in het artikel Problemen oplossen met SMTP. U kunt de virtuele machine waarvoor uitgaande SMTP-toegang nodig is van uw standaardroute naar de firewall ook uitsluiten. In plaats daarvan configureert u uitgaande toegang rechtstreeks naar internet.
|Actieve FTP wordt niet ondersteund|Actieve FTP is uitgeschakeld op Azure Firewall om te beschermen tegen aanvallen met FTP-beveiliging met de opdracht FTP PORT.|U kunt in plaats daarvan passieve FTP gebruiken. U moet nog steeds expliciet TCP-poorten 20 en 21 openen op de firewall.
|De metrische gegevens over het SNAT-poortgebruik tonen 0%|De metrische gegevens over het Azure Firewall SNAT-poortgebruik kunnen 0% gebruik aangeven, zelfs wanneer er een SNAT-poort wordt gebruikt. In dit geval biedt het gebruik van metrische gegevens als onderdeel van de metrische gegevens van de firewallstatus een onjuist resultaat.|Dit probleem is opgelost en de implementatie in productie is beoogd voor mei 2020. In sommige gevallen wordt het probleem opgelost door de firewall opnieuw te implementeren, maar dat is niet consistent. Als een tussenliggende tijdelijke oplossing kunt u alleen de status van de firewall gebruiken om te zoeken naar *status=verslechterd*, niet naar *status=slecht*. Poortuitputting wordt weergegeven als *verslechterd*. *Niet in orde* is gereserveerd voor toekomstig gebruik wanneer er meer metrische gegevens zijn om de firewallstatus te beïnvloeden.
|DNAT wordt niet ondersteund als geforceerde tunneling is ingeschakeld|Firewalls die zijn geïmplementeerd met geforceerde tunneling, bieden geen ondersteuning voor inkomende toegang via internet vanwege asymmetrische routering.|Dit is standaard vanwege asymmetrische routering. Het retourtraject voor binnenkomende verbindingen gaat via de on-premises firewall, waarvoor geen verbinding is gemaakt.
|Uitgaande passieve FTP werkt mogelijk niet voor firewalls met meerdere openbare IP-adressen, afhankelijk van de configuratie van uw FTP-server.|Passieve FTP brengt verschillende verbindingen tot stand voor controle- en gegevenskanalen. Wanneer een firewall met meerdere openbare IP-adressen gegevens uitgaand verzendt, wordt willekeurig een van de openbare IP-adressen geselecteerd voor het bron-IP-adres. FTP kan mislukken wanneer gegevens- en besturingskanalen gebruikmaken van verschillende bron-IP-adressen, afhankelijk van de configuratie van uw FTP-server.|Er wordt een expliciete SNAT-configuratie gepland. In de tussentijd kunt u uw FTP-server zo configureren dat gegevens- en besturingskanalen van verschillende bron-IP-adressen worden geaccepteerd (bekijk [een voorbeeld voor IIS](/iis/configuration/system.applicationhost/sites/sitedefaults/ftpserver/security/datachannelsecurity)). U kunt in deze situatie ook één IP-adres gebruiken.|
|Inkomende passieve FTP werkt mogelijk niet, afhankelijk van de configuratie van uw FTP-server |Passieve FTP brengt verschillende verbindingen tot stand voor controle- en gegevenskanalen. Binnenkomende verbindingen op Azure Firewall worden met SNAT omgezet op een van de privé IP-adressen van de firewall om symmetrische routering te garanderen. FTP kan mislukken wanneer gegevens- en besturingskanalen gebruikmaken van verschillende bron-IP-adressen, afhankelijk van de configuratie van uw FTP-server.|Behoud van het oorspronkelijke bron-IP-adres wordt onderzocht. In de tussentijd kunt u uw FTP-server zo configureren dat gegevens- en besturingskanalen van verschillende bron-IP-adressen worden geaccepteerd.|
|Er ontbreekt een protocoldimensie in de metrische gegevens voor NetworkRuleHit|Met de metrische waarde ApplicationRuleHit kunt u filteren op basis van een protocol, maar deze mogelijkheid ontbreekt in de bijbehorende metrische gegevens voor NetworkRuleHit.|Er wordt een oplossing onderzocht.|
|NAT-regels met poorten tussen 64000 en 65535 worden niet ondersteund|Azure Firewall staat elke poort toe in het bereik 1-65535 toe in netwerk- en toepassingsregels, maar NAT-regels ondersteunen alleen poorten in het bereik van 1-63999.|Dit is een huidige beperking.
|Configuratie-updates kunnen gemiddeld vijf minuten duren|Een Azure Firewall-configuratie-update kan gemiddeld drie tot vijf minuten duren en parallelle updates worden niet ondersteund.|Er wordt een oplossing onderzocht.|
|Azure Firewall gebruikt SNI TLS-headers om HTTPS- en MSSQL-verkeer te filteren|Als de browser- of serversoftware de SNI-extensie (Server Name Indicator) niet ondersteunt, kunt u geen verbinding maken via Azure Firewall.|Als de browser- of serversoftware geen ondersteuning biedt voor SNI, kunt u de verbinding mogelijk beheren met een netwerkregel in plaats van een toepassingsregel. Raadpleeg [Servernaamindicatie](https://wikipedia.org/wiki/Server_Name_Indication) voor software die SNI ondersteunt.|
|Aangepaste DNS werkt niet met geforceerde tunneling|Als geforceerde tunneling is ingeschakeld, werkt aangepaste DNS niet.|Er wordt een oplossing onderzocht.|
|Ondersteuning voor nieuwe openbare IP-adressen voor meerdere beschikbaarheidszones|U kunt geen nieuw openbaar IP-adres toevoegen wanneer u een firewall met twee beschikbaarheidszones implementeert (1 en 2, 2 en 3, of 1 en 3)|Dit is een beperking voor het openbare IP-adres.|
|Starten/stoppen werkt niet met een firewall die is geconfigureerd in de modus geforceerde tunnel|Starten/stoppen werkt niet met een Azure-firewall die is geconfigureerd in de modus geforceerde tunnel. Eem poging om Azure Firewall te starten met geconfigureerde geforceerde tunneling resulteert in de volgende fout:<br><br>*Set-AzFirewall: De IP-configuratie van AzureFirewall FW-xx-beheer kan niet worden toegevoegd aan een bestaande firewall. Implementeer opnieuw met een beheer-IP-configuratie als u ondersteuning voor geforceerde tunneling wilt gebruiken.<br>Statuscode: 400<br>ReasonPhrase: Onjuiste aanvraag*|Wordt onderzocht.<br><br>Als tijdelijke oplossing kunt u de bestaande firewall verwijderen en een nieuwe maken met dezelfde parameters.|
|Kan geen firewallbeleidtags toevoegen via de portal|Azure Firewall-beleid heeft een beperking voor patchondersteuning, zodat u geen tags kunt toevoegen via Azure Portal. De volgende fout wordt gegenereerd: *Kan de tags voor de resource niet opslaan*.|Er wordt een oplossing onderzocht. U kunt ook de Azure PowerShell-cmdlet `Set-AzFirewallPolicy` gebruiken om tags bij te werken.|
|IPv6 wordt nog niet ondersteund|Als u een IPv6-adres toevoegt aan een regel, mislukt de firewall.|Gebruik alleen iPv4-adressen. Ondersteuning voor IPv6 wordt onderzocht.|


## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](tutorial-firewall-deploy-portal.md)
- [Azure Firewall implementeren met behulp van een sjabloon](deploy-template.md)
- [Een Azure Firewall-testomgeving maken](scripts/sample-create-firewall-test.md)