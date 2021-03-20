---
title: Taken uitvoeren onder gebruikers accounts
description: Meer informatie over de typen gebruikers accounts en hoe u deze kunt configureren.
ms.topic: how-to
ms.date: 08/20/2020
ms.custom: seodec18
ms.openlocfilehash: cce374e7d7ffb513bed882b048ea54bcbad81b0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88719356"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Taken uitvoeren onder gebruikers accounts in batch

> [!NOTE]
> De gebruikers accounts die in dit artikel worden besproken, verschillen van gebruikers accounts die worden gebruikt voor Remote Desktop Protocol (RDP) of Secure Shell (SSH) om veiligheids redenen.
>
> Zie [extern bureaublad gebruiken voor een Linux-vm in azure](../virtual-machines/linux/use-remote-desktop.md)om verbinding te maken met een knoop punt met de configuratie van de virtuele Linux-machine via SSH. Zie [verbinding maken met een virtuele machine van Windows Server](../virtual-machines/windows/connect-logon.md)om verbinding te maken met knoop punten met Windows via RDP.<br /><br />
> Zie [verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md)om verbinding te maken met een knoop punt met de Cloud service configuratie via RDP.

Een taak in Azure Batch altijd onder een gebruikers account wordt uitgevoerd. Taken worden standaard uitgevoerd onder standaard gebruikers accounts, zonder beheerders machtigingen. Voor bepaalde scenario's kunt u het gebruikers account waaronder u een taak wilt uitvoeren, configureren. In dit artikel worden de typen gebruikers accounts beschreven en wordt uitgelegd hoe u deze kunt configureren voor uw scenario.

## <a name="types-of-user-accounts"></a>Typen gebruikers accounts

Azure Batch biedt twee typen gebruikers accounts voor het uitvoeren van taken:

- **Automatische gebruikers accounts.** Automatische gebruikers accounts zijn ingebouwde gebruikers accounts die automatisch door de batch-service worden gemaakt. Taken worden standaard uitgevoerd onder een automatische gebruikers account. U kunt de automatische gebruikers specificatie voor een taak configureren om aan te geven onder welke automatische gebruikers account een taak moet worden uitgevoerd. Met de specificatie automatische gebruiker kunt u het niveau van de verhoging en het bereik opgeven van het automatische gebruikers account waarmee de taak wordt uitgevoerd.

- **Een benoemde gebruikers account.** U kunt een of meer benoemde gebruikers accounts voor een pool opgeven wanneer u de groep maakt. Elk gebruikers account wordt gemaakt op elk knoop punt van de groep. Naast de account naam geeft u het wacht woord voor het gebruikers account, het verhogings niveau en, voor Linux-Pools, de persoonlijke SSH-sleutel op. Wanneer u een taak toevoegt, kunt u het benoemde gebruikers account opgeven waaronder die taak moet worden uitgevoerd.

