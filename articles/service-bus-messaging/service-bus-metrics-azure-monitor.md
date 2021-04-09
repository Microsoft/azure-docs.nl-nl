---
title: Azure Service Bus metrische gegevens in Azure Monitor | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u Azure Monitor kunt gebruiken om Service Bus entiteiten (wacht rijen, onderwerpen en abonnementen) te bewaken.
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 1e57def2ee7409ae62cdbe065c36e8a53a140e18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570574"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus metrische gegevens in Azure Monitor

Service Bus metrische gegevens geven u de status van resources in uw Azure-abonnement. Met een uitgebreide set met metrische gegevens kunt u de algemene status van uw Service Bus-resources controleren, niet alleen op het niveau van de naam ruimte, maar ook op het niveau van de entiteit. Deze statistieken kunnen van belang zijn wanneer u de status van Service Bus kunt bewaken. Metrische gegevens kunnen ook helpen bij het oplossen van problemen met de hoofd oorzaak zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

Azure Monitor biedt uniforme gebruikers interfaces voor bewaking in verschillende Azure-Services. Zie voor meer informatie [bewaking in Microsoft Azure](../azure-monitor/overview.md) en het voor beeld [Azure monitor metrische gegevens ophalen met .net](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) op github.

> [!IMPORTANT]
> Als er gedurende twee uur geen interactie met een entiteit is geweest, worden de metrische gegevens als een waarde weer gegeven totdat de entiteit niet langer inactief is.

## <a name="access-metrics"></a>Toegangs gegevens

