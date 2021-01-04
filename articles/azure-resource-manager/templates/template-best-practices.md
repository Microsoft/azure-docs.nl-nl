---
title: Aanbevolen procedures voor sjablonen
description: Hierin worden aanbevolen benaderingen beschreven voor het ontwerpen van Azure Resource Manager sjablonen (ARM-sjablonen). Biedt suggesties om veelvoorkomende problemen te voor komen bij het gebruik van sjablonen.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 85d58098508d5ac7cad6c1cb3cb68ad6c7f179f9
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724983"
---
# <a name="arm-template-best-practices"></a>Aanbevolen procedures voor ARM-sjablonen

Dit artikel laat u zien hoe u aanbevolen procedures kunt gebruiken bij het maken van uw Azure Resource Manager sjabloon (ARM-sjabloon). Met deze aanbevelingen kunt u veelvoorkomende problemen voor komen wanneer u een ARM-sjabloon gebruikt om een oplossing te implementeren.

## <a name="template-limits"></a>Limieten voor sjablonen

Beperk de grootte van uw sjabloon tot 4 MB en elk parameter bestand tot 64 KB. De limiet van 4 MB is van toepassing op de uiteindelijke status van de sjabloon nadat deze is uitgebreid met iteratieve resource definities en waarden voor variabelen en para meters.

U bent ook beperkt tot:

* 256-para meters
* 256 variabelen
* 800 bronnen (met inbegrip van het aantal kopieën)
* 64 uitvoer waarden
* 24.576 tekens in een sjabloon expressie

U kunt enkele limieten voor sjablonen overschrijden met behulp van een geneste sjabloon. Zie voor meer informatie [gekoppelde en geneste sjablonen gebruiken bij het implementeren van Azure-resources](linked-templates.md). Als u het aantal parameters, variabelen of uitvoerwaarden wilt verkleinen, kunt u verschillende waarden combineren in een object. Raadpleeg [Objects as parameters](/azure/architecture/building-blocks/extending-templates/objects-as-parameters) (Objecten als parameters) voor meer informatie.

## <a name="resource-group"></a>Resourcegroep

Wanneer u resources implementeert voor een resource groep, slaat de resource groep meta gegevens over de resources op. De meta gegevens worden opgeslagen op de locatie van de resource groep.

Als de regio van de resourcegroep tijdelijk niet beschikbaar is, kunt u resources in de resourcegroep niet bijwerken, omdat de metagegevens niet beschikbaar zijn. De resources in andere regio's werken nog steeds zoals verwacht, maar u kunt ze niet bijwerken. Als u het risico wilt minimaliseren, zoekt u de resource groep en de resources in dezelfde regio.

## <a name="parameters"></a>Parameters

De informatie in deze sectie kan nuttig zijn wanneer u met [para meters](template-parameters.md)werkt.

### <a name="general-recommendations-for-parameters"></a>Algemene aanbevelingen voor para meters

* Minimaliseer het gebruik van para meters. Gebruik in plaats daarvan variabelen of letterlijke waarden voor eigenschappen die tijdens de implementatie niet hoeven te worden opgegeven.

* Gebruik Camel-Case voor parameter namen.

* Gebruik parameters voor instellingen die variëren afhankelijk van de omgeving, zoals SKU, grootte en capaciteit.

* Gebruik para meters voor resource namen die u voor eenvoudige identificatie wilt opgeven.

* Geef een beschrijving op van elke para meter in de meta gegevens.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Standaard waarden definiëren voor para meters die niet gevoelig zijn. Als u een standaard waarde opgeeft, is het eenvoudiger om de sjabloon te implementeren en zien gebruikers van uw sjabloon een voor beeld van een geschikte waarde. Een standaard waarde voor een para meter moet geldig zijn voor alle gebruikers in de standaard implementatie configuratie.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* Als u een optionele para meter wilt opgeven, gebruikt u geen lege teken reeks als standaard waarde. Gebruik in plaats daarvan een letterlijke waarde of een taal expressie om een waarde te maken.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* Gebruik `allowedValues` spaarzaam. Gebruik deze alleen wanneer u moet controleren of sommige waarden niet zijn opgenomen in de toegestane opties. Als u `allowedValues` te breed gebruikt, kunt u geldige implementaties blok keren door de lijst niet up-to-date te houden.

* Wanneer een parameter naam in uw sjabloon overeenkomt met een para meter in de Power shell-implementatie opdracht, wordt deze naam conflicten opgelost door de achtervoegsel **FromTemplate** toe te voegen aan de sjabloon parameter. Als u bijvoorbeeld een para meter met de naam **ResourceGroupName** in uw sjabloon opneemt, wordt er een conflict veroorzaakt met de para meter **ResourceGroupName** in de cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Tijdens de implementatie wordt u gevraagd om een waarde voor **ResourceGroupNameFromTemplate** op te geven.

### <a name="security-recommendations-for-parameters"></a>Beveiligings aanbevelingen voor para meters

* Gebruik altijd para meters voor gebruikers namen en wacht woorden (of geheimen).

