---
title: 'Zelfstudie: Opslag toevoegen aan een Azure FXT Edge Filer-cluster'
description: Back-endopslag en de clientgerichte pseudonamespace voor Azure FXT Edge Filer configureren
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 7d10c6c1ce440b2ffe964dc78379ef3ab108e78e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92217521"
---
# <a name="tutorial-add-back-end-storage-and-configure-the-virtual-namespace"></a>Zelfstudie: Back-endopslag toevoegen en de virtuele naamruimte configureren

In deze zelfstudie wordt uitgelegd hoe u back-endopslag kunt toevoegen voor uw cache en hoe u het clientgerichte virtuele bestandssysteem kunt instellen.

Het cluster maakt verbinding met back-endsystemen om toegang te krijgen tot de gegevens waar clients om vragen en om wijzigingen permanenter op te slaan dan in de cache.

De naamruimte is het clientgerichte pseudo-bestandssysteem waarmee u back-endopslag kunt verwisselen zonder dat er aan de clientzijde werkstromen worden gewijzigd.

In deze zelfstudie leert u:

> [!div class="checklist"]
>
> * Back-endopslag toevoegen aan het Azure FXT Edge Filer-cluster
> * Het clientgerichte pad voor opslag definiëren

## <a name="about-back-end-storage"></a>Over back-endopslag

Het Azure FXT Edge Filer-cluster maakt gebruik van een *kern-filer* definitie om een back-endopslagsysteem te koppelen aan het FXT-cluster.

Azure FXT Edge Filer is compatibel met verschillende populaire NAS-hardwaresystemen en kan lege containers van Azure Blob of andere cloudopslag gebruiken.

Cloudopslagcontainers moeten leeg zijn wanneer ze worden toegevoegd, zodat het FXT-besturingssysteem alle gegevens op het cloudopslagvolume volledig kan beheren. U kunt de bestaande gegevens naar de cloudcontainer verplaatsen nadat u de container als een kern-filer aan het cluster hebt toegevoegd.

Gebruik het configuratiescherm om een kern-filer aan uw systeem toe te voegen.

