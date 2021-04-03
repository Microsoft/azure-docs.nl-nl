---
title: 'Azure VMware-oplossing op CloudSimple: Firewall tabellen en-regels instellen'
description: Hierin wordt beschreven hoe u Firewall tabellen en-regels voor de Privécloud instelt om verkeer op subnetten en VLAN'S te beperken.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895628"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Firewall tabellen en-regels voor persoonlijke Clouds instellen

Met de firewall tabellen en de gekoppelde regels kunt u beperkingen opgeven voor het verkeer dat moet worden toegepast op bepaalde subnetten en VLAN'S.

* Een subnet kan worden gekoppeld aan één firewall tabel.
* Een firewall tabel kan worden gekoppeld aan meerdere subnetten.

## <a name="add-a-new-firewall-table"></a>Een nieuwe firewall tabel toevoegen

1. [Open de CloudSimple-Portal](access-cloudsimple-portal.md) en selecteer **netwerk** in het menu aan de zijkant.
2. Selecteer **firewall tabellen**.
3. Selecteer **firewall tabel maken**.

    ![VLAN/subnet-pagina](media/firewall-tables-page.png)

4. Voer een naam in voor de tabel.
5. Er wordt een standaard regel voor de tabel weer gegeven. Klik op **nieuwe regel maken** om een extra regel te maken. Raadpleeg de volgende procedure voor meer informatie.
6. Klik op **gereed** om de firewall tabel op te slaan.

> [!IMPORTANT]
> U kunt Maxi maal twee firewall tabellen per Privécloud maken.

## <a name="firewall-rules"></a>Firewall-regels

Firewall regels bepalen hoe de firewall specifieke typen verkeer behandelt. Het tabblad **regels** voor een geselecteerde firewall tabel bevat een lijst met alle gekoppelde regels.

![Tabel met firewall regels](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Een firewallregel maken

1. De instellingen weer geven om een firewall regel op een van de volgende manieren te maken:
    * Klik op **regel toevoegen** bij het maken van een firewall tabel.
    * Selecteer een bepaalde firewall tabel op de pagina **netwerk > firewall tabellen** en klik op **nieuwe firewall regel maken**.
2. Stel de regel als volgt in:
    * **Naam**. Geef een naam op voor de regel.
    * **Prioriteit**. Wijs een prioriteit toe aan de regel. Regels met lagere getallen worden eerst uitgevoerd.
    * **Verkeers type**. Selecteer of de regel voor Privécloud, Internet of VPN-verkeer (stateless) of voor een openbaar IP-adres (stateful) is.
    * **Protocol**. Selecteer het protocol dat onder de regel valt (TCP, UDP of een Protocol).
    * **Richting**. Selecteer of de regel voor inkomend of uitgaand verkeer is. U moet afzonderlijke regels voor binnenkomend en uitgaand verkeer definiëren.
    * **Actie**. Selecteer de actie die moet worden uitgevoerd als de regel overeenkomt (toestaan of weigeren).
    * **Bron**. Geef de bronnen op die onder de regel vallen (CIDR-blok, intern of een wille keurige bron).
    * **Bron poort bereik**. Geef het bereik van poorten op waarop de regel van toepassing is.
    * **Richting**. Selecteer inkomend of uitgaand.
    * **Doel**. Geef de bestemmingen op die onder de regel vallen (CIDR-blok, intern of een wille keurige bron).
    * **Bron poort bereik**. Geef het bereik van poorten op waarop de regel van toepassing is.

    ![Firewall tabel regel toevoegen](media/firewall-rule-create.png)

3. Klik op **gereed** om de regel op te slaan en toe te voegen aan de lijst met regels voor de firewall tabel.

> [!IMPORTANT]
> Elke firewall tabel kan Maxi maal 10 regels voor binnenkomende verbindingen en 20 regels voor uitgaande verbindingen hebben. Deze limieten kunnen worden verhoogd door [contact op](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)te nemen met de ondersteuning.

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN'S/subnetten koppelen

Nadat u een firewall tabel hebt gedefinieerd, kunt u de subnetten opgeven waarop de regels in de tabel van toepassing zijn.

1.   >  Selecteer een firewall tabel op de pagina netwerk **firewall tabellen** .
2. Open het tabblad **gekoppelde vlan's/subnet** .
3. Klik op **koppelen aan een VLAN/subnet**.
4. Selecteer de Privécloud en het VLAN. De bijbehorende subnetnaam en het CIDR-blok worden weer gegeven.
5. Klik op **Submit**
