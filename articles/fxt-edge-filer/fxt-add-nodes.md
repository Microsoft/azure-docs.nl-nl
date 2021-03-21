---
title: 'Zelfstudie: Knooppunten toevoegen aan een Azure FXT Edge Filer-cluster'
description: Meer informatie over het toevoegen van clusterknooppunten aan de Azure FXT Edge Filer-opslagcache en het inschakelen van de functie voor hoge beschikbaarheid (HA).
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 85ad78eeb095b427b1a6334f57c351e926022dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021874"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Zelfstudie: Clusterknooppunten toevoegen aan een Azure FXT Edge Filer-cluster

Er wordt een nieuw Azure FXT Edge Filer-cluster gemaakt met slechts één knooppunt. U moet ten minste twee extra knooppunten toevoegen en hoge beschikbaarheid inschakelen voordat u een andere configuratie uitvoert.

In deze zelfstudie wordt uitgelegd hoe u clusterknooppunten toevoegt en de functie voor hoge beschikbaarheid (HA) inschakelt.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Knooppunten toevoegen aan het FXT-cluster
> * Hoge beschikbaarheid inschakelen

Het voltooien van de stappen in deze zelfstudie duurt ongeveer 45 minuten.

Voordat u met deze zelfstudie begint, moet u de knooppunten die u wilt toevoegen, inschakelen en [de oorspronkelijke wachtwoorden instellen](fxt-node-password.md).

## <a name="1-load-the-cluster-nodes-page"></a>1. De pagina Clusterknooppunten laden

