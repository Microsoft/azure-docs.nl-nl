---
title: Inzichten en rapporten werkmap-Azure Active Directory voor voorwaardelijke toegang
description: De werkmap voor voorwaardelijke toegang van Azure AD gebruiken voor het oplossen van problemen met beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae802038626a1fbf8d533800a0b8eb43c4565e8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574176"
---
# <a name="conditional-access-insights-and-reporting"></a>Inzichten en rapportage voor voorwaardelijke toegang

Met de Insights-werkmap voor voorwaardelijke toegang kunt u de gevolgen van het beleid voor voorwaardelijke toegang in uw organisatie in de loop van de tijd begrijpen. Tijdens het aanmelden kan een of meer beleids regels voor voorwaardelijke toegang van toepassing zijn, waarbij toegang wordt verleend als bepaalde toekennings besturings elementen worden vervuld of anders de toegang wordt geweigerd. Omdat er meerdere beleids regels voor voorwaardelijke toegang kunnen worden geëvalueerd tijdens elke aanmelding, kunt u met de inzichten en rapportage werkmap de impact van een afzonderlijk beleid of een subset van alle beleids regels onderzoeken.  

## <a name="prerequisites"></a>Vereisten

Als u de inzichten en rapportage werkmap wilt inschakelen, moet uw Tenant beschikken over een Log Analytics-werk ruimte voor het bewaren van logboek gegevens van de aanmelding. Gebruikers moeten Azure AD Premium P1-of P2-licenties hebben om voorwaardelijke toegang te kunnen gebruiken.

De volgende rollen hebben toegang tot inzichten en rapportage:  

- Beheerder van voorwaardelijke toegang 
- Beveiligingslezer 
- Beveiligingsbeheerder 
- Algemene lezer 
- Globale beheerder 

Gebruikers hebben ook een van de volgende Log Analytics werkruimte rollen nodig:  

- Inzender  
- Eigenaar 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Aanmeldings logboeken van Azure AD naar Azure Monitor-logboeken 

Als u geen Azure AD-Logboeken hebt geïntegreerd met Azure Monitor-logboeken, moet u de volgende stappen uitvoeren voordat de werkmap wordt geladen:  

