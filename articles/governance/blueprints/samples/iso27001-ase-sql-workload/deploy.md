---
title: Ontwerp van het blauwdrukvoorbeeld voor ISO 27001 conforme ASE-/SQL-workloads implementeren
description: Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads implementeren waaronder parameterdetails van blauwdrukartefact.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 5c329a9d7175772e80ea6d9d8da3baf85ce0d170
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669641"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads implementeren

Om het overzicht en de architectuur van het Azure Blueprints-blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads te implementeren, moet u de volgende stappen nemen:

> [!div class="checklist"]
> - Overzicht van de voorbeeldblauwdruk voor [ISO 27001 conforme gedeelde services](../iso27001-shared/index.md)
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Overzicht van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services

Voordat dit blauwdrukvoorbeeld kan worden geïmplementeerd, moet het blauwdrukvoorbeeld [ISO 27001 gedeelde services](../iso27001-shared/index.md) worden geïmplementeerd in het doelabonnement. Zonder een succesvolle implementatie van het blauwdrukvoorbeeld ISO 27001 Shared Services, ontbreken in dit blauwdrukvoorbeeld infrastructuurafhankelijkheden en zal het tijdens de implementatie.

> [!IMPORTANT]
> Dit blauwdrukvoorbeeld moet worden toegewezen in hetzelfde abonnement als het blauwdrukvoorbeeld [ISO 27001 gedeelde services](../iso27001-shared/index.md).

## <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het **ISO 27001: Blauwdrukvoorbeeld voor** ASE/SQL-workload onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het ISO 27001-blauwdrukvoorbeeld voor ASE/SQL-workload.
   - **Definitielocatie**: Gebruik het weglatingsteken en selecteer de beheergroep waarin u uw kopie van het voorbeeld wilt opslaan.

1. Selecteer het tabblad _Artefacten_ boven aan de pagina of kies **Volgende: Artefacten** onder aan de pagina.

1. Controleer de lijst met artefacten die samen het blauwdrukvoorbeeld vormen. Veel van de artefacten bevatten parameters die we later zullen definiëren. Selecteer **Concept opslaan** wanneer u klaar bent met het controleren van het blauwdrukvoorbeeld.

## <a name="publish-the-sample-copy"></a>De voorbeeldkopie publiceren

Uw kopie van het blauwdrukvoorbeeld is nu gemaakt in uw omgeving. De kopie is gemaakt in de **Concept**-modus en moet worden **Gepubliceerd** voordat deze kan worden toegewezen en geïmplementeerd. De kopie van het blauwdrukvoorbeeld kan worden aangepast aan uw omgeving en behoeften, maar door die aanpassing is het mogelijk dat de kopie niet meer is voldoet aan de ISO 27001-standaard.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk publiceren** boven aan de pagina. Op de nieuwe pagina aan de rechterkant geeft u een **Versie** voor uw kopie van het blauwdrukvoorbeeld op. Deze eigenschap is handig als u later een aanpassing wilt maken. Geef **Notities over wijzigingen** op, zoals 'Eerste gepubliceerde versie op basis van het ISO 27001-blauwdrukvoorbeeld'. Selecteer vervolgens **Publiceren** onder aan de pagina.

## <a name="assign-the-sample-copy"></a>De voorbeeldkopie toewijzen

Zodra de kopie van het blauwdrukvoorbeeld is **Gepubliceerd**, kan het worden toegewezen aan een abonnement binnen de beheergroep waarin de kopie is opgeslagen. Dit is de stap waarin parameters worden opgegeven om elke implementatie van de kopie van het blauwdrukvoorbeeld uniek te maken.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer de pagina **Blauwdrukdefinities** aan de linkerkant. Gebruik de filters om uw kopie van het blauwdrukvoorbeeld te zoeken en selecteer vervolgens uw kopie.

1. Selecteer **Blauwdruk toewijzen** boven aan de pagina Blauwdrukdefinitie.

