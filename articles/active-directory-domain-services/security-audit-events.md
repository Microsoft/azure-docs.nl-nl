---
title: Beveiligings controles inschakelen voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over het inschakelen van beveiligings controles voor het centraliseren van de logboek registratie van gebeurtenissen voor analyse en waarschuwingen in Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574137"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Beveiligings controles inschakelen voor Azure Active Directory Domain Services

Met beveiligings controles van Azure Active Directory Domain Services (Azure AD DS) kunnen beveiligings gebeurtenissen van Azure streamen naar doel bronnen. Deze resources omvatten Azure Storage, Azure Log Analytics-werk ruimten of Azure Event hub. Nadat u beveiligings controle gebeurtenissen hebt ingeschakeld, stuurt Azure AD DS alle gecontroleerde gebeurtenissen voor de geselecteerde categorie naar de doel resource.

U kunt gebeurtenissen in azure Storage archiveren en gebeurtenissen streamen naar SIEM-software (Security Information and Event Management) (of een equivalent) met behulp van Azure Event Hubs, of uw eigen analyse uitvoeren en Azure Log Analytics-werk ruimten van de Azure Portal gebruiken.

> [!IMPORTANT]
> Azure AD DS-beveiligings controles zijn alleen beschikbaar voor beheerde domeinen op basis van Azure Resource Manager. Zie [Azure-AD DS migreren van het klassieke virtuele-netwerk model naar Resource Manager][migrate-azure-adds]voor meer informatie over het migreren van.

## <a name="security-audit-destinations"></a>Doelen voor beveiligings controle

U kunt Azure Storage-, Azure Event Hubs-of Azure Log Analytics-werk ruimten gebruiken als doel resource voor Azure AD DS-beveiligings controles. Deze bestemmingen kunnen worden gecombineerd. U kunt bijvoorbeeld Azure Storage gebruiken om gebeurtenissen voor beveiligings controle te archiveren, maar een Azure Log Analytics-werk ruimte om de informatie op de korte termijn te analyseren en te rapporteren.

De volgende tabel geeft een overzicht van scenario's voor elk doel bron type.

> [!IMPORTANT]
> U moet de doel resource maken voordat u de beveiligings controles van Azure AD DS inschakelt. U kunt deze resources maken met behulp van de Azure Portal, Azure PowerShell of de Azure CLI.

