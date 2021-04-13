---
title: Waarschuwingen configureren en werken met metrische gegevens in de Azure VMware-oplossing
description: Meer informatie over het gebruik van waarschuwingen voor het ontvangen van meldingen. Leer ook hoe u met metrische gegevens kunt werken om meer inzicht te krijgen in uw persoonlijke cloud van Azure VMware-oplossingen.
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 486f25eba017b2d4e37c0796909a0d26adee6ba8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309625"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Azure-waarschuwingen in azure VMware-oplossing configureren 

In dit artikel leert u hoe u [Azure-actie groepen](/azure/azure-monitor/alerts/action-groups) in Microsoft Azure- [waarschuwingen](/azure/azure-monitor/alerts/alerts-overview) kunt configureren om meldingen te ontvangen van geactiveerde gebeurtenissen die u definieert. U leert ook hoe u [Azure monitor metrische gegevens](/azure/azure-monitor/essentials/data-platform-metrics) kunt gebruiken om dieper inzicht te krijgen in uw persoonlijke cloud van Azure VMware-oplossingen.


## <a name="supported-metrics-and-activities"></a>Ondersteunde metrische gegevens en activiteiten

De volgende metrische gegevens zijn zichtbaar door Azure Monitor metrische gegevens.

| **Signaal naam**                                                         | **Signaal type** | **Service bewaken** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Totale capaciteit Data Store-schijf                                           | Metrisch          | Platform            |
| Percentage Data Store-schijf gebruikt                                          | Metrisch          | Platform            |
| Percentage CPU                                                          | Metrisch          | Platform            |
| Gemiddeld effectief geheugen                                                | Metrisch          | Platform            |
| Gemiddeld geheugen overhead                                                 | Metrisch          | Platform            |
| Gemiddeld totaal geheugen                                                    | Metrisch          | Platform            |
| Gemiddeld geheugen gebruik                                                    | Metrisch          | Platform            |
| Gebruikte Data Store-schijf                                                     | Metrisch          | Platform            |
| Alle beheer bewerkingen                                           | Activiteitenlogboek    | Administratief      |
| Registreer de resource provider micro soft. AVS. (Micro soft. AVS/privateClouds) | Activiteitenlogboek    | Administratief      |
| Een PrivateCloud maken of bijwerken. (Micro soft. AVS/privateClouds)          | Activiteitenlogboek    | Administratief      |
| Een PrivateCloud verwijderen. (Micro soft. AVS/privateClouds)                    | Activiteitenlogboek    | Administratief      |

## <a name="configure-an-alert-rule"></a>Een waarschuwings regel configureren
1. Selecteer in de privécloud van uw Azure VMware-oplossing **controle**  >  **waarschuwingen** en vervolgens **nieuwe waarschuwings regel**.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Scherm afbeelding die laat zien waar u een waarschuwings regel in uw privécloud van Azure VMware-oplossing kunt configureren." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Er wordt een nieuw configuratie scherm geopend, waarin u het volgende kunt doen:
   - Het bereik definiëren
   - Een voor waarde configureren
   - De actie groep instellen
   - Details van de waarschuwings regel definiëren
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="Scherm opname van het venster waarschuwings regel maken." lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. Selecteer onder **bereik** de doel resource die u wilt bewaken. De privécloud van de Azure VMware-oplossing van waaruit u het menu waarschuwingen hebt geopend, is standaard gedefinieerd.

