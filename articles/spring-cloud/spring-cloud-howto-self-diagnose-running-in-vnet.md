---
title: Zelf diagnose van Azure lente Cloud VNET
description: Meer informatie over het zelf vaststellen en oplossen van problemen in azure lente-Cloud die wordt uitgevoerd in VNET.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125062"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Zelf diagnose uitvoeren van Azure lente-Cloud in VNET
Azure veer Cloud diagnostiek ondersteunt interactieve probleemoplossings toepassingen die worden uitgevoerd in virtuele netwerken zonder configuratie. Azure lente-Cloud diagnose identificeert problemen en helpt u bij het oplossen van informatie die u helpt bij het oplossen van problemen.

## <a name="navigate-to-the-diagnostics-page"></a>Ga naar de pagina met diagnostische gegevens
Met de volgende procedure wordt Diagnostische gegevens voor toepassingen in een netwerk gestart.
1. Meld u aan bij Azure Portal.
1. Ga naar de Overzichtspagina van Azure Spring Cloud.
1. Selecteer **problemen vaststellen en oplossen** in het menu in het navigatie deel venster aan de linkerkant.
1. Selecteer de derde categorie, **netwerken**.

   ![Zelf diagnose titel](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weer geven
Nadat u op de categorie **netwerken** hebt geklikt, kunt u twee problemen weer geven die betrekking hebben op netwerken die specifiek zijn voor uw VNet dat is geïnjecteerd in azure lente Cloud: **DNS-omzetting** en **vereist uitgaand verkeer**.

   ![Zelf diagnostische opties](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Selecteer uw doel probleem om het diagnostische rapport weer te geven. Er wordt een samen vatting van de diagnostische gegevens weer gegeven, zoals: 

* *De resource is verwijderd.*
* *De resource is niet geïmplementeerd in uw eigen virtuele netwerk*.

Sommige resultaten bevatten gerelateerde documentatie. De resultaten worden afzonderlijk weer gegeven in verschillende subnetten.

## <a name="dns-resolution"></a>DNS-omzetting 
Als u **DNS-omzetting** selecteert, wordt in resultaten aangegeven of er DNS-problemen zijn met toepassingen.  Goede apps worden als volgt weer gegeven:

* *Er zijn DNS-problemen opgelost zonder problemen in het subnet ' subnet01 '*.
* *Er zijn DNS-problemen opgelost zonder problemen in het subnet ' subnet02 '*.

In het volgende voor beeld van een diagnostisch rapport wordt aangegeven dat de status van de toepassing onbekend is. De rapportage tijd bevat niet de tijd waarop de integriteits status is gerapporteerd.  Stel dat de eind tijd van de context *2021-03-03T04:20:00Z*. De laatste TIJDS tempel in de **DNS-omzettings tabel wordt weer** gegeven *2021-03-03T03:39:00Z*, de vorige dag. Mogelijk is het status controle logboek niet verzonden vanwege een geblokkeerd netwerk. 

De resultaten van de onbekende status bevatten gerelateerde documentatie.  U kunt klikken op de hoek haak links om de vervolg keuzelijst weer te geven.

   ![DNS onbekend](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Als u uw Privé-DNS zone-recordset onjuist hebt geconfigureerd, krijgt u een kritiek resultaat zoals: `Failed to resolve the Private DNS in subnet xxx` . 

In de vervolg keuzelijst **DNS-omzettings tabel** ziet u de details van het detail bericht van waaruit u uw configuratie kunt controleren.

## <a name="required-outbound-traffic"></a>Vereist uitgaand verkeer 

Als u **vereist uitgaand verkeer** selecteert, wordt met resultaten aangegeven of er problemen zijn met het uitgaande verkeer met toepassingen.  Goede apps worden als volgt weer gegeven:

* * Vereist uitgaand verkeer opgelost zonder problemen in het subnet ' subnet01 '.
* * Vereist uitgaand verkeer opgelost zonder problemen in het subnet ' subnet02 '.

Als een subnet wordt geblokkeerd door NSG of firewall regels, en als u het logboek niet hebt geblokkeerd, worden de volgende fouten weer te zien. U kunt controleren of u de verantwoordelijkheden van de [klant](spring-cloud-vnet-customer-responsibilities.md)hebt gezien.
    
   ![Eind punt is mislukt](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Als er `Required Outbound Traffic Table Renderings` binnen 30 minuten geen gegevens zijn, wordt het resultaat weer gegeven `health status unknown` . Misschien is uw netwerk geblokkeerd of is de logboek service niet actief.

   ![Diagnostisch eind punt onbekend](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Zie ook
* [Zelf diagnose van Azure lente-Cloud](spring-cloud-howto-self-diagnose-solve.md)