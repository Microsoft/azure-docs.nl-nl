---
title: Taken uitvoeren onder gebruikersaccounts
description: Meer informatie over de typen gebruikersaccounts en hoe u deze kunt configureren.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 02cad0bff9e76ec5db82c417f2439b12ef088045
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389278"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Taken uitvoeren onder gebruikersaccounts in Batch

> [!NOTE]
> De gebruikersaccounts die in dit artikel worden besproken, verschillen uit veiligheidsoverwegingen van gebruikersaccounts die worden gebruikt voor Remote Desktop Protocol (RDP) of Secure Shell (SSH).
>
> Als u verbinding wilt maken met een knooppunt met de configuratie van de virtuele Linux-machine via SSH, gaat u naar [Install and configure xrdp to use Extern bureaublad with Ubuntu (xrdp](../virtual-machines/linux/use-remote-desktop.md)installeren en configureren voor gebruik Extern bureaublad met Ubuntu). Zie Verbinding maken en aanmelden bij een virtuele [Azure-machine](../virtual-machines/windows/connect-logon.md)met Windows als u verbinding wilt maken met knooppunten waarop Windows wordt uitgevoerd via RDP.
>
> Zie Enable Verbinding met extern bureaublad for a Role in Azure Cloud Services om verbinding te maken met een knooppunt dat de cloudserviceconfiguratie via RDP [Azure Cloud Services.](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)

Een taak in Azure Batch wordt altijd uitgevoerd onder een gebruikersaccount. Taken worden standaard uitgevoerd onder standaardgebruikersaccounts, zonder beheerdersmachtigingen. Voor bepaalde scenario's kunt u het gebruikersaccount configureren waarmee u een taak wilt uitvoeren. In dit artikel worden de typen gebruikersaccounts besproken en wordt beschreven hoe u deze kunt configureren voor uw scenario.

## <a name="types-of-user-accounts"></a>Typen gebruikersaccounts

Azure Batch biedt twee soorten gebruikersaccounts voor het uitvoeren van taken:

- **Accounts voor automatische gebruikers.** Accounts voor automatische gebruikers zijn ingebouwde gebruikersaccounts die automatisch worden gemaakt door de Batch-service. Taken worden standaard uitgevoerd onder een automatisch gebruikersaccount. U kunt de specificatie van de automatische gebruiker voor een taak configureren om aan te geven onder welk account voor automatische gebruikers een taak moet worden uitgevoerd. Met de specificatie voor automatische gebruiker kunt u het niveau van bevoegdheden en het bereik opgeven van het account voor automatische gebruikers waarmee de taak wordt uitgevoerd.

- **Een benoemd gebruikersaccount.** U kunt een of meer benoemde gebruikersaccounts voor een pool opgeven wanneer u de pool maakt. Elk gebruikersaccount wordt gemaakt op elk knooppunt van de pool. Naast de accountnaam geeft u het wachtwoord voor het gebruikersaccount, het niveau van bevoegdheden en, voor Linux-pools, de persoonlijke SSH-sleutel op. Wanneer u een taak toevoegt, kunt u het benoemde gebruikersaccount opgeven waaronder deze taak moet worden uitgevoerd.

