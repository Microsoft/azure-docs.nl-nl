---
title: Toepassings pakketten implementeren op reken knooppunten
description: Gebruik de functie toepassings pakketten van Azure Batch om eenvoudig meerdere toepassingen en versies te beheren voor installatie op batch Compute-knoop punten.
ms.topic: how-to
ms.date: 03/24/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: d0ed5cc47694d6576e6aea46b62dfab8ecb73459
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045787"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Toepassingen implementeren op reken knooppunten met batch-toepassings pakketten

Met toepassings pakketten kan de code in uw Azure Batch-oplossing worden vereenvoudigd en kunnen de toepassingen die door uw taken worden uitgevoerd eenvoudiger worden beheerd. Met toepassings pakketten kunt u meerdere versies van toepassingen die door uw taken worden uitgevoerd, uploaden en beheren, met inbegrip van de bijbehorende ondersteunende bestanden. U kunt vervolgens automatisch een of meer van deze toepassingen implementeren op de reken knooppunten in uw pool.

De Api's voor het maken en beheren van toepassings pakketten maken deel uit van de [Batch Management .net](/dotnet/api/overview/azure/batch/management) -bibliotheek. De Api's voor het installeren van toepassings pakketten op een reken knooppunt maken deel uit van de [batch .net](/dotnet/api/overview/azure/batch/client) -bibliotheek. Vergelijk bare functies bevinden zich in de beschikbaar batch-Api's voor andere talen.

In dit artikel wordt uitgelegd hoe u toepassings pakketten uploadt en beheert in de Azure Portal. Ook wordt uitgelegd hoe u deze op de reken knooppunten van een groep kunt installeren met de [batch .net](/dotnet/api/overview/azure/batch/client) -bibliotheek.

## <a name="application-package-requirements"></a>Toepassings pakket vereisten

Als u toepassings pakketten wilt gebruiken, moet u [een Azure Storage-account koppelen](#link-a-storage-account) aan uw batch-account.

Er gelden beperkingen voor het aantal toepassingen en toepassings pakketten in een batch-account en op de maximale grootte van het toepassings pakket. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md)voor meer informatie.

> [!NOTE]
> Batch-Pools die zijn gemaakt vóór 5 juli 2017 bieden geen ondersteuning voor toepassings pakketten (tenzij ze zijn gemaakt na 10 maart 2016 met behulp van Cloud Services configuratie). De functie voor toepassings pakketten die hier wordt beschreven, vervangt de functie Batch-apps die beschikbaar is in vorige versies van de service.

## <a name="understand-applications-and-application-packages"></a>Meer informatie over toepassingen en toepassings pakketten

Binnen Azure Batch verwijst een *toepassing* naar een set binaire bestanden die automatisch kunnen worden gedownload naar de reken knooppunten in uw pool. Een toepassing bevat een of meer *toepassings pakketten* die verschillende versies van de toepassing vertegenwoordigen.

Elk *toepassings pakket* is een zip-bestand met de binaire bestanden van de toepassing en eventuele ondersteunende bestanden. Alleen de zip-indeling wordt ondersteund.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram van een weer gave op hoog niveau van toepassingen en toepassings pakketten.":::

U kunt toepassings pakketten opgeven op het niveau van de groep of taak.

- **Groeps toepassings pakketten** worden geïmplementeerd op elk knoop punt in de pool. Toepassingen worden geïmplementeerd wanneer een knoop punt wordt toegevoegd aan een pool en wanneer deze opnieuw wordt opgestart of een installatie kopie wordt gemaakt.
  
    Groeps toepassings pakketten zijn geschikt wanneer alle knoop punten in een pool de taken van een taak uitvoeren. U kunt een of meer toepassings pakketten opgeven die moeten worden geïmplementeerd wanneer u een groep maakt. U kunt ook de pakketten van een bestaande groep toevoegen of bijwerken. Als u een nieuw pakket wilt installeren op een bestaande pool, moet u de knoop punten opnieuw opstarten.

