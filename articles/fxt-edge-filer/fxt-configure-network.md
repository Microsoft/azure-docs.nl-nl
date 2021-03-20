---
title: 'Zelfstudie: Netwerk configureren in een Azure FXT Edge Filer-cluster'
description: Netwerkinstellingen aanpassen na het maken van het Azure FXT Edge Filer-cluster
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4ce7ffc66e0b6164b2e4ca9725b3f26403292a4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220768"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Zelfstudie: De netwerkinstellingen van het cluster configureren

Voordat u een nieuw gemaakt Azure FXT Edge Filer-cluster gebruikt, moet u verschillende netwerkinstellingen voor uw werkstroom controleren en aanpassen.

In deze zelfstudie worden de netwerkinstellingen beschreven die u mogelijk moet aanpassen voor een nieuw cluster.

U leert:

> [!div class="checklist"]
>
> * Welke netwerkinstellingen mogelijk moeten worden bijgewerkt na het maken van een cluster
> * Welke Azure FXT Edge Filer-use-cases een AD-server of een DNS-server vereisen
> * Hoe u RRDNS (round-robin-DNS) moet configureren om clientaanvragen automatisch te verdelen over het FXT-cluster

De hoeveelheid tijd die nodig is om deze stappen uit te voeren, is afhankelijk van het aantal benodigde configuratiewijzigingen in uw systeem:

* Als u alleen de zelfstudie wilt lezen en enkele instellingen wilt controleren, duurt het 10 tot 15 minuten.
* Als u round-robin-DNS moet configureren, kan deze taak een uur of langer duren.

## <a name="adjust-network-settings"></a>Netwerkinstellingen aanpassen

Verschillende taken die betrekking hebben op het netwerk, maken deel uit van het instellen van een nieuw Azure FXT Edge Filer-cluster. Controleer deze lijst en bepaal welke instellingen van toepassing zijn op uw systeem.

Lees [Netwerkservices configureren](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) in de handleiding voor clusterconfiguratie voor meer informatie over netwerkinstellingen voor het cluster.

* Round-robin-DNS configureren voor het clientgerichte netwerk (optioneel)

  Taakverdeling van het clusterverkeer instellen door het DNS-systeem te configureren, zoals wordt beschreven in [DNS configureren voor het FXT Edge Filer-cluster](#configure-dns-for-load-balancing).

* NTP-instellingen controleren

* Active Directory en downloads van gebruikers- of groepsnaam configureren (indien nodig)

  Als uw netwerkhosts Active Directory of een ander type externe adreslijstservice gebruiken, moet u de configuratie van de adreslijstservices van het cluster aanpassen om in te stellen hoe de gebruikersnaam en groepsinformatie in het cluster worden gedownload. Lees **Cluster** > **Directory Services** in de handleiding voor clusterconfiguratie voor gedetailleerde informatie.

  Een AD-server is vereist als u SMB-ondersteuning wilt. Configureer AD voordat u SMB instelt.

* VLAN'S definiëren (optioneel)
  
  Configureer alle benodigde extra VLAN'S voordat u de vservers en globale naamruimte van uw cluster definieert. Lees [Werken met VLAN's](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) in de handleiding voor clusterconfiguratie voor meer informatie.

