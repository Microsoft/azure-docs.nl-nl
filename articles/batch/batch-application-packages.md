---
title: Toepassingspakketten implementeren op rekenknooppunten
description: Gebruik de functie voor toepassingspakketten van Azure Batch eenvoudig meerdere toepassingen en versies te beheren voor installatie op Batch-rekenknooppunten.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 9c4b40f0e99475fc0b19ec94a14f67af131e5f59
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389380"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Toepassingen implementeren op rekenknooppunten met Batch-toepassingspakketten

Toepassingspakketten kunnen de code in uw Azure Batch vereenvoudigen en het gemakkelijker maken om de toepassingen te beheren die door uw taken worden uitgevoerd. Met toepassingspakketten kunt u meerdere versies van toepassingen uploaden en beheren die door uw taken worden uitgevoerd, inclusief de ondersteunende bestanden. U kunt vervolgens automatisch een of meer van deze toepassingen implementeren op de rekenknooppunten in uw pool.

De API's voor het maken en beheren van toepassingspakketten maken deel uit van de [Batch Management .NET-bibliotheek.](/dotnet/api/overview/azure/batch/management) De API's voor het installeren van toepassingspakketten op een reken knooppunt maken deel uit van de [Batch .NET-bibliotheek.](/dotnet/api/overview/azure/batch/client) Vergelijkbare functies zijn beschikbaar in de Beschikbare Batch-API's voor andere talen.

In dit artikel wordt uitgelegd hoe u toepassingspakketten uploadt en beheert in Azure Portal. U ziet ook hoe u ze installeert op de rekenknooppunten van een pool met de [Batch .NET-bibliotheek.](/dotnet/api/overview/azure/batch/client)

## <a name="application-package-requirements"></a>Vereisten voor toepassingspakketten