| Doel resource | Scenario |
|:---|:---|
|Azure Storage| Dit doel moet worden gebruikt wanneer uw primaire behoefte is om beveiligings controle gebeurtenissen voor archiverings doeleinden op te slaan. Andere doelen kunnen worden gebruikt voor archiverings doeleinden, maar deze doelen bieden mogelijkheden die verder gaan dan de primaire nood zaak van archiveren. <br /><br />Voordat u de beveiligings controle gebeurtenissen van Azure AD DS inschakelt, moet u eerst [een Azure Storage account maken](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Dit doel moet worden gebruikt wanneer uw primaire behoefte is om beveiligings controle gebeurtenissen te delen met aanvullende software, zoals gegevens analyse software of beveiligings gegevens & event management-software (SIEM).<br /><br />Voordat u Azure AD DS beveiligings controle gebeurtenissen inschakelt, [maakt u een event hub met behulp van Azure Portal](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics-werk ruimte| Dit doel moet worden gebruikt wanneer uw primaire behoefte is om veilige controles van de Azure Portal rechtstreeks te analyseren en te controleren.<br /><br />Voordat u Azure AD DS beveiligings controle gebeurtenissen inschakelt, [maakt u een log Analytics-werk ruimte in de Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Beveiligings controle gebeurtenissen inschakelen met behulp van de Azure Portal

Voer de volgende stappen uit om beveiligings controle gebeurtenissen van Azure AD DS in te scha kelen met behulp van de Azure Portal.

> [!IMPORTANT]
> Azure AD DS-beveiligings controles zijn niet terugwerkt. U kunt geen gebeurtenissen uit het verleden ophalen of opnieuw afspelen. Azure AD DS kan alleen gebeurtenissen verzenden die optreden nadat beveiligings controles zijn ingeschakeld.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Zoek en selecteer **Azure AD Domain Services** aan de bovenkant van de Azure Portal. Kies uw beheerde domein, bijvoorbeeld *aaddscontoso.com*.
1. Selecteer in het venster Azure AD DS **Diagnostische instellingen** aan de linkerkant.
1. Er worden standaard geen diagnostische gegevens geconfigureerd. Selecteer **Diagnostische instelling toevoegen** om aan de slag te gaan.

    ![Een diagnostische instelling voor Azure AD Domain Services toevoegen](./media/security-audit-events/add-diagnostic-settings.png)

1. Voer een naam in voor de diagnostische configuratie, zoals *aadds-auditing*.

    Schakel het selectie vakje in voor de gewenste beveiligings controle bestemming. U kunt kiezen uit een Azure Storage-account, een Azure Event Hub of een Log Analytics-werk ruimte. Deze doel resources moeten al bestaan in uw Azure-abonnement. U kunt de doel resources niet in deze wizard maken.

    ![Het vereiste doel en type controle gebeurtenissen inschakelen dat moet worden vastgelegd](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure-opslag**
        * Selecteer **archiveren naar een opslag account** en kies vervolgens **configureren**.
        * Selecteer het **abonnement** en het **opslag account** dat u wilt gebruiken voor het archiveren van beveiligings controle gebeurtenissen.
        * Als u klaar bent, kiest u **OK**.
    * **Azure Event hubs**
        * Selecteer **Stream naar een event hub** en kies vervolgens **configureren**.
        * Selecteer het **abonnement** en de **Event hub-naam ruimte**. Als dat nodig is, kiest u ook de naam van een **Event hub** en vervolgens de naam van het **Event hub-beleid**.
        * Als u klaar bent, kiest u **OK**.
    * **Azure log analytic-werk ruimten**
        * Selecteer **verzenden naar log Analytics** en kies vervolgens het **abonnement** en de **log Analytics werk ruimte** die u wilt gebruiken om beveiligings controle gebeurtenissen op te slaan.

1. Selecteer de logboek categorieën die u voor de specifieke doel resource wilt opnemen. Als u de controle gebeurtenissen naar een Azure Storage-account verzendt, kunt u ook een Bewaar beleid configureren waarmee het aantal dagen voor het bewaren van gegevens wordt gedefinieerd. Een standaard instelling van *0* behoudt alle gegevens en roteert geen gebeurtenissen na een bepaalde tijd.

    U kunt voor elke doel resource een andere logboek categorie selecteren in één configuratie. Met deze mogelijkheid kunt u kiezen welke logboek categorieën u wilt bewaren voor Log Analytics en welke categorieën er moeten worden gearchiveerd, bijvoorbeeld.

1. Wanneer u klaar bent, selecteert u **Opslaan** om uw wijzigingen door te voeren. De doel resources beginnen met het ontvangen van Azure AD DS beveiligings controle gebeurtenissen zodra de configuratie is opgeslagen.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Beveiligings controle gebeurtenissen inschakelen met behulp van Azure PowerShell

Voer de volgende stappen uit om beveiligings controle gebeurtenissen van Azure AD DS in te scha kelen met behulp van Azure PowerShell. Als dat nodig is, [installeert u eerst de Azure PowerShell-module en maakt u verbinding met uw Azure-abonnement](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Azure AD DS-beveiligings controles zijn niet terugwerkt. U kunt geen gebeurtenissen uit het verleden ophalen of opnieuw afspelen. Azure AD DS kan alleen gebeurtenissen verzenden die optreden nadat beveiligings controles zijn ingeschakeld.

1. Verifieer uw Azure-abonnement met de cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . Voer uw account referenties in als dit wordt gevraagd.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Maak de doel resource voor de beveiligings controle gebeurtenissen.

    * **Azure-opslag**  -  [Een opslag account maken met behulp van Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure Event hubs**  -  [Maak een event hub met behulp van Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Mogelijk moet u ook de cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) gebruiken om een autorisatie regel te maken waarmee Azure-AD DS machtigingen worden verleend aan de Event hub *naam ruimte*. De autorisatie regel moet de rechten **beheren**, **Luis teren** en **verzenden** bevatten.

        > [!IMPORTANT]
        > Zorg ervoor dat u de autorisatie regel instelt op de naam ruimte Event Hub en niet op de Event Hub zelf.

    * **Azure log analytic-werk ruimten**  -  [Maak een log Analytics-werk ruimte met Azure PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

1. Gebruik de cmdlet [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) om de resource-id voor uw door Azure AD DS beheerde domein op te halen. Maak een variabele met de naam *$aadds. ResourceId* om de waarde te bevatten:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configureer de diagnostische instellingen voor Azure met behulp van de cmdlet [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) voor het gebruik van de doel resource voor Azure AD Domain Services beveiligings controle gebeurtenissen. In de volgende voor beelden wordt de variabele *$aadds. ResourceId* wordt uit de vorige stap gebruikt.

    * **Azure Storage** : Vervang *storageAccountId* door de naam van uw opslag account:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure Event hubs** -Vervang *eventHubName* door de naam van uw event hub en *eventHubRuleId* met uw autorisatie regel-id:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure log analytic-werk ruimten** : Vervang *workspaceId* door de ID van de log Analytics-werk ruimte:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Beveiligings controle gebeurtenissen opvragen en weer geven met behulp van Azure Monitor

Met log Analytics-werk ruimten kunt u de beveiligings controle gebeurtenissen bekijken en analyseren met behulp van Azure Monitor en de query taal Kusto. Deze query taal is alleen bedoeld voor gebruik met alleen-lezen en biedt mogelijkheden voor Power Analytics met een eenvoudig te lezen-syntaxis. Raadpleeg de volgende artikelen voor meer informatie om aan de slag te gaan met Kusto-query talen:

* [Azure Monitor-documentatie](../azure-monitor/index.yml)
* [Aan de slag met Log Analytics in Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)
* [Aan de slag met logboekquery’s in Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Dashboards van Log Analytics-gegevens maken en delen](../azure-monitor/visualize/tutorial-logs-dashboards.md)

De volgende voorbeeld query's kunnen worden gebruikt om te beginnen met het analyseren van beveiligings controle gebeurtenissen vanuit Azure AD DS.

### <a name="sample-query-1"></a>Voorbeeld query 1

Bekijk alle account vergrendelings gebeurtenissen voor de afgelopen zeven dagen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Voorbeeld query 2

Alle account vergrendelings gebeurtenissen (*4740*) tussen 3 juni 2020 om 9:00 uur weer geven en 10 juni 2020 middernacht, oplopend gesorteerd op de datum en tijd:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Voorbeeld query 3

Aanmeldings gebeurtenissen voor het account zeven dagen geleden weer geven voor het account gebruiker:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Voorbeeld query 4

Aanmeldings gebeurtenissen voor het account zeven dagen geleden weer geven voor het account met de naam gebruiker die zich heeft geprobeerd aan te melden met een onjuist wacht woord (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Voorbeeld query 5

Aanmeldings gebeurtenissen voor het account zeven dagen geleden weer geven voor het account met de naam gebruiker die heeft geprobeerd zich aan te melden terwijl het account is vergrendeld (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Voorbeeld query 6

Het aantal aanmeldings gebeurtenissen voor het account zeven dagen geleden weer geven voor alle aanmeldings pogingen die zijn opgetreden voor alle vergrendelde gebruikers:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Controle gebeurtenis Categorieën

Azure AD DS-beveiligings controles worden uitgelijnd met traditionele controle voor traditionele AD DS-domein controllers. In hybride omgevingen kunt u bestaande controle patronen hergebruiken zodat dezelfde logica kan worden gebruikt bij het analyseren van de gebeurtenissen. Afhankelijk van het scenario dat u nodig hebt om problemen op te lossen of te analyseren, moeten de verschillende controle gebeurtenis categorieën zijn gericht.

De volgende controle gebeurtenis categorieën zijn beschikbaar:

| Naam van audit categorie | Description |
|:---|:---|
| Account aanmelding|Controles proberen account gegevens te verifiëren op een domein controller of op een lokale SAM (Security Accounts Manager).</p>Aanmeldings-en afmeldings beleids instellingen en gebeurtenissen volgen pogingen om toegang te krijgen tot een bepaalde computer. De instellingen en gebeurtenissen in deze categorie zijn gericht op de account database die wordt gebruikt. Deze categorie bevat de volgende subcategorieën:<ul><li>[Validatie van referenties controleren](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[De Kerberos-authenticatieservice controleren](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Kerberos-serviceticketbewerkingen controleren](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Andere gebeurtenissen voor aanmelden/afmelden controleren](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Accountbeheer|Hiermee worden wijzigingen in gebruikers-en computer accounts en-groepen gecontroleerd. Deze categorie bevat de volgende subcategorieën:<ul><li>[Beheer van toepassingsgroepen controleren](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Beheer van computeraccounts controleren](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Beheer van distributiegroepen controleren](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Ander account beheer controleren](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Beheer van beveiligingsgroepen controleren](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Gebruikersaccountbeheer controleren](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Details bijhouden|Controleert activiteiten van afzonderlijke toepassingen en gebruikers op die computer en om te begrijpen hoe een computer wordt gebruikt. Deze categorie bevat de volgende subcategorieën:<ul><li>[DPAPI-activiteiten controleren](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP-activiteit controleren](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Het maken van processen controleren](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Het beëindigen van processen controleren](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[RPC-gebeurtenissen controleren](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Toegang tot Directory Services|Controles pogingen om objecten te openen en te wijzigen in Active Directory Domain Services (AD DS). Deze controle gebeurtenissen worden alleen vastgelegd op domein controllers. Deze categorie bevat de volgende subcategorieën:<ul><li>[Gedetailleerde directoryservice-replicatie controleren](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Directoryservice-toegang controleren](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Directoryservice-wijzigingen controleren](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Directoryservice-replicatie controleren](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|Controles proberen zich interactief aan te melden bij een computer of via een netwerk. Deze gebeurtenissen zijn handig voor het bijhouden van gebruikers activiteiten en het identificeren van mogelijke aanvallen op netwerk bronnen. Deze categorie bevat de volgende subcategorieën:<ul><li>[Accountvergrendeling controleren](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Claims van gebruiker/apparaat controleren](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[IPsec uitgebreide modus controleren](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Groepslid maatschap controleren](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[IPsec-hoofdmodus controleren](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[IPsec snelle modus controleren](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Afmelden controleren](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Aanmelden controleren](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Network Policy Server controleren](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Andere gebeurtenissen voor aanmelden/afmelden controleren](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Speciale aanmelding controleren](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Object toegang| Controles proberen toegang te krijgen tot specifieke objecten of typen objecten in een netwerk of computer. Deze categorie bevat de volgende subcategorieën:<ul><li>[Gegenereerde toepassing controleren](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Gegenereerde toepassing controleren](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Gedetailleerde bestandsshare controleren](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Bestandsshare controleren](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Bestands systeem controleren](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Filterplatform-verbinding controleren](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Verloren gegane pakketten van filterplatform controleren](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Bewerking van controle-ingang](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernel-object controleren](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Andere gebeurtenissen van objecttoegang controleren](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[REGI ster controleren](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Verwissel bare opslag controleren](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[SAM controleren](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Centraal toegangs beleid klaarzetten controleren](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Beleids wijziging|Hiermee worden wijzigingen in belang rijke beveiligings beleidsregels op een lokaal systeem of netwerk gecontroleerd. Beleids regels worden doorgaans door beheerders ingesteld om netwerk bronnen te beveiligen. Het bewaken van wijzigingen of het wijzigen van dit beleid kan een belang rijk aspect zijn van beveiligings beheer voor een netwerk. Deze categorie bevat de volgende subcategorieën:<ul><li>[Wijziging van controlebeleid controleren](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Wijziging in authenticatiebeleid controleren](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Wijziging in autorisatiebeleid controleren](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Wijziging in filterplatformbeleid controleren](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Wijziging in beleid MPSSVC op regelniveau controleren](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Andere beleids wijzigingen controleren](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Gebruik van bevoegdheden| Controleert het gebruik van bepaalde machtigingen op een of meer systemen. Deze categorie bevat de volgende subcategorieën:<ul><li>[Gebruik van niet-gevoelige bevoegdheid controleren](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Gebruik van gevoelige bevoegdheid controleren](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Gebeurtenissen voor gebruik van andere bevoegdheden controleren](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Systeem| Hiermee worden wijzigingen op systeem niveau gecontroleerd op een computer die geen deel uitmaakt van andere categorieën en die mogelijke beveiligings implicaties hebben. Deze categorie bevat de volgende subcategorieën:<ul><li>[IPsec-stuurprogramma controleren](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Andere systeemgebeurtenissen controleren](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Wijziging in beveiligingsstatus controleren](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Uitbreiding van beveiligingssysteem controleren](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Systeemintegriteit controleren](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Gebeurtenis-Id's per categorie

 In azure AD DS-beveiligings controles worden de volgende gebeurtenis-Id's vastgelegd wanneer met de specifieke actie een controle bare gebeurtenis wordt geactiveerd:

| Naam van gebeurtenis categorie | Gebeurtenis-Id's |
|:---|:---|
|Beveiliging account aanmelding|4767, 4774, 4775, 4776, 4777|
|Account beheer beveiliging|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Details bijhouden van beveiliging|Geen|
|Beveiliging van Active Directory-toegang|5136, 5137, 5138, 5139, 5141|
|Logon-Logoff beveiliging|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Beveiliging tegen object toegang|Geen|
|Beveiliging van beleids wijzigingen|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Beveiliging van bevoegdheden gebruiken|4985|
|Systeembeveiliging|4612, 4621|

## <a name="next-steps"></a>Volgende stappen

Voor specifieke informatie over Kusto raadpleegt u de volgende artikelen:

* [Overzicht](/azure/kusto/query/) van de Kusto-query taal.
* [Kusto-zelf studie](/azure/kusto/query/tutorial) voor meer informatie over de basis principes van query's.
* [Voorbeeld query's](/azure/kusto/query/samples) waarmee u nieuwe manieren kunt leren om uw gegevens te bekijken.
* Kusto [Best practices](/azure/kusto/query/best-practices) om uw query's voor succes te optimaliseren.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md