- **Taak toepassings pakketten** worden alleen geïmplementeerd op een reken knooppunt dat is gepland voor het uitvoeren van een taak, net voordat de opdracht regel van de taak wordt uitgevoerd. Als het opgegeven toepassings pakket en de versie zich al op het knoop punt bevindt, wordt het niet opnieuw geïmplementeerd en wordt het bestaande pakket gebruikt.
  
    Taak toepassings pakketten zijn handig in omgevingen met gedeelde groepen, waarbij verschillende taken worden uitgevoerd op één groep en de groep niet wordt verwijderd wanneer een taak is voltooid. Als uw taak minder taken dan knoop punten in de groep heeft, kunnen taak toepassings pakketten gegevens overdracht minimaliseren omdat uw toepassing alleen wordt geïmplementeerd op de knoop punten waarop taken worden uitgevoerd.
  
    Andere scenario's die kunnen profiteren van taak toepassings pakketten zijn taken die een grote toepassing uitvoeren, maar slechts enkele taken. Taak toepassingen kunnen bijvoorbeeld nuttig zijn voor een voorbereidings stadium van een Heavyweight of een samenvoeg taak.

Met toepassings pakketten hoeft de begin taak van de pool geen lange lijst op te geven met afzonderlijke bron bestanden die op de knoop punten moeten worden geïnstalleerd. U hoeft niet hand matig meerdere versies van uw toepassings bestanden in Azure Storage of op uw knoop punten te beheren. En u hoeft zich geen zorgen te maken over het genereren van [SAS-url's](../storage/common/storage-sas-overview.md) om toegang te bieden tot de bestanden in uw opslag account. Batch werkt op de achtergrond met Azure Storage om toepassings pakketten op te slaan en te implementeren op reken knooppunten.

> [!NOTE]
> De totale grootte van een begintaak moet kleiner zijn dan of gelijk zijn aan 32.768 tekens, inclusief bronbestanden en omgevingsvariabelen. Als uw begin taak deze limiet overschrijdt, is het gebruik van toepassings pakketten een andere optie. U kunt ook een zip-bestand met uw bron bestanden maken, dit uploaden als een BLOB naar Azure Storage en het vervolgens uitpakken vanaf de opdracht regel van de begin taak.

## <a name="upload-and-manage-applications"></a>Toepassingen uploaden en beheren

U kunt de [Azure Portal](https://portal.azure.com) -of de batch beheer-api's gebruiken om de toepassings pakketten in uw batch-account te beheren. In de volgende secties wordt uitgelegd hoe u een opslag account koppelt en hoe u toepassingen en toepassings pakketten kunt toevoegen en beheren in de Azure Portal.

> [!NOTE]
> Hoewel u toepassings waarden kunt definiëren in de [Microsoft.BatCH/batchAccounts](/templates/microsoft.batch/batchaccounts) resource van een [arm-sjabloon](quick-create-template.md), is het momenteel niet mogelijk om een arm-sjabloon te gebruiken om toepassings pakketten te uploaden voor gebruik in uw batch-account. U moet deze uploaden naar uw gekoppelde opslag account, zoals [hieronder](#add-a-new-application)wordt beschreven.

### <a name="link-a-storage-account"></a>Een opslag account koppelen

Als u toepassings pakketten wilt gebruiken, moet u een [Azure Storage-account](accounts.md#azure-storage-accounts) koppelen aan uw batch-account. De batch-service gebruikt het bijbehorende opslag account om uw toepassings pakketten op te slaan. We raden u aan om een opslag account te maken dat specifiek is voor gebruik met uw batch-account.

Als u nog geen opslag account hebt geconfigureerd, wordt in de Azure Portal een waarschuwing weer gegeven wanneer u voor het eerst **toepassingen** in uw batch-account selecteert. Als u een opslag account wilt koppelen aan uw batch-account, selecteert u **opslag account** in het **waarschuwings** venster en selecteert u vervolgens **opslag account** opnieuw.

Nadat u de twee accounts hebt gekoppeld, kan batch de pakketten die zijn opgeslagen in het gekoppelde opslag account automatisch implementeren in de reken knooppunten.

> [!IMPORTANT]
> U kunt geen toepassings pakketten gebruiken met Azure Storage accounts die zijn geconfigureerd met [firewall regels](../storage/common/storage-network-security.md)of waarvoor een **hiërarchische naam ruimte** is ingesteld op **ingeschakeld**.

De batch-service gebruikt Azure Storage om uw toepassings pakketten op te slaan als blok-blobs. Er worden [kosten in rekening gebracht](https://azure.microsoft.com/pricing/details/storage/) voor de blok-BLOB-gegevens en de grootte van elk pakket kan niet groter zijn dan de maximale grootte van de blok-blob. Zie [Azure Storage schaalbaarheids-en prestatie doelen voor opslag accounts](../storage/blobs/scalability-targets.md)voor meer informatie. Als u de kosten tot een minimum wilt beperken, moet u rekening houden met de grootte en het aantal van uw toepassings pakketten, en regel matig afgeschafte pakketten verwijderen.

### <a name="view-current-applications"></a>Huidige toepassingen weer geven

Als u de toepassingen wilt weer geven in uw batch-account, selecteert u **toepassingen** in het navigatie menu links.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Scherm opname van het menu-item toepassingen in de Azure Portal.":::

Als u deze menu optie selecteert, wordt het venster **toepassingen** geopend. In dit venster wordt de ID weer gegeven van elke toepassing in uw account en de volgende eigenschappen:

- **Pakketten**: het aantal versies dat is gekoppeld aan deze toepassing.
- **Standaard versie**: indien van toepassing, de versie van de toepassing die wordt geïnstalleerd als er geen versie wordt opgegeven bij het implementeren van de toepassing.
- **Updates toestaan**: Hiermee geeft u op of pakket updates en verwijderingen zijn toegestaan.

Als u de [bestands structuur](files-and-directories.md) van het toepassings pakket op een reken knooppunt wilt zien, gaat u naar uw batch-account in de Azure Portal. Selecteer **groepen**. Selecteer vervolgens de pool die het reken knooppunt bevat. Selecteer het reken knooppunt waarop het toepassings pakket is geïnstalleerd en open de map **toepassingen** .

### <a name="view-application-details"></a>Details van toepassing weer geven

Als u de details van een toepassing wilt bekijken, selecteert u deze in het venster **toepassingen** . U kunt de volgende instellingen configureren voor uw toepassing.

- **Updates toestaan**: geeft aan of toepassings pakketten kunnen worden [bijgewerkt of verwijderd](#update-or-delete-an-application-package). De standaard waarde is **Ja**. Indien ingesteld op **Nee**, kunnen bestaande toepassings pakketten niet worden bijgewerkt of verwijderd, maar nieuwe toepassings pakket versies kunnen nog steeds worden toegevoegd.
- **Standaard versie**: het standaard toepassings pakket dat moet worden gebruikt wanneer de toepassing wordt geïmplementeerd, als er geen versie is opgegeven.
- **Weergave naam**: een beschrijvende naam die door uw batch-oplossing kan worden gebruikt wanneer informatie over de toepassing wordt weer gegeven. Deze naam kan bijvoorbeeld worden gebruikt in de gebruikers interface van een service die u via batch aan uw klanten verstrekt.

### <a name="add-a-new-application"></a>Een nieuwe toepassing toevoegen

Als u een nieuwe toepassing wilt maken, voegt u een toepassings pakket toe en geeft u een unieke toepassings-ID op.

Selecteer in uw batch-account **toepassingen** en selecteer vervolgens **toevoegen**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Scherm opname van het proces voor het maken van nieuwe toepassingen in de Azure Portal.":::

Voer de volgende informatie in:

- **Toepassings-id**: de id van de nieuwe toepassing.
- **Versie**: de versie van het toepassings pakket dat u uploadt.
- **Toepassings pakket**: het. zip-bestand met de binaire bestanden van de toepassing en de ondersteunings dossiers die nodig zijn om de toepassing uit te voeren.

De **toepassings-id en-** **versie** die u invoert, moeten voldoen aan de volgende vereisten:

- Op Windows-knoop punten kan de ID een wille keurige combi natie van alfanumerieke tekens, afbreek streepjes en Under scores bevatten. Op Linux-knoop punten zijn alleen alfanumerieke tekens en onderstrepings rechten toegestaan.
- Mag niet langer zijn dan 64 tekens.
- Moet uniek zijn binnen het batch-account.
- De Id's zijn hoofdletter gevoelig en zijn niet in gebruik.

Wanneer u klaar bent, selecteert u **verzenden**. Nadat het zip-bestand is geüpload naar uw Azure Storage-account, wordt een melding weer gegeven in de portal. Afhankelijk van de grootte van het bestand dat u uploadt en de snelheid van uw netwerk verbinding, kan dit enige tijd duren.

### <a name="add-a-new-application-package"></a>Een nieuw toepassings pakket toevoegen

Als u een toepassings pakket versie voor een bestaande toepassing wilt toevoegen, selecteert u de toepassing in de sectie **toepassingen** van uw batch-account en selecteert u vervolgens **toevoegen**.

Zoals u voor de nieuwe toepassing hebt gedaan, geeft u de **versie** op voor het nieuwe pakket, uploadt u uw zip-bestand in het veld **toepassings pakket** en selecteert u vervolgens **verzenden**.

### <a name="update-or-delete-an-application-package"></a>Een toepassings pakket bijwerken of verwijderen

Als u een bestaand toepassings pakket wilt bijwerken of verwijderen, selecteert u de toepassing in de sectie **toepassingen** van uw batch-account. Selecteer het beletsel teken in de rij van het toepassings pakket dat u wilt wijzigen en selecteer vervolgens de actie die u wilt uitvoeren.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Scherm afbeelding met de opties voor bijwerken en verwijderen voor toepassings pakketten in de Azure Portal.":::

Als u **bijwerken** selecteert, kunt u een nieuw zip-bestand uploaden. Hiermee wordt het vorige zip-bestand vervangen dat u hebt geüpload voor die versie.

Als u **verwijderen** selecteert, wordt u gevraagd om de verwijdering van die versie te bevestigen. Zodra u **OK** hebt geselecteerd, wordt het zip-bestand uit uw Azure Storage-account verwijderd. Als u de standaard versie van een toepassing verwijdert, wordt de **standaard versie** -instelling voor die toepassing verwijderd.

## <a name="install-applications-on-compute-nodes"></a>Toepassingen op reken knooppunten installeren

Nu u hebt geleerd hoe u toepassings pakketten kunt beheren in de Azure Portal, kunnen we bespreken hoe u deze implementeert in reken knooppunten en uitvoeren met batch taken.

### <a name="install-pool-application-packages"></a>Groeps toepassings pakketten installeren

Als u een toepassings pakket wilt installeren op alle reken knooppunten in een pool, geeft u een of meer toepassings pakket verwijzingen op voor de groep. De toepassings pakketten die u voor een groep opgeeft, worden geïnstalleerd op elk reken knooppunt dat wordt gekoppeld aan de groep, en op elk knoop punt dat opnieuw wordt opgestart of de installatie kopie wordt gerecycled.

Geef in batch .NET een of meer [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) op wanneer u een nieuwe groep maakt of voor een bestaande groep. De klasse [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) geeft een toepassings-id en versie op die op de reken knooppunten van een pool moet worden geïnstalleerd.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

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
> Als de implementatie van een toepassings pakket mislukt, markeert de batch-service het knoop punt [onbruikbaar](/dotnet/api/microsoft.azure.batch.computenode.state)en worden er geen taken gepland voor uitvoering op dat knoop punt. Als dit het geval is, start u het knoop punt opnieuw op om de implementatie van het pakket opnieuw te starten. Wanneer het knoop punt opnieuw wordt gestart, wordt de taak planning ook opnieuw ingeschakeld op het knoop punt.

### <a name="install-task-application-packages"></a>Taak toepassings pakketten installeren

Net als bij een pool geeft u toepassings pakket verwijzingen op voor een taak. Wanneer een taak is gepland om te worden uitgevoerd op een knoop punt, wordt het pakket gedownload en geëxtraheerd net voordat de opdracht regel van de taak wordt uitgevoerd. Als een opgegeven pakket en versie al op het knoop punt zijn geïnstalleerd, wordt het pakket niet gedownload en wordt het bestaande pakket gebruikt.

Als u een taak toepassings pakket wilt installeren, configureert u de eigenschap [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) van de taak:

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

De pakketten die u voor een groep of taak hebt opgegeven, worden gedownload en geëxtraheerd naar een benoemde map binnen het `AZ_BATCH_ROOT_DIR` knoop punt. Batch maakt ook een omgevings variabele die het pad naar de benoemde map bevat. De opdracht regels van uw taak gebruiken deze omgevings variabele bij het verwijzen naar de toepassing op het knoop punt.

Op Windows-knoop punten heeft de variabele de volgende indeling:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Op Linux-knoop punten is de indeling iets anders. Punten (.), afbreek streepjes (-) en hekjes (#) worden afgevlakt tot onderstrepings tekens in de omgevings variabele. Houd er ook rekening mee dat het hoofdletter gebruik van de toepassings-ID behouden blijft. Bijvoorbeeld:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` en `version` zijn waarden die overeenkomen met de versie van de toepassing en het pakket die u voor de implementatie hebt opgegeven. Als u bijvoorbeeld hebt opgegeven dat versie 2,7 van de toepassings *blender* moet worden geïnstalleerd op Windows-knoop punten, gebruiken de opdracht regels van de taak deze omgevings variabele voor toegang tot de bestanden:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Op Linux-knoop punten geeft u de omgevings variabele op in deze indeling. De punten (.), afbreek streepjes (-) en hekjes (#) afvlakken tot onderstrepings tekens en het hoofdletter gebruik van de toepassings-ID behouden:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Wanneer u een toepassings pakket uploadt, kunt u een standaard versie opgeven om te implementeren op uw reken knooppunten. Als u een standaard versie voor een toepassing hebt opgegeven, kunt u het versie achtervoegsel weglaten wanneer u verwijst naar de toepassing. U kunt de standaard versie van de toepassing opgeven in de Azure Portal, in het venster **toepassingen** , zoals wordt weer gegeven in [toepassingen uploaden en beheren](#upload-and-manage-applications).

Als u bijvoorbeeld ' 2,7 ' hebt ingesteld als de standaard versie voor de toepassing *blender* en uw taken verwijzen naar de volgende omgevings variabele, wordt versie 2,7 van uw Windows-knoop punten uitgevoerd:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Het volgende code fragment toont een voor beeld van een opdracht regel van de taak waarmee de standaard versie van de *blender* -toepassing wordt gestart:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zie [omgevings instellingen voor taken](jobs-and-tasks.md#environment-settings-for-tasks)voor meer informatie over de instellingen van de omgeving voor het berekenen van knoop punten.

## <a name="update-a-pools-application-packages"></a>De toepassingspakketten van een groep bijwerken

Als een bestaande pool al is geconfigureerd met een toepassings pakket, kunt u een nieuw pakket voor de groep opgeven. Dit betekent:

- De batch-service installeert het zojuist opgegeven pakket op alle nieuwe knoop punten die lid zijn van de groep en op elk bestaand knoop punt dat opnieuw wordt opgestart of een nieuwe installatie kopie heeft gemaakt.
- Reken knooppunten die zich al in de groep bevinden wanneer u de pakket verwijzingen bijwerkt, installeren het nieuwe toepassings pakket niet automatisch. Deze reken knooppunten moeten opnieuw worden opgestart of worden geimageeerd om het nieuwe pakket te ontvangen.
- Wanneer een nieuw pakket wordt geïmplementeerd, worden de nieuwe toepassings pakket verwijzingen door de gemaakte omgevings variabelen weer gegeven.

In dit voor beeld heeft de bestaande pool versie 2,7 van de toepassing voor *blender* geconfigureerd als een van de [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Als u de knoop punten van de pool wilt bijwerken met versie 2.76 b, geeft u een nieuwe [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) op met de nieuwe versie en voert u de wijziging door.

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

Nu de nieuwe versie is geconfigureerd, installeert de batch-Service versie 2.76 b naar een nieuw knoop punt dat lid is van de groep. Als u 2.76 b wilt installeren op de knoop punten die zich al in de groep bevinden, moet u deze opnieuw opstarten of de installatie kopie ervan terugzetten. Houd er rekening mee dat opnieuw gestarte knoop punten bestanden van eerdere pakket implementaties behouden.

## <a name="list-the-applications-in-a-batch-account"></a>De toepassingen in een batch-account weer geven

U kunt de toepassingen en hun pakketten in een batch-account weer geven met behulp van de methode [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) .

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

- De [batch-rest API](/rest/api/batchservice) biedt ook ondersteuning voor het werken met toepassings pakketten. Zie bijvoorbeeld het [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) -element voor het opgeven van pakketten die moeten worden geïnstalleerd, en [toepassingen](/rest/api/batchservice/application) voor het verkrijgen van toepassings informatie.
- Meer informatie over het programmatisch [beheren van Azure batch accounts en quota's met Batch Management .net](batch-management-dotnet.md). Met de [Batch Management .net](/dotnet/api/overview/azure/batch/management) -bibliotheek kunt u functies voor het maken en verwijderen van accounts inschakelen voor uw batch toepassing of service.