Als u toepassingspakketten wilt gebruiken, moet u [een Azure Storage account koppelen](#link-a-storage-account) aan uw Batch-account.

Er gelden beperkingen voor het aantal toepassingen en toepassingspakketten binnen een Batch-account en voor de maximale grootte van het toepassingspakket. Zie Quota en limieten voor de [Azure Batch service voor meer informatie.](batch-quota-limit.md)

> [!NOTE]
> Batchgroepen die vóór 5 juli 2017 zijn gemaakt, bieden geen ondersteuning voor toepassingspakketten (tenzij ze zijn gemaakt na 10 maart 2016 met behulp van Cloud Services Configuration). De functie voor toepassingspakketten die hier wordt beschreven, vereert de Functie Batch-apps die beschikbaar is in eerdere versies van de service.

## <a name="understand-applications-and-application-packages"></a>Toepassingen en toepassingspakketten begrijpen

Binnen Azure Batch verwijst  een toepassing naar een set met versies van binaire bestanden die automatisch kunnen worden gedownload naar de rekenknooppunten in uw pool. Een toepassing bevat een of meer *toepassingspakketten* die verschillende versies van de toepassing vertegenwoordigen.

Elk *toepassingspakket* is een ZIP-bestand dat de binaire bestanden van de toepassing en alle ondersteunende bestanden bevat. Alleen de ZIP-indeling wordt ondersteund.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram met een weergave op hoog niveau van toepassingen en toepassingspakketten.":::

U kunt toepassingspakketten opgeven op groep- of taakniveau.

- **Groeptoepassingspakketten** worden geïmplementeerd op elk knooppunt in de pool. Toepassingen worden geïmplementeerd wanneer een knooppunt lid wordt van een pool en wanneer het knooppunt opnieuw wordt opgestart of opnieuw wordt gemaakt.
  
    Pooltoepassingspakketten zijn geschikt wanneer alle knooppunten in een pool de taken van een job uitvoeren. U kunt een of meer toepassingspakketten opgeven die u wilt implementeren wanneer u een groep maakt. U kunt ook pakketten van een bestaande pool toevoegen of bijwerken. Als u een nieuw pakket wilt installeren in een bestaande pool, moet u de knooppunten ervan opnieuw opstarten.

- **Toepassingspakketten voor taken** worden alleen geïmplementeerd op een rekenpunt dat is gepland om een taak uit te voeren, net voordat de opdrachtregel van de taak wordt uitgevoerd. Als het opgegeven toepassingspakket en de versie al op het knooppunt zijn, wordt het niet opnieuw geïmplementeerd en wordt het bestaande pakket gebruikt.
  
    Toepassingspakketten voor taken zijn handig in omgevingen met gedeelde pool, waarbij verschillende taken worden uitgevoerd op één pool en de groep niet wordt verwijderd wanneer een taak is voltooid. Als uw job minder taken dan knooppunten in de pool heeft, kunnen taaktoepassingspakketten de gegevensoverdracht minimaliseren, omdat uw toepassing alleen wordt geïmplementeerd op de knooppunten die taken uitvoeren.
  
    Andere scenario's die kunnen profiteren van toepassingspakketten voor taken zijn taken die een grote toepassing uitvoeren, maar voor slechts enkele taken. Taaktoepassingen kunnen bijvoorbeeld nuttig zijn voor een zware voorverwerkingsfase of een samenvoegingstaak.

Met toepassingspakketten hoeft de begintaak van uw pool geen lange lijst met afzonderlijke resourcebestanden op te geven die op de knooppunten moeten worden geïnstalleerd. U hoeft niet handmatig meerdere versies van uw toepassingsbestanden in uw Azure Storage of op uw knooppunten te beheren. En u hoeft zich geen zorgen te maken over het genereren van [SAS-URL's](../storage/common/storage-sas-overview.md) om toegang te bieden tot de bestanden in uw opslagaccount. Batch werkt op de achtergrond met Azure Storage om toepassingspakketten op te slaan en te implementeren op rekenknooppunten.

> [!NOTE]
> De totale grootte van een begintaak moet kleiner zijn dan of gelijk zijn aan 32.768 tekens, inclusief bronbestanden en omgevingsvariabelen. Als uw begintaak deze limiet overschrijdt, is het gebruik van toepassingspakketten een andere optie. U kunt ook een ZIP-bestand maken dat uw resourcebestanden bevat, het uploaden als een blob naar Azure Storage en het bestand vervolgens uit de opdrachtregel van uw begintaak uit te halen.

## <a name="upload-and-manage-applications"></a>Toepassingen uploaden en beheren

U kunt de [Azure Portal](https://portal.azure.com) of de Batch Management-API's gebruiken om de toepassingspakketten in uw Batch-account te beheren. In de volgende secties wordt uitgelegd hoe u een opslagaccount koppelt en hoe u toepassingen en toepassingspakketten toevoegt en beheert in de Azure Portal.

> [!NOTE]
> Hoewel u toepassingswaarden kunt definiëren in [ de resourceMicrosoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) van een [ARM-sjabloon,](quick-create-template.md)is het momenteel niet mogelijk om een ARM-sjabloon te gebruiken om toepassingspakketten te uploaden voor gebruik in uw Batch-account. U moet deze uploaden naar uw gekoppelde opslagaccount, zoals hieronder [wordt beschreven.](#add-a-new-application)

### <a name="link-a-storage-account"></a>Een opslagaccount koppelen

Als u toepassingspakketten wilt gebruiken, moet u een [Azure Storage-account koppelen](accounts.md#azure-storage-accounts) aan uw Batch-account. De Batch-service gebruikt het bijbehorende opslagaccount om uw toepassingspakketten op te slaan. U wordt aangeraden een opslagaccount te maken dat speciaal is bedoeld voor gebruik met uw Batch-account.

Als u nog geen opslagaccount hebt geconfigureerd, wordt Azure Portal de eerste keer dat u Toepassingen **in** uw Batch-account selecteert, een waarschuwing weergegeven. Als u een opslagaccount wilt koppelen aan uw Batch-account, selecteert u **Opslagaccount** in het venster **Waarschuwing** en selecteert u vervolgens **opnieuw Opslagaccount.**

Nadat u de twee accounts hebt gekoppeld, kan Batch de pakketten die zijn opgeslagen in het gekoppelde opslagaccount, automatisch implementeren op uw rekenknooppunten.

> [!IMPORTANT]
> U kunt geen toepassingspakketten gebruiken met Azure Storage-accounts die zijn geconfigureerd met [firewallregels](../storage/common/storage-network-security.md)of als Hiërarchische **naamruimte** is ingesteld **op Ingeschakeld.**

De Batch-service gebruikt Azure Storage om uw toepassingspakketten op te slaan als blok-blobs. De [blok-blobgegevens](https://azure.microsoft.com/pricing/details/storage/) worden op de gebruikelijke manier in rekening gebracht en de grootte van elk pakket mag niet groter zijn dan de maximale blok-blobgrootte. Zie schaalbaarheids- Azure Storage prestatiedoelen voor opslagaccounts [voor meer informatie.](../storage/blobs/scalability-targets.md) Om de kosten te minimaliseren, moet u rekening houden met de grootte en het aantal toepassingspakketten en regelmatig afgeschafte pakketten verwijderen.

### <a name="view-current-applications"></a>Huidige toepassingen weergeven

Als u de toepassingen in uw Batch-account wilt weergeven, selecteert **u Toepassingen** in het navigatiemenu aan de linkerkant.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Schermopname van het menu-item Toepassingen in de Azure Portal.":::

Als u deze menuoptie selecteert, wordt het **venster Toepassingen** geopend. In dit venster wordt de id van elke toepassing in uw account en de volgende eigenschappen weergegeven:

- **Pakketten:** het aantal versies dat is gekoppeld aan deze toepassing.
- **Standaardversie:** indien van toepassing, de toepassingsversie die wordt geïnstalleerd als er geen versie is opgegeven bij het implementeren van de toepassing.
- **Updates toestaan:** hiermee geeft u op of pakketupdates en verwijderingen zijn toegestaan.

Als u de [bestandsstructuur van](files-and-directories.md) het toepassingspakket op een reken knooppunt wilt zien, gaat u naar uw Batch-account in de Azure Portal. Selecteer **Pools.** selecteer vervolgens de pool die het reken-knooppunt bevat. Selecteer het reken-knooppunt waarop het toepassingspakket is geïnstalleerd en open de **map toepassingen.**

### <a name="view-application-details"></a>Toepassingsdetails weergeven

Als u de details van een toepassing wilt bekijken, selecteert u deze in het **venster** Toepassingen. U kunt de volgende instellingen voor uw toepassing configureren.

- **Updates toestaan:** geeft aan of toepassingspakketten kunnen [worden bijgewerkt of verwijderd.](#update-or-delete-an-application-package) De standaardwaarde is **Ja.** Als deze waarde **is ingesteld op** Nee, kunnen bestaande toepassingspakketten niet worden bijgewerkt of verwijderd, maar kunnen er nog steeds nieuwe toepassingspakketversies worden toegevoegd.
- **Standaardversie:** het standaardtoepassingspakket dat moet worden gebruikt wanneer de toepassing wordt geïmplementeerd, als er geen versie is opgegeven.
- **Weergavenaam:** een gebruiksvriendelijke naam die uw Batch-oplossing kan gebruiken wanneer er informatie over de toepassing wordt weergegeven. Deze naam kan bijvoorbeeld worden gebruikt in de gebruikersinterface van een service die u via Batch aan uw klanten levert.

### <a name="add-a-new-application"></a>Een nieuwe toepassing toevoegen

Als u een nieuwe toepassing wilt maken, voegt u een toepassingspakket toe en geeft u een unieke toepassings-id op.

Selecteer toepassingen in uw **Batch-account** en selecteer vervolgens **Toevoegen.**

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Schermopname van het proces voor het maken van een nieuwe toepassing in Azure Portal.":::

Voer de volgende informatie in:

- **Toepassings-id:** de id van uw nieuwe toepassing.
- **Versie**': de versie voor het toepassingspakket dat u uploadt.
- **Toepassingspakket:** het ZIP-bestand met de binaire bestanden van de toepassing en ondersteunende bestanden die nodig zijn om de toepassing uit te voeren.

De **toepassings-id** **en versie die** u opvolgt, moeten aan de volgende vereisten voldoen:

- Op Windows-knooppunten kan de id een combinatie van alfanumerieke tekens, afbreekstreepjes en onderstrepingstekens bevatten. Op Linux-knooppunten zijn alleen alfanumerieke tekens en onderstrepingstekens toegestaan.
- Mag niet meer dan 64 tekens bevatten.
- Moet uniek zijn binnen het Batch-account.
- ID's zijn case-preserving en niet-case-niet-gevoelig.

Wanneer u klaar bent, selecteert u **Verzenden.** Nadat het ZIP-bestand is geüpload naar uw Azure Storage account, wordt in de portal een melding weergegeven. Afhankelijk van de grootte van het bestand dat u uploadt en de snelheid van uw netwerkverbinding, kan dit enige tijd duren.

### <a name="add-a-new-application-package"></a>Een nieuw toepassingspakket toevoegen

Als u een toepassingspakketversie voor een bestaande  toepassing wilt toevoegen, selecteert u de toepassing in de sectie Toepassingen van uw Batch-account en selecteert u **vervolgens Toevoegen.**

Net als voor de nieuwe  toepassing geeft u de versie voor het  nieuwe pakket op, uploadt u het ZIP-bestand in het veld Toepassingspakket en selecteert u **verzenden.**

### <a name="update-or-delete-an-application-package"></a>Een toepassingspakket bijwerken of verwijderen

Als u een bestaand toepassingspakket wilt bijwerken of verwijderen, selecteert u de toepassing in **de sectie Toepassingen** van uw Batch-account. Selecteer het beletselteken in de rij van het toepassingspakket dat u wilt wijzigen en selecteer vervolgens de actie die u wilt uitvoeren.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Schermopname van de opties voor bijwerken en verwijderen voor toepassingspakketten in de Azure Portal.":::

Als u **Bijwerken selecteert,** kunt u een nieuw ZIP-bestand uploaden. Hiermee vervangt u het vorige ZIP-bestand dat u voor die versie hebt geüpload.

Als u **Verwijderen selecteert,** wordt u gevraagd om het verwijderen van die versie te bevestigen. Wanneer u **OK selecteert,** verwijdert Batch het ZIP-bestand uit uw Azure Storage account. Als u de standaardversie van een toepassing verwijdert, wordt de instelling **Standaardversie** voor die toepassing verwijderd.

## <a name="install-applications-on-compute-nodes"></a>Toepassingen installeren op rekenknooppunten

Nu u hebt geleerd hoe u toepassingspakketten beheert in de Azure Portal, kunnen we bespreken hoe u ze implementeert op rekenknooppunten en deze uitvoert met Batch-taken.

### <a name="install-pool-application-packages"></a>Groeptoepassingspakketten installeren

Als u een toepassingspakket wilt installeren op alle rekenknooppunten in een pool, geeft u een of meer toepassingspakketverwijzingen voor de pool op. De toepassingspakketten die u voor een pool opgeeft, worden geïnstalleerd op elk rekenpunt dat lid wordt van de pool en op elk knooppunt dat opnieuw wordt opgestart of een installatie terug wordt gemaakt.

Geef in Batch .NET een of meer [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) op wanneer u een nieuwe pool of voor een bestaande pool maakt. De [klasse ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) geeft een toepassings-id en -versie op die moeten worden geïnstalleerd op de rekenknooppunten van een pool.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Als de implementatie van een toepassingspakket mislukt, markeert de Batch-service het knooppunt dat onbruikbaar is [en](/dotnet/api/microsoft.azure.batch.computenode.state)worden er geen taken gepland voor uitvoering op dat knooppunt. Als dit gebeurt, start u het knooppunt opnieuw op om de pakketimplementatie opnieuw te starten. Door het knooppunt opnieuw op te starten, wordt ook opnieuw taakplanning op het knooppunt mogelijk.

### <a name="install-task-application-packages"></a>Toepassingspakketten voor taken installeren

Net als bij een pool geeft u toepassingspakketverwijzingen voor een taak op. Wanneer een taak is gepland om te worden uitgevoerd op een knooppunt, wordt het pakket gedownload en geëxtraheerd net voordat de opdrachtregel van de taak wordt uitgevoerd. Als een opgegeven pakket en versie al op het knooppunt zijn geïnstalleerd, wordt het pakket niet gedownload en wordt het bestaande pakket gebruikt.

Als u een toepassingspakket voor taken wilt installeren, configureert u de eigenschap [CloudTask.ApplicationPackageReferences van de](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) taak:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>De geïnstalleerde toepassingen uitvoeren

De pakketten die u hebt opgegeven voor een pool of taak worden gedownload en uitgepakt naar een benoemde map binnen de `AZ_BATCH_ROOT_DIR` van het knooppunt. Batch maakt ook een omgevingsvariabele die het pad naar de benoemde map bevat. De opdrachtregels van uw taak gebruiken deze omgevingsvariabele bij het verwijzen naar de toepassing op het knooppunt.

Op Windows-knooppunten heeft de variabele de volgende indeling:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Op Linux-knooppunten is de indeling iets anders. Punten (.), afbreekstreepingstekens (-) en cijfertekens (#) worden afgevlakt tot onderstrepingstekens in de omgevingsvariabele. Houd er ook rekening mee dat het geval van de toepassings-id behouden blijft. Bijvoorbeeld:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` en `version` zijn waarden die overeenkomen met de toepassing en pakketversie die u hebt opgegeven voor implementatie. Als u bijvoorbeeld hebt opgegeven dat versie 2.7 van application *blender* moet worden geïnstalleerd op Windows-knooppunten, gebruikt uw taakopdrachtregels deze omgevingsvariabele voor toegang tot de bestanden:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Geef op Linux-knooppunten de omgevingsvariabele in deze indeling op. Vlak de punten (.), afbreekstreepingen (-) en cijfertekens (#) af naar onderstrepingstekens en behoud de toepassings-id:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Wanneer u een toepassingspakket uploadt, kunt u een standaardversie opgeven die moet worden geïmplementeerd op uw rekenknooppunten. Als u een standaardversie voor een toepassing hebt opgegeven, kunt u het versieachtervoegsel weglaten wanneer u naar de toepassing verwijst. U kunt de standaardtoepassingsversie opgeven in Azure Portal, in het venster Toepassingen, zoals wordt weergegeven in [Toepassingen uploaden en beheren.](#upload-and-manage-applications) 

Als u bijvoorbeeld '2.7' in stelt als de standaardversie voor application *blender* en uw taken verwijzen naar de volgende omgevingsvariabele, worden op uw Windows-knooppunten versie 2.7 uitgevoerd:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Het volgende codefragment toont een voorbeeldtaakopdrachtregel die de standaardversie van de *blendertoepassing* start:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zie Omgevingsinstellingen voor taken voor meer informatie over omgevingsinstellingen [voor reken knooppunt.](jobs-and-tasks.md#environment-settings-for-tasks)

## <a name="update-a-pools-application-packages"></a>De toepassingspakketten van een groep bijwerken

Als een bestaande groep al is geconfigureerd met een toepassingspakket, kunt u een nieuw pakket voor de groep opgeven. Dit betekent:

- De Batch-service installeert het zojuist opgegeven pakket op alle nieuwe knooppunten die lid worden van de pool en op elk bestaand knooppunt dat opnieuw wordt opgestart of een installatie terug wordt gemaakt.
- Rekenknooppunten die zich al in de pool hebben wanneer u de pakketverwijzingen bijwerkt, installeren het nieuwe toepassingspakket niet automatisch. Deze rekenknooppunten moeten opnieuw worden opgestart of opnieuw worden opgestart om het nieuwe pakket te ontvangen.
- Wanneer een nieuw pakket wordt geïmplementeerd, weerspiegelen de gemaakte omgevingsvariabelen de verwijzingen naar het nieuwe toepassingspakket.

In dit voorbeeld is voor de bestaande pool versie 2.7 van de *blendertoepassing* geconfigureerd als een van de [cloudpool.ApplicationPackageReferences.](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) Als u de knooppunten van de pool wilt bijwerken met versie 2.76b, geeft u een nieuwe [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) op met de nieuwe versie en geeft u de wijziging door.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nu de nieuwe versie is geconfigureerd, installeert de Batch-service versie 2.76b op elk nieuw knooppunt dat lid wordt van de pool. Als u 2.76b wilt installeren op de knooppunten die zich al in de pool staan, start u deze opnieuw op of gebruikt u de installatie terug. Houd er rekening mee dat opnieuw opgestarte knooppunten bestanden van eerdere pakketimplementaties behouden.

## <a name="list-the-applications-in-a-batch-account"></a>De toepassingen in een Batch-account opneren

U kunt de toepassingen en hun pakketten in een Batch-account in een lijst zetten met behulp van de [methode ApplicationOperations.ListApplicationSummaries.](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries)

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- De [Batch REST API](/rest/api/batchservice) biedt ook ondersteuning voor het werken met toepassingspakketten. Zie bijvoorbeeld het element [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) voor het opgeven van pakketten die moeten worden geïnstalleerd en Toepassingen [voor](/rest/api/batchservice/application) het verkrijgen van toepassingsgegevens.
- Meer informatie over het programmatisch [beheren Azure Batch accounts en quota met Batch Management .NET.](batch-management-dotnet.md) Met [de Batch Management .NET-bibliotheek](/dotnet/api/overview/azure/batch/management) kunt u functies voor het maken en verwijderen van een account maken voor uw Batch-toepassing of -service.
