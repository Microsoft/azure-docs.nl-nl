---
title: Advanced Threat Protection voor Azure Cosmos DB
description: Meer informatie over het Azure Cosmos DB versleutelen van gegevens in rust en hoe deze worden geïmplementeerd.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455583"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Advanced Threat Protection voor Azure Cosmos DB (preview-versie)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Advanced Threat Protection voor Azure Cosmos DB biedt een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen detecteert om Azure Cosmos DB accounts te openen of misbruik te maken. Met deze beveiligingslaag kunt u bedreigingen aanpakken, zelfs zonder een beveiligings expert, en ze integreren met centrale beveiligings bewakings systemen.

Beveiligingswaarschuwingen worden geactiveerd wanneer zich afwijkingen in de activiteit voordoen. Deze beveiligings waarschuwingen zijn geïntegreerd met  [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnements beheerders, met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de bedreigingen.

> [!NOTE]
>
> * Advanced Threat Protection voor Azure Cosmos DB is momenteel alleen beschikbaar voor de SQL-API.
> * Advanced Threat Protection voor Azure Cosmos DB is momenteel niet beschikbaar in azure Government en soevereine Cloud regio's.

Voor een volledige onderzoek van de beveiligings waarschuwingen, is het aanbevolen om [Diagnostische logboek registratie in azure Cosmos db in](./monitor-cosmos-db.md)te scha kelen, waarmee bewerkingen op de data base zelf worden geregistreerd, inclusief ruwe bewerkingen op alle documenten, containers en data bases.

## <a name="threat-types"></a>Bedreigingstypen

Advanced Threat Protection voor Azure Cosmos DB detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van data bases. De volgende waarschuwingen kunnen momenteel worden geactiveerd:

- **Toegang vanaf ongebruikelijke locaties**: deze waarschuwing wordt geactiveerd wanneer er een wijziging is in het toegangs patroon voor een Azure Cosmos-account, waarbij iemand verbinding heeft gemaakt met het Azure Cosmos DB-eind punt vanaf een ongebruikelijke geografische locatie. In sommige gevallen detecteert de waarschuwing een rechtmatige actie, wat een nieuwe toepassing of onderhouds bewerking van de ontwikkelaar is. In andere gevallen detecteert de waarschuwing een schadelijke actie van een voormalige werk nemer, externe aanvaller, enzovoort.

- **Ongebruikelijke gegevens extractie**: deze waarschuwing wordt geactiveerd wanneer een client een ongebruikelijke hoeveelheid gegevens uit een Azure Cosmos DB-account uitpakt. Dit kan het symptoom zijn van sommige gegevens exfiltration die worden uitgevoerd om alle gegevens die zijn opgeslagen in het account over te dragen naar een extern gegevens archief.



## <a name="configure-advanced-threat-protection"></a>Advanced Threat Protection configureren

U kunt geavanceerde beveiliging tegen bedreigingen op verschillende manieren configureren, zoals beschreven in de volgende secties.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Start de Azure Portal op  [https://portal.azure.com](https://portal.azure.com/) .

2. Selecteer vanuit het Azure Cosmos DB-account in het menu **instellingen** de optie **geavanceerde beveiliging**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="ATP instellen":::

3. Op de Blade **Geavanceerde beveiligings** configuratie:

    * Klik op de optie **geavanceerde beveiliging tegen bedreigingen** om deze **in** te stellen op aan.
    * Klik op **Opslaan** om het nieuwe of bijgewerkte Advanced Threat Protection-beleid op te slaan.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Gebruik rest API-opdrachten om de instelling geavanceerde beveiliging tegen bedreigingen te maken, bij te werken of op te halen voor een specifiek Azure Cosmos DB-account.

* [Advanced Threat Protection-maken](/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection-ophalen](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-cmdlets:

* [Advanced Threat Protection inschakelen](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Geavanceerde beveiliging tegen bedreigingen verkrijgen](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Geavanceerde bedreigings beveiliging uitschakelen](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[ARM-sjabloon](#tab/arm-template)

Gebruik een Azure Resource Manager ARM-sjabloon om Cosmos DB in te stellen als Advanced Threat Protection is ingeschakeld.
Zie [een CosmosDB-account maken met geavanceerde beveiliging tegen bedreigingen](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)voor meer informatie.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Gebruik een Azure Policy om geavanceerde bedreigingen beveiliging in te scha kelen voor Cosmos DB.

1. Start de pagina Azure **Policy-Definitions** en zoek naar het **Cosmos DBS beleid Deploying Advanced Threat Protection** .

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Zoek beleid"::: 

1. Klik op het beleid **geavanceerde beveiliging tegen bedreigingen implementeren voor CosmosDB** en klik vervolgens op **toewijzen**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Abonnement of groep selecteren":::


1. Klik in het veld **bereik** op de drie puntjes, selecteer een Azure-abonnement of resource groep en klik vervolgens op **selecteren**.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Pagina beleids definities":::


1. Voer de andere para meters in en klik op **toewijzen**.




## <a name="manage-atp-security-alerts"></a>ATP-beveiligings waarschuwingen beheren

Wanneer Azure Cosmos DB afwijkende activiteiten optreden, wordt een beveiligings waarschuwing geactiveerd met informatie over de verdachte beveiligings gebeurtenis. 

 Vanuit Azure Security Center kunt u uw huidige [beveiligings waarschuwingen](../security-center/security-center-alerts-overview.md)controleren en beheren.  Klik op een specifieke waarschuwing in [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) om mogelijke oorzaken en aanbevolen acties te bekijken om de mogelijke dreiging te onderzoeken en te verhelpen. In de volgende afbeelding ziet u een voor beeld van waarschuwings details die zijn opgenomen in Security Center.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Details van bedreiging":::

Er wordt ook een e-mail melding met de waarschuwings Details en aanbevolen acties verzonden. De volgende afbeelding toont een voor beeld van een e-mail waarschuwing.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Waarschuwings Details":::

## <a name="cosmos-db-atp-alerts"></a>Cosmos DB ATP-waarschuwingen

 Zie de sectie [Cosmos DB-waarschuwingen](../security-center/alerts-reference.md#alerts-azurecosmos) in de Azure Security Center documentatie voor een overzicht van de waarschuwingen die worden gegenereerd bij het controleren van Azure Cosmos DB accounts.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Diagnostische logboek registratie in azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Meer informatie over [Azure Security Center](../security-center/security-center-introduction.md)