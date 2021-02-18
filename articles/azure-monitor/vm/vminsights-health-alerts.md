---
title: Gaststatuswaarschuwingen van Azure Monitor voor VM's (preview)
description: Hierin worden de waarschuwingen beschreven die zijn gemaakt door Azure Monitor voor VM's gast status, inclusief het inschakelen en instellen van meldingen.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 30025f387768aaf1e4d642292c21d5b15ccc7451
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100612684"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>Gaststatuswaarschuwingen van Azure Monitor voor VM's (preview)
Met Azure Monitor voor VM's gast status kunt u de status van een virtuele machine weer geven zoals gedefinieerd door een set prestatie metingen die regel matig worden steek proeven. Er kan een waarschuwing worden gemaakt wanneer de status van een virtuele machine wordt gewijzigd of als de monitor wordt beschadigd. U kunt deze waarschuwingen weer geven en beheren met [gebruikers die zijn gemaakt door waarschuwings regels in azure monitor](../platform/alerts-overview.md) en ervoor kiezen om proactief te worden gewaarschuwd wanneer er een nieuwe waarschuwing wordt gemaakt.

## <a name="configure-alerts"></a>Waarschuwingen configureren
U kunt geen expliciete waarschuwings regel voor Azure Monitor voor VM's gast status maken als deze functie in preview is. Standaard worden er voor elke virtuele machine waarschuwingen gemaakt, maar niet voor elke monitor.  Dit betekent dat als een monitor verandert in een status die niet van invloed is op de huidige status van de virtuele machine, er geen waarschuwing wordt gemaakt, omdat de status van de virtuele machine niet is gewijzigd. 

U kunt waarschuwingen voor een bepaalde virtuele machine uitschakelen of voor een bepaalde monitor op een virtuele machine uit de instelling van de **waarschuwings status** in de configuratie voor de virtuele machine in de Azure Portal. Zie [bewaking configureren in azure monitor voor VM's-gast status (preview)](vminsights-health-configure.md) voor meer informatie over het configureren van monitors in de Azure Portal. Zie [bewaking configureren in azure monitor voor VM's status van de gast met behulp van regels voor gegevens verzameling (preview)](vminsights-health-configure-dcr.md) voor meer informatie over het configureren van monitors in een set virtuele machines.

## <a name="alert-severity"></a>Ernst van waarschuwing
De ernst van de waarschuwing die is gemaakt door de status van de gast, verwijst rechtstreeks naar de ernst van de virtuele machine of de monitor die de waarschuwing heeft geactiveerd.

| Monitor status | Ernst van waarschuwing |
|:---|:---|
| Kritiek | Sev1 |
| Waarschuwing  | Sev2 |
| In orde  | Sev4 |

## <a name="alert-lifecycle"></a>Waarschuwing levens cyclus
Er wordt een [Azure-waarschuwing](../platform/alerts-overview.md) gemaakt voor elke virtuele machine telkens wanneer deze wordt gewijzigd in een **waarschuwing** of **kritieke** status. Bekijk de waarschuwing van **waarschuwingen** in het menu **Azure monitor** of in het menu van de virtuele machine in de Azure Portal.

Als er al een waarschuwing wordt **geactiveerd** wanneer de status van de virtuele machine wordt gewijzigd, wordt er geen tweede waarschuwing gemaakt, maar wordt de ernst van dezelfde waarschuwing gewijzigd zodat deze overeenkomt met de status van de virtuele machine. Als de virtuele machine bijvoorbeeld verandert in een **kritieke** status wanneer er al een **waarschuwing** is **geactiveerd** , wordt de ernst van de waarschuwing gewijzigd in **Sev1**. Als de virtuele machine verandert in een **waarschuwings** status wanneer er al een **Sev1** -waarschuwing is **geactiveerd** , wordt de ernst van de waarschuwing gewijzigd in **Sev2**. Als de virtuele machine weer in de status **in orde** wordt gezet, wordt de waarschuwing opgelost met de ernst gewijzigd in **Sev4**.

## <a name="viewing-alerts"></a>Waarschuwingen weer geven
Waarschuwingen weer geven die zijn gemaakt door Azure Monitor voor VM's gast status met andere [waarschuwingen in de Azure Portal](../platform/alerts-overview.md#alerts-experience). U kunt **waarschuwingen** selecteren in het menu **Azure monitor** om waarschuwingen weer te geven voor alle bewaakte resources, of **waarschuwingen** selecteren in het menu van een virtuele machine om waarschuwingen voor de virtuele machine weer te geven.

## <a name="alert-properties"></a>Waarschuwingseigenschappen

### <a name="properties-in-the-azure-portal"></a>Eigenschappen in de Azure Portal
De eigenschappen van de waarschuwing wanneer u deze in de Azure Portal bekijkt, worden in de volgende tabel beschreven.

| Eigenschap | Beschrijving |
|:---|:---|
| Status controleren voordat de waarschuwing is gemaakt | De status van de monitor of virtuele machine voordat deze waarschuwing de eerste keer werd geactiveerd. |
| Status controleren wanneer de waarschuwing is gemaakt | Status van monitor of virtuele machine wanneer de waarschuwing de eerste keer werd geactiveerd. Dit is de status waardoor de waarschuwing wordt geactiveerd. |
| Meer informatie over de status overgang wanneer de waarschuwing is gemaakt | Koppeling naar de VM-status pagina waar u de exacte status overgang kunt zien. Deze status overgang vertegenwoordigt het exemplaar wanneer de monitor voor het eerst van de status in **orde** is geworden in een niet-goede status. |