> [!IMPORTANT]
> In versie 2017-01-01.4.0 van de Batch-service is een wijziging geïntroduceerd die een grote wijziging in de weg staat. Hiervoor moet u uw code bijwerken om die versie of hoger aan te roepen. Zie [Uw code bijwerken naar de nieuwste Batch-clientbibliotheek](#update-your-code-to-the-latest-batch-client-library) voor snelle richtlijnen voor het bijwerken van uw Batch-code vanuit een oudere versie.

## <a name="user-account-access-to-files-and-directories"></a>Gebruikersaccounttoegang tot bestanden en mappen

Zowel een automatisch gebruikersaccount als een benoemd gebruikersaccount hebben lees-/schrijftoegang tot de werkmap, gedeelde map en takenmap voor meerdere exemplaren van de taak. Beide typen accounts hebben leestoegang tot de opstart- en jobvoorbereidingsdirecties.

Als een taak wordt uitgevoerd onder hetzelfde account dat is gebruikt voor het uitvoeren van een begintaak, heeft de taak lees-/schrijftoegang tot de map van de begintaak. En als een taak wordt uitgevoerd onder hetzelfde account dat is gebruikt voor het uitvoeren van een jobvoorbereidingstaak, heeft de taak lees-/schrijftoegang tot de map van de jobvoorbereidingstaak. Als een taak wordt uitgevoerd onder een ander account dan de begintaak of jobvoorbereidingstaak, heeft de taak alleen leestoegang tot de respectieve map.

Zie Bestanden en mappen voor meer informatie over het openen van bestanden [en mappen vanuit een taak.](files-and-directories.md)

## <a name="elevated-access-for-tasks"></a>Verhoogde toegang voor taken

Het niveau van verhoogde bevoegdheden van het gebruikersaccount geeft aan of een taak wordt uitgevoerd met verhoogde toegang. Zowel een automatisch gebruikersaccount als een benoemd gebruikersaccount kan worden uitgevoerd met verhoogde toegang. De twee opties voor het niveau van de verhoging zijn:

- **NonAdmin:** De taak wordt uitgevoerd als een standaardgebruiker zonder verhoogde toegang. Het standaardniveau voor verhoging van bevoegdheden voor een Batch-gebruikersaccount is **altijd NonAdmin**.
- **Beheerder:** De taak wordt uitgevoerd als een gebruiker met verhoogde toegang en werkt met volledige beheerdersmachtigingen.

## <a name="auto-user-accounts"></a>Accounts voor automatische gebruikers

Taken worden standaard uitgevoerd in Batch onder een automatisch gebruikersaccount, als standaardgebruiker zonder verhoogde toegang en met poolbereik. Poolbereik betekent dat de taak wordt uitgevoerd onder een automatisch gebruikersaccount dat beschikbaar is voor elke taak in de pool. Zie Een taak uitvoeren als een automatische gebruiker met poolbereik voor meer informatie [over het bereik van de pool.](#run-a-task-as-an-auto-user-with-pool-scope)

Het alternatief voor poolbereik is taakbereik. Wanneer de specificatie van de automatische gebruiker is geconfigureerd voor het taakbereik, maakt de Batch-service alleen een automatisch gebruikersaccount voor die taak.

Er zijn vier mogelijke configuraties voor de specificatie van de automatische gebruiker, die elk overeenkomen met een uniek account voor automatische gebruikers:

- Niet-beheerderstoegang met taakbereik
- Beheerderstoegang (met verhoogde rechten) met taakbereik
- Niet-beheerderstoegang met poolbereik
- Beheerderstoegang met poolbereik

> [!IMPORTANT]
> Taken die worden uitgevoerd onder taakbereik hebben geen feitelijke toegang tot andere taken op een knooppunt. Een kwaadwillende gebruiker met toegang tot het account kan deze beperking echter omdraaien door een taak in te dienen die wordt uitgevoerd met beheerdersbevoegdheden en toegang heeft tot andere taakdirecties. Een kwaadwillende gebruiker kan ook RDP of SSH gebruiken om verbinding te maken met een knooppunt. Het is belangrijk om de toegang tot uw Batch-accountsleutels te beveiligen om een dergelijk scenario te voorkomen. Als u vermoedt dat uw account is aangetast, moet u uw sleutels opnieuw maken.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Een taak uitvoeren als een automatische gebruiker met verhoogde toegang

U kunt de specificatie van de automatische gebruiker voor beheerdersbevoegdheden configureren wanneer u een taak met verhoogde toegang moet uitvoeren. Een begintaak heeft bijvoorbeeld verhoogde toegang nodig om software op het knooppunt te installeren.

> [!NOTE]
> Gebruik alleen toegang met verhoogde toegang als dat nodig is. Aanbevolen procedures raden u aan om de minimale bevoegdheid te verlenen die nodig is om het gewenste resultaat te bereiken. Als een begintaak bijvoorbeeld software installeert voor de huidige gebruiker, in plaats van voor alle gebruikers, kunt u mogelijk voorkomen dat u verhoogde toegang tot taken verleent. U kunt de specificatie voor automatische gebruikers voor poolbereik en niet-beheerderstoegang configureren voor alle taken die moeten worden uitgevoerd onder hetzelfde account, met inbegrip van de begintaak.

De volgende codefragmenten laten zien hoe u de specificatie van de automatische gebruiker configureert. In de voorbeelden wordt het niveau van de verhoging `Admin` ingesteld op en het bereik op `Task` .

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```

#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Een taak uitvoeren als een automatische gebruiker met poolbereik

Wanneer een knooppunt is ingericht, worden op elk knooppunt in de pool twee automatische gebruikersaccounts voor de hele groep gemaakt, één met verhoogde toegang en één zonder verhoogde toegang. Door het bereik van de automatische gebruiker in te stellen op poolbereik voor een bepaalde taak, wordt de taak uitgevoerd onder een van deze twee poolbrede accounts voor automatische gebruikers.

Wanneer u het poolbereik voor de automatische gebruiker opgeeft, worden alle taken die worden uitgevoerd met beheerderstoegang uitgevoerd onder hetzelfde poolbrede automatische gebruikersaccount. Op dezelfde manier worden taken die worden uitgevoerd zonder beheerdersmachtigingen ook uitgevoerd onder één poolbreed automatisch gebruikersaccount.

> [!NOTE]
> De twee poolbrede accounts voor automatische gebruikers zijn afzonderlijke accounts. Taken die worden uitgevoerd onder het groepbrede beheerdersaccount kunnen geen gegevens delen met taken die worden uitgevoerd onder het standaardaccount en vice versa.

Het voordeel van het uitvoeren onder hetzelfde account voor automatische gebruikers is dat taken gegevens kunnen delen met andere taken die op hetzelfde knooppunt worden uitgevoerd.

Het delen van geheimen tussen taken is één scenario waarin het uitvoeren van taken onder een van de twee poolbrede automatische gebruikersaccounts nuttig is. Stel bijvoorbeeld dat een begintaak een geheim moet inrichten op het knooppunt dat andere taken kunnen gebruiken. U kunt de Windows Data Protection API (DPAPI) gebruiken, maar hiervoor zijn beheerdersbevoegdheden vereist. In plaats daarvan kunt u het geheim op gebruikersniveau beveiligen. Taken die worden uitgevoerd onder hetzelfde gebruikersaccount hebben toegang tot het geheim zonder verhoogde toegang.

Een ander scenario waarin u taken wilt uitvoeren onder een automatisch gebruikersaccount met poolbereik is een Message Passing Interface (MPI)-bestands share. Een MPI-bestands share is handig wanneer de knooppunten in de MPI-taak aan dezelfde bestandsgegevens moeten werken. Het hoofdknooppunt maakt een bestands share die de onderliggende knooppunten kunnen openen als ze worden uitgevoerd onder hetzelfde automatische gebruikersaccount.

Met het volgende codefragment stelt u het bereik van de automatische gebruiker in op poolbereik voor een taak in Batch .NET. Het niveau van de verhoging wordt weggelaten, zodat de taak wordt uitgevoerd onder het standaardaccount voor de hele groep voor automatische gebruikers.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Benoemde gebruikersaccounts

U kunt benoemde gebruikersaccounts definiëren wanneer u een groep maakt. Een benoemd gebruikersaccount heeft een naam en wachtwoord dat u op geeft. U kunt het verhogingsniveau voor een benoemd gebruikersaccount opgeven. Voor Linux-knooppunten kunt u ook een persoonlijke SSH-sleutel verstrekken.

Een benoemd gebruikersaccount bestaat op alle knooppunten in de pool en is beschikbaar voor alle taken die op deze knooppunten worden uitgevoerd. U kunt een bepaald aantal benoemde gebruikers voor een pool definiëren. Wanneer u een taak of taakverzameling toevoegt, kunt u opgeven dat de taak wordt uitgevoerd onder een van de benoemde gebruikersaccounts die zijn gedefinieerd in de groep.

Een benoemd gebruikersaccount is handig als u alle taken in een job wilt uitvoeren onder hetzelfde gebruikersaccount, maar deze wilt isoleren van taken die tegelijkertijd in andere taken worden uitgevoerd. U kunt bijvoorbeeld een benoemde gebruiker maken voor elke job en de taken van elke job uitvoeren onder dat benoemde gebruikersaccount. Elke job kan vervolgens een geheim delen met zijn eigen taken, maar niet met taken die worden uitgevoerd in andere jobs.

U kunt ook een benoemd gebruikersaccount gebruiken om een taak uit te voeren die machtigingen in stelt voor externe resources, zoals bestands shares. Met een benoemd gebruikersaccount kunt u de gebruikersidentiteit bepalen en die gebruikersidentiteit gebruiken om machtigingen in te stellen.  

Met benoemde gebruikersaccounts wordt SSH met een wachtwoord minder tussen Linux-knooppunten ingeschakeld. U kunt een benoemd gebruikersaccount gebruiken met Linux-knooppunten die taken met meerdere instanties moeten uitvoeren. Elk knooppunt in de pool kan taken uitvoeren onder een gebruikersaccount dat voor de hele pool is gedefinieerd. Zie Use multi instance tasks to run MPI applications (Taken met meerdere exemplaren gebruiken om MPI-toepassingen uit te voeren) voor meer informatie over taken [ \- met meerdere exemplaren.](batch-mpi.md)

### <a name="create-named-user-accounts"></a>Benoemde gebruikersaccounts maken

Als u benoemde gebruikersaccounts in Batch wilt maken, voegt u een verzameling gebruikersaccounts toe aan de groep. De volgende codefragmenten laten zien hoe u benoemde gebruikersaccounts maakt in .NET, Java en Python. Deze codefragmenten laten zien hoe u accounts met zowel beheerders- als niet-beheerdersaccounts maakt in een groep. In de voorbeelden worden pools gemaakt met behulp van de cloudserviceconfiguratie, maar u gebruikt dezelfde benadering bij het maken van een Windows- of Linux-pool met behulp van de configuratie van de virtuele machine.

#### <a name="batch-net-example-windows"></a>Batch .NET-voorbeeld (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
    nodeAgentSkuId: "batch.node.windows amd64");

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET-voorbeeld (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "Standard_A1",
    virtualMachineConfiguration: virtualMachineConfiguration);
// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-java-example"></a>Batch Java-voorbeeld

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withVirtualMachineConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Batch Python-voorbeeld

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Een taak uitvoeren onder een benoemd gebruikersaccount met verhoogde toegang

Als u een taak wilt uitvoeren als een gebruiker met verhoogde bevoegdheden, stelt u de eigenschap **UserIdentity** van de taak in op een benoemd gebruikersaccount dat is gemaakt met de eigenschap **ElevationLevel** ingesteld op `Admin` .

Dit codefragment geeft aan dat de taak moet worden uitgevoerd onder een benoemd gebruikersaccount. Dit benoemde gebruikersaccount is gedefinieerd in de pool toen de pool werd gemaakt. In dit geval is het benoemde gebruikersaccount gemaakt met beheerdersmachtigingen:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uw code bijwerken naar de nieuwste Batch-clientbibliotheek

De Batch-serviceversie 2017-01-01.4.0 heeft een wijziging geïntroduceerd die een grote wijziging doorbreekt, en vervangt de **eigenschap runElevated** die beschikbaar is in eerdere versies door de eigenschap **userIdentity.** De volgende tabellen bieden een eenvoudige toewijzing die u kunt gebruiken om uw code van eerdere versies van de clientbibliotheken bij te werken.

### <a name="batch-net"></a>Batch .NET

| Als uw code gebruikmaakt van...                  | Werk deze bij naar...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` niet opgegeven | Er is geen update vereist                                                                                               |

### <a name="batch-java"></a>Batch Java

| Als uw code gebruikmaakt van...                      | Werk deze bij naar...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` niet opgegeven | Er is geen update vereist                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Als uw code gebruikmaakt van...                      | Werk deze bij naar...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Waar <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Waar <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` niet opgegeven | Er is geen update vereist                                                                                                                                  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie [over bestanden en mappen](files-and-directories.md) in Azure Batch.
