---
title: Adaptieve netwerk beveiliging in Azure Security Center | Microsoft Docs
description: Meer informatie over het gebruik van werkelijke verkeers patronen om uw NSG-regels (netwerk beveiligings groepen) te beveiligen en uw beveiligings postuur verder te verbeteren.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: 14523e814c85469aa02a860e87b86defd7823c16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439592"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Verbeter uw netwerk beveiligings postuur met adaptieve netwerk beveiliging
Meer informatie over het configureren van adaptieve netwerk beveiliging in Security Center.

## <a name="availability"></a>Beschikbaarheid
|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|[Azure Defender voor servers](defender-for-servers-introduction.md) is vereist|
|Vereiste rollen en machtigingen:|Schrijfmachtigingen op de NSG's van de computer|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Nee](./media/icons/no-icon.png) Nationaal/onafhankelijk (overheid van de VS, China, andere overheden)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Wat is adaptieve netwerk beveiliging?
Het Toep assen van [netwerk beveiligings groepen (NSG)](../virtual-network/network-security-groups-overview.md) voor het filteren van verkeer naar en van resources verbetert uw netwerk beveiligings postuur. Maar er kunnen toch nog enkele gevallen zijn waarin het werkelijke verkeer dat via de NSG stroomt een subset is van de gedefinieerde NSG-regels. In dergelijke gevallen kunt u het beveiligingspostuur verder verbeteren door de NSG-regels te versterken op basis van de werkelijke verkeerspatronen.

Adaptieve netwerk beveiliging biedt aanbevelingen voor verdere beveiliging van de NSG-regels. Het maakt gebruik van een machine learning-algoritme dat rekening houdt met werkelijk verkeer, bekende vertrouwde configuratie, bedreigingsinformatie en andere aanwijzingen voor aantasting, en geeft vervolgens aanbevelingen om alleen verkeer van bepaalde IP-/poort-tuples toe te staan.

Stel bijvoorbeeld dat de bestaande NSG-regel verkeer van 140.20.30.10/24 op poort 22 toestaat. Op basis van de analyse van het verkeer kan het voor komen dat een adaptieve netwerk beveiliging het bereik beperkt zodat verkeer van 140.23.30.10/29 wordt toegestaan en dat alle andere verkeer naar die poort wordt geweigerd.

