---
title: Eindpunten beheren in Azure Traffic Manager | Microsoft Docs
description: Dit artikel helpt u bij het toevoegen, verwijderen, inschakelen en uitschakelen van eindpunten vanuit Azure Traffic Manager.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: duau
ms.openlocfilehash: fc6e8bcda17f9d788a430ed763bd9f8702eaf250
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003545"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Eindpunten toevoegen, uitschakelen, inschakelen of verwijderen

De functie Web Apps in Azure App Service biedt al failover- en round-robinverkeersroutering voor websites in een datacenter, ongeacht de websitemodus. Met Azure Traffic Manager kunt u failover- en round-robinverkeersroutering opgeven voor websites en cloudservices in verschillende datacenters. Het eerste dat u moet doen om deze functionaliteit te gebruiken, is het cloudservice- of website-eindpunt toevoegen aan Traffic Manager.

U kunt ook afzonderlijke eindpunten uitschakelen die deel uitmaken van een Traffic Manager-profiel. Als u een eindpunt uitschakelt, blijft het onderdeel van het profiel, maar het profiel gedraagt zich alsof het eindpunt er niet in is opgenomen. Deze actie is handig als u een eindpunt dat in de onderhoudsmodus staat of opnieuw wordt geïmplementeerd, tijdelijk wilt verwijderen. Zodra het eindpunt opnieuw actief is, kan het worden ingeschakeld.

> [!NOTE]
> Het uitschakelen van een eindpunt heeft geen invloed op de implementatiestatus in Azure. Een eindpunt met een goede status blijft actief en in staat om verkeer te verwerken, zelfs als het in Traffic Manager is uitgeschakeld. Bovendien heeft het uitschakelen van een eindpunt in een bepaald profiel geen invloed op de status ervan in een ander profiel.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Een cloudservice of app-service-eindpunt toevoegen aan een Traffic Manager-profiel

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).
2. Zoek in de zoekbalk van de portal naar de naam van het **Traffic Manager-profiel** dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op de blade **Traffic Manager-profiel** in de sectie **Instellingen** op **Eindpunten**.
4. Klik op de blade **Eindpunten** die wordt weergegeven, op **Toevoegen**.
5. Voltooi de blade **Eindpunt toevoegen** als volgt:
    1. Klik bij **Type** op **Azure-eindpunt**.
    2. Geef een **Naam** op waarmee u dit eindpunt kunt herkennen.
    3. Kies bij **Doelresourcetype** in de vervolgkeuzelijst het juiste resourcetype.
    4. Voor **Doelresource** klikt u op de selector **Kiezen** om resources onder hetzelfde abonnement in de **blade Resources** op te nemen. Kies op de blade **Resource** die wordt weergegeven, de service die u wilt toevoegen als eerste eindpunt.
    5. Selecteer bij **Prioriteit** de optie **1**. Dit zorgt ervoor dat alle verkeer naar dit eindpunt wordt geleid, indien het eindpunt in orde is.
    6. Laat **Toevoegen als uitgeschakeld** uit staan.
    7. Klik op **OK**
6.  Herhaal de stappen 4 en 5 om het volgende Azure-eindpunt toe te voegen. Zorg ervoor dat dit eindpunt wordt toegevoegd met de waarde **2** als bijbehorende **Prioriteit**.
7.  Als beide eindpunten zijn toegevoegd, worden ze weergegeven op de blade **Traffic Manager-profiel**, samen met de controlestatus **Online**.

> [!NOTE]
> Nadat een eindpunt hebt toegevoegd aan of verwijderd uit een profiel met de verkeersrouteringsmethode *Failover*, wordt de failover-prioriteitenlijst mogelijk niet geordend zoals u dat wilt. U kunt de volgorde van de Failover-prioriteitenlijst aanpassen op de pagina Configuratie. Zie voor meer informatie [Configure Failover traffic routing](traffic-manager-configure-failover-routing-method.md) (Failover-verkeersroutering configureren).

## <a name="to-disable-an-endpoint"></a>Een eindpunt uitschakelen

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).
2. Zoek in de zoekbalk van de portal naar de naam van het **Traffic Manager-profiel** dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op de blade **Traffic Manager-profiel** in de sectie **Instellingen** op **Eindpunten**. 
4. Klik op het eind punt dat u wilt uitschakelen.
5. Wijzig op de blade **Eindpunt** de eindpuntstatus in **Uitgeschakeld** en klik vervolgens op **Opslaan**.
6. Clients blijven verkeer verzenden naar het eindpunt voor de duur van de Time-to-Live (TTL). U kunt de TTL wijzigen op de pagina Configuratie van het Traffic Manager-profiel.

## <a name="to-enable-an-endpoint"></a>Een eindpunt inschakelen

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).
2. Zoek in de zoekbalk van de portal naar de naam van het **Traffic Manager-profiel** dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op de blade **Traffic Manager-profiel** in de sectie **Instellingen** op **Eindpunten**. 
4. Klik op het eind punt dat u wilt inschakelen.
5. Wijzig op de blade **Eindpunt** de eindpuntstatus in **Ingeschakeld** en klik vervolgens op **Opslaan**.
6. Clients blijven verkeer verzenden naar het eindpunt voor de duur van de Time-to-Live (TTL). U kunt de TTL wijzigen op de pagina Configuratie van het Traffic Manager-profiel.

## <a name="to-delete-an-endpoint"></a>Een eindpunt verwijderen

1. Meld u vanuit een browser aan bij [Azure Portal](https://portal.azure.com).
2. Zoek in de zoekbalk van de portal naar de naam van het **Traffic Manager-profiel** dat u wilt wijzigen, en klik vervolgens in de weergegeven resultaten op het Traffic Manager-profiel.
3. Klik op de blade **Traffic Manager-profiel** in de sectie **Instellingen** op **Eindpunten**. 
4. Klik op het eind punt dat u wilt verwijderen.
5. Klik op de Blade **eind punt** op **verwijderen**


## <a name="next-steps"></a>Volgende stappen

* [Traffic Manager-profielen beheren](traffic-manager-manage-profiles.md)
* [Routeringsmethoden configureren](traffic-manager-configure-routing-method.md)
* [Problemen met Traffic Manager in gedegradeerde status oplossen](traffic-manager-troubleshooting-degraded.md)
* [Prestatieoverwegingen voor Traffic Manager](traffic-manager-performance-considerations.md)
* [Bewerkingen op Traffic Manager (REST API-referentiemateriaal)](https://go.microsoft.com/fwlink/p/?LinkID=313584)

