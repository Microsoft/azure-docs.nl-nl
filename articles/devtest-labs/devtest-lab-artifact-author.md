---
title: Aangepaste artefacten maken voor uw virtuele machine van DevTest Labs | Microsoft Docs
description: Meer informatie over het maken van artefacten voor gebruik met Azure DevTest Labs om toepassingen te implementeren en in te stellen na het inrichten van een virtuele machine.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88270679"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Aangepaste artefacten maken voor uw virtuele machine van DevTest Labs

Bekijk de volgende video voor een overzicht van de stappen die in dit artikel worden beschreven:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Overzicht
U kunt *artefacten* gebruiken om uw toepassing te implementeren en in te stellen nadat u een virtuele machine hebt ingericht. Een artefact bestaat uit een artefact definitie bestand en andere script bestanden die zijn opgeslagen in een map in een Git-opslag plaats. Artefact definitie bestanden bestaan uit JSON en expressies die u kunt gebruiken om op te geven wat u wilt installeren op een virtuele machine. U kunt bijvoorbeeld de naam van een artefact definiëren, een opdracht die moet worden uitgevoerd en para meters die beschikbaar zijn wanneer de opdracht wordt uitgevoerd. U kunt naar andere script bestanden in het artefact definitie bestand op naam verwijzen.

## <a name="artifact-definition-file-format"></a>Indeling van artefact definitie bestand
In het volgende voor beeld ziet u de secties waaruit de basis structuur van een definitie bestand is opgebouwd:

```json
  {
    "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
    "title": "",
    "description": "",
    "iconUri": "",
    "targetOsType": "",
    "parameters": {
      "<parameterName>": {
        "type": "",
        "displayName": "",
        "description": ""
      }
    },
    "runCommand": {
      "commandToExecute": ""
    }
  }
```

| Elementnaam | Vereist? | Beschrijving |
| --- | --- | --- |
| $schema |Nee |Locatie van het JSON-schema bestand. Het JSON-schema bestand kan u helpen de geldigheid van het definitie bestand te testen. |
| titel |Ja |De naam van het artefact dat wordt weer gegeven in het lab. |
| beschrijving |Ja |Beschrijving van het artefact dat wordt weer gegeven in het lab. |
| iconUri |Nee |De URI van het pictogram dat wordt weer gegeven in het lab. |
| targetOsType |Ja |Het besturings systeem van de virtuele machine waarop het artefact is geïnstalleerd. Ondersteunde opties zijn Windows en Linux. |
| parameters |Nee |Waarden die worden gegeven wanneer de opdracht voor artefact installatie wordt uitgevoerd op een computer. Zo kunt u uw artefact aanpassen. |
| Opdracht |Ja |Installatie opdracht voor artefact die op een virtuele machine wordt uitgevoerd. |

### <a name="artifact-parameters"></a>Artefactparameters
Geef in de sectie para meters van het definitie bestand op welke waarden een gebruiker kan invoeren wanneer een artefact wordt geïnstalleerd. U kunt naar deze waarden verwijzen in de opdracht voor artefact installatie.

Gebruik de volgende structuur om para meters te definiëren:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Elementnaam | Vereist? | Beschrijving |
| --- | --- | --- |
| type |Ja |Type parameter waarde. Zie de volgende lijst voor de toegestane typen. |
| displayName |Ja |De naam van de para meter die wordt weer gegeven voor een gebruiker in het lab. |
| beschrijving |Ja |Beschrijving van de para meter die wordt weer gegeven in het lab. |

Toegestane typen zijn:

* teken reeks (wille keurige geldige JSON-teken reeks)
* int (een wille keurig geldig JSON-geheel getal)
* BOOL (wille keurige geldige JSON-Booleaanse waarde)
* matrix (een wille keurige geldige JSON-matrix)