* Gebruiken `securestring` voor alle wacht woorden en geheimen. Gebruik het type als u gevoelige gegevens doorgeeft in een JSON-object `secureObject` . Sjabloon parameters met een beveiligde teken reeks of beveiligde object typen kunnen niet worden gelezen na het implementeren van de resource.

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* Geef geen standaard waarden op voor gebruikers namen, wacht woorden of een waarde die een `secureString` type vereist.

* Geef geen standaard waarden op voor eigenschappen waarmee de aanval surface area van de toepassing wordt verhoogd.

### <a name="location-recommendations-for-parameters"></a>Aanbevelingen voor de locatie van para meters

* Gebruik een para meter om de locatie voor resources op te geven en stel de standaard waarde in op `resourceGroup().location` . Als u een locatie parameter opgeeft, kunnen gebruikers van de sjabloon een locatie opgeven waar ze gemachtigd zijn om resources te implementeren.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* Geef geen `allowedValues` voor de locatie parameter op. De locaties die u opgeeft, zijn mogelijk niet beschikbaar in alle Clouds.

* Gebruik de locatie parameter waarde voor bronnen die zich waarschijnlijk op dezelfde locatie bevinden. Deze aanpak minimaliseert het aantal keren dat gebruikers worden gevraagd om locatie gegevens op te geven.

* Voor bronnen die niet op alle locaties beschikbaar zijn, gebruikt u een afzonderlijke para meter of geeft u een letterlijke locatie waarde op.

## <a name="variables"></a>Variabelen

De volgende informatie kan nuttig zijn wanneer u met [variabelen](template-variables.md)werkt:

* Gebruik Camel Case voor namen van variabelen.

* Gebruik variabelen voor waarden die u meer dan één keer wilt gebruiken in een sjabloon. Als een waarde slechts één keer wordt gebruikt, wordt uw sjabloon in een in code vastgelegde waarde gemakkelijker te lezen.

* Gebruik variabelen voor waarden die u maakt op basis van een complexe schikking van sjabloon functies. De sjabloon is gemakkelijker te lezen wanneer de complexe expressie alleen in variabelen wordt weer gegeven.

