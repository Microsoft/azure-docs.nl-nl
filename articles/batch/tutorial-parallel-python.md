---
title: 'Zelfstudie: Een parallelle workload uitvoeren met behulp van de Python API'
description: 'Zelfstudie: Mediabestanden parallel verwerken met ffmpeg in Azure Batch met behulp van de clientbibliotheek Batch Python'
ms.devlang: python
ms.topic: tutorial
ms.date: 11/29/2018
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 48df2cb6ac103d1f03d2d64fce0d10a8f2f128e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043407"
---
# <a name="tutorial-run-a-parallel-workload-with-azure-batch-using-the-python-api"></a>Zelfstudie: Een parallelle workload uitvoeren met Azure Batch met behulp van de Python API

Met Azure Batch kunt u grootschalige parallelle en HPC-batchrekentaken (High Performance Computing) efficiënt uitvoeren in Azure. Deze zelfstudie leidt u door een Python-voorbeeld van het uitvoeren van een parallelle workload met behulp van Batch. U leert een gangbare Batch-toepassingswerkstroom en leert hoe u via programmacode werkt met Batch- en Storage-bronnen. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Verifiëren met Batch- en Storage-accounts
> * Invoerbestanden uploaden naar Storage
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Een job en taken maken om invoerbestanden te verwerken
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

In deze zelfstudie zet u MP4-mediabestanden parallel om in de MP3-indeling met behulp van het open-source hulpprogramma [ffmpeg](https://ffmpeg.org/). 

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Python-versie 2,7 of 3.6 +](https://www.python.org/downloads/)

* [PIP](https://pip.pypa.io/en/stable/installing/)-pakketbeheer

* Een Azure Batch-account en een gekoppeld Azure Storage-account. Raadpleeg de Batch-quickstarts met behulp van [Azure Portal](quick-create-portal.md) of [Azure CLI](quick-create-cli.md) voor instructies voor het maken van deze accounts.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)] 

## <a name="download-and-run-the-sample"></a>Het voorbeeld downloaden en uitvoeren

### <a name="download-the-sample"></a>Het voorbeeld downloaden

[Download of kloon de voorbeeld-app](https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial) vanuit GitHub. Als u de opslagplaats van de voorbeeld-app wilt klonen met een Git-client, gebruikt u de volgende opdracht:

```
git clone https://github.com/Azure-Samples/batch-python-ffmpeg-tutorial.git
```

Ga naar de map die het bestand `batch_python_tutorial_ffmpeg.py` bevat.

Installeer de vereiste pakketten in de Python-omgeving met behulp van `pip`.

```bash
pip install -r requirements.txt
```

Open het bestand `config.py`. Werk de referentietekenreeksen van het Batch- en Storage-account bij met waarden die uniek zijn voor uw accounts. Bijvoorbeeld:


```Python
_BATCH_ACCOUNT_NAME = 'mybatchaccount'
_BATCH_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxE+yXrRvJAqT9BlXwwo1CwF+SwAYOxxxxxxxxxxxxxxxx43pXi/gdiATkvbpLRl3x14pcEQ=='
_BATCH_ACCOUNT_URL = 'https://mybatchaccount.mybatchregion.batch.azure.com'
_STORAGE_ACCOUNT_NAME = 'mystorageaccount'
_STORAGE_ACCOUNT_KEY = 'xxxxxxxxxxxxxxxxy4/xxxxxxxxxxxxxxxxfwpbIC5aAWA8wDu+AFXZB827Mt9lybZB1nUcQbQiUrkPtilK5BQ=='
```

### <a name="run-the-app"></a>De app uitvoeren

Het script uitvoeren:

```
python batch_python_tutorial_ffmpeg.py
```

Wanneer u de voorbeeldtoepassing uitvoert, ziet de uitvoer van de console er ongeveer als volgt uit. Tijdens de uitvoering wordt bij `Monitoring all tasks for 'Completed' state, timeout in 00:30:00...` gewacht terwijl de rekenknooppunten van de pool worden gestart. 
   
