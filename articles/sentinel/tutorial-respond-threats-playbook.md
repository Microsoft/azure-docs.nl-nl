---
title: 'Zelfstudie: Een playbook uitvoeren in Azure Sentinel'
description: Gebruik deze zelfstudie om beveiligingsplaybooks te gebruiken in Azure Sentinel om geautomatiseerde bedreigingsreacties in te stellen voor beveiligingsproblemen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: e4afc5c8-ffad-4169-8b73-98d00155fa5a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2019
ms.author: yelevin
ms.openlocfilehash: b6fd26b4965b92f5f06a008d67e2d585fd1b41b7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94652073"
---
# <a name="tutorial-set-up-automated-threat-responses-in-azure-sentinel"></a>Zelfstudie: Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel



Gebruik deze zelfstudie om beveiligingsplaybooks te gebruiken in Azure Sentinel om geautomatiseerde bedreigingsreacties in te stellen voor beveiligingsproblemen die gedetecteerd worden door Azure Sentinel.


> [!div class="checklist"]
> * Meer informatie over playbooks
> * Een playbook maken
> * Een playbook uitvoeren
> * Bedreigingsreacties automatiseren


## <a name="what-is-a-security-playbook-in-azure-sentinel"></a>Wat is een beveiligingsplaybook in Azure Sentinel?

Een beveiligingsplaybook is een groep procedures die uitgevoerd kunnen worden vanaf Azure Sentinel als reactie op een waarschuwing. Een beveiligingsplaybook kan u helpen uw reactie te automatiseren en te organiseren, en kan handmatig worden uitgevoerd of worden ingesteld voor automatische uitvoering wanneer bepaalde waarschuwingen geactiveerd worden. Beveiliging playbooks in Azure Sentinel zijn gebaseerd op [Azure Logic Apps](../logic-apps/logic-apps-overview.md), wat betekent dat u alle kracht, mogelijkheden voor aanpassing en ingebouwde sjablonen van Logic Apps krijgt. Elke playbook wordt ontworpen voor het specifieke abonnement dat u kiest, maar wanneer u naar de pagina Playbooks kijkt, dan ziet u alle playbooks voor de geselecteerde abonnementen.

> [!NOTE]
> Playbooks maken gebruik van Azure Logic Apps, en hiervoor worden kosten in rekening gebracht. Ga naar de pagina met prijzen voor [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) voor meer informatie.

Als u zich bijvoorbeeld zorgen maakt over kwaadaardige aanvallers die toegang hebben tot uw netwerkresources, kunt u een waarschuwing instellen die schadelijke IP-adressen die verbinding maken met uw netwerk opspoort. Vervolgens kunt u een playbook maken dat het volgende doet:
1. Wanneer de waarschuwing wordt geactiveerd, opent u een ticket in ServiceNow of een ander IT-ticketsysteem.
2. Stuur een bericht naar uw beveiligingskanaal in Microsoft Teams of Slack om ervoor te zorgen dat uw beveilligingsanalisten op de hoogte zijn van het incident.
3. Deel alle informatie in de waarschuwing met uw senior netwerkbeheerder en beveiligingsbeheerder. Het e-mailbericht bevat ook twee knoppen voor gebruikersopties, **Blokkeren** of **Negeren**.
4. De Playbook blijft actief na het antwoord van de beheerders.
5. Als de beheerders kiezen voor **Blokkeren**, dan wordt het IP-adres geblokkeerd in de firewall en de gebruiker uitgeschakeld in Azure AD.
6. Als de beheerders kiezen voor **Negeren**, dan wordt de waarschuwing gesloten in Azure Sentinel en het incident gesloten in ServiceNow.

Beveiligingsplaybooks kunnen handmatig of automatisch worden uitgevoerd. Als u ze handmatig uitvoert, kunt u ervoor kiezen om een playbook op aanvraag uit te voeren als reactie op de geselecteerde waarschuwing. Automatisch uitvoeren betekent dat u de correlatieregel instelt om automatisch één of meer playbooks uit te voeren wanneer de waarschuwing geactiveerd wordt.


## <a name="create-a-security-playbook"></a>Een beveiligingsplaybook maken

Volg deze stappen om een nieuw beveiligingsplaybook te maken in Azure Sentinel:

1. Open het **Azure Sentinel**-dashboard.
2. Selecteer onder **Configuratie** de optie **Playbooks**.

   ![Logische apps](./media/tutorial-respond-threats-playbook/playbookimg.png)

