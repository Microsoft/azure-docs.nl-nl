---
title: Verbind uw Agari-beschermings-en merk beveiligings oplossingen met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Agari phishing verdediging en de Brand Protection connector om de logboeken in azure Sentinel te halen. Bekijk Agari-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: ba37b2280ba4d7138f4ed652b7b330bcaf7b9935
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566773"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Verbind uw Agari-beschermings-en merk beveiligings oplossingen met Azure Sentinel

> [!IMPORTANT]
> De Agari phishing-verdediging en de merk beveiligings connector bevindt zich momenteel in de **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met de Agari phishing-verdediging en de merk beveiligings connector kunt u eenvoudig de logboeken van uw merk beveiliging en phishing-oplossingen koppelen aan Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, een query voor het maken van aangepaste waarschuwingen en het opnemen ervan om het onderzoek te verbeteren. De oplossingen van Agari zijn geïntegreerd met Azure Sentinel met behulp van Azure Functions en REST API.

Daarnaast kunnen klanten met een merk beveiliging en phishing-respons gebruikmaken van het delen van bedreigingen voor de beveiliging via de beveiligings Graph API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

Het volgende is vereist voor de verbinding tussen de Agari van de phishing-en merk beveiliging voor Azure Sentinel:

- Lees-en schrijf machtigingen voor de Azure-Sentinel-werk ruimte.

- Lees machtigingen voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Lees-en schrijf machtigingen voor Azure Functions om een functie-app te maken. Meer [informatie over Azure functions](../azure-functions/index.yml).

- Zorg ervoor dat u beschikt over de Agari **-client-id** en de **geheime sleutels** (identiek zijn in alle Agari-oplossingen). Raadpleeg de [Agari-ontwikkelaars site](https://developers.agari.com/agari-platform/docs/quick-start) voor instructies.

## <a name="configure-and-connect-agari-solutions"></a>Agari-oplossingen configureren en verbinden 

Agari-oplossingen kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel met behulp van een Azure-functie-app.

> [!NOTE]
> Deze connector maakt gebruik van Azure Functions om verbinding te maken met de oplossingen van Agari om hun logboeken te verzamelen in azure Sentinel. Dit kan leiden tot extra kosten voor gegevens opname. Controleer de pagina met [prijzen voor Azure functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

1. **Uw Agari-API-referenties verzamelen:** 

    Zorg ervoor dat u uw Agari **-client-id** en **geheime sleutels** hebt. U vindt de instructies op de [site Agari-ontwikkel aars](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Beschrijving De beveiligings Graph API inschakelen:** 

    Met de Agari-functie-app kunt u bedreigings informatie delen met Sentinel via de beveiligings Graph API. Als u deze functie wilt gebruiken, moet u de connector voor de [Sentinel Threat Intelligence-platforms](connect-threat-intelligence.md) inschakelen en ook [een toepassing registreren](/graph/auth-register-app-v2) in azure Active Directory.

    Dit proces biedt u drie stukjes informatie voor gebruik bij het implementeren van de functie-app hieronder: de **Tenant-id** van de grafiek, de **grafiek-client-id** en het **client geheim** van de grafiek.

1. **De connector en de bijbehorende Azure-functie-app implementeren:** 

    1. Selecteer in de Azure-Sentinel-Portal de optie **Data connectors**. Selecteer **Agari phishing verdediging en merk beveiliging (preview)** en **Open vervolgens de pagina connector**.

    1. Kopieer onder **configuratie** de Azure Sentinel **Workspace-id** en de **primaire sleutel** en plak deze in plaats daarvan.

    1. Selecteer **implementeren naar Azure**. (Mogelijk moet u omlaag schuiven om de knop te vinden.)

    1. Het scherm voor **aangepaste implementatie** wordt weer gegeven.

        - Voer uw Agari **-client-id** en **client geheim** in (geheime sleutels)

        - Voer uw Azure Sentinel **Workspace-id** en **werkruimte sleutel** (primaire sleutel) in die u hebt gekopieerd en opgeslagen.

        - Selecteer **waar** of **Onwaar** voor de Agari-oplossingen waarvoor u actieve abonnementen hebt.

        - Als u een Azure-toepassing hebt gemaakt om IoCs met Azure Sentinel te delen met behulp van de beveiligings Graph API, selecteert u **waar** om het delen van de **beveiligings grafiek in te scha kelen** en voert u de **Tenant-id** van de grafiek in, de **grafiek-client-id** en het **client geheim** van de grafiek.

    1. Selecteer **Controleren + maken**. Wanneer de validatie is voltooid, klikt u op **maken**.

1. **Wijs de benodigde machtigingen toe aan uw functie-app:**

    De Agari-connector gebruikt een omgevings variabele om tijds tempels voor logboek toegang op te slaan. Om ervoor te zorgen dat de toepassing naar deze variabele kan schrijven, moeten machtigingen worden toegewezen aan de door het systeem toegewezen identiteit.

    1. Ga in het Azure Portal naar **functie-app**.

    1. Selecteer in de Blade **functie-app** uw functie-app in de lijst en selecteer vervolgens **identiteit** onder **instellingen** in het navigatie menu van de functie-app.

    1. Stel op het tabblad **systeem toegewezen** de **status** in **op aan**. 

    1. Selecteer **Opslaan** en er wordt een knop voor **Azure-roltoewijzingen** weer gegeven. Klik erop.

    1. Selecteer in het scherm **toewijzingen van Azure-rollen** de optie **roltoewijzing toevoegen**. Stel **Scope** in op **abonnement**, selecteer uw abonnement in de vervolg keuzelijst **abonnement** en stel **rol** in op **eigenaar van app-configuratie gegevens**. 

    1. Selecteer **Opslaan**.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder *CustomLogs* in de volgende tabellen: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Als u gegevens van Agari-oplossingen wilt opvragen, voert u een van de bovenstaande tabel namen in het query venster in.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Agari phishing-en merk beveiligings oplossingen verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
