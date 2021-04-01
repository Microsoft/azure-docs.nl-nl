---
title: Een Python-runbook maken in Azure Automation
description: In dit artikel leert u een eenvoudig Python-runbook maken, testen en publiceren.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: has-adal-ref, devx-track-python
ms.openlocfilehash: e12327651165606e6a9b571d410f547a09a8ec8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87847921"
---
# <a name="tutorial-create-a-python-runbook"></a>Zelfstudie: Een Python-runbook maken

In deze zelfstudie wordt stap voor stap het maken van een [Python-runbook](../automation-runbook-types.md#python-runbooks) in Azure Automation beschreven. Python-runbooks worden gecompileerd onder Python 2. U kunt de code van het runbook rechtstreeks bewerken met de teksteditor in de Azure-portal.

> [!div class="checklist"]
> * Een eenvoudig Python-runbook maken
> * Het runbook testen en publiceren
> * De status van de runbooktaak uitvoeren en bijhouden
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbookparameters

> [!NOTE]
> Het gebruiken van een webhook om een Python-runbook te starten wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Automation-account](../index.yml) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
- Een virtuele machine van Azure. U stopt en start deze machine, dus het mag geen productiemachine zijn.

## <a name="create-a-new-runbook"></a>Een nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld* als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).

2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.

3. Selecteer **Een runbook toevoegen** om een nieuw runbook te maken.

4. Geef het runbook de naam **MyFirstRunbook-Python**.

5. Selecteer **Python 2** bij **Runbooktype**.

6. Klik op **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Code toevoegen aan het runbook

Nu voegt u een eenvoudige opdracht toe om de tekst `Hello World` af te drukken.

```python
print("Hello World!")
```

Klik op **Opslaan** om het runbook op te slaan.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de conceptversie uit en geeft u de uitvoer interactief weer.

1. Klik op **Testvenster** om het testvenster te openen.