> [!IMPORTANT]
> De batch-Service versie 2017 -01-01.4.0 introduceert een belang rijke wijziging die vereist dat u uw code bijwerkt om die versie aan te roepen. Als u code migreert vanuit een oudere versie van batch, moet u er rekening mee houden dat de eigenschap **runElevated** niet meer wordt ondersteund in de client bibliotheken van rest API of batch. Gebruik de nieuwe eigenschap **userIdentity** van een taak om het verhogings niveau op te geven. Zie [uw code bijwerken in de nieuwste batch-client bibliotheek](#update-your-code-to-the-latest-batch-client-library) voor snelle richt lijnen voor het bijwerken van uw batch-code als u een van de client bibliotheken gebruikt.

## <a name="user-account-access-to-files-and-directories"></a>Gebruikers account toegang tot bestanden en mappen

Zowel een automatische gebruikers account als een benoemd gebruikers account hebben lees-/schrijftoegang tot de werkmap van de werk directory, gedeelde map en taken lijst met meerdere instanties. Beide typen accounts hebben lees toegang tot de mappen voor opstarten en het voorbereiden van taken.

Als een taak wordt uitgevoerd onder hetzelfde account dat is gebruikt voor het uitvoeren van een begin taak, heeft de taak lees-/schrijftoegang tot de map voor het starten van de taak. Evenzo geldt dat als een taak wordt uitgevoerd onder hetzelfde account dat is gebruikt voor het uitvoeren van een taak voorbereidings taak, de taak lees-/schrijftoegang heeft tot de map taak voorbereidings taak. Als een taak wordt uitgevoerd onder een ander account dan de taak begin taak of taak voorbereiding, heeft de taak alleen lees toegang tot de betreffende map.

Zie [bestanden en mappen](files-and-directories.md)voor meer informatie over het openen van bestanden en mappen van een taak.

## <a name="elevated-access-for-tasks"></a>Verhoogde toegang voor taken

Het niveau van de verhoging van het gebruikers account geeft aan of een taak wordt uitgevoerd met verhoogde toegang. Zowel een account voor automatische gebruikers als een benoemd gebruikers account kan worden uitgevoerd met verhoogde toegang. De twee opties voor verhogings niveau zijn:

- Niet- **beheerder:** De taak wordt uitgevoerd als een standaard gebruiker zonder verhoogde toegang. Het standaard niveau voor uitbrei ding van bevoegdheden voor een batch-gebruikers account is altijd niet- **beheerder**.
- **Beheerder:** De taak wordt uitgevoerd als een gebruiker met verhoogde toegang en werkt met volledige beheerders machtigingen.

## <a name="auto-user-accounts"></a>Automatische gebruikers accounts

Taken worden standaard uitgevoerd in batch onder een automatische gebruikers account, als standaard gebruiker zonder verhoogde toegang en met groeps bereik. Groeps bereik betekent dat de taak wordt uitgevoerd onder een automatische gebruikers account dat beschikbaar is voor elke taak in de groep. Zie [een taak uitvoeren als een automatische gebruiker met een groeps bereik](#run-a-task-as-an-auto-user-with-pool-scope)voor meer informatie over het groeps bereik.

Het alternatief voor het bereik van de groep is het taak bereik. Wanneer de specificatie van de automatische gebruiker is geconfigureerd voor het taak bereik, maakt de batch-service alleen een automatische-gebruikers account voor die taak.

Er zijn vier mogelijke configuraties voor de specificatie van automatische gebruikers, elk met een unieke automatische gebruikers account:

- Niet-beheerders toegang met taak bereik
- Beheerder (verhoogde toegang) met taak bereik
- Niet-beheerders toegang met groeps bereik
- Beheerders toegang met groeps bereik

> [!IMPORTANT]
> Taken die worden uitgevoerd onder het taak bereik hebben geen feitelijke toegang tot andere taken op een knoop punt. Een kwaadwillende gebruiker met toegang tot het account kan deze beperking echter omzeilen door een taak te verzenden die wordt uitgevoerd met beheerders bevoegdheden en andere taak mappen kan openen. Een kwaadwillende gebruiker kan ook RDP of SSH gebruiken om verbinding te maken met een knoop punt. Het is belang rijk dat u de toegang tot uw batch-account sleutels beveiligt om een dergelijk scenario te voor komen. Als u vermoedt dat uw account is aangetast, moet u de sleutels opnieuw genereren.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Een taak uitvoeren als automatische gebruiker met verhoogde toegang

U kunt de automatische gebruikers specificatie voor beheerders bevoegdheden configureren wanneer u een taak met verhoogde toegang moet uitvoeren. Een begin taak kan bijvoorbeeld verhoogde toegang nodig hebben om software te installeren op het knoop punt.

> [!NOTE]
> Gebruik alleen verhoogde toegang wanneer dit nodig is. Aanbevolen procedures worden aanbevolen de minimale bevoegdheid toe te kennen die nodig is om het gewenste resultaat te krijgen. Als een begin taak bijvoorbeeld software installeert voor de huidige gebruiker, in plaats van voor alle gebruikers, kunt u voor komen dat er verhoogde toegang tot taken wordt verleend. U kunt de automatische gebruikers specificatie voor groeps bereik en niet-beheerders toegang configureren voor alle taken die moeten worden uitgevoerd onder hetzelfde account, met inbegrip van de start taak.

De volgende code fragmenten laten zien hoe de automatische gebruikers specificatie moet worden geconfigureerd. In de voor beelden is het verhogings niveau ingesteld op `Admin` en het bereik `Task` .

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

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Een taak uitvoeren als een automatische gebruiker met een groeps bereik

Wanneer een knoop punt wordt ingericht, worden er twee accounts voor automatische gebruikers gemaakt op elk knoop punt in de pool, een met verhoogde toegang en één zonder verhoogde toegang. Als u het bereik van de automatische gebruiker instelt op het groeps bereik voor een bepaalde taak, wordt de taak uitgevoerd onder een van deze twee groepen accounts voor automatische gebruikers.

Wanneer u het groeps bereik voor de automatische gebruiker opgeeft, worden alle taken die worden uitgevoerd met beheerders toegang uitgevoerd onder hetzelfde groeps-brede automatische gebruikers account. Taken die worden uitgevoerd zonder beheerders machtigingen, worden ook uitgevoerd onder één account voor automatische gebruikers van de hele groep.

> [!NOTE]
> De twee groeps accounts voor automatische gebruikers zijn afzonderlijke accounts. Taken die worden uitgevoerd onder het beheerders account voor de hele groep, kunnen geen gegevens delen met taken die worden uitgevoerd onder het standaard account en vice versa.

Het voor deel van het uitvoeren van hetzelfde automatische gebruikers account is dat taken gegevens kunnen delen met andere taken die op hetzelfde knoop punt worden uitgevoerd.

Het delen van geheimen tussen taken is een scenario waarbij het uitvoeren van taken onder een van de twee accounts voor automatische gebruikers handig is. Stel bijvoorbeeld dat een begin taak een geheim moet inrichten op het knoop punt dat door andere taken kan worden gebruikt. U kunt de Windows Data Protection API (DPAPI) gebruiken, maar hiervoor zijn beheerders bevoegdheden vereist. In plaats daarvan kunt u het geheim op gebruikers niveau beveiligen. Taken die worden uitgevoerd onder hetzelfde gebruikers account hebben toegang tot het geheim zonder verhoogde toegang.

Een ander scenario waarin u taken wilt uitvoeren onder een automatische gebruikers account met groeps bereik is een MPI-bestands share (Message Passing Interface). Een MPI-bestands share is handig wanneer de knoop punten in de MPI-taak moeten werken aan dezelfde bestands gegevens. Het hoofd knooppunt maakt een bestands share waartoe de onderliggende knoop punten toegang hebben als ze worden uitgevoerd onder hetzelfde automatische gebruikers account.

Met het volgende code fragment wordt het bereik van de automatische gebruiker ingesteld op het groeps bereik voor een taak in batch .NET. Het verhogings niveau wordt wegge laten, zodat de taak wordt uitgevoerd onder het standaard groeps account voor automatische gebruikers.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Benoemde gebruikers accounts

U kunt benoemde gebruikers accounts definiëren wanneer u een groep maakt. Een benoemde gebruikers account heeft een naam en een wacht woord dat u opgeeft. U kunt het verhogings niveau voor een benoemde gebruikers account opgeven. Voor Linux-knoop punten kunt u ook een persoonlijke SSH-sleutel opgeven.

Er bestaat een benoemd gebruikers account op alle knoop punten in de groep en is beschikbaar voor alle taken die op deze knoop punten worden uitgevoerd. U kunt een wille keurig aantal benoemde gebruikers voor een groep definiëren. Wanneer u een taak of taak verzameling toevoegt, kunt u opgeven dat de taak wordt uitgevoerd onder een van de benoemde gebruikers accounts die zijn gedefinieerd in de groep.

Een gebruikers account met een naam is handig als u alle taken in een taak wilt uitvoeren onder hetzelfde gebruikers account, maar deze wilt isoleren van taken die worden uitgevoerd in andere taken op hetzelfde moment. U kunt bijvoorbeeld een benoemde gebruiker voor elke taak maken en de taken van elke taak uitvoeren onder die benoemde gebruikers account. Elke taak kan vervolgens een geheim delen met zijn eigen taken, maar niet met taken die worden uitgevoerd in andere taken.

U kunt ook een benoemd gebruikers account gebruiken om een taak uit te voeren waarmee machtigingen worden ingesteld voor externe resources, zoals bestands shares. Met een benoemde gebruikers account beheert u de gebruikers identiteit en kunt u deze gebruikers-id gebruiken om machtigingen in te stellen.  

Met benoemde gebruikers accounts schakelt u wacht woord-minder SSH tussen Linux-knoop punten in. U kunt een benoemd gebruikers account gebruiken met Linux-knoop punten die taken met meerdere instanties moeten uitvoeren. Elk knoop punt in de pool kan taken uitvoeren onder een gebruikers account dat is gedefinieerd in de hele groep. Zie voor meer informatie over taken met meerdere instanties [multi instance- \- taken gebruiken voor het uitvoeren van MPI-toepassingen](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Benoemde gebruikers accounts maken

Als u benoemde gebruikers accounts in batch wilt maken, voegt u een verzameling gebruikers accounts toe aan de groep. De volgende code fragmenten laten zien hoe u benoemde gebruikers accounts maakt in .NET, Java en python. Deze code fragmenten laten zien hoe u accounts voor zowel beheerders als niet-Administrators kunt maken in een groep. De voor beelden maken Pools met behulp van de Cloud service configuratie, maar u gebruikt dezelfde methode wanneer u een Windows-of Linux-groep maakt met behulp van de configuratie van de virtuele machine.

#### <a name="batch-net-example-windows"></a>Batch .NET-voor beeld (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Batch .NET-voor beeld (Linux)

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

#### <a name="batch-java-example"></a>Voor beeld van batch-java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Voor beeld batch python

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

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Een taak uitvoeren onder een benoemd gebruikers account met verhoogde toegang

Als u een taak wilt uitvoeren als een verhoogde gebruiker, stelt u de eigenschap **UserIdentity** van de taak in op een benoemd gebruikers account dat is gemaakt met de eigenschap **ElevationLevel** ingesteld op `Admin` .

Dit code fragment geeft aan dat de taak moet worden uitgevoerd onder een benoemd gebruikers account. Dit benoemde gebruikers account is gedefinieerd in de groep toen de groep werd gemaakt. In dit geval is het benoemde gebruikers account met beheerders machtigingen gemaakt:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Uw code bijwerken naar de laatste batch-client bibliotheek

De batch-Service versie 2017 -01-01.4.0 introduceert een belang rijke wijziging, waarbij de eigenschap **runElevated** die in eerdere versies beschikbaar is, wordt vervangen door de eigenschap **userIdentity** . De volgende tabellen bevatten een eenvoudige toewijzing die u kunt gebruiken om uw code bij te werken uit eerdere versies van de client bibliotheken.

### <a name="batch-net"></a>Batch .NET

| Als uw code gebruikmaakt van...                  | Bijwerken naar....                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` niet opgegeven | Er is geen update vereist                                                                                               |

### <a name="batch-java"></a>Batch Java

| Als uw code gebruikmaakt van...                      | Bijwerken naar....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` niet opgegeven | Er is geen update vereist                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Als uw code gebruikmaakt van...                      | Bijwerken naar....                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, waarbij <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, waarbij <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` niet opgegeven | Er is geen update vereist                                                                                                                                  |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over [bestanden en mappen](files-and-directories.md) in azure batch.