Open het configuratiescherm van het cluster in een webbrowser en meld u aan als beheerder. (Gedetailleerde instructies vindt u in het overzichtsartikel, onder [Open the Settings pages](fxt-cluster-create.md#open-the-settings-pages).)

In het configuratiescherm ziet u het tabblad **Dashboard** wanneer dit wordt geopend. 

![Dashboard van het configuratiescherm (eerste tabblad)](media/fxt-cluster-config/dashboard-1-node.png)

Deze afbeelding toont het dashboard van een nieuw gemaakt cluster, met één knooppunt.

## <a name="2-locate-the-node-to-add"></a>2. Het toe te voegen knooppunt zoeken

Als u knooppunten wilt toevoegen, klikt u op het tabblad **Settings** en kiest u de pagina **FXT Nodes** in het gedeelte **Cluster**.

![Tabblad Instellingen van het configuratiescherm (tweede tabblad) met Cluster > FXT-knooppunten geladen](media/fxt-cluster-config/settings-fxt-nodes.png)

In de lijst **FXT Nodes - Unjoined** worden alle niet-toegewezen FXT-knooppunten weergegeven (de meeste datacenters hebben er slechts enkele). Zoek de FXT-knooppunten die u aan het cluster wilt toevoegen.

> [!Tip]
> Als u het gewenste knooppunt niet kunt vinden in de lijst **Unjoined**, controleert u of het aan deze vereisten voldoet:
>
> * Het is ingeschakeld en er is een [hoofdwachtwoord ingesteld](fxt-node-password.md).
> * Het is verbonden met een netwerk waartoe u toegang hebt. Als u VLAN's gebruikt, moet het zich op hetzelfde VLAN bevinden als het cluster.
> * Het kan worden gedetecteerd met het Bonjour-protocol.
>
>   Sommige firewallinstellingen blokkeren de TCP/UDP-poorten die door Bonjour worden gebruikt, waarmee wordt voorkomen dat de knooppunten automatisch door het besturingssysteem van FXT worden gedetecteerd.
>
> Als het knooppunt dat u wilt toevoegen, zich niet in de lijst bevindt, probeert u de volgende oplossingen:
>
> * Klik op de knop **Manual Discover** om deze op basis van het IP-adres te zoeken.
>
> * Wijs handmatig tijdelijke IP-adressen toe. Dit komt zelden voor, maar kan ook nodig zijn als u gelabelde VLAN'S gebruikt en de knooppunten zich niet in het juiste netwerk bevinden, of als uw netwerk geen zelf-toegewezen IP-adressen toestaat. Volg de instructies in de oudere versie van dit document om [handmatig een vast IP-adres in te stellen](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

De naam van het knooppunt, het IP-adres, de softwareversie en de geschiktheidsstatus worden in de lijst weergegeven. Normaal gesproken bevat de kolom **Status** de tekst Wants to join of wordt een systeem- of hardwareprobleem beschreven waardoor het knooppunt niet meer in aanmerking komt om aan het cluster te worden toegevoegd.

De kolom **Actions** bevat knoppen waarmee u het knooppunt aan het cluster kunt toevoegen of de software kunt bijwerken. Met de bijwerkknop wordt de softwareversie die overeenkomt met de knooppunten die zich al in het cluster bevinden, automatisch geïnstalleerd.

Alle knooppunten in een cluster moeten dezelfde versie van het besturingssysteem gebruiken, maar u hoeft de software niet bij te werken voordat u een knooppunt toevoegt. Nadat u op de knop **Allow to join** hebt geklikt, wordt de besturingssysteemsoftware die overeenkomt met de versie op het cluster, automatisch gecontroleerd en geïnstalleerd.

Lees voor meer informatie over de opties op deze pagina [**Cluster** > **FXT Nodes**](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) in de handleiding over clusterconfiguratie.

## <a name="3-click-the-allow-to-join-button"></a>3. Klikken op de knop Allow to join

Klik op de knop **Allow to join** _ in de kolom _ *Actions* voor het knooppunt dat u wilt toevoegen.

Nadat u op de knop hebt geklikt, kan de status van het knooppunt veranderen wanneer de software wordt bijgewerkt als voorbereiding op het toevoegen van het knooppunt aan het cluster.

In de onderstaande afbeelding ziet u een knooppunt dat aan het cluster wordt toegevoegd (waarschijnlijk wordt er een update van het besturingssysteem opgehaald voordat het wordt toegevoegd). Voor knooppunten die momenteel aan het cluster worden toegevoegd, worden in de kolom **Actions** geen knoppen weergegeven.

![een rij van de knooppunttabel, met de naam van een knooppunt, het IP-adres, de softwareversie, het bericht Allow to join en een lege laatste kolom](media/fxt-cluster-config/node-join-in-process.png)

Na enkele ogenblikken moet het nieuwe knooppunt boven aan de instellingenpagina in de lijst met clusterknooppunten **FXT Nodes** worden weergegeven.

Herhaal dit proces om de andere knooppunten aan het cluster toe te voegen. U hoeft niet te wachten tot het ene knooppunt aan het cluster is toegevoegd voordat u met een ander begint.

## <a name="enable-high-availability"></a>Hoge beschikbaarheid inschakelen

Nadat u een tweede knooppunt aan het cluster hebt toegevoegd, kan er in het dashboard van het configuratiescherm de melding verschijnen dat de functie voor hoge beschikbaarheid niet is geconfigureerd.

Met hoge beschikbaarheid (HA) kunnen de clusterknooppunten elkaar compenseren als er een uitvalt. Hoge beschikbaarheid is standaard niet ingeschakeld.

![Tabblad Dashboard met het bericht The cluster has more than one node, but HA is not enabled ... in de tabel Conditions](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note]
> Schakel hoge beschikbaarheid pas in als het cluster ten minste drie knooppunten bevat.

Volg deze procedure om hoge beschikbaarheid in te schakelen:

1. Laad de pagina **High Availability** in het gedeelte **Cluster** van het tabblad **Settings**.

   ![Pagina voor configuratie van hoge beschikbaarheid (Cluster > High Availability). Het selectievakje Enable HA bevindt zich bovenaan en de knop voor verzenden onderaan.](media/fxt-cluster-config/enable-ha.png)

2. Klik op het vak **Enable HA** en vervolgens op de knop **Submit**.

Er wordt een waarschuwing op het **dashboard** weergegeven om te bevestigen dat hoge beschikbaarheid is ingeschakeld.

![Dashboardtabel met het bericht HA is now fully configured](media/fxt-cluster-config/ha-configured-alert.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u alle knooppunten aan het cluster hebt toegevoegd, kunt u doorgaan met de installatie door de langetermijnopslag van uw cluster te configureren.

> [!div class="nextstepaction"]
> [Back-endopslag toevoegen en de virtuele naamruimte instellen](fxt-add-storage.md)