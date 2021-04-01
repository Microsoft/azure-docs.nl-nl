---
title: 'Op afstand werken: NVA-overwegingen (Network Virtual Appliance) voor extern werk | Azure VPN Gateway'
description: Dit artikel helpt u bij het begrijpen van de dingen die u moet overwegen bij het werken met virtuele netwerk apparaten (Nva's) in azure tijdens de COVID-19 Pandemic.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 70b5732e1293e35127a19fbe736d8562056a870b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96499676"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Op afstand werken: NVA-overwegingen (Network Virtual Appliance) voor extern werk

>[!NOTE]
>In dit artikel wordt beschreven hoe u virtuele netwerk apparaten, azure, micro soft Network en het Azure-partner ecosysteem kunt gebruiken om op afstand te werken en netwerk problemen te verhelpen die u als gevolg van COVID-19-crisis.
>

Sommige Azure-klanten gebruiken virtuele netwerk apparaten van derden (Nva's) van Azure Marketplace om essentiële services zoals punt-naar-site-VPN te bieden voor hun werk nemers die vanuit huis werken tijdens de COVIDe-epidemie. In dit artikel wordt een overzicht gegeven van een hoog niveau om rekening mee te houden bij het gebruik van Nva's in azure om oplossingen voor externe toegang te bieden.

## <a name="nva-performance-considerations"></a>NVA-prestatie overwegingen

Alle belang rijke NVA-leveranciers in azure Marketplace moeten aanbevelingen hebben over de grootte van de virtuele machine en het aantal exemplaren dat moet worden gebruikt bij het implementeren van hun oplossingen.  Hoewel nagenoeg alle leveranciers van NVA u een grootte kunt kiezen die voor u beschikbaar is in een bepaalde regio, is het heel belang rijk dat u de aanbevelingen voor de leveranciers van Azure VM-exemplaren volgt, omdat deze aanbevelingen de VM-grootten zijn die de leverancier heeft uitgevoerd om prestaties te testen met in Azure.  

### <a name="consider-the-following"></a>Houd rekening met het volgende

- **Capaciteit en aantal gelijktijdige gebruikers** : dit aantal is met name belang rijk voor punt-naar-site-VPN-gebruikers, omdat elke verbonden gebruiker één versleutelde tunnel (IPSec of SSL-VPN) heeft gemaakt.  
- **Cumulatieve door Voer** : wat is de cumulatieve band breedte die u nodig hebt om te voldoen aan het aantal gebruikers dat u nodig hebt om externe toegang te bieden.
- **De VM-grootte die u nodig** hebt: gebruik altijd VM-grootten die worden aanbevolen door de NVA-leverancier.  Als u een punt-naar-site-VPN-verbinding wilt maken, moet u een grotere VM-grootte gebruiken, zoals dv2- [en DSv2-serie](../virtual-machines/dv2-dsv2-series.md "Dv2-en Dsv2-serie") vm's. Deze Vm's hebben vaak meer Vcpu's en kunnen meer gelijktijdige VPN-sessies afhandelen.  Grotere VM-grootten in azure hebben niet alleen meer virtuele kernen, maar hebben een meer geaggregeerde bandbreedte capaciteit dan kleinere VM-grootten.
    > **Belang rijk:** Elke leverancier gebruikt verschillende bronnen.  Als het niet duidelijk is welke instantie grootte u moet gebruiken voor de geschatte gebruikers belasting, neemt u rechtstreeks contact op met de software leverancier en vraagt u deze om een aanbeveling.
- **Aantal exemplaren** : als u verwacht een groot aantal gebruikers en verbindingen te hebben, zijn er beperkingen voor het schalen van de grootte van uw NVA-exemplaar.  Overweeg het implementeren van meerdere VM-exemplaren.
- **IPSec VPN versus SSL VPN** -in algemene IPSec-VPN-implementaties werken beter dan SSL-VPN-implementaties.  
- **Licentie verlening** : Zorg ervoor dat de software licenties die u hebt aangeschaft voor de NVA-oplossing, betrekking hebben op de plotselinge groei die u tijdens de COVID-19-epidemie kunt ervaren.  Veel NVA-licentie Programma's beperken het aantal verbindingen of de band breedte die door de oplossing kan worden ondersteund.
- **Versneld netwerken** : overweeg een NVA-oplossing die ondersteuning biedt voor versneld netwerken.  Met Versnelde netwerken wordt I/O-virtualisatie met één hoofdmap (SR-IOV) mogelijk voor een VM, waarmee de netwerkprestaties van de VM aanzienlijk worden verbeterd. Dit pad met hoge prestaties omzeilt de host van het gegevenspad, beperkt latentie, jitter en CPU-gebruik voor gebruik met de meest veeleisende netwerk workloads op ondersteunde VM-typen. Versnelde netwerken worden ondersteund in de meeste algemene doel stellingen en met Compute geoptimaliseerde exemplaar grootten met twee of meer Vcpu's.

## <a name="monitoring-resources"></a>Bewakings bronnen

Elke NVA-oplossing heeft zijn eigen hulp middelen en bronnen voor het bewaken van de prestaties van hun NVA.  Raadpleeg de documentatie van uw leveranciers om ervoor te zorgen dat u de prestatie beperkingen begrijpt en kan detecteren wanneer uw NVA bijna of vol is.  Daarnaast kunt u Azure Monitor netwerk inzichten bekijken en algemene prestatie gegevens weer geven over uw virtuele netwerk apparaten, zoals:

- CPU-gebruik
- Netwerk in
- Netwerk uit
- Binnenkomende stromen
- Uitgaande stromen

## <a name="next-steps"></a>Volgende stappen

De belangrijkste NVA-partners hebben richt lijnen om te schalen voor plotselinge, onverwachte groei tijdens COVID-19. Hier volgen enkele handige koppelingen naar partner bronnen.

[Barracuda werken vanaf thuis en tijdens het beveiligen van uw gegevens tijdens COVID-19](https://www.barracuda.com/covid-19/work-from-home "Werk vanaf thuis inschakelen en uw gegevens beschermen tijdens COVID-19")

[Controle punt veilig extern personeel tijdens coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Veilig extern personeel tijdens coronavirus")

[Cisco AnyConnect-implementatie en prestaties/schaal referentie voor COVID-19-voor bereiding](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Cisco AnyConnect-implementatie en prestaties/schaal referentie voor COVID-19-voor bereiding")

[Citrix COVID-19-antwoord ondersteunings centrum](https://www.citrix.com/support/covid-19-coronavirus.html "Citrix COVID-19-antwoord ondersteunings centrum")

[F5-richt lijnen voor het aanpakken van de aanzienlijke toename van externe werk nemers](https://www.f5.com/business-continuity "F5-richt lijnen voor het aanpakken van de aanzienlijke toename van externe werk nemers")

[Fortinet COVID-19-updates voor klanten en partners](https://www.fortinet.com/covid-19.html "COVID-19-updates voor klanten en partners")

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Response Center")

[Kemp voor externe werk en Always-On van apps inschakelen voor bedrijfs continuïteit](https://kemptechnologies.com/remote-work-always-on-application-experience-business-continuity/ "Kemp voor externe werk en Always-On van apps inschakelen voor bedrijfs continuïteit")