2. Klik op **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Een [runbooktaak](../automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.
   In eerste instantie is de taakstatus ‘In de wachtrij geplaatst’. Hiermee wordt aangegeven dat er wordt gewacht tot er in de cloud een runbook worker beschikbaar is. De taakstatus verandert in ‘Starten’ wanneer een worker de taak claimt en daarna in ‘Wordt uitgevoerd’ wanneer het runbook daadwerkelijk wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In dit geval moet u `Hello World` zien.

5. Sluit het testvenster om terug te gaan naar het papier.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de modus Concept. U moet het publiceren voordat u het in productie kunt uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Klik op **Publiceren** om het runbook te publiceren en klik vervolgens op **Ja** wanneer hierom wordt gevraagd.

2. Als u naar links schuift om het runbook weer te geven op de pagina Runbooks, moet de **Ontwerpstatus** als **Gepubliceerd** worden getoond.

3. Schuif terug naar rechts om het deelvenster voor **MyFirstRunbook-Python** weer te geven.

   Met de opties bovenaan kunt u het runbook starten, het runbook weergeven of plannen dat het op een bepaald moment in de toekomst start.

4. Klik op **Starten** en vervolgens op **OK** wanneer de blade Runbook starten wordt geopend.

5. Er wordt een taakdeelvenster geopend voor de runbooktaak die u hebt gemaakt. U kunt dit deelvenster sluiten, maar laten we het open houden, zodat u de voortgang van de taak kunt bekijken.

6. De taakstatus wordt weergegeven in **Taakoverzicht** en komt overeen met de statuswaarden die u zag toen u het runbook testte.

7. Zodra voor het runbook de status Voltooid wordt weergegeven, klikt u op **Uitvoer**. Het deelvenster Uitvoer wordt geopend, waar u `Hello World` kunt zien.

8. Sluit het deelvenster Uitvoer.

9. Klik op **Alle logboeken** om het deelvenster Streams voor de runbooktaak te openen. U ziet enkel `Hello World` in de uitvoerstroom. In dit deelvenster kunnen er echter andere stromen voor een runbooktaak worden weergegeven, zoals Uitgebreid en Fout, als het runbook hiernaar schrijft.

10. Sluit het deelvenster Streams en het deelvenster Taak om terug te gaan naar het deelvenster MyFirstRunbook-Python.

11. Klik op **Taken** om de pagina Taken voor dit runbook te openen. Op deze pagina worden alle taken weergegeven die met dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.

12. U kunt op deze taak klikken om hetzelfde taakvenster te openen dat u hebt bekeken toen u het runbook startte. Met dit deelvenster kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd.
Daarvoor moet het script verifiëren met behulp van de referenties van uw Automation-account. Om u te helpen, kunt u het [pakket met Azure Automation-hulpprogramma’s](https://github.com/azureautomation/azure_automation_utility) gebruiken om het gemakkelijker te maken Azure-resources te verifiëren en ermee te werken.

> [!NOTE]
> Het Automation-account moet met de service-principalfunctie zijn gemaakt, anders is er geen Uitvoeren als-certificaat.
> Als uw Automation-account niet met de service-principal is gemaakt, kunt u verifiëren zoals beschreven in [Verifiëren met de Azure-beheerbibliotheken voor Python](/azure/python/python-sdk-azure-authenticate).

1. Open de teksteditor door te klikken op **Bewerken** in het deelvenster MyFirstRunbook-Python.

2. Voeg de volgende code toe om te verifiëren bij Azure:

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>Voeg code toe om de Python Compute-client te maken en de VM te starten

Als u met Azure VM’s wilt werken, maakt u een exemplaar van de [Azure Compute-client voor Python](/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient).

Gebruik de compute-client om de VM te starten. Voeg de volgende code toe aan het runbook:

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

Waarbij `MyResourceGroup` de naam van de resourcegroep is die de VM bevat, en `TestVM` de naam van de VM is die u wilt starten.

Test het runbook en voer het nogmaals uit om te zien of het de VM start.

## <a name="use-input-parameters"></a>Invoerparameters gebruiken

Het runbook gebruikt momenteel vastgelegde waarden voor de naam van de resourcegroep en de naam van de VM. Laten we nu code toevoegen die deze waarden ophaalt uit invoerparameters.

U gebruikt de `sys.argv`-variabele om de parameterwaarden op te halen. Voeg onmiddellijk na de andere `import`-instructies de volgende code toe aan het runbook:

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

Hiermee wordt de `sys`-module geïmporteerd en worden er twee variabelen gemaakt voor de naam van de resourcegroep en de naam van de VM. Merk op dat het element van de lijst met argumenten, `sys.argv[0]`, de naam van het script is en niet door de gebruiker wordt ingevoerd.

Nu kunt u de laatste twee regels van het runbook wijzigen om de invoerparameterwaarden te gebruiken in plaats van vastgelegde waarden:

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

Wanneer u een Python-runbook start (in het testvenster of als een gepubliceerd runbook), kunt u de waarden voor parameters invoeren op de pagina Runbook starten onder **Parameters**.

Nadat u een waarde in het eerste vak begint in te voeren, verschijnt er een tweede, enzovoort, zodat u zoveel parameterwaarden kunt invoeren als nodig is.

De waarden zijn beschikbaar voor het script in de `sys.argv`-matrix zoals in de code die u zojuist hebt toegevoegd.

Voer de naam van uw resourcegroep in als de waarde van de eerste parameter, en de naam van de VM die moet worden gestart als de waarde van de tweede parameter.

![Parameterwaarden invoeren](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Klik op **OK** om het runbook te starten. Het runbook wordt uitgevoerd en start de VM die u hebt opgegeven.

## <a name="error-handling-in-python"></a>Foutafhandeling in Python

U kunt ook de volgende conventies gebruiken om verschillende stromen op te halen uit uw Python-runbooks, waaronder een waarschuwings-, fout- en foutopsporingsstroom.

```python
print("Hello World output")
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

Het volgende voorbeeld toont hoe deze conventie in een `try...except`-blok wordt gebruikt.

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure Automation biedt geen ondersteuning voor `sys.stderr`.

## <a name="next-steps"></a>Volgende stappen

- Zie [Een PowerShell-runbook maken](automation-tutorial-runbook-textual-powershell.md) om aan de slag te gaan met PowerShell-runbooks.
- Zie [Een grafisch runbook maken](automation-tutorial-runbook-graphical.md) om aan de slag te gaan met grafische runbooks.
- Zie [Een PowerShell Workflow-runbook maken](automation-tutorial-runbook-textual.md) om aan de slag te gaan met PowerShell Workflow-runbooks.
- Zie [Azure Automation-runbooktypen](../automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen.
- Zie [Azure voor Python-ontwikkelaars](/azure/python/) voor meer informatie over het ontwikkelen van Azure met Python.
- Zie de [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python) voor voorbeelden van Python 2-runbooks.