* Proxyservers configureren (indien nodig)

  Als uw cluster een proxyserver gebruikt om externe adressen te bereiken, voert u de volgende stappen uit om deze in te stellen:

  1. Definieer de proxyserver op de instellingspagina **Proxyconfiguratie**
  1. Pas de configuratie van de proxyserver toe met de pagina **Cluster** > **Algemene instellingen** of de pagina **Core Filer Details**.
  
  Lees de sectie [Using Web Proxies](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) (Webproxy's gebruiken) van de handleiding voor clusterconfiguratie voor meer informatie (Engelstalig).

* Upload de [versleutelingscertificaten](#encryption-certificates) die in het cluster moet worden gebruikt (optioneel)

### <a name="encryption-certificates"></a>Versleutelingscertificaten

Het FXT Edge Filer-cluster maakt gebruik van X.509-certificaten voor deze functies:

* Versleuteling van het clusterbeheerverkeer

* Verificatie namens een client bij KMIP-servers van derden

* Controle van servercertificaten van cloudproviders

Als u certificaten moet uploaden naar het cluster, gebruikt u de instellingspagina **Cluster** > **Certificaten**. Meer details vindt u op de pagina [Cluster > Certificates](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) van de handleiding voor clusterconfiguratie (Engelstalig).

Als u de communicatie voor clusterbeheer wilt versleutelen, gebruikt u de instellingspagina **Cluster** > **Algemene instellingen** om te selecteren welk certificaat moet worden gebruikt voor het beheer van TLS.

> [!Note]
> Toegangssleutels voor cloudservices worden opgeslagen met behulp van de configuratiepagina voor **cloudreferenties**. In de sectie [Add a core filer](fxt-add-storage.md#add-a-core-filer) hierboven wordt een voorbeeld weergegeven. Lees de sectie [Cloud Credentials](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) in de handleiding voor clusterconfiguratie voor meer informatie (Engelstalig).

## <a name="configure-dns-for-load-balancing"></a>DNS voor taakverdeling configureren

In deze sectie worden de basisbeginselen uitgelegd van het configureren van een round-robin-DNS-systeem (RRDNS) voor clienttaakverdeling tussen alle clientgerichte IP-adressen in uw FXT Edge Filer-cluster.

### <a name="decide-whether-or-not-to-use-dns"></a>Beslissen of u DNS wilt gebruiken

Taakverdeling wordt altijd aanbevolen, maar u hoeft niet altijd DNS te gebruiken. Bij bepaalde typen clientwerkstromen kan het bijvoorbeeld handiger zijn om een script te gebruiken om IP-adressen van clusters gelijkmatig toe te wijzen aan clients wanneer ze het cluster koppelen. Sommige methoden worden beschreven in [Het cluster koppelen](fxt-mount-clients.md).

Houd rekening met het volgende bij het bepalen of u wel of geen DNS-server wilt gebruiken:

* Als uw systeem alleen door NFS-clients wordt gebruikt, is DNS niet vereist. Het is mogelijk om alle netwerkadressen op te geven met behulp van numerieke IP-adressen.

* Als uw systeem ondersteuning biedt voor toegang via SMB (CIFS), is DNS wel vereist, omdat u een DNS-domein moet opgeven voor de Active Directory-server.

* DNS is vereist als u Kerberos-verificatie wilt gebruiken.

### <a name="round-robin-dns-configuration-details"></a>Details van round-robin-DNS-configuratie

Wanneer clients toegang hebben tot het cluster, worden de aanvragen via RRDNS gelijkmatig verdeeld over alle beschikbare interfaces.

Voor de beste prestaties moet u de DNS-server configureren voor het afhandelen van clientgerichte clusteradressen, zoals wordt weergegeven in het volgende diagram.

Een cluster-vserver wordt aan de linkerkant weergegeven, en IP-adressen worden in het midden en aan de rechterkant weergegeven. Configureer elk clienttoegangspunt met A-records en pointers zoals geïllustreerd.

![Diagram van round-robin-DNS-cluster: gedetailleerde alt-tekst koppeling naar de afbeelding](media/fxt-cluster-config/fxt-rrdns-diagram.png) 
[gedetailleerde beschrijving van de tekst](https://azure.github.io/Avere/legacy/Azure-FXT-EdgeFilerDNSconfiguration-alt-text.html)

Elk clientgericht IP-adres moet een unieke naam hebben voor intern gebruik door het cluster. (In dit diagram hebben de IP-adressen van de client de naam vs1-client-IP-* voor de duidelijkheid, maar in een productieomgeving moet u waarschijnlijk een beknoptere naam gebruiken, zoals client*.)

Clients koppelen het cluster met de naam van de vserver als het serverargument.

Wijzig het bestand ``named.conf`` van uw DNS-server om de cyclische volgorde voor query's naar uw vserver in te stellen. Deze optie zorgt ervoor dat alle beschikbare waarden de cyclus doorlopen. Voeg een instructie zoals deze toe:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

De volgende ``nsupdate``-opdrachten zijn voorbeelden voor het correct configureren van DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>DNS inschakelen in het cluster

Geef de DNS-server op die door het cluster wordt gebruikt op de instellingspagina **Cluster** > **Beheernetwerk**. Instellingen op die pagina zijn onder andere:

* DNS-serveradres
* DNS-domeinnaam
* DNS-zoekdomeinen

Lees [DNS Settings](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) in de handleiding voor clusterconfiguratie voor meer informatie (Engelstalig).

## <a name="next-steps"></a>Volgende stappen

Dit is de laatste basisconfiguratiestap voor het Azure FXT Edge Filer-cluster.

* Zie [Hardwarestatus controleren](fxt-monitor.md) voor meer informatie over de LED's en andere indicatoren van het systeem.
* Zie [Het cluster koppelen](fxt-mount-clients.md) voor meer informatie over hoe clients het FXT Edge Filer-cluster moeten koppelen.
* Zie de [handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html) voor meer informatie over het gebruiken en beheren van een FXT Edge Filer-cluster.
