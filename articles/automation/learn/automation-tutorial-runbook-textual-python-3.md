---
title: Een Python 3-runbook (preview) maken in Azure Automation
description: In dit artikel leert u een eenvoudig Python 3-runbook (preview) maken, testen en publiceren.
services: automation
ms.subservice: process-automation
ms.date: 02/16/2021
ms.topic: tutorial
ms.openlocfilehash: c19f7e177d51a3de75e7d7ae2b83442e23efd243
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100546139"
---
# <a name="tutorial-create-a-python-3-runbook-preview"></a>Zelfstudie: Een Python 3-runbook (preview) maken

In deze zelfstudie wordt stap voor stap het maken van een [Python 3-runbook](../automation-runbook-types.md#python-runbooks) (preview) in Azure Automation beschreven. Python-runbooks worden gecompileerd onder Python 2 en 3. U kunt de code van het runbook rechtstreeks bewerken met de teksteditor in de Azure-portal.

> [!div class="checklist"]
> * Een eenvoudig Python-runbook maken
> * Het runbook testen en publiceren
> * De status van de runbooktaak uitvoeren en bijhouden
> * Het runbook bijwerken om een virtuele Azure-machine te starten met runbookparameters

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

- Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- [Automation-account](../automation-security-overview.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.

- Een virtuele machine van Azure. Tijdens deze zelfstudie start en stopt u deze machine, dus het mag geen productie-VM zijn.

- U kunt een Hybrid Runbook Worker onder Linux of Windows gebruiken om Python 3-runbooks uit te voeren. Volg de instructies voor het installeren van een Hybrid Runbook Worker onder  [Linux](../automation-linux-hrw-install.md) of [Windows](../automation-windows-hrw-install.md) . Er bestaat geen specifieke vereiste voor een Hybrid Worker onder Linux. Als u echter een Windows Hybrid Worker voor Python 3-runbooks wilt gebruiken, configureert u de computer die als host fungeert voor de runbook worker afhankelijk van of u alleen Python 3 ondersteunt of of Python 2 en 3 naast elkaar moeten worden gebruikt.

   * Als u *alleen* Python 2 of Python 3 hebt geïnstalleerd, moet u het pad van de map waarin python.exe os opgeslagen, toevoegen aan de **PATH**-omgevingsvariabele. Als python.exe bijvoorbeeld is opgeslagen in het installatiepad `C:\Python`, moet dat pad worden toegevoegd aan de **PATH-** -omgevingsvariabele.

   * Als u zowel Python 2 als Python 3 hebt geïnstalleerd en u beide typen runbooks wilt uitvoeren, moet u de volgende omgevingsvariabelen configureren:

     * Python 2: maak een nieuwe omgevingsvariabele met de naam `PYTHON_2_PATH` en geef de installatiemap op. Als de installatiemap bijvoorbeeld `C:\Python27` is, moet dit pad aan de variabele worden toegevoegd.

     * Python 3: maak een nieuwe omgevingsvariabele met de naam `PYTHON_3_PATH` en geef de installatiemap op. Als de installatiemap bijvoorbeeld `C:\Python3` is, moet dit pad aan de variabele worden toegevoegd.

## <a name="create-a-new-runbook"></a>Een nieuw runbook maken

U begint met het maken van een eenvoudig runbook waarmee de tekst *Hallo wereld* als uitvoer wordt gegeven.

1. Open uw Automation-account in Azure Portal.

    Op de Automation-accountpagina vindt u een beknopte weergave van de resources in dit account. U hebt als het goed is al enkele assets. De meeste van deze assets zijn de modules die automatisch zijn opgenomen in een nieuw Automation-account. Ook moet u de referentieasset hebben die wordt genoemd in de [vereisten](#prerequisites).

2. Selecteer **Runbooks** onder **Procesautomatisering** om de lijst met runbooks te openen.

3. Selecteer **Een runbook toevoegen** om een nieuw runbook te maken.

4. Geef het runbook de naam **MyFirstRunbook-Python**.

5. Selecteer **Python 3** als **Runbooktype**.

6. Selecteer **Maken** om het runbook te maken en de teksteditor te openen.

## <a name="add-code-to-the-runbook"></a>Code toevoegen aan het runbook

Nu voegt u een eenvoudige opdracht toe om de tekst `Hello World` af te drukken.

```python
print("Hello World!")
```

Selecteer **Opslaan** om het runbook op te slaan.

## <a name="test-the-runbook"></a>Het runbook testen

Voordat u het runbook publiceert om het beschikbaar te maken in productie, wilt u het testen om er zeker van te zijn dat het goed werkt. Wanneer u een runbook test, voert u de conceptversie uit en geeft u de uitvoer interactief weer.

1. Selecteer **Testvenster** om het deelvenster **Testen** te openen.

2. Selecteer **Start** om de test te starten. Dit moet de enige ingeschakelde optie zijn.

3. Een [runbooktaak](../automation-runbook-execution.md) wordt gemaakt en de status ervan wordt weergegeven.
   In eerste instantie is de taakstatus **In de wachtrij geplaatst**. Hiermee wordt aangegeven dat er wordt gewacht tot er in de cloud een runbook worker beschikbaar is. De taakstatus verandert in **Starten** wanneer een worker de taak claimt en daarna in **Wordt uitgevoerd**, wanneer het runbook daadwerkelijk wordt uitgevoerd.

4. Wanneer de runbooktaak is voltooid, wordt de uitvoer ervan weergegeven. In dit geval moet u `Hello World` zien.

5. Sluit het deelvenster **Testen** om terug te gaan naar het canvas.

## <a name="publish-and-start-the-runbook"></a>Het runbook publiceren en starten

Het runbook dat u hebt gemaakt, bevindt zich nog steeds in de conceptmodus. U moet het publiceren voordat u het in productie kunt uitvoeren. Wanneer u een runbook publiceert, overschrijft u de bestaande gepubliceerde versie met de conceptversie. In dit geval hebt u nog geen gepubliceerde versie omdat het runbook zojuist is gemaakt.

1. Selecteer **Publiceren** om het runbook te publiceren en vervolgens **Ja** wanneer hierom wordt gevraagd.

2. Als u naar links schuift om het runbook weer te geven op de pagina **Runbooks**, moet de **Ontwerpstatus** als **Gepubliceerd** worden weergegeven.

3. Schuif terug naar rechts om het deelvenster voor **MyFirstRunbook-Python3** weer te geven.

   Met de opties bovenaan kunt u het runbook starten, het runbook weergeven of plannen dat het op een bepaald moment in de toekomst start.

4. Selecteer **Starten** en vervolgens **OK** wanneer het deelvenster **Runbook starten** wordt geopend.

5. Er wordt een deelvenster **Taak** geopend voor de runbooktaak die u hebt gemaakt. U kunt dit deelvenster sluiten, maar laten we het open houden, zodat u de voortgang van de taak kunt bekijken.

6. De taakstatus wordt weergegeven in **Taakoverzicht** en komt overeen met de statuswaarden die u zag toen u het runbook testte.

7. Zodra voor het runbook de status **Voltooid** wordt weergegeven, klikt u op **Uitvoer**. Het deelvenster **Uitvoer** wordt geopend. Hierin ziet u `Hello World`.

8. Sluit het deelvenster **Uitvoer**.

9. Selecteer **Alle logboeken** om het deelvenster **Streams** voor de runbooktaak te openen. U ziet enkel `Hello World` in de uitvoerstroom. In dit deelvenster kunnen echter andere stromen voor een runbooktaak worden weergegeven, zoals **Uitgebreid** en **Fout**, als het runbook hiernaar schrijft.

10. Sluit het deelvenster **Streams** en het deelvenster **Taak** om terug te gaan naar het deelvenster **MyFirstRunbook-Python3**.

11. Selecteer **Taken** om de pagina **Taken** voor dit runbook te openen. Op deze pagina worden alle taken weergegeven die met dit runbook zijn gemaakt. U zou slechts één weergegeven taak moeten zien, aangezien de taak slechts eenmaal is uitgevoerd.

12. U kunt op deze taak klikken om hetzelfde deelvenster **Taak** te openen dat u hebt bekeken toen u het runbook startte. Met dit deelvenster kunt u teruggaan in de tijd en de details bekijken van elke taak die voor een bepaald runbook is gemaakt.

## <a name="add-authentication-to-manage-azure-resources"></a>Verificatie toevoegen voor het beheren van Azure-resources

U hebt het runbook getest en gepubliceerd, maar tot nu toe doet het nog niets nuttigs. U wilt dat er Azure-resources mee worden beheerd.
Daarvoor moet het script verifiëren met behulp van de referenties van uw Automation-account.

> [!NOTE]
> Het Automation-account moet met de service-principalfunctie zijn gemaakt, anders is er geen Uitvoeren als-certificaat.
> Als uw Automation-account niet met de service-principal is gemaakt, kunt u verifiëren zoals beschreven in [Verifiëren met de Azure-beheerbibliotheken voor Python](/azure/python/python-sdk-azure-authenticate).

1. Open de teksteditor door **Bewerken** te selecteren in het deelvenster **MyFirstRunbook-Python3**.

2. Voeg de volgende code toe om te verifiëren bij Azure:

    ```python
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

Wanneer u een Python-runbook start, hetzij in het deelvenster **Testen**, hetzij als een gepubliceerd runbook, kunt u op de pagina **Runbook starten**, onder **Parameters**, de waarden voor de parameters invoeren.

Nadat u een waarde in het eerste vak begint in te voeren, verschijnt er een tweede, enzovoort, zodat u zoveel parameterwaarden kunt invoeren als nodig is.

De waarden zijn beschikbaar voor het script in de `sys.argv`-matrix zoals in de code die u zojuist hebt toegevoegd.

Voer de naam van uw resourcegroep in als de waarde van de eerste parameter, en de naam van de VM die moet worden gestart als de waarde van de tweede parameter.

![Parameterwaarden invoeren](../media/automation-tutorial-runbook-textual-python/runbook-python-params.png)

Selecteer **OK** om het runbook te starten. Het runbook wordt uitgevoerd en start de VM die u hebt opgegeven.

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
    print ('ERROR: Handling run-time error:', detail)
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation-runbooktypen](../automation-runbook-types.md) voor meer informatie over runbooktypen, hun voordelen en beperkingen.

- Zie [Azure voor Python-ontwikkelaars](/azure/python/) voor meer informatie over het ontwikkelen van Azure met Python.

- Zie [Azure Automation GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python) voor voorbeelden van Python 3-runbooks.