```
Sample start: 11/28/2018 3:20:21 PM

Container [input] created.
Container [output] created.
Uploading file LowPriVMs-1.mp4 to container [input]...
Uploading file LowPriVMs-2.mp4 to container [input]...
Uploading file LowPriVMs-3.mp4 to container [input]...
Uploading file LowPriVMs-4.mp4 to container [input]...
Uploading file LowPriVMs-5.mp4 to container [input]...
Creating pool [LinuxFFmpegPool]...
Creating job [LinuxFFmpegJob]...
Adding 5 tasks to job [LinuxFFmpegJob]...
Monitoring all tasks for 'Completed' state, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Deleting container [input]....

Sample end: 11/28/2018 3:29:36 PM
Elapsed time: 00:09:14.3418742
```

Ga naar uw Batch-account in de Azure Portal om de pool, rekenknooppunten, job en taken te controleren. Als u bijvoorbeeld een heatmap van de rekenknooppunten in uw pool wilt zien, klikt u op **Pools** > *LinuxFFmpegPool*.

Wanneer taken worden uitgevoerd, ziet de heatmap er bijvoorbeeld als volgt uit:

![Heatmap van pool](./media/tutorial-parallel-python/pool.png)

Wanneer u de toepassing uitvoert in de standaardconfiguratie, bedraagt de uitvoeringstijd doorgaans ongeveer **5 minuten**. Het maken van de pool kost de meeste tijd. 

[!INCLUDE [batch-common-tutorial-download](../../includes/batch-common-tutorial-download.md)]

## <a name="review-the-code"></a>De code bekijken

In de volgende secties wordt de voorbeeldtoepassing uitgesplitst in de stappen die ermee worden uitgevoerd om een workload in de Batch-service te verwerken. Raadpleeg de Python-code terwijl u de rest van dit artikel leest omdat niet elke regel in de code van het voorbeeld wordt besproken.

### <a name="authenticate-blob-and-batch-clients"></a>Blob- en Batch-clients verifiëren

