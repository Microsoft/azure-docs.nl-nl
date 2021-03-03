---
title: Typen test stations, micro soft Commercial Marketplace
description: Typen test stations in de commerciële Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: 2addf415c39691b4e662f304522a418aa8a778c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730368"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Gebruik dit type als u een aanbieding hebt op Azure Marketplace of AppSource, maar een test drive wilt bouwen met alleen Azure-resources. Een Azure Resource Manager ARM-sjabloon is een gecodeerde container van Azure-resources die u ontwerpt om uw oplossing het beste weer te geven. Test station neemt de aangestuurde ARM-sjabloon en implementeert alle resources die nodig zijn voor een resource groep. Dit is de enige test drive optie voor virtuele machines of aanbiedingen van Azure-apps.

Als u niet bekend bent met wat een ARM-sjabloon is, lees dan [Wat is Azure Resource Manager?](../azure-resource-manager/management/overview.md) en [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](../azure-resource-manager/templates/template-syntax.md) om beter te begrijpen hoe u uw eigen sjablonen kunt bouwen en testen.

Zie [Wat is een test drive?](what-is-test-drive.md) voor informatie over een **gehoste** of **logische app** test drive.

## <a name="technical-configuration"></a>Technische configuratie

Een implementatie sjabloon bevat alle Azure-resources waaruit uw oplossing bestaat. Voor producten die in dit scenario passen, worden alleen Azure-resources gebruikt. Stel de volgende eigenschappen in het partner centrum in:

- **Regio's** (vereist): er zijn momenteel 26 door Azure ondersteunde regio's waar uw test drive beschikbaar kan worden gemaakt. Normaal gesp roken wilt u uw test drive beschikbaar maken in de regio's waar u het grootste aantal klanten verwacht, zodat ze de dichtstbijzijnde regio voor de beste prestaties kunnen selecteren. U moet ervoor zorgen dat uw abonnement alle resources mag implementeren die nodig zijn voor elk van de regio's die u selecteert.

- **Instanties** : Selecteer het type (warm of koud) en het aantal beschik bare instanties dat wordt vermenigvuldigd met het aantal regio's waar uw aanbieding beschikbaar is.

  - **Dynamisch** : dit type exemplaar wordt geïmplementeerd en wacht op toegang per geselecteerde regio. Klanten hebben direct toegang tot *Hot* instances van een test drive in plaats van te wachten op een implementatie. De balans is dat deze instanties altijd worden uitgevoerd op uw Azure-abonnement, zodat de kosten voor de uptime groter worden. Het wordt ten zeerste aanbevolen om ten minste één *Hot* instance te hebben, aangezien de meeste klanten niet hoeven te wachten op volledige implementaties, wat leidt tot uitval van klant gebruik als er geen *Hot* instance beschikbaar is.

  - **Koud** : dit type exemplaar vertegenwoordigt het totale aantal instanties dat mogelijk per regio kan worden geïmplementeerd. Voor koude instanties is het hele test station Resource Manager-sjabloon vereist om te implementeren wanneer een klant de test drive aanvraagt, zodat *koude* instanties veel langzamer worden geladen dan *Hot* instances. Het bedrag dat u alleen hoeft te betalen voor de duur van de test drive, wordt *niet* altijd uitgevoerd op uw Azure-abonnement, net als bij een *Hot* -instance.