1. Geef de parameterwaarden op voor de blauwdruktoewijzing:

   - Basisbeginselen

     - **Abonnementen**: Selecteer een of meer van de abonnementen in de beheergroep waarin u uw kopie van het blauwdrukvoorbeeld hebt opgeslagen. Als u meer dan één abonnement selecteert, wordt een toewijzing gemaakt voor elk abonnement waarvoor de ingevoerde parameters worden gebruikt.
     - **Naam van toewijzing**: De naam wordt vooraf voor u ingevuld op basis van de naam van de blauwdruk.
       Wijzig de naam als dat nodig is of gebruik de opgegeven naam.
     - **Locatie**: Selecteer een regio waarin u de beheerde identiteit wilt maken. Azure Blueprint gebruikt deze beheerde identiteit om alle artefacten in de toegewezen blauwdruk te implementeren. Zie [Beheerde identiteiten voor Azure-resources](../../../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.
     - **Blauwdrukdefinitieversie**: Kies een **gepubliceerde** versie van uw kopie van het blauwdrukvoorbeeld.

   - Toewijzing vergrendelen

     Selecteer de instelling voor het vergrendelen van de blauwdruk voor uw omgeving. Zie voor meer informatie [Vergrendeling van blauwdrukresources](../../concepts/resource-locking.md).

   - Beheerde identiteit

     Laat de optie voor de standaard door het _systeem toegewezen_ beheerde identiteit staan.

   - Blauwdrukparameters

     De parameters die in deze sectie worden gedefinieerd, worden door veel van de artefacten in de definitie van de blauwdruk gebruikt om consistentie te bieden.

     - **Naam van de organisatie**: Geef een korte naam op voor uw organisatie. Deze eigenschap wordt voornamelijk gebruikt voor de naamgeving van resources.
     - **Abonnements-id voor gedeelde services**: Abonnements-id waar het blauwdrukvoorbeeld [ISO 27001 gedeelde services](../iso27001-shared/index.md) is toegewezen.
     - **Standaardvoorvoegsel van subnetadres**: De CIDR-notatie voor het standaardsubnet van de virtuele netwerk.
       De standaardwaarde is _10.1.0.0/16_.
     - **Locatie van de werkbelasting**: Hiermee bepaalt u op welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn op alle locaties beschikbaar. Artefacten die dergelijke services implementeren, bieden een parameteroptie voor de locatie waarop dat artefact moet worden geïmplementeerd.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

## <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|Log Analytics-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-workload-log-rg` om de resourcegroep uniek te maken.|
|Log Analytics-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Servicelaag|Hiermee stelt u de laag van de Log Analytics-werkruimte in. Standaardwaarde is _PerNode_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Locatie|De regio die wordt gebruikt voor het maken van de Log Analytics-werkruimte. De standaardwaarde is _US - west 2_.|
|Resourcegroep van netwerk|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-workload-net-rg` om de resourcegroep uniek te maken.|
|Resourcegroep van netwerk|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Sjabloon voor netwerkbeveiligingsgroep|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Privé-IP-adres voor Azure-firewall|Hiermee configureert u het privé IP-adres van de [Azure-firewall](../../../../firewall/overview.md). Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in _ISO 27001: Artefactparameter voor gedeelde services_ **Voorvoegsel voor Azure Firewall-subnetadres**. De standaardwaarde is _10.0.4.4_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Abonnements-id voor gedeelde services|De waarde die wordt gebruikt om VNet-peering in te schakelen tussen een werkbelasting en gedeelde services.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van adres voor virtueel netwerk|De CIDR-notatie voor het virtuele netwerk. De standaardwaarde is _10.1.0.0/16_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Standaardvoorvoegsel van subnetadres|De CIDR-notatie voor het standaardsubnet van de virtuele netwerk. De standaardwaarde is _10.1.0.0/16_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|ADDS-IP-adres|IP-adres van de eerste ADDS VM. Deze waarde wordt gebruikt als aangepaste VNET DNS.|
|Key Vault-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-workload-kv-rg` om de resourcegroep uniek te maken.|
|Key Vault-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Key Vault-sjabloon|Resource Manager-sjabloon|AAD-object-ID|De AAD-object-id van het account dat toegang moet hebben tot het Key Vault-exemplaar. Geen standaardwaarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure-portal, zoekt en selecteert u Gebruikers onder _Services_. Gebruik het vak _Naam_ om de accountnaam te filteren en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram Klik om te kopiëren naast de _object-ID_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Key Vault-SKU|Hiermee geeft u de SKU op van de Key Vault die is gemaakt. De standaardwaarde is _Premium_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Azure SQL Server-beheerder|De gebruikersnaam die wordt gebruikt voor toegang tot Azure SQL Server. Moet overeenkomen met de eigenschapswaarde in de **Azure SQL Database-sjabloon**. De standaardwaarde is _sql-admin-user_.|
|Azure SQL Database-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-workload-azsql-rg` om de resourcegroep uniek te maken.|
|Azure SQL Database-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Azure SQL Server-beheerder|Gebruikersnaam voor de Azure SQL Server. Moet overeenkomen met de eigenschapswaarde in de **Key Vault-sjabloon**. De standaardwaarde is _sql-admin-user_.|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Wachtwoord van Azure SQL Server-beheerder (resource-ID van Key Vault)|De resource-ID van de Key Vault. Gebruik '/subscriptions/{subscriptionId}/resourceGroups/{orgName}-workload-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv ' en vervang door `{subscriptionId}` uw abonnements-id en `{orgName}` met de para meter voor de naam van de **organisatie** .|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Wachtwoord van Azure SQL Server-beheerder (naam van Key Vault-geheim)|Gebruikersnaam van SQL Server-beheerder. Moet overeenkomen met de eigenschapswaarde van de **Gebruikersnaam van Azure SQL Server-beheerder** in de **Key Vault-sjabloon**.|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Object-id van AAD-beheerder|AAD-object-id van de gebruiker die wordt toegewezen als een Active Directory-beheerder. Geen standaardwaarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure-portal, zoekt en selecteert u Gebruikers onder _Services_. Gebruik het vak _Naam_ om de accountnaam te filteren en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram Klik om te kopiëren naast de _object-ID_.|
|Azure SQL Database-sjabloon|Resource Manager-sjabloon|Aanmeldgegevens van AAD-beheerder|Op dit moment kunnen Microsoft-accounts (zoals live.com of outlook.com) niet als beheerder worden ingesteld. Alleen gebruikers en beveiligingsgroepen in uw organisatie kunnen als beheerder worden ingesteld. Geen standaardwaarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure-portal, zoekt en selecteert u Gebruikers onder _Services_. Gebruik het vak _Naam_ om de accountnaam te filteren en selecteer dat account. Kopieer op de pagina _Gebruikersprofiel_ de _Gebruikersnaam_.|
|App Service Environment-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-workload-ase-rg` om de resourcegroep uniek te maken.|
|App Service Environment-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Sjabloon voor App Service Environment|Resource Manager-sjabloon|Domeinnaam|De naam van de Active Directory die door het voorbeeld is gemaakt. De standaardwaarde is _contoso.com_.|
|Sjabloon voor App Service Environment|Resource Manager-sjabloon|ASE-locatie|Locatie van App Service Environment. De standaardwaarde is _US - west 2_.|
|Sjabloon voor App Service Environment|Resource Manager-sjabloon|Behoud van Application Gateway-logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het blauwdrukvoorbeeld voor ISO 27001 conforme App Service Environment-/SQL Database-workloads hebt bekeken, kunt u naar de volgende artikelen gaan voor informatie over de architectuur en het toewijzen van de beheeropties:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - overzicht](./index.md)
> [Blauwdruk voor ISO 27001 conforme App Service Environment-/SQL Database-workloads - Toewijzen van beheeropties](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