3. Klik op de knop **Toevoegen** op de pagina **Azure Sentinel - Playbooks**.

   ![Logische app maken](./media/tutorial-respond-threats-playbook/create-playbook.png) 

4. Geef op de pagina **Logische app maken** de benodigde gegevens op voor het maken van de nieuwe logische app en klik op **Maken**. 

5. Selecteer in de [**Logic app Designer**](../logic-apps/logic-apps-overview.md) de sjabloon die u wilt gebruiken. Als u een sjabloon selecteert waarvoor referenties nodig zijn, dan moet u deze opgeven. U kunt ook een nieuw playbook maken vanaf nul. Selecteer **Lege logische app**. 

   ![Schermopname van het deelvenster Lege logische app.](./media/tutorial-respond-threats-playbook/playbook-template.png)

6. U wordt naar de Logic App Designer geleid, waar u een nieuwe app kunt maken of het sjabloon kunt bewerken. Voor meer informatie over een playbook maken met [Logic Apps](../logic-apps/logic-apps-create-logic-apps-from-templates.md).

7. Als u een nieuw playbook maakt, typ dan *Azure Sentinel* in het veld **Alle connectors en triggers doorzoeken** en selecteer **Wanneer een reactie op een Azure Sentinel-waarschuwing is geactiveerd**. <br>Nadat deze gemaakt is verschijnt de nieuwe playbook in de lijst **Playbooks**. Als dat niet het geval is, klikt u op **Vernieuwen**.

1. Gebruik de functies **Entiteiten ophalen** om de relevante entiteiten op te halen vanuit de lijst **Entiteiten**, zoals accounts, IP-adressen en hosts. Hiermee kunt u acties uitvoeren voor specifieke entiteiten.

7. Nu kunt u bepalen wat er moet gebeuren wanneer de playbook wordt geactiveerd. U kunt een actie, een logische voorwaarde, switch case-voorwaarden of lussen toevoegen.

   ![Ontwerper van logische app](./media/tutorial-respond-threats-playbook/logic-app.png)

## <a name="how-to-run-a-security-playbook"></a>Een beveiligingsplaybook uitvoeren

U kunt een playbook uitvoeren op aanvraag.

Een playbook uitvoeren op aanvraag:

1. Selecteer op de pagina **incidenten** een incident en klik op **Volledige gegevens weergeven**.

2. Klik in het tabblad **Waarschuwingen** op de waarschuwing waar u de playbook op wilt uitvoeren, schuif helemaal naar rechts en klik op **Playbooks weergeven** en selecteer een playbook om **uit te voeren** in de lijst met beschikbare playbooks in het abonnement. 



## <a name="automate-threat-responses"></a>Bedreigingsreacties automatiseren

SIEM/SOC-teams worden regelmatig overspoeld met beveiligingswaarschuwingen. Het aantal waarschuwingen is zo groot dat beveiligingsbeheerders overweldigd worden. Het resultaat is vaak dat waarschuwingen niet onderzocht kunnen worden, zodat de organisatie kwetsbaar is en aanvallen niet gedetecteerd worden. 

Veel, of zelfs alle, van deze waarschuwingen passen in terugkerende patronen die aangepakt kunnen worden met specifieke en vastgelegde herstelacties. Met Azure Sentinel kunt u al uw herstelacties vastleggen in playbooks. Het is ook mogelijk om realtime automatisering op te nemen in uw playbook om een volledig geautomatiseerde reactie in te stellen voor bepaalde beveiligingswaarschuwingen. Door realtime-automatisering te gebruiken, wordt de workload voor herstelteams aanzienlijk verlaagd. Ze kunnen de gebruikelijke reactie op veel voorkomende soorten waarschuwingen volledig automatiseren, zodat u zich kunt richten op meer unieke waarschuwingen, patronen kunt analyseren, bedreigingen kunt opsporen en meer.

Om reacties te automatiseren:

1. Selecteer de waarschuwing waarvoor u de reactie wilt automatiseren.
1. Kies op de pagina **Waarschuwingsregel bewerken**, onder **Realtime-automatisering**, de **Geactiveerde playbooks** die u wilt uitvoeren wanneer er een overeenkomst is met een waarschuwingsregel.
1. Selecteer **Opslaan**.

   ![realtime-automatisering](./media/tutorial-detect-threats/rt-configuration.png)






## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een playbook uitvoert in Azure Sentinel. Ga door naar [Proactief bedreigingen opsporen](hunting.md) met behulp van Azure Sentinel.