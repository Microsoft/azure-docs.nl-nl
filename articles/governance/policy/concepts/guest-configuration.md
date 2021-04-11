---
title: Meer informatie over het controleren van de inhoud van virtuele machines
description: Meer informatie over hoe Azure Policy de gast configuratie-client gebruikt om instellingen in virtuele machines te controleren.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: 6fb3ed3644ccdb5de8f03bedf56943a91570322b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105733023"
---
# <a name="understand-azure-policys-guest-configuration"></a>Gastconfiguratie van Azure Policy begrijpen


Azure Policy kunt instellingen in een computer controleren, zowel voor machines die worden uitgevoerd in azure als [met Arc verbonden computers](../../../azure-arc/servers/overview.md). De validatie wordt uitgevoerd door de extensie en client voor gastconfiguratie. De extensie valideert, via de client, instellingen zoals:

- De configuratie van het besturingssysteem
- Configuratie of aanwezigheid van toepassingen
- Omgevingsinstellingen

Op dit moment worden in de meeste Azure Policy beleids definities voor gast configuratie alleen de instellingen van de computer gecontroleerd. U kunt ze niet om configuraties toepassen. De uitzonde ring hierop is één ingebouwd beleid [waarnaar hieronder wordt verwezen](#applying-configurations-using-guest-configuration).

[Er is een video-overzicht van dit document beschikbaar](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>Gast configuratie inschakelen

Als u de status van machines in uw omgeving wilt controleren, inclusief machines in Azure en Arc Connected machines, raadpleegt u de volgende details.

## <a name="resource-provider"></a>Resourceprovider

Voordat u de gast configuratie kunt gebruiken, moet u de resource provider registreren. Als de toewijzing van een gast configuratie beleid wordt uitgevoerd via de portal, of als het abonnement is geregistreerd bij Azure Security Center, wordt de resource provider automatisch geregistreerd. U kunt hand matig registreren via de [Portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure POWERSHELL](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)of [Azure cli](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Vereisten voor Azure virtual machines implementeren

Voor het controleren van instellingen op een machine is de extensie van de [virtuele machine](../../../virtual-machines/extensions/overview.md) ingeschakeld en moet de computer een door het systeem beheerde identiteit hebben. De extensie downloadt de toepasselijke beleidstoewijzing en de bijbehorende configuratiedefinitie. De identiteit wordt gebruikt om de computer te verifiëren tijdens het lezen en schrijven naar de gast configuratie service. De uitbrei ding is niet vereist voor met Arc verbonden computers, omdat deze is opgenomen in de Arc Connected machine agent.

> [!IMPORTANT]
> De gast configuratie-extensie en een beheerde identiteit zijn vereist voor het controleren van virtuele Azure-machines. Wijs het volgende beleids initiatief toe om de uitbrei ding op schaal te implementeren:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Limieten die zijn ingesteld voor de uitbrei ding

Als u de uitbrei ding wilt beperken voor toepassingen die worden uitgevoerd op de computer, mag de gast configuratie niet meer dan 5% van de CPU overschrijden. Deze beperking bestaat voor zowel ingebouwde als aangepaste definities. Hetzelfde geldt voor de gast configuratie service in Arc Connected machine agent.

### <a name="validation-tools"></a>Validatie hulpprogramma's

In de computer gebruikt de gast configuratie-client lokale hulpprogram ma's om de controle uit te voeren.

De volgende tabel bevat een lijst met de lokale hulpprogram ma's die op elk ondersteund besturings systeem worden gebruikt. Voor ingebouwde inhoud verwerkt gast configuratie automatisch het laden van deze hulpprogram ma's.

|Besturingssysteem|Validatie programma|Notities|
|-|-|-|
|Windows|[Power shell desired state Configuration](/powershell/scripting/dsc/overview/overview) v2| Een kant geladen naar een map die alleen door Azure Policy wordt gebruikt. Er is geen conflict met Windows Power shell DSC. Power shell Core is niet toegevoegd aan het systeempad.|
|Linux|[Chef-specificatie](https://www.chef.io/inspec/)| Installeert chef Inspec-versie 2.2.61 op de standaard locatie en wordt toegevoegd aan het systeempad. Afhankelijkheden voor het INSPEC-pakket, waaronder Ruby en Python, worden ook geïnstalleerd. |

### <a name="validation-frequency"></a>Validatie frequentie

De gast configuratie client controleert elke vijf minuten of er nieuwe of gewijzigde gast toewijzingen zijn. Zodra een gast toewijzing is ontvangen, worden de instellingen voor die configuratie opnieuw gecontroleerd met een interval van vijf tien minuten. Er worden resultaten verzonden naar de provider van de gast configuratie bron wanneer de controle is voltooid. Wanneer er een beleids [evaluatie](../how-to/get-compliance-data.md#evaluation-triggers) wordt uitgevoerd, wordt de status van de machine naar de provider van de gast configuratie genoteerd. Deze update zorgt ervoor Azure Policy de Azure Resource Manager eigenschappen te evalueren. Een Azure Policy evaluatie op aanvraag haalt de meest recente waarde op van de provider van de gast configuratie resource. Er wordt echter geen nieuwe controle van de configuratie op de computer geactiveerd. De status is tegelijkertijd geschreven naar Azure resource Graph.

## <a name="supported-client-types"></a>Ondersteunde client typen

Beleids definities voor gast configuratie zijn inclusief nieuwe versies. Oudere versies van besturings systemen die beschikbaar zijn in azure Marketplace, worden uitgesloten als de gast configuratie-client niet compatibel is. In de volgende tabel ziet u een lijst met ondersteunde besturings systemen in azure-installatie kopieën:

|Publisher|Name|Versies|
|-|-|-|
|Canonical|Ubuntu Server|14,04-20,04|
|Credativ|Debian|8 - 10|
|Microsoft|Windows Server|2012-2019|
|Microsoft|Windows-client|Windows 10|
|OpenLogic|CentOS|7,3-8|
|Red Hat|Red Hat Enterprise Linux|7,4-8|
|SuSE|SLES|12 SP3-SP5, 15|

Aangepaste installatie kopieën van virtuele machines worden ondersteund door beleids definities voor gast configuraties zolang ze een van de besturings systemen in de bovenstaande tabel zijn.

## <a name="network-requirements"></a>Netwerkvereisten

Virtuele machines in azure kunnen de lokale netwerk adapter of een persoonlijke koppeling gebruiken om te communiceren met de gast configuratie service.

Azure-computers verbinden met behulp van de on-premises netwerk infrastructuur om Azure-Services te bereiken en de nalevings status van het rapport te rapporteren.

### <a name="communicate-over-virtual-networks-in-azure"></a>Communiceren via virtuele netwerken in azure

Voor virtuele machines die gebruikmaken van virtuele netwerken voor communicatie is uitgaande toegang tot Azure-data centers op poort vereist `443` . Als u een particulier virtueel netwerk in azure gebruikt dat geen uitgaand verkeer toestaat, moet u uitzonde ringen configureren met de regels voor de netwerk beveiligings groep. De servicetag ' GuestAndHybridManagement ' kan worden gebruikt om te verwijzen naar de gast configuratie service.

### <a name="communicate-over-private-link-in-azure"></a>Communiceren via een persoonlijke koppeling in azure

Virtuele machines kunnen een [persoonlijke koppeling](../../../private-link/private-link-overview.md) gebruiken voor communicatie met de gast configuratie service. Pas tag toe met de naam `EnablePrivateNeworkGC` (zonder ' t ' in het netwerk) en de waarde `TRUE` om deze functie in te scha kelen. De tag kan worden toegepast vóór of na het Toep assen van beleids definities voor gast configuratie op de computer.

Verkeer wordt gerouteerd met behulp van het [virtuele openbaar IP-adres](../../../virtual-network/what-is-ip-address-168-63-129-16.md) van Azure om een beveiligd, geverifieerd kanaal met Azure-platform bronnen te maken.

### <a name="azure-arc-connected-machines"></a>Verbonden Azure Arc-machines

Knoop punten die zich buiten Azure bevinden en die zijn verbonden met Azure Arc, vereisen connectiviteit met de gast configuratie service. Details over netwerk-en proxy vereisten in de [documentatie van Azure Arc](../../../azure-arc/servers/overview.md).

Voor de communicatie met de provider van de gast configuratie resource in azure, hebben computers uitgaande toegang tot Azure-data centers op poort **443**. Als een netwerk in azure geen uitgaand verkeer toestaat, moet u uitzonde ringen configureren met de regels voor de [netwerk beveiligings groep](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . De [servicetag ' GuestAndHybridManagement ' kan](../../../virtual-network/service-tags-overview.md) worden gebruikt om te verwijzen naar de gast configuratie service.

Voor Arc connected servers in private data centers, verkeer toestaan met de volgende patronen:

- Poort: alleen TCP 443 vereist voor uitgaande internet toegang
- Globale URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Vereisten voor beheerde identiteit

Met beleids definities in het initiatief _voor het implementeren van vereisten om gast configuratie beleid in te scha kelen op virtuele machines_ , kan een door het systeem toegewezen beheerde identiteit worden ingeschakeld als er geen bestaat. Er zijn twee beleids definities in het initiatief die het maken van identiteiten beheren. De IF-voor waarden in de beleids definities zorgen voor het juiste gedrag op basis van de huidige status van de machine resource in Azure.

Als de computer momenteel geen beheerde identiteiten heeft, is het effectief beleid: aan het [systeem toegewezen beheerde identiteit toevoegen om gast configuratie toewijzingen op virtuele machines zonder identiteiten in te scha kelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Als de computer momenteel een door de gebruiker toegewezen systeem identiteit heeft, is het effectief beleid: door het [systeem toegewezen beheerde identiteit toevoegen om gast configuratie toewijzingen op vm's met een door de gebruiker toegewezen identiteit in te scha kelen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Vereisten voor gast configuratie definitie

Beleids definities voor gast configuraties gebruiken het **AuditIfNotExists** -effect. Wanneer de definitie wordt toegewezen, wordt de levens cyclus van alle vereisten in de Azure-resource provider automatisch door een back-end-service afgehandeld `Microsoft.GuestConfiguration` .

De **AuditIfNotExists** -beleids definities retour neren geen compliantie resultaten totdat aan alle vereisten wordt voldaan op de computer. De vereisten worden beschreven in de sectie [vereisten voor Azure virtual machines implementeren](#deploy-requirements-for-azure-virtual-machines)

> [!IMPORTANT]
> In een eerdere versie van de gast configuratie was een initiatief vereist om **DeployIfNoteExists** -en **AuditIfNotExists** -definities te combi neren. **DeployIfNotExists** -definities zijn niet meer vereist. De definities en initiatieven hebben een label, `[Deprecated]` maar bestaande toewijzingen blijven functioneren. Zie voor meer informatie het blog bericht: [belang rijke wijziging vrijgegeven voor controle beleid gast configuratie](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

### <a name="what-is-a-guest-assignment"></a>Wat is een gast toewijzing?

Als er een Azure Policy is toegewezen, als het zich in de categorie gast configuratie bevindt, zijn de meta gegevens opgenomen om een gast toewijzing te beschrijven.
U kunt een gast toewijzing beschouwen als een koppeling tussen een computer en een Azure Policy scenario.
Het onderstaande fragment koppelt bijvoorbeeld de Azure Windows-basis lijn configuratie met de minimale versie `1.0.0` aan alle computers binnen het bereik van het beleid. De gast toewijzing voert standaard alleen een controle uit van de machine.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

Gast toewijzingen worden automatisch per computer gemaakt door de gast configuratie service. Het resourcetype is `Microsoft.GuestConfiguration/guestConfigurationAssignments`.
Azure Policy gebruikt de eigenschap **complianceStatus** van de resource van de gast toewijzing om de nalevings status te rapporteren. Zie [nalevings gegevens ophalen](../how-to/get-compliance-data.md)voor meer informatie.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>De instellingen van het besturings systeem controleren volgens de industrie basislijnen

Een initiatief in Azure Policy controleert de instellingen van het besturings systeem na een basis lijn. De definitie, _\[ Preview \] : Windows-computers moeten voldoen aan de vereisten voor de basis lijn van Azure Security_ bevat een set regels op basis van Active Directory groepsbeleid.

De meeste instellingen zijn beschikbaar als para meters. Met para meters kunt u bepalen wat er wordt gecontroleerd.
Lijn het beleid uit met uw vereisten of wijs het beleid toe aan gegevens van derden, zoals industriële regelgevende normen.

Sommige para meters ondersteunen een bereik van gehele waarden. Met de instelling maximale wachtwoord duur kunt u bijvoorbeeld de instelling effectief groepsbeleid controleren. Een bereik van ' 1, 70 ' zou bevestigen dat gebruikers hun wacht woord moeten wijzigen ten minste elke 70 dagen, maar niet minder dan een dag.

Als u het beleid toewijst met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon), gebruikt u een parameter bestand voor het beheren van uitzonde ringen. Controleer de bestanden in een versie beheersysteem, zoals git. Opmerkingen over bestands wijzigingen bieden bewijs waarom een toewijzing een uitzonde ring is op de verwachte waarde.

#### <a name="applying-configurations-using-guest-configuration"></a>Configuraties Toep assen met behulp van gast configuratie

Alleen de definitie _configureren van de tijd zone op Windows-machines_ wijzigt de machine door de tijd zone te configureren. Aangepaste beleids definities voor het configureren van instellingen binnen machines worden niet ondersteund.

Bij het toewijzen van definities die beginnen met _configureren_, moet u ook de _vereisten voor definitie-implementatie toewijzen om gast configuratie beleid in te scha kelen op Windows-vm's_. U kunt deze definities in een initiatief combi neren, indien gewenst.

> [!NOTE]
> Het ingebouwde tijd zone beleid is de enige definitie die het configureren van instellingen binnen machines ondersteunt en aangepaste beleids definities waarmee instellingen worden geconfigureerd binnen machines worden niet ondersteund.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Beleids regels toewijzen aan computers buiten Azure

De controle beleids definities die beschikbaar zijn voor gast configuratie zijn onder andere het resource type **micro soft. HybridCompute/machines** . Computers die worden uitgevoerd op [Azure Arc voor servers](../../../azure-arc/servers/overview.md) die zich binnen het bereik van de beleids toewijzing bevinden, worden automatisch opgenomen.

## <a name="troubleshooting-guest-configuration"></a>Problemen met de gast configuratie oplossen

Zie [Azure Policy Troubleshooting](../troubleshoot/general.md)(Engelstalig) voor meer informatie over het oplossen van problemen met de gast configuratie.

### <a name="multiple-assignments"></a>Meerdere toewijzingen

Beleids definities voor gast configuraties bieden momenteel alleen ondersteuning voor het toewijzen van dezelfde gast toewijzing per computer, zelfs als de beleids toewijzing gebruikmaakt van verschillende para meters.

### <a name="client-log-files"></a>Client logboek bestanden

De gast configuratie-extensie schrijft logboek bestanden naar de volgende locaties:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux

- Azure VM: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`
- Azure VM: `/var/lib/GuestConfig/arc_policy_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Logboeken extern verzamelen

De eerste stap in het oplossen van problemen met configuratie van gast configuraties of modules moet zijn om de-cmdlet te gebruiken `Test-GuestConfigurationPackage` volgens de stappen voor het [maken van een aangepaste gast configuratie-controle beleid voor Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Als dat niet lukt, kan het verzamelen van client logboeken helpen bij het vaststellen van problemen.

#### <a name="windows"></a>Windows

Gegevens vastleggen vanuit logboek bestanden met de [opdracht Azure VM run](../../../virtual-machines/windows/run-command.md). het volgende voor beeld van een Power shell-script kan nuttig zijn.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Gegevens van logboek bestanden vastleggen met behulp van de [opdracht Azure VM run](../../../virtual-machines/linux/run-command.md), het volgende voor beeld bash-script kan handig zijn.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>Client bestanden

De gast configuratie-client downloadt inhouds pakketten naar een machine en extraheert de inhoud.
Als u wilt controleren welke inhoud is gedownload en opgeslagen, bekijkt u de hieronder vermelde locaties.

Windows: `c:\programdata\guestconfig\configuration`

Linux: `/var/lib/GuestConfig/Configuration`

## <a name="guest-configuration-samples"></a>Voor beelden van gast configuraties

De ingebouwde beleids voorbeelden van de gast configuratie zijn beschikbaar op de volgende locaties:

- [Ingebouwde beleids definities-gast configuratie](../samples/built-in-policies.md#guest-configuration)
- [Ingebouwde initiatieven-gast configuratie](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-voor beelden GitHub opslag plaats](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>Video-overzicht

Het volgende overzicht van Azure Policy gast configuratie is van ITOps lezingen 2021.

[Basis lijnen in hybride server omgevingen met Azure Policy gast configuratie beheren](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het weer geven van de details van elke instelling in de [weer gave naleving van gast configuratie](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Lees over de [structuur van Azure Policy-definities](./definition-structure.md).
- Lees [Informatie over de effecten van het beleid](./effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Ontdek hoe u [niet-compatibele resources kunt herstellen](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
