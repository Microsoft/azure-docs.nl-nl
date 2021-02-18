---
title: Azure Monitor werkmappen voor rapporten | Microsoft Docs
description: Meer informatie over het gebruik van Azure Monitor werkmappen voor Azure Active Directory rapporten.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: b84c38b28b51f867160272883465242fc81ff2bf
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588047"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor werkmappen gebruiken voor Azure Active Directory rapporten

> [!IMPORTANT]
> Als u de onderliggende query's in deze werkmap wilt optimaliseren, klikt u op bewerken, klikt u op het pictogram instellingen en selecteert u de werk ruimte waarin u deze query's wilt uitvoeren. Met werkmappen worden standaard alle werk ruimten geselecteerd waar u uw Azure AD-logboeken wilt routeren. 

Wilt u:

- Meer informatie over het effect van uw [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) voor de aanmeldings ervaring van uw gebruikers?

- Problemen met aanmelden oplossen om een beter beeld te krijgen van de status van aanmelding van uw organisatie en om problemen snel op te lossen?

- Weet wie een verouderde verificatie gebruikt om u aan te melden bij uw omgeving? (Door [verouderde verificatie te blok keren](../conditional-access/block-legacy-authentication.md), kunt u de beveiliging van uw Tenant verbeteren.)

- Moet u weten wat het effect van het beleid voor voorwaardelijke toegang in uw Tenant is?

- Wilt u de mogelijkheid hebben om te controleren: aanmeldings logboek query's, de werkmap rapporteert aan hoeveel gebruikers toegang is verleend of geweigerd, en hoeveel gebruikers het beleid voor voorwaardelijke toegang hebben overgeslagen bij het openen van bronnen?

- U bent geïnteresseerd in het ontwikkelen van een dieper inzicht in: de gegevens van de werkmap per situatie, zodat de impact van een beleid kan worden becontextd per toestand, inclusief het platform, de apparaatstatus, de client-app, aanmeldings risico, locatie en toepassing?

- Krijg meer inzicht in aanmeldings logboek query's, de werkmap rapporteert aan hoeveel gebruikers toegang is verleend of geweigerd, en hoeveel gebruikers het beleid voor voorwaardelijke toegang hebben overgeslagen bij het openen van bronnen.

- Azure Active Directory biedt werkmappen voor het bewaken van deze vragen. [Azure monitor-werkmappen](../../azure-monitor/visualize/workbooks-overview.md) combi neer tekst, analyse query's, metrische gegevens en para meters in uitgebreide interactieve rapporten.



Dit artikel:

- Er wordt van uitgegaan dat u bekend bent met het [maken van interactieve rapporten met behulp van werk bladen controleren](../../azure-monitor/visualize/workbooks-overview.md).

- In dit artikel wordt uitgelegd hoe u Bewaak werkmappen gebruikt om inzicht te krijgen in het effect van uw beleid voor voorwaardelijke toegang, problemen met aanmelding op te lossen en verouderde authenticaties te identificeren.
 


## <a name="prerequisites"></a>Vereisten

Als u werkmappen wilt gebruiken, hebt u het volgende nodig:

- Een Azure Active Directory-Tenant met een Premium-licentie (P1 of P2). Meer informatie over het [verkrijgen van een Premium-licentie](../fundamentals/active-directory-get-started-premium.md).

- Een [Log Analytics-werkruimte](../../azure-monitor/logs/quick-create-workspace.md).

- [Toegang](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) tot de log Analytics-werk ruimte
- De volgende rollen in Azure Active Directory (als u Log Analytics via Azure Active Directory Portal opent)
    - Beveiligingsbeheerder
    - Beveiligingslezer
    - Rapport lezer
    - Globale beheerder

## <a name="roles"></a>Rollen
U moet een van de volgende rollen hebben en toegang hebben tot de [onderliggende log Analytics](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) werk ruimte om de werkmappen te beheren:
-   Globale beheerder
-   Beveiligingsbeheerder
-   Beveiligingslezer
-   Rapport lezer
-   Toepassings beheerder

## <a name="workbook-access"></a>Werkmap toegang 

Voor toegang tot werkmappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Navigeer naar **Azure Active Directory**  >  **bewakings**  >  **werkmappen**. 

1. Selecteer een rapport of sjabloon of selecteer **openen** op de werk balk. 

![De Azure Monitor werkmappen in azure AD zoeken](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Aanmeldings analyse

Als u toegang wilt krijgen tot de werkmap voor het aanmelden, selecteert u **aanmeldingen** in de sectie **gebruik** . 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd

- Gebruikers actie in behandeling

- Fout

U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldings analyse](./media/howto-use-azure-monitor-workbooks/43.png)


Voor elke trend krijgt u een uitsplitsing van de volgende categorieën:

- Locatie

    ![Aanmeldingen per locatie](./media/howto-use-azure-monitor-workbooks/45.png)