- **Azure Resource Manager-sjabloon testen** : Upload de. zip met uw Azure Resource Manager sjabloon. Meer informatie over het maken van een Azure Resource Manager sjabloon in het Quick Start-artikel [Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    > [!note]
    > Als u wilt publiceren, is het belang rijk om de opmaak van de ARM-sjabloon te valideren. Er zijn twee manieren om dit te doen (1) met behulp van een [online API-hulp programma](/rest/api/resources/deployments/validate) of (2) met een [test implementatie](../azure-resource-manager/templates/deploy-portal.md).

- **Duur van test drive** (vereist): Voer het aantal uren in dat het test drive actief blijft. De test drive wordt automatisch beëindigd nadat deze periode is verstreken. Gebruik alleen hele getallen (bijvoorbeeld ' 2 ' uur is geldig, ' 1,5 ' is niet).

## <a name="write-the-test-drive-template"></a>De test drive-sjabloon schrijven

Nadat u het gewenste pakket van resources hebt ontworpen, schrijft en bouwt u de test drive ARM-sjabloon. Omdat test drive implementaties uitvoert in een volledig geautomatiseerde modus, hebben test drive sjablonen een aantal beperkingen:

### <a name="parameters"></a>Parameters

De meeste sjablonen bevatten een set para meters waarmee resource namen, bronnen (zoals typen opslag accounts of grootte van virtuele machines), gebruikers namen en wacht woorden, DNS-namen, enzovoort worden gedefinieerd. Wanneer u oplossingen implementeert met behulp van Azure Portal, kunt u alle para meters hand matig invullen, beschik bare DNS-namen of namen van opslag accounts kiezen, enzovoort.

![Lijst met para meters in een Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

Test drive werkt echter automatisch, zonder menselijke tussen komst, zodat het slechts een beperkt aantal parameter categorieën ondersteunt. Als een para meter in de test drive ARM-sjabloon niet in een van de ondersteunde categorieën valt, moet u deze para meter vervangen door een variabele of constante waarde.

U kunt een geldige naam voor de para meters gebruiken. test drive herkent parameter categorie door gebruik te maken van een meta gegevens type waarde. Geef het meta gegevens type voor elke sjabloon parameter op. anders wordt uw sjabloon niet gevalideerd:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Alle para meters zijn optioneel, dus als u geen gebruik wilt maken van, hoeft u dit niet te doen.

### <a name="accepted-parameter-metadata-types"></a>Geaccepteerde meta gegevens typen

| Meta gegevens type   | Parameter type  | Beschrijving     | Voorbeeld waarde    |
|---|---|---|---|
| **baseuri**     | tekenreeks          | Basis-URI van uw implementatie pakket| `https://<..>.blob.core.windows.net/<..>` |
| **gebruikers**    | tekenreeks          | Nieuwe wille keurige gebruikers naam.| admin68876      |
| **password**    | beveiligde teken reeks    | Nieuw wille keurig wacht woord | LP! ACS \^ 2kh     |
| **sessie-id**   | tekenreeks          | Unieke test drive sessie-ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

Het test station initialiseert deze para meter met een **basis-URI** van uw implementatie pakket, zodat u deze para meter kunt gebruiken om een URI te maken van elk bestand dat in uw pakket is opgenomen.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Gebruik deze para meter in uw sjabloon om een URI te maken van elk bestand uit uw test drive-implementatie pakket. In het volgende voor beeld ziet u hoe u een URI van de gekoppelde sjabloon bouwt:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>gebruikersnaam

Het test station initialiseert deze para meter met een nieuwe wille keurige gebruikers naam:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Voorbeeld waarde: `admin68876`

U kunt wille keurige of constante gebruikers namen gebruiken voor uw oplossing.

#### <a name="password"></a>wachtwoord

Het test station initialiseert deze para meter met een nieuw wille keurig wacht woord:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Voorbeeld waarde:  `Lp!ACS^2kh`

U kunt wille keurige of constante wacht woorden gebruiken voor uw oplossing.

#### <a name="session-id"></a>sessie-ID

Het test station initialiseert deze para meter met een unieke GUID die de sessie-ID van het test station vertegenwoordigt:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Voorbeeld waarde: `b8c8693e-5673-449c-badd-257a405a6dee`

U kunt deze para meter gebruiken om de test drive-sessie uniek te identificeren, als dat nodig is.

### <a name="unique-names"></a>Unieke namen

Voor sommige Azure-resources, zoals opslag accounts of DNS-namen, zijn wereld wijd unieke namen vereist. Dit betekent dat elke keer dat test drive de ARM-sjabloon implementeert, een nieuwe resource groep wordt gemaakt met een unieke naam voor alle bijbehorende resources. Daarom moet u de functie [Unique string](../azure-resource-manager/templates/template-functions.md) samen voegen met de namen van variabelen op resource groep-id's voor het genereren van wille keurige unieke waarden:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Zorg ervoor dat u uw para meter/variabele teken reeksen () samenvoegt `contosovm` met een unieke teken reeks uitvoer ( `resourceGroup().id` ), omdat hiermee de unieke waarde en betrouw baarheid van elke variabele wordt gegarandeerd.

De meeste resource namen kunnen bijvoorbeeld niet beginnen met een cijfer, maar een unieke teken reeks functie kan een teken reeks retour neren die begint met een cijfer. Als u onbewerkte unieke teken reeks uitvoer gebruikt, mislukken uw implementaties.

In [dit artikel](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)vindt u meer informatie over regels en beperkingen voor de naamgeving van resources.

### <a name="deployment-location"></a>Implementatie locatie

U kunt test drive beschikbaar maken in verschillende Azure-regio's. Het is een goed idee om een gebruiker te laten kiezen uit de dichtstbijzijnde regio om te voorzien in de Beast-gebruikers ervaring.

Wanneer test drive een exemplaar van het Lab maakt, wordt er altijd een resource groep gemaakt in de regio die is gekozen door een gebruiker en wordt uw implementatie sjabloon uitgevoerd in deze groeps context. Uw sjabloon moet dus de implementatie locatie van de resource groep kiezen:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

En gebruik deze locatie voor elke resource voor een specifiek Lab-exemplaar:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Zorg ervoor dat uw abonnement alle gewenste resources in elk van de geselecteerde regio's mag implementeren. Zorg er ook voor dat uw installatie kopieën voor virtuele machines beschikbaar zijn in alle regio's die u gaat inschakelen. anders werkt uw implementatie sjabloon niet voor bepaalde regio's.

### <a name="outputs"></a>Uitvoerwaarden

Normaal gesp roken kunt u met Resource Manager-sjablonen implementeren zonder dat er uitvoer wordt geproduceerd. Dit komt doordat u alle waarden kent die u gebruikt om sjabloon parameters te vullen en u kunt altijd hand matig eigenschappen van een resource controleren.

Voor test drive Resource Manager-sjablonen is het echter belang rijk om terug te gaan naar test drive alle informatie die nodig is om toegang te krijgen tot het lab (website-Uri's, hostnamen van virtuele machines, gebruikers namen en wacht woorden). Zorg ervoor dat alle uitvoer namen leesbaar zijn omdat deze variabelen aan de klant worden gepresenteerd.

Er zijn geen beperkingen met betrekking tot sjabloon uitvoer. Met test drive worden alle uitvoer waarden geconverteerd naar teken reeksen, dus als u een object naar de uitvoer verzendt, ziet de gebruiker een JSON-teken reeks.

Voorbeeld:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Abonnements limieten

Vergeet niet over de limieten voor abonnementen en services te komen. Als u bijvoorbeeld tot 10 4-core virtuele machines wilt implementeren, kunt u ervoor zorgen dat voor het abonnement dat u gebruikt voor uw Lab, 40-kernen kunnen worden gebruikt. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over Azure-abonnementen en service limieten. Als er meerdere test stations tegelijk kunnen worden gemaakt, controleert u of uw abonnement het aantal kern geheugens kan verwerken vermenigvuldigd met het totale aantal gelijktijdige test schijven dat kan worden gemaakt.

### <a name="what-to-upload"></a>Wat u kunt uploaden

De test drive ARM-sjabloon wordt geüpload als een zip-bestand, dat verschillende implementatie artefacten kan bevatten, maar moet één bestand met de naam hebben `main-template.json` . Dit is de ARM-implementatie sjabloon die test drive gebruikt voor het instantiëren van een lab. Als u meer resources voorbij dit bestand hebt, kunt u ernaar verwijzen als externe resources binnen de sjabloon of deze toevoegen aan het zip-bestand.

Tijdens de publicatie certificering pakt test drive uw implementatie pakket uit en wordt de inhoud ervan in een interne test drive BLOB-container geplaatst. De container structuur weerspiegelt de structuur van uw implementatie pakket:

| package.zip                       | BLOB-container van test station         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

We noemen een URI van deze basis-URI voor de BLOB-container. Omdat elke revisie van uw Lab een eigen BLOB-container heeft, heeft elke revisie van uw Lab een eigen basis-URI. Het test station kan een basis-URI van uw uitgepakt implementatie pakket door geven aan uw sjabloon via sjabloon parameters.

### <a name="transform-template-examples-for-test-drive"></a>Voor beelden van sjablonen transformeren voor test drive

Het proces voor het omzetten van een architectuur van resources in een test drive Resource Manager-sjabloon kan bevallen. Voor meer informatie raadpleegt u deze voor beelden van de beste trans formatie van huidige implementatie sjablonen op [Wat is een test drive?](what-is-test-drive.md#transforming-examples).

## <a name="test-drive-deployment-subscription-details"></a>Details van implementatie abonnement testen

De laatste sectie die u moet volt ooien, is om automatisch de test stations te kunnen implementeren door uw Azure-abonnement en Azure Active Directory (AD) te verbinden.

![Details van implementatie abonnement testen](media/test-drive/deployment-subscription-details.png)

1. Vraag een **Azure-abonnements-id** aan. Hiermee wordt toegang verleend aan Azure-Services en de Azure Portal. In het abonnement wordt het resource gebruik gerapporteerd en de services worden gefactureerd. Als u nog geen apart Azure-abonnement hebt voor alleen test stations, maakt u er een. U kunt Azure-abonnements-Id's (zoals `1a83645ac-1234-5ab6-6789-1h234g764ghty1` ) vinden door u aan te melden bij Azure Portal en **abonnementen** te selecteren in het menu links in de Navigator.

   ![Azure-abonnementen](media/test-drive/azure-subscriptions.png)

2. Vraag een **Azure AD-Tenant-id** op. Als u al een Tenant-id beschikbaar hebt, kunt u deze vinden in de map met de **Azure Active Directory**  >    >  **-** eigenschappen:

   ![Azure Active Directory eigenschappen](media/test-drive/azure-active-directory-properties.png)

   Als u geen Tenant-ID hebt, maakt u een nieuwe in Azure Active Directory. Voor hulp bij het instellen van een Tenant raadpleegt [u Quick Start: een Tenant instellen](../active-directory/develop/quickstart-create-new-tenant.md).

3. **Azure AD-App-ID** : een nieuwe toepassing maken en registreren. Deze toepassing wordt gebruikt voor het uitvoeren van bewerkingen op uw test drive-exemplaar.

   1. Ga naar de zojuist gemaakte map of al een bestaande map en selecteer Azure Active Directory in het deel venster filter.
   2. Zoek **app-registraties** en selecteer **toevoegen**.
   3. Geef een toepassings naam op.
   4. Selecteer het **type** **Web-app/API**.
   5. Geef een wille keurige waarde op in de aanmeldings-URL. dit veld wordt niet gebruikt.
   6. Selecteer **Maken**.
   7. Nadat de toepassing is gemaakt, selecteert u **Eigenschappen**  >  **de toepassing instellen als multi tenant** en vervolgens **Opslaan**.

4. Selecteer **Opslaan**.

5. Kopieer de toepassings-ID voor deze geregistreerde app en plak deze in het veld test drive.

   ![Details van Azure AD-toepassings-ID](media/test-drive/azure-ad-application-id-detail.png)

6. Omdat we de toepassing gebruiken om te implementeren in het abonnement, moeten we de toepassing toevoegen als een bijdrager aan het abonnement:

   1. Selecteer het type **abonnement** dat u voor de test drive gebruikt.
   1. Klik op **Toegangsbeheer (IAM)** .
   1. Selecteer het tabblad **roltoewijzingen** en vervolgens **functie toewijzing toevoegen**.

      ![Een nieuwe Access Control Principal toevoegen](media/test-drive/access-control-principal.jpg)

   1. Stel **rol** **in en wijs toegang toe aan** zoals weer gegeven. Voer in het veld **selecteren** de naam van de Azure AD-toepassing in. Selecteer de toepassing waaraan u de rol **Inzender** wilt toewijzen.

      ![De machtigingen toevoegen](media/test-drive/access-control-permissions.jpg)

   1. Selecteer **Opslaan**.

7. Genereer een **Azure AD-App** verificatie sleutel. Voeg onder **sleutels** een **sleutel beschrijving** toe, stel de duur in op **nooit verlopen** (een verlopen sleutel verbreekt uw test drive in productie) en selecteer vervolgens **Opslaan**. Kopieer en plak deze waarde in uw vereiste test drive veld.

![Hiermee worden de sleutels voor de Azure AD-toepassing weer gegeven](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Opnieuw publiceren

Nu alle test drive velden zijn voltooid, publiceert u uw aanbieding **opnieuw** . Wanneer uw test drive certificering heeft door gegeven, test u de gebruikers ervaring in de preview van uw aanbieding:

1. Start een test drive in de gebruikers interface.
1. Open uw Azure-abonnement in de Azure Portal.
1. Controleer of uw test drive correct wordt geïmplementeerd.

   ![Azure Portal](media/test-drive/azure-portal.png)

Verwijder geen test drive exemplaren die zijn ingericht voor uw klanten. de test drive-service zal deze resource groepen automatisch opschonen nadat een klant deze heeft voltooid.

Zodra u vertrouwd bent met uw preview-aanbieding, is het tijd om **Live te gaan**! Er is een laatste controle proces om de volledige end-to-end-ervaring te controleren. Als we het aanbod afwijzen, sturen we een e-mail naar de technische contact persoon voor uw aanbieding waarin wordt uitgelegd wat er moet worden opgelost.

## <a name="next-steps"></a>Volgende stappen

- Als u de instructies voor het maken van uw aanbieding in het partner centrum hebt gevolgd, gebruikt u de pijl terug om terug te keren naar dat onderwerp.
- Meer informatie over andere typen test stations bij [Wat is een test drive?](what-is-test-drive.md).