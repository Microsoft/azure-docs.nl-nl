---
title: Autorisatie met gedeelde sleutel voor komen (preview-versie)
titleSuffix: Azure Storage
description: Als u wilt dat clients Azure AD gebruiken om aanvragen te autoriseren, kunt u geen aanvragen toestaan voor het opslag account dat is geautoriseerd met een gedeelde sleutel (preview).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 6a24713a6027c38d2b9817928f3a82161bd37314
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96936723"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Verificatie van gedeelde sleutels voor een Azure Storage account voor komen (preview-versie)

Elke beveiligde aanvraag voor een Azure Storage account moet worden geautoriseerd. Standaard kunnen aanvragen worden geautoriseerd met de referenties van de Azure Active Directory (Azure AD) of met behulp van de toegangs sleutel voor een gedeelde sleutel. Van deze twee soorten autorisatie biedt Azure Active Directory een superieure beveiliging en gebruiks vriendelijk gebruik van de gedeelde sleutel en wordt aanbevolen door micro soft. Als u wilt dat clients Azure AD gebruiken om aanvragen te autoriseren, kunt u geen aanvragen toestaan voor het opslag account dat is geautoriseerd met een gedeelde sleutel (preview).

Wanneer u de verificatie van een gedeelde sleutel voor een opslag account niet toestaat, wijst Azure Storage alle volgende aanvragen af bij dat account dat is geautoriseerd met de toegangs sleutels voor het account. Alleen beveiligde aanvragen die zijn gemachtigd met Azure AD, slagen. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie over het gebruik van Azure AD.

> [!WARNING]
> Azure Storage ondersteunt Azure AD-autorisatie alleen voor aanvragen voor Blob-en wachtrij opslag. Als u autorisatie met gedeelde sleutel voor een opslag account niet toestaat, zullen aanvragen naar Azure Files-of tabel opslag die gebruikmaken van gedeelde sleutel autorisatie, mislukken. Omdat de Azure Portal altijd een verificatie met een gedeelde sleutel gebruikt om toegang te krijgen tot bestands-en tabel gegevens, is het niet mogelijk om toegang te krijgen tot de gegevens van het bestand of de tabel in de Azure Portal als u autorisatie met gedeelde sleutel voor het opslag account niet toestaat.
>
> Micro soft raadt u aan om alle Azure Files-of tabel opslag gegevens te migreren naar een afzonderlijk opslag account voordat u toegang tot het account via een gedeelde sleutel niet toestaat, of als u deze instelling niet toepast op opslag accounts die ondersteuning bieden voor Azure Files of de werk belasting van de tabel opslag.
>
> Het niet toestaan van toegang tot gedeelde sleutels voor een opslag account heeft geen invloed op SMB-verbindingen met Azure Files.