* U kunt de functie [Reference](template-functions-resource.md#reference) niet gebruiken in de `variables` sectie van de sjabloon. De `reference` functie heeft zijn waarde afgeleid van de runtime status van de resource. Variabelen worden echter opgelost tijdens het eerst parseren van de sjabloon. Construct waarden die de `reference` functie rechtstreeks nodig hebben in `resources` de `outputs` sectie of van de sjabloon.

* Variabelen voor resource namen bevatten die uniek moeten zijn.

* Gebruik een [copy-lus in variabelen](copy-variables.md) om een herhaald patroon van JSON-objecten te maken.

* Ongebruikte variabelen verwijderen.

## <a name="api-version"></a>API-versie

Stel de `apiVersion` eigenschap in op een in code vastgelegde API-versie voor het bron type. Wanneer u een nieuwe sjabloon maakt, raden we u aan om de nieuwste API-versie voor een resource type te gebruiken. Zie [Naslag informatie over sjablonen](/azure/templates/)om beschik bare waarden te bepalen.

Als uw sjabloon werkt zoals verwacht, kunt u het beste dezelfde API-versie gebruiken. Als u dezelfde API-versie gebruikt, hoeft u zich geen zorgen te maken over het afbreken van wijzigingen die in latere versies kunnen worden geïntroduceerd.

Gebruik geen para meter voor de API-versie. De resource-eigenschappen en-waarden kunnen variëren per API-versie. IntelliSense in een code-editor kan het juiste schema niet bepalen wanneer de API-versie is ingesteld op een para meter. Als u een API-versie doorgeeft die niet overeenkomt met de eigenschappen in uw sjabloon, mislukt de implementatie.

Gebruik geen variabelen voor de API-versie. Gebruik met name niet de [functie providers](template-functions-resource.md#providers) voor het dynamisch ophalen van API-versies tijdens de implementatie. De dynamisch opgehaalde API-versie komt mogelijk niet overeen met de eigenschappen in uw sjabloon.

## <a name="resource-dependencies"></a>Bronafhankelijkheden

Wanneer u wilt bepalen welke [afhankelijkheden](define-resource-dependency.md) er moeten worden ingesteld, gebruikt u de volgende richt lijnen:

* Gebruik de `reference` functie en geef de resource naam door om een impliciete afhankelijkheid in te stellen tussen resources die een eigenschap moeten delen. Voeg geen expliciet `dependsOn` element toe wanneer u al een impliciete afhankelijkheid hebt gedefinieerd. Deze aanpak vermindert het risico van overbodige afhankelijkheden. Zie [Naslag informatie en lijst functies](define-resource-dependency.md#reference-and-list-functions)voor een voor beeld van het instellen van een impliciete afhankelijkheid.

* Stel een onderliggende bron in die afhankelijk is van de bovenliggende resource.

* Resources waarvoor het [element voor waarde](conditional-resource-deployment.md) is ingesteld op ONWAAR, worden automatisch verwijderd uit de afhankelijkheids volgorde. Stel de afhankelijkheden zo in dat de resource altijd wordt geïmplementeerd.

* Zorg ervoor dat afhankelijkheden trapsgewijs worden ingesteld zonder ze expliciet in te stellen. Uw virtuele machine is bijvoorbeeld afhankelijk van een virtuele netwerk interface en de virtuele netwerk interface is afhankelijk van een virtueel netwerk en open bare IP-adressen. De virtuele machine wordt daarom geïmplementeerd na alle drie de resources, maar u hoeft de virtuele machine niet expliciet in te stellen als afhankelijk van alle drie de resources. Deze benadering verduidelijkt de afhankelijkheids volgorde en maakt het eenvoudiger om de sjabloon later te wijzigen.

* Als een waarde kan worden bepaald vóór de implementatie, probeert u de resource te implementeren zonder een afhankelijkheid. Als voor een configuratie waarde bijvoorbeeld de naam van een andere resource nodig is, hebt u mogelijk geen afhankelijkheid nodig. Deze richt lijnen werken niet altijd omdat sommige resources de aanwezigheid van de andere bron verifiëren. Als er een fout bericht wordt weer gegeven, voegt u een afhankelijkheid toe.

## <a name="resources"></a>Resources

De volgende informatie kan nuttig zijn wanneer u met [resources](template-syntax.md#resources)werkt:

* Geef `comments` voor elke resource in de sjabloon op om andere inzenders inzicht te geven in het doel van de resource.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* Als u een *openbaar eind punt* in uw sjabloon gebruikt (zoals een openbaar eind punt voor Azure Blob-opslag), hoeft u de naam ruimte *niet vast te coderen* . Gebruik de `reference` functie om de naam ruimte dynamisch op te halen. U kunt deze methode gebruiken om de sjabloon te implementeren in verschillende open bare naam ruimte omgevingen zonder het eind punt in de sjabloon hand matig te wijzigen. Stel de API-versie in op de versie die u gebruikt voor het opslag account in uw sjabloon.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   Als het opslag account is geïmplementeerd in dezelfde sjabloon die u maakt en de naam van het opslag account niet wordt gedeeld met een andere resource in de sjabloon, hoeft u de naam ruimte van de provider niet op te geven of de `apiVersion` Wanneer u naar de bron verwijst. In het volgende voor beeld wordt de vereenvoudigde syntaxis weer gegeven.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   U kunt ook verwijzen naar een bestaand opslag account in een andere resource groep.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* Wijs alleen open bare IP-adressen toe aan een virtuele machine wanneer deze vereist zijn voor een toepassing. Als u verbinding wilt maken met een virtuele machine (VM) voor fout opsporing of voor beheer-of beheer doeleinden, gebruikt u binnenkomende NAT-regels, een virtuele netwerk gateway of een JumpBox.

     Zie voor meer informatie over het maken van verbinding met virtuele machines:

   * [Vm's uitvoeren voor een architectuur met meerdere lagen in azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [WinRM-toegang instellen voor virtuele machines in Azure Resource Manager](../../virtual-machines/windows/winrm.md)
   * [Externe toegang tot uw virtuele machine toestaan met behulp van de Azure Portal](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Externe toegang tot uw virtuele machine toestaan met behulp van Power shell](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Externe toegang tot uw virtuele Linux-machine toestaan met behulp van Azure CLI](../../virtual-machines/linux/nsg-quickstart.md)

* De `domainNameLabel` eigenschap voor open bare IP-adressen moet uniek zijn. De `domainNameLabel` waarde moet tussen de 3 en 63 tekens lang zijn en de regels volgen die zijn opgegeven met deze reguliere expressie: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` . Omdat de `uniqueString` functie een teken reeks met een lengte van 13 tekens genereert, `dnsPrefixString` is de para meter beperkt tot 50 tekens.

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* Wanneer u een wacht woord aan een aangepaste script extensie toevoegt, gebruikt u de `commandToExecute` eigenschap in de `protectedSettings` eigenschap.

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > Gebruik de `protectedSettings` eigenschap van de relevante extensies om ervoor te zorgen dat geheimen worden versleuteld wanneer ze worden door gegeven als para meters voor vm's en uitbrei dingen.

## <a name="use-test-toolkit"></a>Test Toolkit gebruiken

De ARM-sjabloon test Toolkit is een script dat controleert of uw sjabloon aanbevolen procedures gebruikt. Als uw sjabloon niet voldoet aan de aanbevolen procedures, wordt een lijst met waarschuwingen met voorgestelde wijzigingen geretourneerd. De test Toolkit kan u helpen bij het implementeren van aanbevolen procedures in uw sjabloon.

Nadat u uw sjabloon hebt voltooid, voert u de test Toolkit uit om te zien of er manieren zijn om de implementatie te verbeteren. Zie [test Toolkit voor arm-sjablonen gebruiken](test-toolkit.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over de structuur van het sjabloon bestand.
* Zie [arm-sjablonen ontwikkelen voor Cloud consistentie](templates-cloud-consistency.md)voor aanbevelingen voor het maken van sjablonen die in alle Azure-Cloud omgevingen werken.