Een monitor gaat bijvoorbeeld van in **orde** tot **kritiek** in tijd t0 en er wordt een nieuwe waarschuwing geactiveerd met **Sev1**. Het gaat vervolgens van **kritiek** tot **waarschuwing** op tijdstip T1 en de ernst van de waarschuwing wordt bijgewerkt naar **Sev2**. De waarschuwing wordt in **orde** gewijzigd op het tijdstip T2 en het waarschuwings bericht wordt opgelost.

De waarschuwings eigenschappen hebben deze waarden tijdens deze hele reeks.

- Controleer de status voordat de waarschuwing is gemaakt: in orde
- Status controleren wanneer de waarschuwing is gemaakt: kritiek
- Meer informatie over de status overgang tijdens het maken van een waarschuwing: de navigatie koppeling naar de status overgang is op tijd t0 gebeurd.


### <a name="properties-in-notifications"></a>Eigenschappen in meldingen
De eigenschappen van de waarschuwing die in meldingen zijn opgenomen, worden in de volgende tabel beschreven.

| Eigenschap | Beschrijving |
|:---|:---|
| Vorige monitor status | Status van monitor of virtuele machine voordat deze de status heeft gewijzigd. Als met de update voor de ernst van de waarschuwing deze melding wordt geactiveerd, vertegenwoordigt deze eigenschap de status die net vóór de ernst update was. |
| Huidige monitor status | Status van monitor of virtuele machine wanneer deze de status heeft gewijzigd. Als met de update voor de ernst van de waarschuwing deze melding wordt geactiveerd, vertegenwoordigt deze eigenschap de nieuwe status. |
| Meer informatie over deze status overgang | Koppeling naar de VM-status pagina waar u de exacte status overgang kunt zien. Deze status overgang vertegenwoordigt het exemplaar wanneer de status van de monitor is gewijzigd en deze melding heeft geactiveerd. |

In het bovenstaande voor beeld hebben meldingen de volgende eigenschappen op elk moment.

Melding ontvangen op tijdstip t0
- Vorige monitor status: in orde
- Huidige monitor status: kritiek
- Meer informatie over deze status overgang: navigatie koppeling naar de status overgang is op tijd t0.

Melding ontvangen op tijdstip T1
- Vorige monitor status: kritiek
- Huidige monitor status: waarschuwing
- Als u meer wilt weten over deze status overgang: de navigatie koppeling naar de status overgang is opgetreden op tijdstip T1.

Melding ontvangen op tijdstip T2
- Vorige monitor status: waarschuwing
- Huidige monitor status: in orde
- Als u meer wilt weten over deze status overgang: de navigatie koppeling naar de status overgang is opgetreden op tijdstip t2.

## <a name="configure-notifications"></a>Meldingen configureren
Als u proactief een melding wilt ontvangen van een waarschuwing die wordt geactiveerd door de gast status, maakt u een [actie groep](../alerts/action-groups.md) om de verschillende acties te definiëren die moeten worden uitgevoerd, zoals het verzenden van een SMS-bericht of het starten van een logische app. Maak vervolgens een [actie regel](../alerts/alerts-action-rules.md) waarmee het bereik van monitors en virtuele machines wordt opgegeven en die actie groep wordt gebruikt.

Selecteer in het menu **monitor** in de Azure Portal **waarschuwingen**.  Selecteer **acties beheren** en vervolgens **actie regels (preview)**. 

![Nieuwe actie regel](media/vminsights-health-alerts/action-rule-new.png)

Klik op **nieuwe actie regel** om een nieuwe regel te maken. Klik op **selecteren** naast bereik en selecteer een abonnement, resource groep of een of meer specifieke virtuele machines. De melding wordt alleen geactiveerd voor virtuele machines die binnen het bereik vallen.

![Bereik van actie regel](media/vminsights-health-alerts/action-rule-scope.png)

Klik op **toevoegen** naast **filter**. Een filter maken waarbij de **Monitor-service gelijk is aan de status van de VM-inzichten**. Voeg andere filters toe om de specifieke waarschuwingen op te geven waarmee de melding moet worden geactiveerd. U kunt bijvoorbeeld **Ernst** gebruiken om waarschuwingen te vinden van alle monitors die overeenkomen met een bepaalde ernst.

![Actie regel filter](media/vminsights-health-alerts/action-rule-filter.png)

Selecteer in **definiëren voor dit bereik** **actie groep** en selecteer vervolgens de actie groep die u aan de monitor wilt koppelen. Geef een naam op voor de regel en selecteer de resource groep die moet worden opgeslagen in. Klik op **maken** om de regel te maken.

![Actie regel](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Volgende stappen

- [Gast status inschakelen in Azure Monitor voor VM's en onboard agents.](vminsights-health-enable.md)
- [Configureer monitors met behulp van de Azure Portal.](vminsights-health-configure.md)
- [Configureer monitors met behulp van regels voor het verzamelen van gegevens.](vminsights-health-configure-dcr.md)