In dit artikel wordt beschreven hoe u aanvragen detecteert die zijn verzonden met gedeelde sleutel autorisatie en hoe u de autorisatie van gedeelde sleutels voor uw opslag account herstelt. Zie [over de preview-versie](#about-the-preview)als u wilt weten hoe u zich kunt registreren voor de preview-versie.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Het type verificatie dat door client toepassingen wordt gebruikt, detecteren

Wanneer u de verificatie van de gedeelde sleutel voor een opslag account niet toestaat, zullen aanvragen van clients die gebruikmaken van de toegangs sleutels voor de gedeelde sleutel, mislukken. Schakel logboek registratie en metrische gegevens in voor het opslag account om te begrijpen hoe het niet toestaan van een gedeelde sleutel autorisatie kan invloed hebben op client toepassingen voordat u deze wijziging aanbrengt. U kunt vervolgens de patronen van aanvragen voor uw account analyseren gedurende een bepaalde periode om te bepalen hoe aanvragen worden geautoriseerd.

Metrische gegevens gebruiken om te bepalen hoeveel aanvragen het opslag account ontvangt dat is geautoriseerd met een gedeelde sleutel of een Shared Access Signature (SAS). Gebruik Logboeken om te bepalen welke clients deze aanvragen verzenden.

Zie [over de preview-versie](#about-the-preview)voor meer informatie over het interpreteren van aanvragen die zijn gedaan met een Shared Access Signature tijdens de preview-versie.

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Controleren hoeveel aanvragen zijn geautoriseerd met een gedeelde sleutel

Als u wilt bijhouden hoe aanvragen voor een opslag account worden geautoriseerd, gebruikt u Azure Metrics Explorer in de Azure Portal. Zie aan de slag [met Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)voor meer informatie over Metrics Explorer.

Volg deze stappen om een metriek te maken waarmee aanvragen worden bijgehouden die zijn gemaakt met gedeelde sleutel of SA'S:

1. Ga in Azure Portal naar uw opslagaccount. Selecteer **metrische gegevens** onder het gedeelte **bewaking** .
1. Selecteer **Metrische waarde toevoegen**. Geef in het dialoog venster **metrisch** de volgende waarden op:
    1. Zorg ervoor dat het veld **bereik** is ingesteld op de naam van het opslag account.
    1. Stel de **metrische naam ruimte** in op *account*. Met deze metriek worden alle aanvragen voor het opslag account gerapporteerd.
    1. Stel het veld **metrische waarde** in op *trans acties*.
    1. Stel het **aggregatie** veld in op *Sum*.

    Met de nieuwe metriek wordt de som van het aantal trans acties voor het opslag account in een bepaalde tijds interval weer gegeven. De resulterende metriek wordt weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Scherm afbeelding die laat zien hoe u metrische gegevens kunt optellen voor trans acties die zijn gemaakt met gedeelde sleutel of SAS":::

1. Selecteer vervolgens de knop **filter toevoegen** om een filter te maken voor de metriek voor het type autorisatie.
1. Geef in het dialoog venster **filter** de volgende waarden op:
    1. Stel de waarde van de **eigenschap** in op *authenticatie*.
    1. Stel het **operator** veld in op het gelijkteken (=).
    1. Selecteer *account sleutel* en *sa's* in het veld **waarden** .
1. Selecteer in de rechter bovenhoek het tijds bereik waarvoor u de metrische gegevens wilt weer geven. U kunt ook aangeven hoe gedetailleerd de aggregatie van aanvragen moet zijn, door intervallen van 1 minuut tot 1 maand op te geven. Stel bijvoorbeeld het **tijds bereik** in op 30 dagen en de **tijd granulatie** op 1 dag om de aanvragen weer te geven die zijn geaggregeerd per dag gedurende de afgelopen 30 dagen.

Nadat u de metrische gegevens hebt geconfigureerd, worden de aanvragen voor uw opslag account weer gegeven in de grafiek. De volgende afbeelding toont aanvragen die zijn geautoriseerd met een gedeelde sleutel of zijn gemaakt met een SAS-token. Aanvragen worden per dag in de afgelopen dertig dagen geaggregeerd.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Scherm opname van geaggregeerde aanvragen die zijn gemachtigd met een gedeelde sleutel":::

U kunt ook een waarschuwings regel configureren om u op de hoogte te stellen wanneer een bepaald aantal aanvragen dat is geautoriseerd met een gedeelde sleutel, wordt gemaakt op basis van uw opslag account. Zie [metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-metric.md)voor meer informatie.

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Logboeken analyseren om clients te identificeren die aanvragen toestaan met gedeelde sleutel of SAS

Azure Storage legt vastgelegde gegevens vast over aanvragen voor het opslag account, met inbegrip van de manier waarop een aanvraag is geautoriseerd. U kunt de logboeken analyseren om te bepalen welke clients aanvragen toestaan met een gedeelde sleutel of een SAS-token.

Als u aanvragen wilt registreren voor uw Azure Storage-account om te controleren hoe ze zijn geautoriseerd, kunt u Azure Storage logboek registratie gebruiken in Azure Monitor (preview). Zie [Azure Storage bewaken](../blobs/monitor-blob-storage.md)voor meer informatie.

Azure Storage logboek registratie in Azure Monitor ondersteunt het gebruik van logboek query's voor het analyseren van logboek gegevens. Als u een query wilt uitvoeren op Logboeken, kunt u een Azure Log Analytics-werk ruimte gebruiken. Zie [zelf studie: aan de slag met log Analytics query's](../../azure-monitor/log-query/log-analytics-tutorial.md)voor meer informatie over logboek query's.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Een diagnostische instelling maken in de Azure Portal

Als u Azure Storage gegevens wilt registreren met Azure Monitor en deze wilt analyseren met Azure Log Analytics, moet u eerst een diagnostische instelling maken die aangeeft welke typen aanvragen en voor welke opslag Services u gegevens wilt registreren. Voer de volgende stappen uit om een diagnostische instelling te maken in de Azure Portal:

1. Schrijf u in [voor de Azure Storage registratie in azure monitor preview](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Maak een nieuwe Log Analytics-werk ruimte in het abonnement dat uw Azure Storage-account bevat of gebruik een bestaande Log Analytics-werk ruimte. Nadat u logboek registratie voor uw opslag account hebt geconfigureerd, zijn de logboeken beschikbaar in de werk ruimte Log Analytics. Raadpleeg [Een Log Analytics-werkruimte maken in Azure Portal](../../azure-monitor/learn/quick-create-workspace.md) voor meer informatie.
1. Ga in Azure Portal naar uw opslagaccount.
1. Selecteer in de sectie controle de optie **Diagnostische instellingen (preview)**.
1. Selecteer de Azure Storage-service waarvoor u aanvragen wilt registreren. Kies bijvoorbeeld **BLOB** om aanvragen te registreren bij Blob Storage.
1. Selecteer **Diagnostische instellingen toevoegen**.
1. Geef een naam op voor de diagnostische instelling.
1. Onder **categorie Details**, in de sectie **logboek** , kiest u **StorageRead**, **StorageWrite** en **StorageDelete** om alle gegevens aanvragen bij de geselecteerde service te registreren.
1. Selecteer onder **doel gegevens** **verzenden naar log Analytics**. Selecteer uw abonnement en de Log Analytics werk ruimte die u eerder hebt gemaakt, zoals wordt weer gegeven in de volgende afbeelding.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Scherm afbeelding die laat zien hoe u een diagnostische instelling voor logboek registratie aanvragen maakt":::

U kunt een diagnostische instelling maken voor elk type Azure Storage bron in uw opslag account.

Nadat u de diagnostische instelling hebt gemaakt, worden aanvragen voor het opslag account vervolgens geregistreerd volgens die instelling. Zie voor meer informatie [Diagnostische instelling maken om bron logboeken en metrische gegevens in azure te verzamelen](../../azure-monitor/platform/diagnostic-settings.md).

Zie [bron Logboeken (preview)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)voor een verwijzing van velden die beschikbaar zijn in azure Storage-Logboeken in azure monitor.

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Query logboeken voor aanvragen die zijn gemaakt met gedeelde sleutel of SAS

Azure Storage-Logboeken in Azure Monitor bevatten het type autorisatie dat is gebruikt voor het maken van een aanvraag voor een opslag account. Als u logboeken wilt ophalen voor aanvragen die in de afgelopen zeven dagen zijn goedgekeurd met gedeelde sleutel of SA'S, opent u uw Log Analytics-werk ruimte. Plak vervolgens de volgende query in een nieuwe logboek query en voer deze uit. Met deze query worden de tien IP-adressen weer gegeven die het vaakst verzonden aanvragen die zijn geautoriseerd met gedeelde sleutel of SA'S:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

U kunt ook een waarschuwings regel configureren op basis van deze query om u te informeren over aanvragen die zijn gemachtigd met gedeelde sleutel of SA'S. Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](../../azure-monitor/platform/alerts-log.md)voor meer informatie.

## <a name="remediate-authorization-via-shared-key"></a>Autorisatie herstellen via gedeelde sleutel

Nadat u hebt geanalyseerd hoe aanvragen voor uw opslag account worden geautoriseerd, kunt u actie ondernemen om te voor komen dat toegang via een gedeelde sleutel. Maar eerst moet u alle toepassingen bijwerken die gebruikmaken van de verificatie van de gedeelde sleutel voor het gebruik van Azure AD. U kunt Logboeken en metrische gegevens controleren, zoals wordt beschreven in [het type verificatie dat door client toepassingen wordt gebruikt detecteren](#detect-the-type-of-authorization-used-by-client-applications) om de overgang bij te houden. Zie [toegang tot blobs en wacht rijen toestaan met Azure Active Directory](storage-auth-aad.md)voor meer informatie over het gebruik van Azure AD met Blob-en wachtrij gegevens.

Wanneer u zeker weet dat u aanvragen die zijn geautoriseerd met een gedeelde sleutel veilig kunt afwijzen, kunt u de eigenschap **AllowSharedKeyAccess** voor het opslag account instellen op **False**.

De eigenschap **AllowSharedKeyAccess** is niet standaard ingesteld en retourneert geen waarde voordat u deze expliciet hebt ingesteld. Het opslag account staat aanvragen toe die zijn geautoriseerd met een gedeelde sleutel wanneer de waarde van de eigenschap **Null** is of wanneer deze **waar** is.

> [!WARNING]
> Als clients momenteel toegang hebben tot gegevens in uw opslag account met een gedeelde sleutel, raadt micro soft u aan deze clients te migreren naar Azure AD voordat u de gedeelde sleutel toegang tot het opslag account niet toestaat.

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

Voer de volgende stappen uit om de verificatie van de gedeelde sleutel voor een opslag account in de Azure Portal niet toe te staan:

1. Ga in Azure Portal naar uw opslagaccount.
1. Zoek de **configuratie** -instelling onder **instellingen**.
1. Stel **gedeelde sleutel toegang toestaan** in op **uitgeschakeld**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Scherm afbeelding die laat zien hoe toegang tot gedeelde sleutels voor accounts niet kan worden toegestaan":::

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u de verificatie van gedeelde sleutels voor een opslag account met Azure CLI niet wilt toestaan, installeert u Azure CLI versie 2.9.1 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie. Configureer vervolgens de eigenschap **allowSharedKeyAccess** voor een nieuw of bestaand opslag account.

In het volgende voor beeld ziet u hoe u de eigenschap **allowSharedKeyAccess** instelt met behulp van Azure cli. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Nadat u de verificatie van de gedeelde sleutel niet hebt toegestaan, mislukt het maken van een aanvraag naar het opslag account met gedeelde sleutel autorisatie, met fout code 403 (verboden). Azure Storage retourneert een fout die aangeeft dat autorisatie op basis van een sleutel niet is toegestaan voor het opslag account.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Controleren of de toegang tot gedeelde sleutels niet is toegestaan

Als u wilt controleren of de verificatie van de gedeelde sleutel niet meer is toegestaan, kunt u proberen een gegevens bewerking aan te roepen met de toegangs sleutel voor het account. In het volgende voor beeld wordt geprobeerd een container te maken met behulp van de toegangs sleutel. Deze aanroep mislukt wanneer de gedeelde sleutel autorisatie niet is toegestaan voor het opslag account. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Anonieme aanvragen zijn niet geautoriseerd en worden voortgezet als u het opslag account en de container voor anonieme open bare Lees toegang hebt geconfigureerd. Zie [anonieme open bare Lees toegang voor containers en blobs configureren](../blobs/anonymous-read-access-configure.md)voor meer informatie.

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>De toegangs instelling voor gedeelde sleutels voor meerdere accounts controleren

Als u de instelling voor gedeelde toegangs sleutels wilt controleren over een set opslag accounts met optimale prestaties, kunt u de Azure resource Graph Explorer gebruiken in de Azure Portal. Voor meer informatie over het gebruik van de resource Graph Explorer raadpleegt u [Quick Start: uw eerste resource grafiek query uitvoeren met Azure resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Als u de volgende query uitvoert in de resource Graph Explorer, wordt een lijst met opslag accounts geretourneerd en wordt de gedeelde sleutel toegangs instelling voor elk account weer gegeven:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>Machtigingen voor het toestaan of weigeren van toegang tot gedeelde sleutels

Als u de eigenschap **AllowSharedKeyAccess** voor het opslag account wilt instellen, moet een gebruiker gemachtigd zijn om opslag accounts te maken en te beheren. Azure RBAC-rollen (op rollen gebaseerd toegangs beheer) die deze machtigingen bieden, zijn onder andere de actie **micro soft. Storage/Storage accounts/write** of **micro \* soft. Storage/Storage accounts/* _. Ingebouwde rollen met deze actie zijn onder andere:

- De rol van Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner)
- De rol [inzender](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- De rol [Inzender voor opslag accounts](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Deze rollen bieden geen toegang tot gegevens in een opslag account via Azure Active Directory (Azure AD). Ze bevatten echter de _ * micro soft. Storage/Storage accounts/listkeys ophalen/Action * *, waarmee toegang wordt verleend aan de toegangs sleutels voor het account. Met deze machtiging kan een gebruiker de toegangs sleutels voor het account gebruiken om toegang te krijgen tot alle gegevens in een opslag account.

Roltoewijzingen moeten worden ingedeeld op het niveau van het opslag account of hoger, zodat een gebruiker toegang tot gedeelde sleutels voor het opslag account toestaat of weigert. Zie [inzicht in het bereik voor Azure RBAC](../../role-based-access-control/scope-overview.md)voor meer informatie over het gebruikersrol bereik.

Zorg ervoor dat u alleen de toewijzing van deze rollen beperkt tot degenen die de mogelijkheid moeten hebben om een opslag account te maken of om de eigenschappen ervan bij te werken. Gebruik het principe van minimale bevoegdheden om ervoor te zorgen dat gebruikers de minste machtigingen hebben die ze nodig hebben om hun taken uit te voeren. Zie [Aanbevolen procedures voor Azure RBAC](../../role-based-access-control/best-practices.md)voor meer informatie over het beheren van toegang met Azure RBAC.

> [!NOTE]
> De service beheerder rollen van de klassieke abonnements beheerder en Co-Administrator omvatten het equivalent van de Azure Resource Manager [eigenaar](../../role-based-access-control/built-in-roles.md#owner) van de rol. De rol **eigenaar** omvat alle acties, dus een gebruiker met een van deze beheerders rollen kan ook opslag accounts maken en beheren. Zie [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-beheerdersrollen](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles) voor meer informatie.

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Begrijpen hoe het niet toestaan van gedeelde sleutels van invloed is op SAS-tokens

Wanneer gedeelde sleutel toegang niet is toegestaan voor het opslag account, worden met Azure Storage SAS-tokens verwerkt op basis van het type SA'S en de service waarop de aanvraag is gericht. In de volgende tabel ziet u hoe elk type SAS is toegestaan en hoe Azure Storage die SA'S afhandelt wanneer de eigenschap **AllowSharedKeyAccess** voor het opslag account **False** is.

| Type SAS | Type autorisatie | Gedrag wanneer AllowSharedKeyAccess onwaar is |
|-|-|-|
| SAS voor gebruikers overdracht (alleen Blob-opslag) | Azure AD | De aanvraag is toegestaan. Micro soft raadt u aan om de SAS voor gebruikers te gebruiken wanneer dat mogelijk is voor een superieure beveiliging. |
| Service-SA'S | Gedeelde sleutel | De aanvraag is geweigerd voor alle Azure Storage services. |
| Account-SAS | Gedeelde sleutel | De aanvraag is geweigerd voor alle Azure Storage services. |

Zie voor meer informatie over gedeelde toegangs handtekeningen [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Houd rekening met de compatibiliteit met andere Azure-hulpprogram ma's en-services

Een aantal Azure-Services gebruiken verificatie met gedeelde sleutel om te communiceren met Azure Storage. Als u de verificatie van de gedeelde sleutel voor een opslag account niet toestaat, hebben deze services geen toegang meer tot gegevens in dat account en kunnen uw toepassingen nadelig worden beïnvloed.

Sommige hulpprogram ma's van Azure bieden de mogelijkheid om Azure AD-autorisatie te gebruiken voor toegang tot Azure Storage. De volgende tabel bevat enkele populaire Azure-hulpprogram ma's en-notities, of ze Azure AD kunnen gebruiken om aanvragen voor Azure Storage te autoriseren.

| Azure-hulp programma | Azure AD-autorisatie voor Azure Storage |
|-|-|
| Azure Portal | Ondersteund. Voor informatie over het autoriseren van uw Azure AD-account uit de Azure Portal, Zie [kiezen hoe u de toegang tot BLOB-gegevens in de Azure Portal wilt autoriseren](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Ondersteund voor Blob Storage. Zie [kiezen hoe u autorisatie referenties opgeeft](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) in de AzCopy-documentatie voor meer informatie over het autoriseren van AzCopy-bewerkingen. |
| Azure Storage Explorer | Alleen ondersteund voor Blob Storage en alleen Azure Data Lake Storage Gen2. Azure AD-toegang tot de wachtrij opslag wordt niet ondersteund. Zorg ervoor dat u de juiste Azure AD-Tenant selecteert. Zie [aan de slag met Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure) voor meer informatie. |
| Azure PowerShell | Ondersteund. Zie [Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot blobgegevens](../blobs/authorize-data-operations-powershell.md) of [Power shell-opdrachten uitvoeren met Azure AD-referenties voor toegang tot wachtrij gegevens](../queues/authorize-data-operations-powershell.md)voor meer informatie over het autoriseren van Power shell-opdrachten voor BLOB-of wachtrij bewerkingen met Azure AD. |
| Azure CLI | Ondersteund. Zie [Azure cli-opdrachten uitvoeren met Azure AD-referenties voor toegang tot BLOB-of wachtrij gegevens](../blobs/authorize-data-operations-cli.md)voor meer informatie over het autoriseren van Azure cli-opdrachten met Azure AD voor toegang tot Blob-en wachtrij gegevens. |
| Azure IoT Hub | Ondersteund. Zie [IOT hub-ondersteuning voor virtuele netwerken](../../iot-hub/virtual-network-support.md)voor meer informatie. |
| Azure Cloud Shell | Azure Cloud Shell is een geïntegreerde shell in de Azure Portal. Azure Cloud Shell hosts bestanden voor persistentie in een Azure-bestands share in een opslag account. Deze bestanden worden niet meer toegankelijk als de autorisatie van de gedeelde sleutel niet is toegestaan voor dat opslag account. Zie [verbinding maken met de opslag van uw Microsoft Azure-bestanden](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage)voor meer informatie. <br /><br /> Als u de opdrachten in Azure Cloud Shell wilt uitvoeren om opslag accounts te beheren waarvoor gedeelde-sleutel toegang niet is toegestaan, moet u eerst controleren of u de juiste machtigingen hebt gekregen voor deze accounts via Azure RBAC. Zie [Wat is Azure Role-based Access Control (Azure RBAC)?](../../role-based-access-control/overview.md)voor meer informatie. |

## <a name="about-the-preview"></a>Over de preview-versie

De preview voor het niet toestaan van gedeelde sleutel autorisatie is beschikbaar in de open bare Azure-Cloud. Het wordt ondersteund voor opslag accounts die alleen gebruikmaken van het Azure Resource Manager-implementatie model. Zie [typen opslag accounts](storage-account-overview.md#types-of-storage-accounts)voor meer informatie over welke opslag accounts gebruikmaken van het Azure Resource Manager-implementatie model.

Als u zich wilt registreren voor de preview-versie, raadpleegt u [Azure Storage gedeelde sleutel toegang met beperkte open bare preview toestaan](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u).

> [!IMPORTANT]
> Dit voor beeld is alleen bedoeld voor niet-productie gebruik.

De preview bevat de beperkingen die in de volgende secties worden beschreven.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Metrische gegevens en logboek registratie alle aanvragen met een SAS, ongeacht hoe ze zijn geautoriseerd

Azure-metrische gegevens en logboek registratie Azure Monitor geen onderscheid maken tussen verschillende typen hand tekeningen voor gedeelde toegang in de preview-versie. Het **SAS** -filter in azure Metrics Explorer en het veld **SAS** in Azure Storage logboek registratie in azure monitor beide rapport aanvragen die zijn geautoriseerd met elk type sa's. Verschillende typen Shared Access-hand tekeningen zijn echter anders geautoriseerd en gedragen zich anders als gedeelde sleutel toegang niet is toegestaan:

- Een SAS-token of een account-SAS-token is geautoriseerd met gedeelde sleutel en is niet toegestaan op een aanvraag voor Blob-opslag wanneer de eigenschap **AllowSharedKeyAccess** is ingesteld op **False**.
- Een SAS voor gebruikers overdracht is geautoriseerd met Azure AD en is toegestaan op een aanvraag voor Blob-opslag wanneer de eigenschap **AllowSharedKeyAccess** is ingesteld op **False**.

Wanneer u het verkeer naar uw opslag account evalueert, moet u er rekening mee houden dat metrische gegevens en logboeken zoals beschreven in [het type verificatie dat door client toepassingen wordt gebruikt](#detect-the-type-of-authorization-used-by-client-applications) , aanvragen kunnen bevatten die zijn gemaakt met een SAS voor gebruikers overdracht. Voor meer informatie over hoe Azure Storage reageert op een SAS wanneer de eigenschap **AllowSharedKeyAccess** is ingesteld op **Onwaar**, Zie [begrijpen hoe het niet toestaan van gedeelde sleutels van invloed is op SAS-tokens](#understand-how-disallowing-shared-key-affects-sas-tokens).

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot gegevens in Azure Storage autoriseren](storage-auth.md)
- [Toegang verlenen tot blobs en wacht rijen met behulp van Azure Active Directory](storage-auth-aad.md)
- [Autoriseren met gedeelde sleutel](/rest/api/storageservices/authorize-with-shared-key)