- Apparaat

    ![Aanmeldingen per apparaat](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Aanmeldingen met behulp van verouderde verificatie 


Voor toegang tot de werkmap voor aanmeldingen die gebruikmaken van [verouderde verificatie](../conditional-access/block-legacy-authentication.md), selecteert u in de sectie **gebruik** **aanmeldingen met behulp van verouderde verificatie**. 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

- Protocollen

![Aanmeldingen op verouderde verificatie](./media/howto-use-azure-monitor-workbooks/47.png)


Voor elke trend krijgt u een uitsplitsing per app en protocol.

![Aanmeldingen voor verouderde authenticatie per app en protocol](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Aanmeldingen op voorwaardelijke toegang 


Als u toegang wilt krijgen tot de werkmap voor aanmeldingen via [beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md), selecteert u in de sectie **voorwaardelijke toegang** de optie **aanmeldingen via voorwaardelijke toegang**. 

In deze werkmap ziet u de trends voor uitgeschakelde aanmeldingen. U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen via voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/49.png)


Bij uitgeschakelde aanmeldingen krijgt u een uitsplitsing van de status van de voorwaardelijke toegang.

![Scherm opname toont de status van de voorwaardelijke toegang en recente aanmeldingen.](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Voorwaardelijke toegang in Insights

### <a name="overview"></a>Overzicht

Werkmappen bevatten aanmeld logboek query's waarmee IT-beheerders de impact van het beleid voor voorwaardelijke toegang in hun Tenant kunnen controleren. U hebt de mogelijkheid om te rapporteren over hoeveel gebruikers toegang hebben gekregen of geweigerd. De werkmap bevat inzichten over hoeveel gebruikers beleid voor voorwaardelijke toegang hebben overs laan op basis van de kenmerken van de gebruikers op het moment van aanmelding. Het bevat details per situatie, zodat de impact van een beleid kan worden becontextd per toestand, inclusief het platform, de apparaatstatus, de client-app, aanmeldings risico, locatie en toepassing.

### <a name="instructions"></a>Instructies 
Als u toegang wilt krijgen tot de werkmap voor inzichten voor voorwaardelijke toegang, selecteert u de Insights-werkmap voor **voorwaardelijke toegang** in de sectie voorwaardelijke toegang. Deze werkmap toont de verwachte impact van elk beleid voor voorwaardelijke toegang in uw Tenant. Selecteer een of meer beleids regels voor voorwaardelijke toegang in de vervolg keuzelijst en Verfijn het bereik van de werkmap door de volgende filters toe te passen: 

- **Tijdsbereik**

- **Gebruiker**

- **Apps**

- **Gegevens weergave**

![Scherm afbeelding toont het deel venster voorwaardelijke toegang waarin u een beleid voor voorwaardelijke toegang kunt selecteren.](./media/howto-use-azure-monitor-workbooks/access-insights.png)


De overzicht van de impact toont het aantal gebruikers of aanmeldingen waarvoor de geselecteerde beleids regels een bepaald resultaat hebben. Totaal is het aantal gebruikers of aanmeldingen waarvoor de geselecteerde beleids regels zijn geëvalueerd in het geselecteerde tijds bereik. Klik op een tegel om de gegevens in de werkmap te filteren op basis van het resultaat type. 

![Scherm afbeelding toont tegels die u kunt gebruiken om resultaten te filteren, zoals totaal, geslaagd en mislukt.](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

In deze werkmap ziet u ook de impact van de geselecteerde beleids regels, onderverdeeld op basis van zes voor waarden: 
- **Apparaatstatus**
- **Apparaatplatform**
- **Client-apps**
- **Aanmeldingsrisico**
- **Locatie**
- **Toepassingen**

![Scherm afbeelding toont de details van het filter totaal aantal aanmeldingen.](./media/howto-use-azure-monitor-workbooks/device-platform.png)

U kunt ook afzonderlijke aanmeldingen onderzoeken, gefilterd op de geselecteerde para meters in de werkmap. Zoeken naar afzonderlijke gebruikers, gesorteerd op aanmeldings frequentie en de bijbehorende aanmeldings gebeurtenissen weer geven. 

![Scherm opname toont afzonderlijke aanmeldingen die u kunt bekijken.](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Aanmeldingen door besturings elementen te verlenen

Als u de werkmap voor aanmeldingen wilt openen door [besturings elementen te verlenen](../conditional-access/controls.md), selecteert u in de sectie **voorwaardelijke toegang** de optie **aanmeldingen door besturings elementen verlenen**. 

In deze werkmap worden de volgende uitgeschakelde aanmeldings trends weer gegeven:

- MFA vereisen
 
- Gebruiksvoorwaarden vereisen

- Privacyverklaring vereist

- Anders


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen door besturings elementen te verlenen](./media/howto-use-azure-monitor-workbooks/50.png)


Voor elke trend krijgt u een uitsplitsing per app en protocol.

![Uitsplitsing van recente aanmeldingen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse van mislukte aanmeldingen

Gebruik de werkmap voor **Fout analyse van aanmeldingen** om fouten op te lossen:

- Aanmeldingen
- Beleid voor voorwaardelijke toegang
- Verouderde verificatie 


Als u toegang wilt krijgen tot de aanmeldingen via gegevens voor voorwaardelijke toegang, selecteert u in de sectie **probleem oplossing** **aanmeldingen met behulp van verouderde verificatie**. 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd

- Actie in behandeling

- Fout


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Problemen met aanmeldingen oplossen](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor biedt u een uitsplitsing van de volgende categorieën om u te helpen bij het oplossen van aanmeldingen:

- Belangrijkste fouten

    ![Samen vatting van de belangrijkste fouten](./media/howto-use-azure-monitor-workbooks/53.png)

- Aanmeldingen die wachten op gebruikers actie

    ![Samen vatting van de aanmeldingen die wachten op gebruikers actie](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Volgende stappen

[Interactieve rapporten maken met behulp van werk bladen bewaken](../../azure-monitor/visualize/workbooks-overview.md).