1. [Een log Analytics-werk ruimte maken in azure monitor](../../azure-monitor/logs/quick-create-workspace.md).
1. [Integreer Azure AD-logboeken met Azure monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Uitleg 

Voor toegang tot de werkmap inzichten en rapportage:  

1. Meld u aan bij **Azure Portal**.
1. Blader naar **Azure Active Directory**  >    >    >  **inzichten en rapportage** voor voorwaardelijke toegang van de beveiliging.

### <a name="get-started-select-parameters"></a>Aan de slag: para meters selecteren 

Met het inzicht-en rapportage dashboard kunt u de gevolgen van een of meer beleids regels voor voorwaardelijke toegang gedurende een opgegeven periode bekijken. Begin door elk van de para meters boven aan de werkmap in te stellen. 

![Inzicht in het dash board voor voorwaardelijke toegang en rapportage in de Azure Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Beleid voor voorwaardelijke toegang**: Selecteer een of meer beleids regels voor voorwaardelijke toegang om de gecombineerde impact ervan weer te geven. Beleids regels worden onderverdeeld in twee groepen: ingeschakeld en alleen rapport beleid. Standaard is alle ingeschakelde beleids regels geselecteerd. Deze ingeschakelde beleids regels zijn het beleid dat momenteel wordt afgedwongen in uw Tenant.  

**Tijds bereik**: Selecteer een tijds bereik van 4 uur tot nu terug tot 90 dagen. Als u een tijds bereik meer wilt selecteren dan wanneer u de Azure AD-Logboeken hebt geïntegreerd met Azure Monitor, worden alleen aanmeldingen weer gegeven na de integratie tijd.  

**Gebruiker**: in het dash board wordt standaard de impact van de geselecteerde beleids regels voor alle gebruikers weer gegeven. Als u wilt filteren op een afzonderlijke gebruiker, typt u de naam van de gebruiker in het tekst veld. Als u wilt filteren op alle gebruikers, typt u ' alle gebruikers ' in het tekst veld of laat u de para meter leeg. 

**App**: in het dash board wordt standaard de impact van de geselecteerde beleids regels voor alle apps weer gegeven. Als u wilt filteren op een afzonderlijke app, typt u de naam van de app in het tekst veld. Als u wilt filteren op alle apps, typt u ' alle apps ' in het tekst veld of laat u de para meter leeg. 

**Gegevens weergave**: Selecteer of u wilt dat in het dash board resultaten worden weer gegeven in termen van het aantal gebruikers of het aantal aanmeldingen. Een individuele gebruiker heeft mogelijk honderden aanmeldingen bij veel apps met veel verschillende resultaten gedurende een bepaald tijds bereik. Als u de gegevens weergave selecteert om gebruikers te zijn, kan een gebruiker worden opgenomen in het aantal geslaagde en mislukte pogingen (als er bijvoorbeeld tien gebruikers zijn, kan het zijn dat er in de afgelopen 30 dagen een resultaat van succes is opgetreden en dat er in de afgelopen 30 dagen geen resultaten kunnen zijn gevonden.

## <a name="impact-summary"></a>Overzicht van impact 

Zodra de para meters zijn ingesteld, wordt de impact samenvatting geladen. In de samen vatting ziet u hoeveel gebruikers of aanmeldingen tijdens het tijds bereik hebben geresulteerd in ' geslaagd ', ' fout ', ' gebruikers actie vereist ' of ' niet toegepast ' wanneer de geselecteerde beleids regels zijn geëvalueerd.  

![Impact samenvatting in de werkmap voor voorwaardelijke toegang](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Totaal**: het aantal gebruikers of aanmeldingen gedurende de periode waarin ten minste één van de geselecteerde beleids regels is geëvalueerd.

**Geslaagd**: het aantal gebruikers of aanmeldingen gedurende de periode waarin het gecombineerde resultaat van de geselecteerde beleids regels "geslaagd" of "alleen rapport: geslaagd" was.

**Fout**: het aantal gebruikers of aanmeldingen gedurende de periode waarin het resultaat van ten minste één van de geselecteerde beleids regels ' fout ' of ' alleen rapport: fout ' is.

**Gebruikers actie vereist**: het aantal gebruikers of aanmeldingen gedurende de periode waarin het gecombineerde resultaat van de geselecteerde beleids regels ' rapport-only: gebruikers actie vereist ' is. Gebruikers actie is vereist wanneer een interactieve machtiging voor toekennen, zoals multi-factor Authentication, vereist is voor een beleid voor voorwaardelijke toegang voor een rapport. Omdat interactieve granting-besturings elementen niet worden afgedwongen door alleen-rapport beleid, kan het slagen of mislukken niet worden bepaald.  

**Niet toegepast**: het aantal gebruikers of aanmeldingen gedurende de periode waarin geen van de geselecteerde beleids regels is toegepast.

### <a name="understanding-the-impact"></a>Meer informatie over de impact 

![Werkmap uitsplitsing per voor waarde en status](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Bekijk de uitsplitsing van gebruikers of aanmeldingen voor elk van de voor waarden. U kunt de aanmeldingen van een bepaald resultaat filteren (bijvoorbeeld geslaagd of mislukt) door op de overzichts tegels boven aan de werkmap te selecteren. U kunt de uitsplitsing van aanmeldingen zien voor elk van de voor waarden voor voorwaardelijke toegang: Apparaatstatus, platform, client-app, locatie, toepassing en aanmeldings risico.  

## <a name="sign-in-details"></a>Aanmeldingsgegevens 

![Details van werkmap aanmelding](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

U kunt ook de aanmeldingen van een specifieke gebruiker onderzoeken door te zoeken naar aanmeldingen aan de onderkant van het dash board. In de query aan de linkerkant worden de meest voorkomende gebruikers weer gegeven. Als u een gebruiker selecteert, wordt de query aan de rechter kant gefilterd.  

> [!NOTE]
> Wanneer u de logboeken wilt downloaden, kiest u JSON-indeling om alleen rapport gegevens van het type voorwaardelijke toegang toe te voegen.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Een beleid voor voorwaardelijke toegang configureren in de modus alleen rapport

Een beleid voor voorwaardelijke toegang configureren in de modus alleen rapport:

1. Meld u aan bij de **Azure Portal** als beheerder voor voorwaardelijke toegang, beveiligings beheerder of globale beheerder.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer een bestaand beleid of maak een nieuw beleid.
1. Stel onder **beleid inschakelen** de modus voor **alleen rapport** in.
1. Selecteer **Opslaan**

> [!TIP]
> Als u de **beleids status inschakelen** van een bestaand beleid **van aan aan** **rapporteert,** wordt bestaande beleids afdwinging uitgeschakeld. 

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Waarom mislukken query's als gevolg van een machtigings fout?

Als u toegang wilt krijgen tot de werkmap, hebt u de juiste Azure AD-machtigingen nodig, evenals Log Analytics werkruimte machtigingen. Als u wilt testen of u over de juiste machtigingen voor de werk ruimte beschikt door een voorbeeld query voor log Analytics uit te voeren:

1. Meld u aan bij **Azure Portal**.
1. Blader naar **Azure Active Directory**  >  **Logboeken**.
1. Typ `SigninLogs` in het vak query en selecteer **uitvoeren**.
1. Als de query geen resultaten retourneert, is de werk ruimte mogelijk niet op de juiste wijze geconfigureerd. 

![Problemen met mislukte query's oplossen](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Zie het artikel [Azure AD-logboeken integreren met Azure monitor-logboeken](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)voor meer informatie over het streamen van Azure AD-aanmeldings logboeken naar een log Analytics-werk ruimte.

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Waarom worden de query's in de werkmap niet uitgevoerd?

Klanten hebben opgemerkt dat query's soms mislukken als er verkeerde of meerdere werk ruimten zijn gekoppeld aan de werkmap. Om dit probleem op te lossen, klikt u boven aan de werkmap op **bewerken** en vervolgens op het vistuig instellingen. Selecteer en verwijder werk ruimten die niet aan de werkmap zijn gekoppeld. Er mag slechts één werk ruimte aan elke werkmap zijn gekoppeld.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Waarom is de para meter voor het beleid voor voorwaardelijke toegang leeg?

De lijst met beleids regels wordt gegenereerd door te kijken naar het beleid dat wordt geëvalueerd voor de meest recente aanmeldings gebeurtenis. Als uw Tenant geen recente aanmeldingen bevat, moet u mogelijk enkele minuten wachten totdat de werkmap de lijst met beleids regels voor voorwaardelijke toegang heeft geladen. Dit kan gebeuren onmiddellijk na het configureren van Log Analytics of langer duren als een Tenant geen recente aanmeldings activiteit heeft.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Waarom neemt de werkmap veel tijd in beslag om te worden geladen?  

Afhankelijk van het geselecteerde tijds bereik en de grootte van uw Tenant, kan de werkmap een buiten gewoon groot aantal aanmeld gebeurtenissen evalueren. Voor grote tenants is het mogelijk dat het volume van de aanmeldingen de query capaciteit van Log Analytics overschrijdt. Verkort het tijds bereik tot vier uur om te zien of de werkmap wordt geladen.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Waarom wordt er een paar minuten na het laden van de werkmap nul resultaten geretourneerd? 

Wanneer het volume van de aanmeldingen de query capaciteit van Log Analytics overschrijdt, retourneert de werkmap nul resultaten. Verkort het tijds bereik tot vier uur om te zien of de werkmap wordt geladen.  

### <a name="can-i-save-my-parameter-selections"></a>Kan ik mijn parameter selecties opslaan?  

U kunt de opties voor de para meters boven aan de werkmap opslaan door naar **Azure Active Directory**-  >  **werkmappen**  >  **voorwaardelijke toegang inzicht en rapportage** te gaan. Hier vindt u de werkmap sjabloon, waar u de werkmap kunt bewerken en een kopie opslaat in uw werk ruimte, met inbegrip van de parameter selecties, in **mijn rapporten** of **gedeelde rapporten**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Kan ik de werkmap bewerken en aanpassen met aanvullende query's? 

U kunt de werkmap bewerken en aanpassen door naar **Azure Active Directory**-  >  **werkmappen**  >  **voorwaardelijke toegang inzicht en rapportage** te gaan. Hier vindt u de werkmap sjabloon, waar u de werkmap kunt bewerken en een kopie opslaat in uw werk ruimte, met inbegrip van de parameter selecties, in **mijn rapporten** of **gedeelde rapporten**. Klik op **bewerken** boven aan de werkmap om te beginnen met het bewerken van de query's.  
 
## <a name="next-steps"></a>Volgende stappen

- [Alleen rapport modus voor voorwaardelijke toegang](concept-conditional-access-report-only.md)

- Voor meer informatie over Azure AD-werkmappen raadpleegt u het artikel [How to use Azure monitor Workbooks for Azure Active Directory Reports](../reports-monitoring/howto-use-azure-monitor-workbooks.md)(Engelstalig).

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