>[!Note]
> Aanbevelingen voor adaptieve netwerk beveiliging worden alleen ondersteund op de volgende specifieke poorten (voor UDP en TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


## <a name="view-and-manage-hardening-alerts-and-rules"></a>Beveiligings waarschuwingen en-regels weer geven en beheren

1. Open in het menu van Security Center het **Azure Defender** -dash board en selecteer de tegel adaptieve netwerk beveiliging (1) of het deel venster inzichten met betrekking tot adaptieve netwerk beveiliging (2). 

    :::image type="content" source="./media/security-center-adaptive-network-hardening/traffic-hardening.png" alt-text="Toegang tot de hulpprogram ma's voor adaptieve netwerk beveiliging" lightbox="./media/security-center-adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Het deel venster inzichten toont het percentage van uw virtuele machines dat momenteel is beveiligd met adaptieve netwerk beveiliging. 

1. De pagina Details voor de **aanbevelingen voor adaptieve netwerk beveiliging moet worden toegepast op Internet gerichte virtuele machines** . de aanbeveling wordt geopend met uw netwerk-vm's gegroepeerd op drie tabbladen:
   * **Slechte resources**: vm's die momenteel aanbevelingen en waarschuwingen hebben geactiveerd door het uitvoeren van het adaptieve netwerk beveiligings algoritme. 
   * **Gezonde resources**: vm's zonder waarschuwingen en aanbevelingen.
   * Niet- **gescande resources**: vm's waarvoor het adaptieve netwerk beveiligings algoritme niet kan worden uitgevoerd om een van de volgende redenen:
      * **Vm's zijn klassieke vm's**: alleen Azure Resource Manager vm's worden ondersteund.
      * **Er zijn onvoldoende gegevens beschikbaar**: voor het genereren van nauw keurige aanbevelingen voor het beveiligen van de beveiliging is Security Center ten minste 30 dagen aan verkeers gegevens vereist.
      * **VM wordt niet beveiligd door Azure Defender**: alleen vm's die zijn beveiligd met [Azure Defender voor servers](defender-for-servers-introduction.md) komen in aanmerking voor deze functie.

    :::image type="content" source="./media/security-center-adaptive-network-hardening/recommendation-details-page.png" alt-text="De pagina Details van de aanbevelingen voor het aanbevelen van adaptieve netwerk beveiliging moet worden toegepast op Internet gerichte virtuele machines":::

1. Op het tabblad **beschadigde bronnen** selecteert u een virtuele machine om de waarschuwingen en de aanbevolen beveiligings regels weer te geven die moeten worden toegepast.

    - Het tabblad **regels** bevat de regels die adaptieve netwerk beveiliging aanbeveelt
    - Op het tabblad **waarschuwingen** worden de waarschuwingen weer gegeven die zijn gegenereerd als gevolg van verkeer, stromen naar de resource, die niet binnen het IP-bereik vallen dat is toegestaan in de aanbevolen regels.

1. Bewerk eventueel de regels:

    - [Een regel wijzigen](#modify-rule)
    - [Een regel verwijderen](#delete-rule) 
    - [Een regel toevoegen](#add-rule)

3. Selecteer de regels die u wilt Toep assen op de NSG en klik op **afdwingen**.

    > [!TIP]
    > Als de toegestane bron-IP-bereiken als ' geen ' worden weer gegeven, betekent dit dat de aanbevolen regel een regel voor *weigeren* is, anders is dit een regel voor *toestaan* .

    :::image type="content" source="./media/security-center-adaptive-network-hardening/hardening-alerts.png" alt-text="Regels voor adaptieve netwerk beveiliging beheren":::

      > [!NOTE]
      > De afgedwongen regels worden toegevoegd aan de NSG (s) die de virtuele machine beveiligen. (Een virtuele machine kan worden beveiligd door een NSG die is gekoppeld aan de NIC of het subnet waarin de virtuele machine zich bevindt, of beide)

### <a name="modify-a-rule"></a>Een regel <a name ="modify-rule"></a> wijzigen

Mogelijk wilt u de para meters van een regel die is aanbevolen wijzigen. Het is bijvoorbeeld mogelijk dat u de aanbevolen IP-bereiken wilt wijzigen.

Enkele belang rijke richt lijnen voor het wijzigen van een adaptieve netwerk beperkings regel:

* U kunt de para meters van alleen regels wijzigen. 
* U kunt regels voor ' toestaan ' niet wijzigen zodat regels worden ' deny '. 

  > [!NOTE]
  > Het maken en wijzigen van regels voor weigeren wordt rechtstreeks uitgevoerd op het NSG. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](../virtual-network/manage-network-security-group.md)voor meer informatie.

* Een regel voor het **weigeren van alle verkeer** is het enige type ' deny ' dat hier wordt vermeld en kan niet worden gewijzigd. U kunt het echter wel verwijderen (Zie [een regel verwijderen](#delete-rule)).
  > [!NOTE]
  > Een regel voor het weigeren van een **netwerk** wordt aanbevolen wanneer als gevolg van het uitvoeren van het Security Center algoritme geen verkeer wordt geïdentificeerd dat moet worden toegestaan op basis van de bestaande NSG-configuratie. Daarom is de aanbevolen regel het weigeren van al het verkeer naar de opgegeven poort. De naam van dit type regel wordt weer gegeven als '*systeem gegenereerd*'. Na het afdwingen van deze regel is de daad werkelijke naam in de NSG een teken reeks die bestaat uit het Protocol, de richting van het verkeer, de "weigeren" en een wille keurig getal.

*Een regel voor adaptieve netwerk beveiliging wijzigen:*

1. Als u een aantal para meters van een regel wilt wijzigen, klikt u op het tabblad **regels** op de drie puntjes (...) aan het einde van de rij van de regel en klikt u op **bewerken**.

   ![Regel voor het bewerken van s](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. In het venster **regel bewerken** werkt u de details bij die u wilt wijzigen en klikt u op **Opslaan**.

   > [!NOTE]
   > Nadat u op **Opslaan** hebt geklikt, is de regel gewijzigd. *U hebt deze echter niet toegepast op de NSG.* Als u deze wilt Toep assen, moet u de regel in de lijst selecteren en vervolgens **afdwingen** selecteren (zoals in de volgende stap wordt uitgelegd).

   ![Opslaan selecteren](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Als u de bijgewerkte regel wilt Toep assen, selecteert u in de lijst de bijgewerkte regel en klikt u op **afdwingen**.

    ![regel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Een nieuwe regel <a name ="add-rule"></a> toevoegen

U kunt een regel ' toestaan ' toevoegen die niet wordt aanbevolen door Security Center.

> [!NOTE]
> U kunt hier alleen regels voor ' toestaan ' toevoegen. Als u regels voor weigeren wilt toevoegen, kunt u dit rechtstreeks doen op het NSG. Zie [een netwerk beveiligings groep maken, wijzigen of verwijderen](../virtual-network/manage-network-security-group.md)voor meer informatie.

*Een regel voor adaptieve netwerk beveiliging toevoegen:*

1. Klik op **regel toevoegen** (bevindt zich in de linkerbovenhoek).

   ![regel toevoegen](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. Voer in het venster **nieuwe regel** de details in en klik op **toevoegen**.

   > [!NOTE]
   > Nadat u op **toevoegen** hebt geklikt, is de regel toegevoegd en wordt deze weer gegeven met de andere aanbevolen regels. U hebt deze echter niet toegepast op de NSG. Als u deze wilt activeren, moet u de regel in de lijst selecteren en op **afdwingen** klikken (zoals in de volgende stap wordt uitgelegd).

3. Als u de nieuwe regel wilt Toep assen, selecteert u de nieuwe regel in de lijst en klikt u op **afdwingen**.

    ![regel afdwingen](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Een regel <a name ="delete-rule"></a> verwijderen

Als dat nodig is, kunt u een aanbevolen regel voor de huidige sessie verwijderen. U kunt bijvoorbeeld bepalen dat het Toep assen van een voorgestelde regel legitiem verkeer kan blok keren.

*Een regel voor het beveiligen van een adaptieve netwerk beveiliging voor uw huidige sessie verwijderen:*

1. Klik op het tabblad **regels** op de drie puntjes (...) aan het einde van de rij van de regel en klik op **verwijderen**.  

    ![Een regel verwijderen](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)