> [!NOTE]
>
> Als u Amazon AWS of Google Cloudopslag wilt gebruiken, moet u een FlashCloud<sup>TM</sup>-functielicentie installeren. Neem contact op met uw Microsoft-vertegenwoordiger voor een licentiecode en volg de instructies in de verouderde configuratiehandleiding voor [Het toevoegen of verwijderen van functielicenties](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/install_licenses.html#install-licenses).
>
> Ondersteuning voor Azure Blob-opslag is opgenomen in de softwarelicentie van Azure FXT Edge Filer.

Lees deze gedeeltes van de Handleiding voor clusterconfiguratie voor meer informatie over het toevoegen van kern-filers:

* Lees [Werken met kern-filers](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/core_filer_overview.html#core-filer-overview) voor meer informatie over het kiezen en voorbereiden van het toevoegen van een kern-filer.
* Lees de volgende artikelen voor gedetailleerde vereisten en stapsgewijze instructies:

  * [Een nieuwe NAS-kern-filer toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html#create-core-filer-nas)
  * [Een nieuwe Cloud-kern-filer toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#create-core-filer-cloud)

Nadat u een kern-filer hebt toegevoegd, kunt u de instellingen ervan bijwerken op de instellingspagina met details van de kern-filer.

## <a name="add-a-core-filer"></a>Een kern-filer toevoegen

Definieer een kern-filer door te klikken op de knop **Maken** op de instellingspagina van **Kern-filer** > **Kern-filers beheren**.

![Klik op de knop maken boven de lijst met kern-filers op de pagina Kern-filers beheren](media/fxt-cluster-config/create-core-filer-button.png)

De wizard **Nieuwe kern-filer toevoegen** begeleidt u stapsgewijs door het proces van het maken van een kern-filer die is gekoppeld aan uw back-endopslag. De Handleiding voor clusterconfiguratie bevat stapsgewijze beschrijvingen van het proces, dat anders is dan dat van NFS/NAS-opslag en Cloudopslag (links hierboven).

Subtaken zijn onder andere:

* Geef het type kern-filer (NAS of cloud) op

  ![Eerste pagina van de wizard voor een nieuwe kern-filer voor hardware NAS. De optie 'cloud-kern-filer ' is uitgeschakeld en er wordt een foutbericht over de ontbrekende licentie weergegeven.](media/fxt-cluster-config/new-nas-1.png)

* Stel de naam van de kern-filer in. Kies een naam waardoor de clusterbeheerders begrijpen welk opslagsysteem het vertegenwoordigt.

* Geef voor NAS-kern-filers de Fully Qualified Domain Name (FQDN) of het IP-adres op. FQDN wordt aanbevolen voor alle kern-filers en is vereist voor SMB-toegang.

* Selecteer een cache-beleid: de tweede pagina van de wizard bevat een lijst met de beschikbare cache-beleidsregels voor de nieuwe kern-filer. Lees voor meer informatie het [gedeelte over cache-beleid uit de Handleiding voor clusterconfiguratie](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_manage_cache_policies.html).

  ![Tweede pagina van de wizard voor een nieuwe kern-filer voor hardware NAS. De vervolgkeuzelijst cache-beleid is geopend, met verschillende uitgeschakelde opties en drie geldige cachebeleidsopties (bypass, opslaan in cache lezen en opslaan in cache lezen/schrijven).](media/fxt-cluster-config/new-nas-choose-cache-policy.png)

* Voor cloudopslag moet u de cloudservice en toegangsreferenties opgeven, naast andere parameters. Lees voor meer informatie [Cloudservice en -protocol](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html#cloud-service-and-protocol) in de Handleiding voor clusterconfiguratie.

  ![Informatie over cloud-kern-filers in de wizard Nieuwe kern-filer](media/fxt-cluster-config/new-core-filer-cloud3.png)
  
  Als u de aanmeldingsgegevens voor cloudtoegang voor dit cluster al hebt toegevoegd, worden ze weergegeven in de lijst. Aanmeldingsgegevens bijwerken en toevoegen op de instellingspagina **Cluster** > **aanmeldingsgegevens cloud**.

Nadat u alle vereiste instellingen in de wizard hebt ingevuld, klikt u op de knop **Filer toevoegen** om de wijziging in te dienen.

Na enkele ogenblikken wordt het opslagsysteem weergegeven op de lijst met kern-filers op het **Dashboard** en is toegankelijk via de pagina met instellingen van de kern-filer.

![kern-filer ' Flurry-NAS ' op de instellingspagina Kern-filers beheren, met de detailweergave van de filer uitgevouwen](media/fxt-cluster-config/core-filer-in-manage-page.png)

In de kern-filer in deze schermopname ontbreekt een vserver. U moet de kern-filer koppelen aan een vserver en een verbinding maken zodat clients toegang hebben tot de opslag. Deze stappen worden hieronder beschreven in [De naamruimte configureren](#configure-the-namespace).

## <a name="configure-the-namespace"></a>De naamruimte configureren

Het Azure FXT Edge Filer-cluster maakt een virtueel bestandssysteem met de naam *clusternaamruimte* dat de clienttoegang tot gegevens die zijn opgeslagen op verschillende back-endsystemen vereenvoudigt. Omdat clients bestanden aanvragen via een virtueel pad, kunnen opslagsystemen worden toegevoegd of vervangen zonder dat de werkstroom van de client hoeft te worden gewijzigd.

Met de clusternaamruimte kunt u ook cloud- en NAS-opslagsystemen in een vergelijkbare bestandsstructuur presenteren.

De vservers van het cluster behouden de naamruimte en het leveren van inhoud aan clients. Er zijn twee stappen voor het maken van de clusternaamruimte:

1. Een vserver maken
1. Verbindingen instellen tussen de back-endopslagsystemen en de clientgerichte bestandssysteempaden

### <a name="create-a-vserver"></a>Een vserver maken

VServers zijn virtuele bestandsservers die bepalen hoe gegevens stromen tussen de client en de kern-filers van het cluster:

* VServers hosten clientgerichte IP-adressen
* VServers maken de naamruimte en definiëren verbindingen die de clientgerichte virtuele mappenstructuur toewijzen aan exports op back-endopslag
* VServers dwingen bestandstoegangsbeheer af, waaronder exportbeleid van kern-filers en gebruikersverificatiesystemen
* VServers bieden SMB-infrastructuur

Lees voordat u begint met het configureren van een cluster-vserver de bijbehorende documentatie en neem contact op met uw Microsoft-vertegenwoordiger voor meer informatie over naamruimte en vservers. Als u VLAN'S gebruikt, [maak deze](fxt-configure-network.md#adjust-network-settings) dan voordat u de vserver maakt.

In deze secties van de Handleiding voor clusterconfiguratie vindt u meer informatie over de functies FXT-vserver en Globale naamruimte:

* [VServers maken en gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#creating-and-working-with-vservers)
* [Een Globale naamruimte gebruiken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)
* [Een VServer maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver)

U hebt ten minste één vserver nodig voor uw cluster.

U heeft de volgende gegevens nodig om een nieuwe vserver te maken:

* De naam die moet worden ingesteld voor de vserver

* Het bereik van clientgerichte IP-adressen die door de vserver worden verwerkt

  U moet één bereik van aaneengesloten IP-adressen opgeven wanneer u de vserver maakt. U kunt later meer adressen toevoegen met behulp van de instellingspagina **Clientgericht netwerk**.

* Als uw netwerk VLAN's heeft, welk VLAN moet dan worden gebruikt voor deze vserver

Gebruik de instellingspagina **VServer** >  **VServers beheren** om een nieuwe vserver te maken. Lees voor meer informatie [Een VServer maken](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_vserver_manage.html#creating-a-vserver) in de Handleiding voor clusterconfiguratie.

![pop-upvenster voor het maken van een nieuwe vserver](media/fxt-cluster-config/new-vserver.png)

### <a name="create-a-junction"></a>Een verbinding maken

Een *verbinding* wijst een back-endopslagpad toe aan de naamruimte die zichtbaar is voor de client.

U kunt dit systeem gebruiken voor het vereenvoudigen van het pad dat wordt gebruikt in clientkoppelpunten en om de capaciteit naadloos te schalen, omdat één virtueel pad opslag van meerdere kern-filers kan bevatten.

![Pagina Wizard nieuwe verbinding toevoegen met de instellingen ingevuld](media/fxt-cluster-config/add-junction-full.png)

Raadpleeg [**VServer** > **Naamruimte**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html) in de Handleiding voor clusterconfiguratie voor gedetailleerde informatie over het maken van een naamruimteverbinding.

![De instellingspagina VServer > Naamruimte geeft details voor een verbinding weer](media/fxt-cluster-config/namespace-populated.png)

## <a name="configure-export-rules"></a>Exportregels configureren

Wanneer u zowel een vserver als een kern-filer hebt, moet u de exportregels en het exportbeleid aanpassen die bepalen hoe clients toegang hebben tot exports van kern-filers.

Gebruik eerst de pagina **VServer** > **Exportregels** om nieuwe regels toe te voegen, het standaardbeleid te wijzigen of uw eigen aangepaste exportbeleid te maken.

Gebruik vervolgens de pagina **VServer** > **Exportbeleid** om het aangepaste beleid toe te passen op de exports van uw kern-filer wanneer deze wordt geopend via die vserver.

Lees het artikel [Beheren van toegang tot kern-filer-exports](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/export_rules_overview.html) uit de Handleiding voor clusterconfiguratie voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Nadat u opslag hebt toegevoegd en de clientgerichte naamruimte hebt geconfigureerd, voltooit u de eerste installatie van het cluster: 

> [!div class="nextstepaction"]
> [De netwerkinstellingen van het cluster configureren](fxt-configure-network.md)
