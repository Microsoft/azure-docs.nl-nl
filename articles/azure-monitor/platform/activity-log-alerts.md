---
title: Waarschuwingen voor activiteiten logboek in Azure Monitor
description: Ontvang een melding via SMS, webhook, SMS, e-mail en meer wanneer bepaalde gebeurtenissen in het activiteiten logboek optreden.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 8a30c0a0527f98cc00f7888299c09f1f26c3dd09
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735211"
---
# <a name="alerts-on-activity-log"></a>Meldingen over activiteitenlogboek

## <a name="overview"></a>Overzicht

Waarschuwingen voor activiteiten logboeken zijn waarschuwingen die worden geactiveerd wanneer een nieuwe [activiteiten logboek gebeurtenis](activity-log-schema.md) optreedt die overeenkomt met de voor waarden die zijn opgegeven in de waarschuwing. Op basis van de volg orde en het volume van de gebeurtenissen die zijn vastgelegd in het [activiteiten logboek van Azure](platform-logs-overview.md), wordt de waarschuwings regel geactiveerd. Waarschuwings regels voor activiteiten logboeken zijn Azure-resources, zodat ze kunnen worden gemaakt met behulp van een Azure Resource Manager sjabloon. Ze kunnen ook worden gemaakt, bijgewerkt of verwijderd in de Azure Portal. In dit artikel worden de concepten geïntroduceerd van waarschuwingen voor activiteiten Logboeken. Zie [waarschuwingen voor activiteiten logboek maken en beheren](alerts-activity-log.md)voor meer informatie over het maken of gebruiken van waarschuwings regels voor activiteiten Logboeken.

> [!NOTE]
> * Er **kunnen geen** waarschuwingen worden gemaakt voor gebeurtenissen in de categorie waarschuwing van het activiteiten logboek.
> * Waarschuwingen voor activiteiten logboeken met de beveiligings categorie kunnen ook worden gedefinieerd in een [nieuwe bijgewerkte stroom](../../security-center/continuous-export.md?tabs=azure-portal) naar [ServiceNow](../../security-center/export-to-siem.md)

Normaal gesp roken maakt u waarschuwingen voor activiteiten Logboeken om meldingen te ontvangen wanneer:

* Specifieke bewerkingen worden uitgevoerd op resources in uw Azure-abonnement, wat vaak van toepassing is op bepaalde resource groepen of resources. Stel dat u een melding wilt ontvangen wanneer een virtuele machine in myProductionResourceGroup wordt verwijderd. Het kan ook zijn dat u een melding wilt ontvangen als nieuwe rollen zijn toegewezen aan een gebruiker in uw abonnement.
* Er treedt een service Health-gebeurtenis op. Service Health-gebeurtenissen zijn onder andere melding van incidenten en onderhouds gebeurtenissen die van toepassing zijn op resources in uw abonnement.

Een eenvoudige analoge waarde voor de voor waarden waarop waarschuwings regels kunnen worden gemaakt in het activiteiten logboek, is om gebeurtenissen te verkennen of filteren via [activiteiten logboek in azure Portal](./activity-log.md#view-the-activity-log). In Azure Monitor-activiteiten logboek kan één een vereiste gebeurtenis filteren of vinden en vervolgens een waarschuwing maken met behulp van de knop waarschuwing voor het **activiteiten logboek toevoegen** .

In beide gevallen controleert een activiteiten logboek waarschuwing alleen op gebeurtenissen in het abonnement waarin de waarschuwing is gemaakt.

U kunt een waarschuwing voor een activiteiten logboek configureren op basis van een eigenschap op het hoogste niveau in het JSON-object voor een activiteiten logboek gebeurtenis. Zie [Categorieën in het activiteiten logboek](./activity-log.md#view-the-activity-log)voor meer informatie. Zie [waarschuwingen voor activiteiten logboeken ontvangen in service meldingen](../../service-health/alerts-activity-log-service-notifications-portal.md)voor meer informatie over service status gebeurtenissen. 

Waarschuwingen voor activiteiten logboeken bevatten enkele algemene opties:

- **Categorie**: beheer, service Health, automatisch schalen, beveiliging, beleid en aanbeveling. 
- **Bereik**: de afzonderlijke resource of set met resources waarvoor de waarschuwing voor het activiteiten logboek is gedefinieerd. Het bereik van een waarschuwing voor een activiteiten logboek kan op verschillende niveaus worden gedefinieerd:
    - Resource niveau: bijvoorbeeld voor een specifieke virtuele machine
    - Niveau van de resource groep: bijvoorbeeld alle virtuele machines in een specifieke resource groep
    - Abonnements niveau: bijvoorbeeld alle virtuele machines in een abonnement (of) alle resources in een abonnement
- **Resource groep**: standaard wordt de waarschuwings regel opgeslagen in dezelfde resource groep als die van het doel dat in bereik is gedefinieerd. De gebruiker kan ook de resource groep definiëren waarin de waarschuwings regel moet worden opgeslagen.
- **Resource type**: in Resource Manager gedefinieerde naam ruimte voor het doel van de waarschuwing.
- **Bewerkings naam**: de [bewerkings](../../role-based-access-control/resource-provider-operations.md) naam van de Azure-resource provider die wordt gebruikt voor toegangs beheer op basis van rollen. Bewerkingen die niet zijn geregistreerd met Azure Resource Manager kunnen niet worden gebruikt in een waarschuwings regel voor het activiteiten logboek.
- **Niveau**: het Ernst niveau van de gebeurtenis (informatief, waarschuwing, fout of kritiek).
- **Status**: de status van de gebeurtenis, meestal gestart, mislukt of geslaagd.
- **Gebeurtenis gestart door**: ook wel bekend als de aanroeper. Het e-mail adres of de Azure Active Directory id van de gebruiker die de bewerking heeft uitgevoerd.

> [!NOTE]
> In een abonnement kunnen Maxi maal 100 waarschuwings regels worden gemaakt voor een activiteit van het bereik: een enkele resource, alle resources in de resource groep (of) het volledige abonnements niveau.

Wanneer een waarschuwing voor een activiteiten logboek wordt geactiveerd, wordt een actie groep gebruikt voor het genereren van acties of meldingen. Een actie groep is een herbruikbare set meldings ontvangers, zoals e-mail adressen, webhook-Url's of SMS-telefoon nummers. Er kan vanuit meerdere waarschuwingen naar de ontvangers worden verwezen om uw meldings kanalen te centraliseren en te groeperen. Wanneer u de waarschuwing voor uw activiteiten logboek definieert, hebt u twee opties. U kunt:

* Een bestaande actie groep gebruiken in uw activiteiten logboek waarschuwing.
* Maak een nieuwe actie groep.

Zie voor meer informatie over actie groepen [actie groepen maken en beheren in de Azure Portal](action-groups.md).


## <a name="next-steps"></a>Volgende stappen

- Een [overzicht van waarschuwingen](alerts-overview.md)weer geven.
- Meer informatie over het [maken en wijzigen van waarschuwingen voor activiteiten logboeken](alerts-activity-log.md).
- Bekijk het [webhookschema voor waarschuwingen voor het activiteitenlogboek](activity-log-alerts-webhook.md).
- Meer informatie over [servicestatusmeldingen](../../service-health/service-notifications.md).