Voor het werken met een opslagaccount gebruikt de app het pakket [azure-storage-blob](https://pypi.python.org/pypi/azure-storage-blob) om een [BlockBlobService](/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice)-object te maken.

```python
blob_client = azureblob.BlockBlobService(
    account_name=_STORAGE_ACCOUNT_NAME,
    account_key=_STORAGE_ACCOUNT_KEY)
```

De app maakt een [BatchServiceClient](/python/api/azure.batch.batchserviceclient)-object om pools, jobs en taken in de Batch-service te maken en te beheren. De Batch-client in het voorbeeld gebruikt verificatie op basis van gedeelde sleutels. Batch ondersteunt ook verificatie via [Azure Active Directory](batch-aad-auth.md) om afzonderlijke gebruikers of een toepassing zonder toezicht te verifiëren.

```python
credentials = batchauth.SharedKeyCredentials(_BATCH_ACCOUNT_NAME,
                                             _BATCH_ACCOUNT_KEY)

batch_client = batch.BatchServiceClient(
    credentials,
    base_url=_BATCH_ACCOUNT_URL)
```

### <a name="upload-input-files"></a>Invoerbestanden uploaden

De app gebruikt de verwijzing `blob_client` om een opslagcontainer voor de MP4-invoerbestanden en een container voor de taakuitvoer te maken. Vervolgens wordt de functie `upload_file_to_container` aangeroepen om MP4-bestanden in de lokale map `InputFiles` naar de container te uploaden. De bestanden in de opslag zijn gedefinieerd als Batch [ResourceFile](/python/api/azure-batch/azure.batch.models.resourcefile)-objecten die later met Batch kunnen worden gedownload op rekenknooppunten.

```python
blob_client.create_container(input_container_name, fail_on_exist=False)
blob_client.create_container(output_container_name, fail_on_exist=False)
input_file_paths = []

for folder, subs, files in os.walk(os.path.join(sys.path[0], './InputFiles/')):
    for filename in files:
        if filename.endswith(".mp4"):
            input_file_paths.append(os.path.abspath(
                os.path.join(folder, filename)))

# Upload the input files. This is the collection of files that are to be processed by the tasks.
input_files = [
    upload_file_to_container(blob_client, input_container_name, file_path)
    for file_path in input_file_paths]
```

### <a name="create-a-pool-of-compute-nodes"></a>Een pool van rekenknooppunten maken

Daarna wordt in het voorbeeld met behulp van een aanroep naar `create_pool` een pool van rekenknooppunten in het Batch-account gemaakt. Deze gedefinieerd functie gebruikt de Batch-klasse [PoolAddParameter](/python/api/azure-batch/azure.batch.models.pooladdparameter) om het aantal rekenknooppunten, de VM-grootte en een poolconfiguratie in te stellen. Hier geeft het object [VirtualMachineConfiguration](/python/api/azure-batch/azure.batch.models.virtualmachineconfiguration) een [ImageReference](/python/api/azure-batch/azure.batch.models.imagereference) naar een Ubuntu Server 18.04 LTS-installatiekopie op die is gepubliceerd in de Azure Marketplace. Batch ondersteunt diverse VM-installatiekopieën in de Azure Marketplace, evenals aangepaste VM-installatiekopieën.

Het aantal knooppunten en de VM-grootte worden ingesteld met behulp van gedefinieerde constanten. Batch ondersteunt toegewezen knooppunten en [knooppunten met een lage prioriteit](batch-low-pri-vms.md) en u kunt een of beide gebruiken in uw pools. Toegewezen rekenknooppunten zijn gereserveerd voor uw pool. Knooppunten met een lage prioriteit worden aangeboden tegen een lagere prijs en worden gehaald uit het overschot van de VM-capaciteit in Azure. Knooppunten met een lage prioriteit zijn niet beschikbaar als Azure onvoldoende capaciteit heeft. In het voorbeeld wordt standaard een pool met slechts 5 knooppunten met lage prioriteit gemaakt met de grootte *Standard_A1_v2*. 

Naast fysieke eigenschappen van knooppunten bevat deze poolconfiguratie het object [StartTask](/python/api/azure-batch/azure.batch.models.starttask). StartTask wordt in elk knooppunt uitgevoerd wanneer dat knooppunt aan de pool wordt toegevoegd en telkens wanneer dat knooppunt opnieuw wordt opgestart. In dit voorbeeld voert StartTask Bash-shell-opdrachten uit om het ffmpeg-pakket en afhankelijkheden op de knooppunten te installeren.

Met de methode [pool.add](/python/api/azure-batch/azure.batch.operations.pooloperations) wordt de pool naar de Batch-service verzonden.

```python
new_pool = batch.models.PoolAddParameter(
    id=pool_id,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=batchmodels.ImageReference(
            publisher="Canonical",
            offer="UbuntuServer",
            sku="18.04-LTS",
            version="latest"
        ),
        node_agent_sku_id="batch.node.ubuntu 18.04"),
    vm_size=_POOL_VM_SIZE,
    target_dedicated_nodes=_DEDICATED_POOL_NODE_COUNT,
    target_low_priority_nodes=_LOW_PRIORITY_POOL_NODE_COUNT,
    start_task=batchmodels.StartTask(
        command_line="/bin/bash -c \"apt-get update && apt-get install -y ffmpeg\"",
        wait_for_success=True,
        user_identity=batchmodels.UserIdentity(
            auto_user=batchmodels.AutoUserSpecification(
                scope=batchmodels.AutoUserScope.pool,
                elevation_level=batchmodels.ElevationLevel.admin)),
    )
)
batch_service_client.pool.add(new_pool)
```

### <a name="create-a-job"></a>Een taak maken

Een Batch-taak (job) geeft een pool op die taken moet uitvoeren en optionele instellingen, zoals een prioriteit en planning voor het werk. In het voorbeeld wordt een job gemaakt met een aanroep naar `create_job`. De app gebruikt de klasse [JobAddParameter](/python/api/azure-batch/azure.batch.models.jobaddparameter) om een taak te maken in de pool. Met de methode [job.add](/python/api/azure-batch/azure.batch.operations.joboperations) wordt de pool naar de Batch-service verzonden. De Batch-taak heeft in eerste instantie geen taken.

```python
job = batch.models.JobAddParameter(
    id=job_id,
    pool_info=batch.models.PoolInformation(pool_id=pool_id))

batch_service_client.job.add(job)
```

### <a name="create-tasks"></a>Taken maken

De app maakt taken in de Batch-taak met een aanroep naar `add_tasks`. Deze gedefinieerde functie gebruikt de klasse [TaskAddParameter](/python/api/azure-batch/azure.batch.models.taskaddparameter) om een lijst met taakobjecten te maken. Elke taak voert ffmpeg uit om een `resource_files`-invoerobject met de parameter `command_line` verwerken. ffmpeg is eerder op elk knooppunt geïnstalleerd toen de pool werd gemaakt. Hier voert de opdrachtregel ffmpeg uit om elk MP4-invoerbestand (video) te converteren naar een MP3-bestand (audio).

Het voorbeeld maakt een [OutputFile](/python/api/azure-batch/azure.batch.models.outputfile)-object voor het MP3-bestand nadat de opdrachtregel is uitgevoerd. De uitvoerbestanden van elke taak (één in dit geval) worden geüpload naar een container in het gekoppelde opslagaccount met behulp van de eigenschap `output_files` van de taak.

Vervolgens worden met de app taken toegevoegd aan de Batch-taak met behulp van de methode [task.add_collection](/python/api/azure-batch/azure.batch.operations.taskoperations). Deze methode plaatst de taken in een wachtrij voor uitvoering op de rekenknooppunten. 

```python
tasks = list()

for idx, input_file in enumerate(input_files):
    input_file_path = input_file.file_path
    output_file_path = "".join((input_file_path).split('.')[:-1]) + '.mp3'
    command = "/bin/bash -c \"ffmpeg -i {} {} \"".format(
        input_file_path, output_file_path)
    tasks.append(batch.models.TaskAddParameter(
        id='Task{}'.format(idx),
        command_line=command,
        resource_files=[input_file],
        output_files=[batchmodels.OutputFile(
            file_pattern=output_file_path,
            destination=batchmodels.OutputFileDestination(
                container=batchmodels.OutputFileBlobContainerDestination(
                    container_url=output_container_sas_url)),
            upload_options=batchmodels.OutputFileUploadOptions(
                upload_condition=batchmodels.OutputFileUploadCondition.task_success))]
    )
    )
batch_service_client.task.add_collection(job_id, tasks)
```

### <a name="monitor-tasks"></a>Taken controleren

Wanneer taken worden toegevoegd aan een Batch-taak, plaatst Batch ze automatisch in de wachtrij en plant de uitvoering ervan op rekenknooppunten in de gekoppelde pool. Op basis van de instellingen die u opgeeft, zal Batch alle taken in de wachtrij plaatsen, plannen en opnieuw proberen uit te voeren, en ook andere taakbeheertaken uitvoeren. 

Er zijn veel manieren om de uitvoering van taken te controleren. De functie `wait_for_tasks_to_complete` in dit voorbeeld gebruikt het object [TaskState](/python/api/azure-batch/azure.batch.models.taskstate) om taken te controleren op een bepaalde status, in dit geval de status voltooid, binnen een tijdslimiet.

```python
while datetime.datetime.now() < timeout_expiration:
    print('.', end='')
    sys.stdout.flush()
    tasks = batch_service_client.task.list(job_id)

    incomplete_tasks = [task for task in tasks if
                        task.state != batchmodels.TaskState.completed]
    if not incomplete_tasks:
        print()
        return True
    else:
        time.sleep(1)
...
```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat de taken zijn uitgevoerd, verwijdert de app automatisch de gemaakte invoeropslagcontainer en biedt u de mogelijkheid de Batch-pool en -taak te verwijderen. De klassen [JobOperations](/python/api/azure-batch/azure.batch.operations.joboperations) en [PoolOperations](/python/api/azure-batch/azure.batch.operations.pooloperations) van BatchClient hebben verwijderingsmethoden, die worden aangeroepen wanneer u de verwijdering bevestigt. Hoewel jobs en taken zelf niet in rekening worden gebracht, worden rekenknooppunten wel in rekening gebracht. Daarom is het raadzaam om pools alleen toe te wijzen als dat nodig is. Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd. De invoer- en uitvoerbestanden blijven echter aanwezig in het opslagaccount.

Verwijder de resourcegroep, het Batch-account en het opslagaccount wanneer u deze niet meer nodig hebt. Hiervoor selecteert u in Azure Portal de resourcegroep voor het Batch-account en klikt u op **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verifiëren met Batch- en Storage-accounts
> * Invoerbestanden uploaden naar Storage
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Een job en taken maken om invoerbestanden te verwerken
> * Taakuitvoering controleren
> * Uitvoerbestanden ophalen

Voor meer voorbeelden van het gebruik van de Python API om Batch-workloads te plannen en te verwerken raadpleegt u de voorbeelden op GitHub.

> [!div class="nextstepaction"]
> [Voorbeelden van Batch Python](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)