Azure Monitor biedt meerdere manieren om toegang te krijgen tot metrische gegevens. U hebt toegang tot metrische gegevens via de [Azure Portal](https://portal.azure.com), of u kunt gebruikmaken van de Azure monitor API'S (rest en .net) en analyse oplossingen, zoals Azure monitor logboeken en Event hubs. Zie [metrische gegevens in azure monitor](../azure-monitor/essentials/data-platform-metrics.md)voor meer informatie.

Metrische gegevens zijn standaard ingeschakeld, en u kunt toegang krijgen tot de meest recente 30 dagen. Als u gegevens gedurende een langere periode wilt bewaren, kunt u metrische gegevens archiveren naar een Azure Storage-account. Deze waarde wordt geconfigureerd in [Diagnostische instellingen](../azure-monitor/essentials/diagnostic-settings.md) in azure monitor.

## <a name="access-metrics-in-the-portal"></a>Toegang tot metrische gegevens in de portal

U kunt metrische gegevens gedurende een bepaalde periode bewaken in de [Azure Portal](https://portal.azure.com). In het volgende voor beeld ziet u hoe u geslaagde aanvragen en inkomende aanvragen kunt weer geven op account niveau:

![Scherm afbeelding van de pagina met de monitor metrieken (preview) in de Azure Portal.][1]

U kunt ook rechtstreeks toegang krijgen tot metrische gegevens via de naam ruimte. Hiertoe selecteert u uw naam ruimte en selecteert u vervolgens **metrische gegevens**. Als u metrische gegevens wilt weer geven die zijn gefilterd op het bereik van de entiteit, selecteert u de entiteit en selecteert u vervolgens **metrische gegevens**.

![Scherm afbeelding van de pagina met monitor-metrische gegevens (preview), gefilterd op het bereik van de entiteit.][2]

Voor metrische gegevens ondersteunen dimensies, moet u filteren met de gewenste dimensie waarde.

## <a name="billing"></a>Billing

Metrische gegevens en waarschuwingen op Azure Monitor worden per waarschuwing in rekening gebracht. Deze kosten moeten beschikbaar zijn op de Portal wanneer de waarschuwing is ingesteld en voordat deze wordt opgeslagen. 

Aanvullende oplossingen waarmee metrische gegevens worden opgenomen, worden rechtstreeks door deze oplossingen in rekening gebracht. U wordt bijvoorbeeld gefactureerd door Azure Storage als u metrische gegevens archiveert naar een Azure Storage-account. u wordt ook gefactureerd door Log Analytics als u metrische gegevens streamt naar Log Analytics voor geavanceerde analyse.

Met de volgende metrische gegevens krijgt u een overzicht van de status van uw service. 

> [!NOTE]
> Er worden verschillende metrische gegevens overgezet omdat deze onder een andere naam worden geplaatst. Hiervoor moet u mogelijk uw referenties bijwerken. Metrische gegevens die zijn gemarkeerd met het sleutel woord ' afgeschaft ' worden niet verder ondersteund.

Alle waarden voor metrische gegevens worden elke minuut naar Azure Monitor verzonden. De granulatie tijd definieert het tijds interval waarvoor metrische waarden worden weer gegeven. Het ondersteunde tijds interval voor alle Service Bus meet waarden is 1 minuut.

## <a name="request-metrics"></a>Metrische gegevens aanvragen

Telt het aantal gegevens-en beheer bewerkings aanvragen.

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
| Binnenkomende aanvragen| Het aantal aanvragen voor de Service Bus-service gedurende een opgegeven periode. <br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Geslaagde aanvragen|Het aantal geslaagde aanvragen voor de Service Bus-service gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Server fouten|Het aantal aanvragen dat niet is verwerkt wegens een fout in de Service Bus-service gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Gebruikers fouten (Zie de volgende Subsectie)|Het aantal aanvragen dat niet is verwerkt wegens gebruikers fouten gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Vertraagde aanvragen|Het aantal aanvragen dat is beperkt omdat het gebruik is overschreden.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|

### <a name="user-errors"></a>Gebruikers fouten

De volgende twee typen fouten worden geclassificeerd als gebruikers fouten:

1. Fouten aan de client zijde (in HTTP die 400 fouten zouden zijn).
2. Fouten die optreden tijdens het verwerken van berichten, zoals [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metrische gegevens van bericht

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|Binnenkomende berichten|Het aantal gebeurtenissen of berichten dat is verzonden naar Service Bus gedurende een opgegeven periode. Deze metrische gegevens bevatten geen berichten die automatisch worden doorgestuurd.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Uitgaande berichten|Het aantal gebeurtenissen of berichten dat is ontvangen van Service Bus gedurende een opgegeven periode.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
| Berichten| Aantal berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: naam van entiteit |
| Actieve berichten| Aantal actieve berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: naam van entiteit |
| Onbestelbare berichten| Aantal onbestelbare berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/>Dimensie: naam van entiteit |
| Geplande berichten| Aantal geplande berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld  <br/> Dimensie: naam van entiteit |
| Voltooide berichten| Aantal voltooide berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: naam van entiteit |
| Afgebroken berichten| Aantal afgebroken berichten in een wachtrij/onderwerp. <br/><br/> Eenheid: aantal <br/> Aggregatie type: gemiddeld <br/> Dimensie: naam van entiteit |
| Grootte | Grootte van een entiteit (wachtrij of onderwerp) in bytes. <br/><br/>Eenheid: aantal <br/>Aggregatie type: gemiddeld <br/>Dimensie: naam van entiteit | 

> [!NOTE]
> Waarden voor berichten, actieve, onbestelbare, geplande, voltooide en afgebroken berichten zijn tijdgebonden waarden. Inkomende berichten die onmiddellijk na dat tijdstip zijn verbruikt, mogen niet worden weer gegeven in deze metrische gegevens. 

## <a name="connection-metrics"></a>Verbindings gegevens

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|Actieve verbindingen|Het aantal actieve verbindingen op een naam ruimte en een entiteit in de naam ruimte. De waarde voor deze metriek is een tijdgebonden waarde. Verbindingen die direct na dat tijdstip zijn geactiveerd, worden mogelijk niet weer spie geld in de metrische gegevens.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Geopende verbindingen |Het aantal geopende verbindingen.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|
|Gesloten verbindingen |Het aantal gesloten verbindingen.<br/><br/> Eenheid: aantal <br/> Aggregatie type: totaal <br/> Dimensie: naam van entiteit|

## <a name="resource-usage-metrics"></a>Metrische gegevens over resource gebruik

> [!NOTE] 
> De volgende metrische gegevens zijn alleen beschikbaar voor de **Premium** -laag. 
> 
> De belangrijkste metrische gegevens die moeten worden bewaakt voor de uitval van een naam ruimte met een Premium-laag zijn: **CPU-gebruik per naam ruimte** en **geheugen grootte per naam ruimte**. [Stel waarschuwingen](../azure-monitor/alerts/alerts-metric.md) in voor deze metrische gegevens met behulp van Azure monitor.
> 
> De andere metriek die u kunt bewaken is: **vertraagde aanvragen**. Het is niet mogelijk om een probleem op te lossen, omdat de naam ruimte binnen het geheugen, de CPU en de brokered Connections limieten blijft. Zie [beperking in azure service bus Premium-laag](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier) voor meer informatie.

| Naam meetwaarde | Description |
| ------------------- | ----------------- |
|CPU-gebruik per naam ruimte|Het percentage CPU-gebruik van de naam ruimte.<br/><br/> Eenheid: percentage <br/> Aggregatie type: maximum <br/> Dimensie: naam van entiteit|
|Gebruik van geheugen grootte per naam ruimte|Het percentage geheugen gebruik van de naam ruimte.<br/><br/> Eenheid: percentage <br/> Aggregatie type: maximum <br/> Dimensie: naam van entiteit|

## <a name="metrics-dimensions"></a>Metrische dimensies

Azure Service Bus ondersteunt de volgende dimensies voor metrische gegevens in Azure Monitor. Het toevoegen van dimensies aan uw metrische gegevens is optioneel. Als u geen dimensies toevoegt, worden metrische gegevens opgegeven op het niveau van de naam ruimte. 

|Dimensie naam|Description|
| ------------------- | ----------------- |
|Naam van entiteit| Service Bus ondersteunt Messa ging-entiteiten in de naam ruimte.|

## <a name="set-up-alerts-on-metrics"></a>Waarschuwingen instellen voor metrische gegevens

1. Selecteer op het tabblad **metrieken** van de pagina **Service Bus naam ruimte** de optie **waarschuwingen configureren**. 

    ![Pagina metrische gegevens: menu waarschuwingen configureren](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Selecteer de optie **doel selecteren** en voer de volgende acties uit op de pagina **een resource selecteren** : 
    1. Selecteer **Service Bus naam ruimten** voor het veld **filteren op resource type** . 
    2. Selecteer uw abonnement voor het veld **filteren op abonnement** .
    3. Selecteer de **Service Bus-naam ruimte** in de lijst. 
    4. Selecteer **Gereed**. 
    
        ![Naamruimte selecteren](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Selecteer **criteria toevoegen** en voer de volgende acties uit op de pagina **signaal logica configureren** :
    1. Selecteer **metrische gegevens** voor het **signaal type**. 
    2. Selecteer een signaal. Bijvoorbeeld: **service fouten**. 

        ![Server fouten selecteren](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Selecteer **groter dan** voor **waarde**.
    2. Selecteer **totaal** voor **tijd aggregatie**. 
    3. Voer **5** in als **drempel waarde**. 
    4. Selecteer **Gereed**.    

        ![Voor waarde opgeven](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Vouw op de pagina **regel maken** de optie **waarschuwings Details definiëren** uit en voer de volgende acties uit:
    1. Voer een **naam** in voor de waarschuwing. 
    2. Voer een **Beschrijving** in voor de waarschuwing.
    3. Selecteer **Ernst** voor de waarschuwing. 

        ![Scherm afbeelding van de pagina regel maken. Het definiëren van waarschuwings Details is uitgevouwen en de velden voor de naam, beschrijving en ernst van de waarschuwings regel zijn gemarkeerd.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Vouw op de pagina **regel maken** de optie **actie groep definiëren** uit, selecteer **nieuwe actie groep** en voer de volgende acties uit op de **pagina actie groep toevoegen**. 
    1. Voer een naam in voor de actie groep.
    2. Voer een korte naam in voor de actie groep. 
    3. Selecteer uw abonnement. 
    4. Selecteer een resourcegroep. 
    5. Voor dit scenario voert u **E-mail verzenden** in voor de **actie naam**.
    6. Selecteer **e-mail/SMS/push/Voice** voor **actie type**. 
    7. Selecteer **Details bewerken**. 
    8. Voer op de pagina **e-mail/SMS/push/Voice** de volgende acties uit:
        1. Selecteer **E-mail**. 
        2. Typ het **e-mail adres**. 
        3. Selecteer **OK**.

            ![Scherm afbeelding van de pagina actie groep toevoegen. Een actie met de naam ' e-mail verzenden ' met het actie type E-mail/SMS/push/Voice wordt toegevoegd aan de groep.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Selecteer **OK** op de pagina **actie groep toevoegen** . 
1. Selecteer op de pagina **regel maken** de optie **waarschuwings regel maken**. 

    ![Knop waarschuwings regel maken](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Azure monitor](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png