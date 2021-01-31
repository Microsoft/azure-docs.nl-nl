---
title: Meer informatie over Windows Virtual Desktop-netwerk connectiviteit
titleSuffix: Azure
description: Meer informatie over Windows Virtual Desktop-netwerk connectiviteit
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: e4149864e16196b695d38a8c46ab5af835453412
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221207"
---
# <a name="understanding-windows-virtual-desktop-network-connectivity"></a>Meer informatie over Windows Virtual Desktop-netwerk connectiviteit

Virtueel bureau blad van Windows biedt de mogelijkheid om client sessies te hosten op de sessie-hosts die worden uitgevoerd op Azure. Micro soft beheert delen van de services namens de klant en biedt beveiligde eind punten voor het verbinden van clients en sessie-hosts. In het onderstaande diagram ziet u een overzicht van de netwerk verbindingen die door Windows virtueel bureau blad worden gebruikt

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagram van Windows virtueel bureau blad-netwerk verbindingen" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Sessieconnectiviteit

Windows Virtual Desktop gebruikt Remote Desktop Protocol (RDP) om externe weer gave-en invoer mogelijkheden via netwerk verbindingen te bieden. RDP is in eerste instantie uitgebracht met Windows NT 4,0 Terminal Server Edition en is voortdurend in ontwikkeling met elke versie van micro soft Windows en Windows Server. Vanaf het begin kan RDP onafhankelijk van de onderliggende transport stack worden ontworpen en de huidige service ondersteunt meerdere typen Trans Port.

## <a name="reverse-connect-transport"></a>Trans Port reverse Connect

Virtueel bureau blad van Windows maakt gebruik van Trans Port reverse Connect voor het instellen van de externe sessie en voor het uitvoeren van RDP-verkeer. In tegens telling tot de on-premises Extern bureaublad-services-implementaties, maakt reverse Connect Trans Port geen gebruik van een TCP-listener voor het ontvangen van binnenkomende RDP-verbindingen. In plaats daarvan wordt uitgaande verbinding met de virtuele Windows-desktop infrastructuur via de HTTPS-verbinding gebruikt.

## <a name="session-host-communication-channel"></a>Communicatie kanaal sessie-host

Bij het opstarten van de virtuele bureau blad-sessiehost van Windows, wordt door de Extern bureaublad agent loader-service het permanente communicatie kanaal van de Windows Virtual Desktop Broker tot stand gebracht. Dit communicatie kanaal bevindt zich boven op een beveiligde Transport Layer Security (TLS)-verbinding en fungeert als een bus voor service berichten uitwisseling tussen sessiehost en virtuele bureau blad-infra structuur van Windows.

## <a name="client-connection-sequence"></a>Reeks client verbindingen

Hieronder beschreven client verbindings volgorde:

1. Een ondersteunde Windows virtual desktop-client gebruiker abonneren op de virtuele bureau blad-werk ruimte van Windows
2. Azure Active Directory verifieert de gebruiker en retourneert het token dat wordt gebruikt voor het opsommen van resources die beschikbaar zijn voor een gebruiker
3. Client geeft token door aan de Windows Virtual Desktop feed Subscription Service
4. Windows Virtual Desktop feed Subscription Service valideert het token
5. Windows Virtual Desktop feed Subscription service stuurt de lijst met beschik bare Bureau bladen en RemoteApps terug naar de client in de vorm van een digitaal ondertekende verbindings configuratie
6. Client slaat de configuratie van de verbinding voor elke beschik bare bron op in een set RDP-bestanden
7. Wanneer een gebruiker de bron selecteert om verbinding te maken, gebruikt de client het bijbehorende RDP-bestand en wordt de beveiligde TLS 1,2-verbinding met het dichtstbijzijnde Windows-bureau blad-gateway exemplaar gemaakt en worden de verbindings gegevens door gegeven
8. De virtuele bureau blad-gateway van Windows valideert de aanvraag en vraagt de Windows Virtual Desktop Broker om de verbinding te organiseren
9. Windows Virtual Desktop Broker identificeert de sessiehost en maakt gebruik van het eerder vastgelegde permanente communicatie kanaal om de verbinding te initialiseren
10. Extern bureaublad stack initieert de TLS 1,2-verbinding met hetzelfde exemplaar van het Windows-bureau blad-gateway, zoals gebruikt door de client
11. Nadat zowel de client als de sessiehost zijn verbonden met de gateway, begint de gateway met het door sturen van de onbewerkte gegevens tussen beide eind punten. Hierdoor wordt het basis transport protocol voor omgekeerde verbinding voor de RDP vastgelegd.
12. Nadat het basis transport is ingesteld, start de client de RDP-Handshake

## <a name="connection-security"></a>Verbindingsbeveiliging

TLS 1,2 wordt gebruikt voor alle verbindingen die worden geïnitieerd vanaf de clients en de sessies van de host naar de onderdelen van de virtuele bureau blad-infra structuur van Windows. Windows virtueel bureau blad maakt gebruik van dezelfde TLS 1,2-code ringen als [Azure front-deur](../frontdoor/front-door-faq.md#what-are-the-current-cipher-suites-supported-by-azure-front-door). Het is belang rijk om ervoor te zorgen dat zowel client computers als sessie-hosts deze versleuteling kunnen gebruiken.
Voor reverse Connect Trans Port maakt zowel de client als de sessiehost verbinding met de virtuele bureau blad-gateway van Windows. Nadat de TCP-verbinding tot stand is gebracht, valideert de client of de sessiehost het certificaat van de virtuele bureau blad-gateway van Windows.
Na het tot stand brengen van het basis transportt RDP een geneste TLS-verbinding tussen de client en sessie-host met behulp van de certificaten van de sessiehost. Het certificaat dat wordt gebruikt voor RDP-versleuteling wordt standaard automatisch gegenereerd door het besturings systeem tijdens de implementatie. Zo nodig kunnen klanten centraal beheerde certificaten implementeren die zijn uitgegeven door de certificerings instantie van de onderneming. Zie [Windows Server-documentatie](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations)voor meer informatie over het configureren van certificaten.

## <a name="next-steps"></a>Volgende stappen

* Zie [vereisten voor de band breedte van Remote Desktop Protocol (RDP) voor Windows Virtual Desktop](rdp-bandwidth.md)voor meer informatie over de bandbreedte vereisten voor virtuele Windows-Bureau bladen.
* Als u aan de slag wilt gaan met Quality of Service (QoS) voor virtueel bureau blad van Windows, raadpleegt u [implementing Quality of service (QoS) voor Windows virtueel bureau blad](rdp-quality-of-service-qos.md).