1. Onder **voor waarde**, selecteert u **voor waarde toevoegen**, en in het venster dat wordt geopend, selecteert u het signaal dat u voor de waarschuwings regel wilt maken. 

   In ons voor beeld hebben we het **percentage Data Store-schijf gebruikt** geselecteerd, dat relevant is voor een sla-perspectief van [Azure VMware-oplossing](https://aka.ms/avs/sla) . 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Scherm opname van het venster signaal logica configureren met vooraf gedefinieerde signaal namen."::: 

1. Definieer de logica waarmee de waarschuwing wordt geactiveerd en selecteer vervolgens **gereed**. 

   In ons voor beeld zijn alleen de **drempel waarde** en de **frequentie van evaluatie** aangepast. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Scherm afbeelding met de informatie voor de geselecteerde signaal logica."::: 

1. Onder **acties**, selecteer **actie groepen toevoegen**. De actie groep definieert *hoe* de melding wordt ontvangen en *wie* deze ontvangt.   U kunt meldingen ontvangen via e-mail, SMS, een [push melding van Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/) of een gesp roken bericht.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="Scherm opname van de bestaande actie groepen en waar een nieuwe actie groep wordt gemaakt.":::

1. In het venster dat wordt geopend, selecteert u **actie groep maken**.

   >[!TIP]
   > U kunt ook een bestaande actie groep gebruiken.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="Scherm opname van de actie groepen die moeten worden geselecteerd voor de waarschuwing."::: 

 

 
1. Geef in het venster dat wordt geopend, op het tabblad **basis beginselen** , de actie groep een naam en een weergave naam.

1. Selecteer het tabblad **meldingen** , selecteer een **meldings type** en **naam**. Selecteer vervolgens **OK**.

   Ons voor beeld is gebaseerd op e-mail meldingen.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="Scherm afbeelding met de instellingen voor e-mail, SMS-bericht, push en Voice voor de waarschuwing." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. Beschrijving Configureer de **acties** als u proactieve acties wilt uitvoeren en meldingen over de gebeurtenis wilt ontvangen. Selecteer een beschikbaar **actie type** en selecteer vervolgens **controleren + maken**. 
   - Automation-runbooks
   - Azure Functions – voor het uitvoeren van aangepaste, door de gebeurtenis gestuurde uitvoering van server code
   - ITSM – om te integreren met een service provider als ServiceNow voor het maken van een ticket
   - Logische app: voor complexere werk stroom indeling
   - Webhooks: een proces in een andere service activeren

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="Scherm opname van het venster actie groep maken met de focus op de vervolg keuzelijst actie type." lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. Geef onder de details van de **waarschuwings regel** een naam, beschrijving, resource groep op voor het opslaan van de waarschuwings regel, de ernst. Selecteer vervolgens **waarschuwings regel maken**.
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="Scherm opname van de details van de waarschuwings regel."::: 
 
   De waarschuwings regel is zichtbaar en kan worden beheerd vanuit het Azure Portal.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Scherm opname van de nieuwe waarschuwings regel in het regel venster." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   Zodra een metriek de drempel waarde bereikt zoals gedefinieerd in een waarschuwings regel, wordt het menu **waarschuwingen** bijgewerkt en zichtbaar gemaakt.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Scherm opname waarin de waarschuwing wordt weer gegeven nadat de drempel waarde is opgegeven." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   Afhankelijk van de geconfigureerde actie groep ontvangt u een melding via het geconfigureerde medium. In ons voor beeld hebben we e-mail geconfigureerd.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Scherm opname van een Azure Monitor waarschuwing met de fout teken reeks en de datum-en tijd gebeurtenis is geactiveerd."::: 

## <a name="work-with-metrics"></a>Werken met metrische gegevens

1. Selecteer metrische gegevens **bewaken** in de privécloud van uw Azure VMware-oplossing  >  . Selecteer vervolgens de gewenste waarde in de vervolg keuzelijst.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Scherm opname van het venster metrische gegevens en de focus op de vervolg keuzelijst metriek." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. U kunt de para meters van het diagram wijzigen, zoals het **tijds bereik** of de granulatie van de **tijd**. 

   Andere opties zijn:
   - **Inzoomen op Logboeken** en query's uitvoeren op de gegevens in de gerelateerde log Analytics werk ruimte
   - **Dit diagram vastmaken** aan een Azure-dash board voor het gemak.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Scherm afbeelding met de opties voor het tijds bereik en de tijd voor metrische gegevens." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Volgende stappen

Nu u een waarschuwings regel hebt geconfigureerd voor uw persoonlijke cloud van Azure VMware-oplossing, kunt u meer informatie over het volgende doen:
- [Metrische gegevens van Azure Monitor](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure Monitor-waarschuwingen](/azure/azure-monitor/alerts/alerts-overview)
- [Azure-actie groepen](/azure/azure-monitor/alerts/action-groups)

U kunt ook door gaan met een van de andere hand leidingen voor [Azure VMware-oplossingen](index.yml) .