## <a name="secrets-as-secure-strings"></a>Geheimen als beveiligde teken reeksen
Geheimen declareren als beveiligde teken reeksen. Hier volgt de syntaxis voor het declareren van een veilige teken reeks parameter in de `parameters` sectie van de **artifactfile.jsvoor** het volgende bestand:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Voer voor de installatie opdracht voor artefacten het Power shell-script uit met de beveiligde teken reeks die is gemaakt met behulp van de ConvertTo-SecureString opdracht. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Zie voor het volledige voor beeld artifactfile.jsen het artifact.ps1 (Power shell-script) [dit voor beeld op github](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Een ander belang rijk punt om te weten dat er geen geheimen naar de console moeten worden geregistreerd als uitvoer wordt vastgelegd voor fout opsporing door gebruikers. 

## <a name="artifact-expressions-and-functions"></a>Artefact expressies en-functies
U kunt expressies en functies gebruiken om de installatie opdracht voor artefacten te maken.
Expressies worden tussen vier Kante haken ([en]) geplaatst en geëvalueerd wanneer het artefact is geïnstalleerd. Expressies kunnen ergens in een JSON-teken reeks waarde worden weer gegeven. Expressies retour neren altijd een andere JSON-waarde. Als u een letterlijke teken reeks moet gebruiken die begint met een haakje ([), moet u twee haken gebruiken ([[).
Normaal gesp roken gebruikt u expressies met functies om een waarde te maken. Net als in Java script worden functie aanroepen ingedeeld als **functie naam (Arg1, arg2, arg3)**.

De volgende lijst bevat algemene functies:

* **para meters (para meter)**: retourneert een parameter waarde die wordt opgegeven wanneer de artefact opdracht wordt uitgevoerd.
* **concat (Arg1, arg2, arg3,.....)**: combineert meerdere teken reeks waarden. Deze functie kan allerlei argumenten hebben.

In het volgende voor beeld ziet u hoe u expressies en functies kunt gebruiken om een waarde te maken:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Een aangepast artefact maken

1. Installeer een JSON-editor. U hebt een JSON-editor nodig om te kunnen werken met artefact definitie bestanden. We raden u aan [Visual Studio code](https://code.visualstudio.com/)te gebruiken, die beschikbaar is voor Windows, Linux en OS X.
2. Een voor beeld van een artifactfile.jsin het definitie bestand ophalen. Bekijk de artefacten die zijn gemaakt door het DevTest Labs-team in onze [github-opslag plaats](https://github.com/Azure/azure-devtestlab). We hebben een uitgebreide bibliotheek met artefacten gemaakt die u kunnen helpen bij het maken van uw eigen artefacten. Down load een artefact definitie bestand en breng wijzigingen aan om uw eigen artefacten te maken.
3. Gebruik IntelliSense. Gebruik IntelliSense om geldige elementen te bekijken die u kunt gebruiken om een artefact definitie bestand te maken. U kunt ook de verschillende opties voor waarden van een element bekijken. Wanneer u bijvoorbeeld het element **targetOsType** bewerkt, toont IntelliSense u twee opties voor Windows of Linux.
4. Sla het artefact op in de [open bare Git-opslag plaats voor DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) of [uw eigen Git-opslag plaats](devtest-lab-add-artifact-repo.md). In de open bare opslag plaats kunt u artefacten weer geven die door anderen worden gedeeld en die u rechtstreeks kunt gebruiken of aanpassen aan uw behoeften.
   
   1. Maak een afzonderlijke map voor elk artefact. De mapnaam moet hetzelfde zijn als de naam van het artefact.
   2. Sla het artefact definitie bestand (artifactfile.jsaan) op in de map die u hebt gemaakt.
   3. Sla de scripts op waarnaar wordt verwezen vanuit de opdracht voor artefact installatie.
      
      Hier volgt een voor beeld van hoe een artefact map eruit kan zien:
      
      ![Voor beeld van map voor artefacten](./media/devtest-lab-artifact-author/git-repo.png)
5. Als u uw eigen opslag plaats gebruikt om artefacten op te slaan, voegt u de opslag plaats aan het Lab toe door de volgende instructies in het artikel: [een Git-opslag plaats voor artefacten en sjablonen toe te voegen](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Verwante artikelen:
* [Artefact fouten in DevTest Labs diagnosticeren](devtest-lab-troubleshoot-artifact-failure.md)
* [Een virtuele machine toevoegen aan een bestaand Active Directory domein met behulp van een resource manager-sjabloon in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [toevoegen van een Git-artefact opslagplaats aan een Lab](devtest-lab-add-artifact-repo.md).
