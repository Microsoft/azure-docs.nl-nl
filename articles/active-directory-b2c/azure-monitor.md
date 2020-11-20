---
title: Azure AD B2C met Azure Monitor bewaken
titleSuffix: Azure AD B2C
description: Meer informatie over het registreren van Azure AD B2C gebeurtenissen met Azure Monitor met behulp van gedelegeerd resource beheer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 11/12/2020
ms.openlocfilehash: b41f5e9a3bd4d3cbe52cf2e1c567d24de8a661f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949952"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure AD B2C met Azure Monitor bewaken

Gebruik Azure Monitor om Azure Active Directory B2C (Azure AD B2C) aanmeld-en [audit](view-audit-logs.md) logboeken te routeren naar verschillende bewakings oplossingen. U kunt de logboeken bewaren voor lange termijn gebruik of integratie met SIEM-hulpprogram ma's (Security Information and Event Management) van derden om inzicht te krijgen in uw omgeving.

U kunt logboek gebeurtenissen door sturen naar:

* Een Azure- [opslag account](../storage/blobs/storage-blobs-introduction.md).
* Een [log Analytics-werk ruimte](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (voor het analyseren van gegevens, het maken van Dash boards en waarschuwingen voor specifieke gebeurtenissen).
* Een Azure- [Event hub](../event-hubs/event-hubs-about.md) (en kan worden geïntegreerd met uw logische Splunk-en Sumo-instanties).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

In dit artikel leert u hoe u de logboeken overbrengt naar een Azure Log Analytics-werk ruimte. Vervolgens kunt u een dash board maken of waarschuwingen maken die zijn gebaseerd op de activiteiten van Azure AD B2C gebruikers.

## <a name="deployment-overview"></a>Implementatieoverzicht

Azure AD B2C maakt gebruik van [Azure Active Directory bewaking](../active-directory/reports-monitoring/overview-monitoring.md). Als u *Diagnostische instellingen* in azure Active Directory binnen uw Azure AD B2C Tenant wilt inschakelen, gebruikt u [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) voor het [delegeren van een resource](../lighthouse/concepts/azure-delegated-resource-management.md), waarmee uw Azure AD B2C (de **service provider**) een Azure AD-resource (de **klant**) kan beheren. Nadat u de stappen in dit artikel hebt voltooid, hebt u toegang tot de resource groep *Azure-AD-B2C-monitor* die de [log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md) in uw **Azure AD B2C** Portal bevat. U kunt de logboeken ook overdragen van Azure AD B2C naar uw Log Analytics-werk ruimte.

Tijdens deze implementatie moet u een gebruiker of groep in uw Azure AD B2C Directory machtigen om de Log Analytics werkruimte-instantie te configureren binnen de Tenant die uw Azure-abonnement bevat. Als u de autorisatie wilt maken, implementeert u een [Azure Resource Manager](../azure-resource-manager/index.yml) sjabloon voor uw Azure AD-Tenant met het abonnement.

In het volgende diagram ziet u de onderdelen die u configureert in uw Azure AD-en Azure AD B2C-tenants.

![Projectie van resource groep](./media/azure-monitor/resource-group-projection.png)

Tijdens deze implementatie configureert u uw Azure AD B2C Tenant en Azure AD-Tenant waar de Log Analytics-werk ruimte wordt gehost. Aan het account dat wordt gebruikt om de implementatie uit te voeren, moet de rol [globale beheerder](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) zijn toegewezen in beide tenants. Het is ook belang rijk om ervoor te zorgen dat u bent aangemeld bij de juiste directory bij het volt ooien van elke stap zoals beschreven.

## <a name="1-create-or-choose-resource-group"></a>1. Maak of kies een resource groep

Maak eerst een resource groep die de doel Log Analytics werk ruimte bevat waarmee gegevens worden ontvangen van Azure AD B2C. U geeft de naam van de resource groep op wanneer u de Azure Resource Manager sjabloon implementeert.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw **Azure AD-Tenant** bevat.
1. [Maak een resource groep](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) of kies een bestaande. In dit voor beeld wordt een resource groep met de naam *Azure-AD-B2C-monitor* gebruikt.

## <a name="2-create-a-log-analytics-workspace"></a>2. een Log Analytics-werk ruimte maken

Een **log Analytics-werk ruimte** is een unieke omgeving voor Azure monitor logboek gegevens. U gebruikt deze Log Analytics werk ruimte om gegevens te verzamelen uit Azure AD B2C [audit logboeken](view-audit-logs.md)en vervolgens te visualiseren met query's en werkmappen, of om waarschuwingen te maken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw **Azure AD-Tenant** bevat.
1. [Maak een log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md). In dit voor beeld wordt een Log Analytics-werk ruimte met de naam *AzureAdB2C* gebruikt in een resource groep met de naam *Azure-AD-B2C-monitor*.

## <a name="3-delegate-resource-management"></a>3. resource beheer delegeren

In deze stap kiest u uw Azure AD B2C-Tenant als een **service provider**. U definieert ook de autorisaties die u nodig hebt om de juiste ingebouwde Azure-rollen toe te wijzen aan groepen in uw Azure AD-Tenant.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 uw Azure AD B2C Tenant-ID ophalen

Haal eerst de **Tenant-id** op van uw Azure AD B2C Directory (ook wel de Directory-id genoemd).

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw **Azure AD B2C** Tenant bevat.
1. Selecteer **Azure Active Directory** en selecteer **overzicht**.
1. Registreer de **Tenant-id**.

### <a name="32-select-a-security-group"></a>3,2 een beveiligings groep selecteren

Selecteer nu een Azure AD B2C groep of gebruiker aan wie u machtigingen wilt verlenen voor de resource groep die u eerder hebt gemaakt in de map met uw abonnement.  

Om het beheer te vereenvoudigen, raden we u aan Azure AD-gebruikers *groepen* te gebruiken voor elke rol, zodat u afzonderlijke gebruikers aan de groep kunt toevoegen of verwijderen, in plaats van machtigingen rechtstreeks aan die gebruiker toe te wijzen. In dit overzicht wordt een beveiligings groep toegevoegd.

> [!IMPORTANT]
> Als u machtigingen wilt toevoegen voor een Azure AD-groep, moet u het **groeps type** instellen op **beveiliging**. Deze optie wordt geselecteerd wanneer de groep wordt gemaakt. Zie [Een basisgroep maken en leden toevoegen met behulp van Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) voor meer informatie.

1. Als **Azure Active Directory** nog steeds in uw **Azure AD B2C** Directory is geselecteerd, selecteert u **groepen** en selecteert u vervolgens een groep. Als u geen bestaande groep hebt, maakt u een **beveiligings** groep en vervolgens voegt u leden toe. Volg de procedure [een basis groep maken en leden toevoegen met Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie. 
1. Selecteer **overzicht** en noteer de **object-id** van de groep.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 een Azure Resource Manager-sjabloon maken

Vervolgens maakt u een Azure Resource Manager sjabloon waarmee Azure AD B2C toegang krijgt tot de Azure AD-resource groep die u eerder hebt gemaakt (bijvoorbeeld *Azure-AD-B2C-monitor*). Implementeer de sjabloon vanuit het GitHub-voor beeld met behulp van de knop **implementeren in azure** waarmee de Azure portal wordt geopend. Hiermee kunt u de sjabloon direct in de portal configureren en implementeren. Zorg ervoor dat u bent aangemeld bij uw Azure AD-Tenant (niet de Azure AD B2C Tenant) voor deze stappen.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer het pictogram voor het adres van de map en het **abonnement** op de werk balk van de portal en selecteer vervolgens de map die uw **Azure AD** -Tenant bevat.
3. Gebruik de knop **implementeren naar Azure** om de Azure portal te openen en de sjabloon direct in de portal te implementeren. Zie [een Azure Resource Manager sjabloon maken](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)voor meer informatie.

   [![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Voer op de pagina **aangepaste implementatie** de volgende gegevens in:

   | Veld   | Definitie |
   |---------|------------|
   | Abonnement |  Selecteer de map met het Azure-abonnement waarin de resource groep *Azure-AD-B2C-monitor* is gemaakt. |
   | Regio| Selecteer de regio waar de resource wordt geïmplementeerd.  | 
   | Naam van msp-aanbod| Een naam die deze definitie beschrijft. Bijvoorbeeld *Azure AD B2C bewaking*.  |
   | Beschrijving van msp-aanbod| Een korte beschrijving van uw aanbieding. *Hiermee schakelt u bijvoorbeeld Azure monitor in azure AD B2C*.|
   | Beheerd door Tenant-id| De **Tenant-id** van uw Azure AD B2C Tenant (ook wel de Directory-id genoemd). |
   |Autorisaties|Geef een JSON-matrix op met objecten die de Azure AD `principalId` , `principalIdDisplayName` en Azure bevatten `roleDefinitionId` . De `principalId` is de **object-id** van de B2C-groep of-gebruiker die toegang heeft tot resources in dit Azure-abonnement. Voor dit scenario geeft u de object-ID van de groep op die u eerder hebt vastgelegd. Voor de `roleDefinitionId` gebruikt u de [ingebouwde rol](../role-based-access-control/built-in-roles.md) waarde voor de *rol Inzender* `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Rg-naam | De naam van de resource groep die u eerder hebt gemaakt in uw Azure AD-Tenant. Bijvoorbeeld *Azure-AD-B2C-monitor*. |

   In het volgende voor beeld wordt een matrix met autorisaties met één beveiligings groep gedemonstreerd.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Nadat u de sjabloon hebt geïmplementeerd, kan het enkele minuten duren (meestal niet meer dan vijf) om de resource projectie te volt ooien. U kunt de implementatie controleren in uw Azure AD-Tenant en de details van de resource projectie ophalen. Zie [service providers weer geven en beheren](../lighthouse/how-to/view-manage-service-providers.md)voor meer informatie.

## <a name="4-select-your-subscription"></a>4. Selecteer uw abonnement

Nadat u de sjabloon hebt geïmplementeerd en enkele minuten hebt gewacht totdat de resource projectie is voltooid, volgt u deze stappen om uw abonnement te koppelen aan uw Azure AD B2C Directory.

1. Meld u af bij de Azure Portal als u momenteel bent aangemeld (Hiermee kunnen uw sessie referenties in de volgende stap worden vernieuwd).
2. Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw **Azure AD B2C** beheerders account. Dit account moet lid zijn van de beveiligings groep die u hebt opgegeven in de stap [resource beheer voor gemachtigden](#3-delegate-resource-management) .
3. Selecteer het pictogram voor het adres van de **map en het abonnement** op de werk balk van de portal.
4. Selecteer de Azure AD-map met het Azure-abonnement en de *Azure-AD-B2C-monitor-* resource groep die u hebt gemaakt.

    ![Schakelen tussen mappen](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Controleer of u de juiste directory en dit abonnement hebt geselecteerd. In dit voor beeld zijn alle directory's en alle abonnementen geselecteerd.

    ![Alle mappen die zijn geselecteerd in het Directory &-abonnements filter](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Diagnostische instellingen configureren

Diagnostische instellingen bepalen waar logboeken en metrische gegevens voor een resource moeten worden verzonden. Mogelijke bestemmingen zijn:

- [Azure Storage-account](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Event hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) -oplossingen
- [Log Analytics werk ruimte](../azure-monitor/platform/resource-logs-collect-workspace.md)

In dit voor beeld gebruiken we de Log Analytics-werk ruimte om een dash board te maken.

### <a name="51-create-diagnostic-settings"></a>5,1 Diagnostische instellingen maken

U kunt nu [Diagnostische instellingen maken](../active-directory/reports-monitoring/overview-monitoring.md) in de Azure Portal.

Controle-instellingen voor Azure AD B2C activiteiten logboeken configureren:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met uw Azure AD B2C beheerders account. Dit account moet lid zijn van de beveiligings groep die u hebt opgegeven in de stap [een beveiligings groep selecteren](#32-select-a-security-group) .
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. **Azure Active Directory** selecteren
1. Selecteer **Diagnostische instellingen** onder **Controle**.
1. Als er bestaande instellingen zijn voor de resource, ziet u een lijst met instellingen die al zijn geconfigureerd. Selecteer **Diagnostische instelling toevoegen** om een nieuwe instelling toe te voegen of selecteer **bewerken** om een bestaande instelling te bewerken. Elke instelling kan niet meer dan één van de doel typen hebben.

    ![Het deel venster Diagnostische instellingen in Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Geef een naam op voor uw instelling als deze nog niet aanwezig is.
1. Schakel het selectie vakje voor elke bestemming in om de logboeken te verzenden. Selecteer **configureren** om de instellingen op te geven **, zoals wordt beschreven in de volgende tabel**.
1. Selecteer **verzenden naar log Analytics** en selecteer vervolgens de **naam van de werk ruimte** die u eerder hebt gemaakt ( `AzureAdB2C` ).
1. Selecteer **audit logs bevat** en **SignInLogs**.
1. Selecteer **Opslaan**.

> [!NOTE]
> Het kan tot vijf tien minuten duren nadat een gebeurtenis is verzonden zodat deze wordt [weer gegeven in een log Analytics-werk ruimte](../azure-monitor/platform/data-ingestion-time.md). Lees ook meer informatie over [Active Directory rapportage latenties](../active-directory/reports-monitoring/reference-reports-latencies.md), die van invloed kunnen zijn op de verouderde hoeveelheid gegevens en een belang rijke rol spelen bij rapportage.

Als u het fout bericht ' Diagnostische instellingen voor het gebruik van Azure Monitor voor uw Azure AD B2C Directory wilt instellen ' wordt weer gegeven, moet u het beheer van gedelegeerde resources instellen, ' zorg ervoor dat u zich aanmeldt met een gebruiker die lid is van de [beveiligings groep](#32-select-a-security-group) en [Selecteer uw abonnement](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. uw gegevens visualiseren

U kunt nu uw Log Analytics-werk ruimte configureren om uw gegevens te visualiseren en waarschuwingen te configureren. Deze configuraties kunnen worden gemaakt in zowel uw Azure AD-Tenant als uw Azure AD B2C-Tenant.

### <a name="61-create-a-query"></a>6,1 een query maken

Met logboekquery's kunt u de waarde van de gegevens die in Azure Monitor-logboeken worden verzameld, volledig benutten. Met een krachtige query taal kunt u gegevens uit meerdere tabellen samen voegen, grote gegevens sets verzamelen en complexe bewerkingen met minimale code uitvoeren. Vrijwel elke vraag kan worden beantwoord en de analyse wordt uitgevoerd zolang de ondersteunende gegevens zijn verzameld en u begrijpt hoe u de juiste query kunt bouwen. Zie [aan de slag met logboek query's in azure monitor](../azure-monitor/log-query/get-started-queries.md)voor meer informatie.

1. Selecteer in **log Analytics werk ruimte** **Logboeken**
1. Plak in de query-editor de volgende [query taal](https://docs.microsoft.com/azure/data-explorer/kusto/query/) query van Kusto. Deze query toont het beleids gebruik per bewerking over de afgelopen x dagen. De standaard duur is ingesteld op 90 dagen (90d). U ziet dat de query alleen gericht is op de bewerking waarbij een token/code wordt uitgegeven door het beleid.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Selecteer **Uitvoeren**. De resultaten van de query worden weer gegeven aan de onderkant van het scherm.
1. Selecteer **Opslaan** om uw query op te slaan voor later gebruik.

   ![Logboek editor Log Analytics](./media/azure-monitor/query-policy-usage.png)

1. Vul de volgende gegevens in:

    - **Naam** : Voer de naam van de query in.
    - **Opslaan als** -selecteren `query` .
    - **Categorie** -selecteren `Log` .

1. Selecteer **Opslaan**.

U kunt de query ook zo wijzigen dat de gegevens worden gevisualiseerd met behulp van de operator [render](https://docs.microsoft.com/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) .

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Segment van de logboek editor Log Analytics](./media/azure-monitor/query-policy-usage-pie.png)

Zie de Azure AD B2C [Siem github opslag plaats](https://aka.ms/b2csiem)voor meer voor beelden.

### <a name="62-create-a-workbook"></a>6,2 een werkmap maken

Werkmappen bieden een flexibel canvas voor gegevensanalyse en het maken van uitgebreide visuele rapporten in Azure Portal. Hiermee kunt u in meerdere gegevens bronnen in azure tikken en deze combi neren in Unified Interactive-ervaringen. Zie [Azure monitor werkmappen](../azure-monitor/platform/workbooks-overview.md)voor meer informatie.

Volg de onderstaande instructies om een nieuwe werkmap te maken met behulp van een JSON-galerie sjabloon. Deze werkmap bevat een **gebruikers inzicht** en een **verificatie** dashboard voor Azure AD B2C Tenant.

1. Selecteer in de **werk ruimte log Analytics** de optie **werkmappen**.
1. Selecteer op de werk balk **+ nieuwe** optie om een nieuwe werkmap te maken.
1. Selecteer op de pagina **nieuwe werkmap** de **Geavanceerde editor** met behulp **</>** van de optie op de werk balk.

     ![Galerie sjabloon](./media/azure-monitor/wrkb-adv-editor.png)

1. Selecteer een **Galerie sjabloon**.
1. Vervang de JSON in de **Galerie sjabloon**  door de inhoud van [Azure AD B2C Basic-werkmap](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Pas de sjabloon toe met behulp van de knop **Toep assen** .
1. Selecteer de knop **bewerking gereed** op de werk balk om het bewerken van de werkmap te volt ooien.
1. Sla de werkmap ten slotte op met behulp van de knop **Opslaan** op de werk balk.
1. Geef een **titel** op, bijvoorbeeld *Azure AD B2C dash board*.
1. Selecteer **Opslaan**.

    ![Sla de werkmap op](./media/azure-monitor/wrkb-title.png)

In de werkmap worden rapporten weer gegeven in de vorm van een dash board.

![Het eerste dash board van de werkmap](./media/azure-monitor/wkrb-dashboard-1.png)

![Tweede dash board van werkmap](./media/azure-monitor/wrkb-dashboard-2.png)

![Derde dash board werkmap](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Waarschuwingen maken

Waarschuwingen worden gemaakt door regels voor waarschuwingen in Azure Monitor en kunnen automatisch opgeslagen query's uitvoeren of aangepaste zoekopdrachten in logboeken met regelmatige tussenpozen. U kunt waarschuwingen maken op basis van specifieke prestatiemetrieken of wanneer bepaalde gebeurtenissen worden gemaakt, de afwezigheid van een gebeurtenis of een aantal gebeurtenissen die binnen een bepaalde periode worden gemaakt. Waarschuwingen kunnen bijvoorbeeld worden gebruikt om u te waarschuwen wanneer het gemiddelde aantal aanmeldingen een bepaalde drempel waarde overschrijdt. Zie [Create Alerts](../azure-monitor/learn/tutorial-response.md)(Engelstalig) voor meer informatie.


Gebruik de volgende instructies om een nieuwe Azure-waarschuwing te maken, waarmee een [e-mail melding](../azure-monitor/platform/action-groups.md#configure-notifications) wordt verzonden wanneer er 25% van de **Totaal aanvragen** wordt vergeleken met de vorige periode. De waarschuwing wordt elke vijf minuten uitgevoerd en er wordt gezocht naar de laatste 24 uur in Windows. De waarschuwingen worden gemaakt met behulp van de Kusto-query taal.


1. Selecteer in **log Analytics werk ruimte** **Logboeken**. 
1. Maak een nieuwe **Kusto-query** met behulp van de onderstaande query.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Selecteer **uitvoeren** om de query te testen. De resultaten moeten worden weer geven als er 25% of meer is in het totaal aantal aanvragen in de afgelopen 24 uur.
1. Als u een waarschuwings regel wilt maken op basis van de bovenstaande query, gebruikt u de optie **+ nieuwe waarschuwings regel** die beschikbaar is op de werk balk.
1. Selecteer op de pagina **een waarschuwings regel maken** de optie **voorwaarde naam** 
1. Stel op de pagina **signaal logica configureren** de volgende waarden in en gebruik vervolgens de knop **gereed** om de wijzigingen op te slaan.
    * Waarschuwings logica: Stel **aantal resultaten in dat** **groter is dan** **0**.
    * Evaluatie op basis van: Selecteer **1440** voor de periode (in minuten) en **5** voor de frequentie (in minuten) 

    ![Een voor waarde voor een waarschuwings regel maken](./media/azure-monitor/alert-create-rule-condition.png)

Nadat de waarschuwing is gemaakt, gaat u naar **log Analytics werk ruimte** en selecteert u **waarschuwingen**. Op deze pagina worden alle waarschuwingen weer gegeven die zijn geactiveerd in de optie duur is ingesteld op **tijds bereik** .  

### <a name="configure-action-groups"></a>Actie groepen configureren

Azure Monitor-en Service Health-waarschuwingen gebruiken actie groepen om gebruikers te laten weten dat een waarschuwing is geactiveerd. U kunt onder andere het verzenden van een telefoon gesprek, SMS, e-mail, gebruiken. of het activeren van verschillende typen geautomatiseerde acties. Volg de instructies [voor het maken en beheren van actie groepen in de Azure Portal](../azure-monitor/platform/action-groups.md)

Hier volgt een voor beeld van een e-mail melding voor waarschuwingen. 

   ![E-mailmelding](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Meerdere tenants

Als u meerdere Azure AD B2C Tenant logboeken wilt toevoegen aan dezelfde Log Analytics werk ruimte (of een Azure-opslag account of Event Hub), moet u afzonderlijke implementaties met verschillende waarden voor de naam van het **MSP-aanbod** hebben. Zorg ervoor dat uw Log Analytics-werk ruimte zich in dezelfde resource groep bevindt als het account dat u hebt geconfigureerd in de [groep maken of kies resource groepen](#1-create-or-choose-resource-group).

Wanneer u met meerdere Log Analytics-werk ruimten werkt, kunt u met de [query tussen werk ruimten](../azure-monitor/log-query/cross-workspace-query.md) query's maken die in meerdere werk ruimten werken. Met de volgende query wordt bijvoorbeeld een koppeling uitgevoerd van twee audit logboeken van verschillende tenants op basis van dezelfde categorie (bijvoorbeeld verificatie):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>De gegevensretentieperiode wijzigen

Azure Monitor-logboeken zijn ontworpen om het verzamelen, indexeren en ondersteunen van enorme hoeveel heden gegevens per dag te schalen en op te slaan in een bron in uw onderneming of die in Azure is geïmplementeerd. Standaard worden logboeken 30 dagen bewaard, maar de Bewaar periode kan Maxi maal twee jaar worden verhoogd. Meer informatie over het [beheren van het gebruik en de kosten met Azure monitor-logboeken](../azure-monitor/platform/manage-cost-storage.md). Nadat u de prijs categorie hebt geselecteerd, kunt u [de Bewaar periode voor gegevens wijzigen](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Volgende stappen

* Meer voor beelden vindt u in de galerie met Azure AD B2C [Siem](https://aka.ms/b2csiem). 

* Zie [zelf studie: resource logboeken verzamelen en analyseren vanuit een Azure-resource](../azure-monitor/insights/monitor-azure-resource.md)voor meer informatie over het toevoegen en configureren van diagnostische instellingen in azure monitor.

* Zie [zelf studie: Stream Azure Active Directory-logboeken naar een Azure-Event hub](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)voor informatie over het streamen van Azure AD-logboeken naar een event hub.
