---
title: Implementatie van blauwdrukvoorbeeld voor ISO 27001 conforme gedeelde services
description: Implementeer stappen voor het blauwdrukvoorbeeld voor ISO 27001 conforme gedeelde services, waaronder details van de parameters voor blauwdrukartefacten.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577313"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Overzicht van de voorbeeldblauwdruk voor ISO 27001 conforme gedeelde services

Als u het Azure-blauwdrukvoorbeeld voor ISO 27001 conforme gedeelde services wilt implementeren, moet u de volgende stappen uitvoeren:

> [!div class="checklist"]
> - Een nieuwe blauwdruk maken op basis van het voorbeeld
> - Uw kopie van het voorbeeld markeren als **Gepubliceerd**
> - Uw kopie van de blauwdruk toewijzen aan een bestaand abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free) aan voordat u begint.

## <a name="create-blueprint-from-sample"></a>Een blauwdruk maken op basis van een voorbeeld

Implementeer eerst het blauwdrukvoorbeeld door een nieuwe blauwdruk in uw omgeving te maken op basis van het voorbeeld.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Op de pagina **Aan de slag** aan de linkerkant selecteert u de knop **Maken** onder _Een blauwdruk maken_.

1. Zoek het **ISO 27001: Blauwdrukvoorbeeld voor** Gedeelde services onder _Andere voorbeelden_ en selecteer **Dit voorbeeld gebruiken**.

1. Voer de _Basisinstellingen_ van het blauwdrukvoorbeeld in:

   - **Naam van blauwdruk**: Geef een naam op voor uw kopie van het ISO 27001-blauwdrukvoorbeeld voor gedeelde services.
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
     - **Voorvoegsel van subnetadres van gedeelde services**: Geef de waarde voor de CIDR-notatie op om de geïmplementeerde resources met elkaar te verbinden.
     - **Locatie van gedeelde services**: Hiermee bepaalt u op welke locatie de artefacten worden geïmplementeerd. Niet alle services zijn op alle locaties beschikbaar. Artefacten die dergelijke services implementeren, bieden een parameteroptie voor de locatie waarop dat artefact moet worden geïmplementeerd.
     - **Toegestane locatie (Beleid: Blauwdrukinitiatief voor ISO 27001)** : Waarde die de toegestane locaties voor resourcegroepen en resources aangeeft.
     - **Log Analytics-wekruimte voor VM-agenten (Beleid: Blauwdrukinitiatief voor ISO 27001)** : Hiermee geeft u de resource-ID van een werkruimte op. Deze parameter gebruikt een `concat`-functie om de resource-ID te maken.

   - Artefactparameters

     De parameters die in deze sectie worden gedefinieerd, zijn van toepassing op het artefact waaronder de parameter is gedefinieerd. Deze parameters zijn [dynamische parameters](../../concepts/parameters.md#dynamic-parameters), aangezien ze zijn gedefinieerd tijdens de toewijzing van de blauwdruk. Zie de [tabel met artefactparameters](#artifact-parameters-table) voor een volledige lijst met artefactparameters en hun beschrijvingen.

1. Zodra alle parameters zijn ingevoerd, selecteert u **Toewijzen** onder aan de pagina. De blauwdruktoewijzing wordt gemaakt en de implementatie van het artefact begint. De implementatie duurt circa één uur. Open de blauwdruktoewijzing om de status van de implementatie te controleren.

> [!WARNING]
> De Azure Blueprints-service en de ingebouwde blauwdrukvoorbeelden zijn **gratis**. Azure-resources zijn [geprijsd per product](https://azure.microsoft.com/pricing/). Gebruik de [prijscalculator](https://azure.microsoft.com/pricing/calculator/) om de kosten te schatten voor het uitvoeren van resources die door dit blauwdrukvoorbeeld zijn geïmplementeerd.

## <a name="artifact-parameters-table"></a>Tabel met artefactparameters

In de volgende tabel ziet u een lijst met de blauwdrukartefactparameters:

|Naam van het artefact|Type artefact|Parameternaam|Beschrijving|
|-|-|-|-|
|\[Preview\]: Log Analytics-agent voor Linux-VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Linux-besturingssysteem die aan het bereik kunnen worden toegevoegd|(Optioneel) standaardwaarde is _["none"]_ .|
|\[Preview\]: Log Analytics-agent voor virtuele Linux-machines implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Linux-besturingssysteem die aan het bereik kunnen worden toegevoegd|(Optioneel) standaardwaarde is _["none"]_ .|
|\[Preview\]: Log Analytics-agent voor Windows-VM-schaalsets (VMSS) implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Windows-besturingssysteem die aan het bereik kunnen worden toegevoegd|(Optioneel) standaardwaarde is _["none"]_ .|
|\[Preview\]: Log Analytics-agent voor Windows-VM's implementeren|Beleidstoewijzing|Optioneel: Lijst met VM-installatiekopieën met ondersteuning van het Windows-besturingssysteem die aan het bereik kunnen worden toegevoegd|(Optioneel) standaardwaarde is _["none"]_ .|
|Toegestane brontypen|Beleidstoewijzing|Toegestane brontypen|Lijst met resourcetypen die mogen worden geïmplementeerd. Deze lijst bestaat uit alle resourcetypen die in gedeelde services worden geïmplementeerd.|
|Toegestane opslagaccount-SKU's|Beleidstoewijzing|Toegestane opslag-SKU's|Lijst met toegestane opslagaccount-SKU's voor diagnostische logboeken. De standaardwaarde is _["Standard_LRS"]_ .|
|Toegestane SKU's van virtuele machines|Beleidstoewijzing|Lijst met SKU's voor virtuele machines die mogen worden geïmplementeerd. De standaardwaarde is _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Blauwdrukinitiatief voor ISO 27001|Beleidstoewijzing|Resourcetypen voor het controleren van diagnostische logboeken|Lijst met resourcetypen die moeten worden gecontroleerd als de instelling voor diagnostische logboeken niet is ingeschakeld. Acceptabele waarden vindt u in de [schema's van diagnostische logboeken van Azure Monitor](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Log Analytics-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-sharedsvsc-log-rg` om de resourcegroep uniek te maken.|
|Log Analytics-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Servicelaag|Hiermee stelt u de laag van de Log Analytics-werkruimte in. Standaardwaarde is _PerNode_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Log Analytics-sjabloon|Resource Manager-sjabloon|Locatie|De regio die wordt gebruikt voor het maken van de Log Analytics-werkruimte. De standaardwaarde is _US - west 2_.|
|Resourcegroep van netwerk|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-sharedsvcs-net-rg` om de resourcegroep uniek te maken.|
|Resourcegroep van netwerk|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Sjabloon voor Azure Firewall|Resource Manager-sjabloon|Privé-IP-adres voor Azure-firewall|Hiermee configureert u het privé IP-adres van de [Azure-firewall](../../../../firewall/overview.md). Deze waarde wordt ook gebruikt als standaardrouteringstabel voor subnets van gedeelde services. Moet deel uitmaken van de CIDR-notatie die is gedefinieerd in het **voorvoegsel van het subnet van de Azure Firewall**. De standaardwaarde is _10.0.4.4_.|
|Sjabloon voor Azure Firewall|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Sjabloon voor netwerkbeveiligingsgroep|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van adres voor virtueel netwerk|De CIDR-notatie voor het virtuele netwerk. De standaardwaarde is _10.0.0.0/16_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|DDoS-beveiliging voor virtueel netwerk inschakelen|Hiermee configureert u DDoS-beveiliging voor het virtuele netwerk. De standaardwaarde is _true_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van gedeelde services|De CIDR-notatie voor subnets van gedeelde services. De standaardwaarde is _10.0.0.0/24_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van DMZ|De CIDR-notatie voor het DMZ-subnet. De standaardwaarde is _10.0.1.0/24_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van toepassingsgateway|De CIDR-notatie voor het subnet van de toepassingsgateway. De standaardwaarde is _10.0.2.0/24_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van gateway voor virtueel netwerk|De CIDR-notatie voor het subnet van de virtuele netwerkgateway. De standaardwaarde is _10.0.3.0/24_.|
|Sjabloon voor virtueel netwerk en routeringstabel|Resource Manager-sjabloon|Voorvoegsel van subnetadres van Azure Firewall|De CIDR-notatie voor het subnet van de [Azure firewall](../../../../firewall/overview.md). Moet de parameter **Privé-IP-adres voor Azure firewall** bevatten.|
|Key Vault-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-sharedsvcs-kv-rg` om de resourcegroep uniek te maken.|
|Key Vault-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Jumpbox-beheerder|Gebruikersnaam voor de jumpbox. Moet overeenkomen met de eigenschapswaarde in de **JumpBox-sjabloon**. De standaardwaarde is _jb-admin-user_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|SSH-sleutel of wachtwoord van de Jumpbox- beheerder|De sleutel of het wachtwoord voor het account op de jumpbox. Moet overeenkomen met de eigenschapswaarde in de **JumpBox-sjabloon**. Geen standaardwaarde en mag niet leeg zijn.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van domeinbeheerder|De gebruikersnaam die wordt gebruikt voor toegang tot de Active Directory-VM en om andere VM's aan een domein toe te voegen. Moet overeenkomen met de eigenschapswaarde van **Domeinbeheerder** in de **Active Directory Domain Services-sjabloon**. De standaardwaarde is _domain-admin-user_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder|Wachtwoord van domeinbeheerder. Geen standaardwaarde en mag niet leeg zijn.|
|Key Vault-sjabloon|Resource Manager-sjabloon|AAD-object-ID|De AAD-object-id van het account dat toegang moet hebben tot het Key Vault-exemplaar. Geen standaardwaarde en mag niet leeg zijn. Als u deze waarde wilt vinden in de Azure-portal, zoekt en selecteert u Gebruikers onder _Services_. Gebruik het vak _Naam_ om de accountnaam te filteren en selecteer dat account. Selecteer op de pagina _Gebruikersprofiel_ het pictogram Klik om te kopiëren naast de _object-ID_.  |
|Key Vault-sjabloon|Resource Manager-sjabloon|Behoud van logboeken in dagen|Gegevensretentie in dagen. De standaardwaarde is _365_.|
|Key Vault-sjabloon|Resource Manager-sjabloon|Key Vault-SKU|Hiermee geeft u de SKU op van de Key Vault die is gemaakt. De standaardwaarde is _Premium_.|
|JumpBox-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-sharedsvcs-jb-rg` om de resourcegroep uniek te maken.|
|JumpBox-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|Gebruikersnaam van Jumpbox-beheerder|De gebruikersnaam die wordt gebruikt voor toegang tot Jumpbox-VM's. Moet overeenkomen met de eigenschapswaarde in de **Key Vault-sjabloon**. De standaardwaarde is _jb-admin-user_.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerderswachtwoord (resource-ID van de Key Vault)|De resource-ID van de Key Vault. Gebruik "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" en vervang `{subscriptionId}` door uw abonnements-ID en `{orgName}` door de blauwdrukparameter van de **organisatienaam**.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|JumpBox-beheerderswachtwoord (geheime naam van de Key Vault)|De gebruikersnaam van de JumpBox-beheerder. Moet overeenkomen met de eigenschapswaarde van de **Gebruikersnaam van JumpBox-beheerder** in de **Key Vault-sjabloon**.|
|Jumpbox-sjabloon|Resource Manager-sjabloon|Jumpbox-besturingssysteem|Hiermee bepaalt u het besturingssysteem van de jumpbox-VM. De standaardwaarde is _Windows_.|
|Active Directory Domain Services-resourcegroep|Resourcegroep|Naam|**Vergrendeld**: voegt de **organisatienaam** samen met `-sharedsvcs-adds-rg` om de resourcegroep uniek te maken.|
|Active Directory Domain Services-resourcegroep|Resourcegroep|Locatie|**Vergrendeld**: maakt gebruik van de blauwdrukparameter.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Gebruikersnaam van domeinbeheerder|Gebruikersnaam voor de ADDS-jumpbox. Moet overeenkomen met de eigenschapswaarde in de **Key Vault-sjabloon**. De standaardwaarde is _adds-admin-user_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder (resource-ID van Key Vault)|De resource-ID van de Key Vault. Gebruik "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" en vervang `{subscriptionId}` door uw abonnements-ID en `{orgName}` door de blauwdrukparameter van de **organisatienaam**.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder (geheime naam van Key Vault)|De gebruikersnaam van de domeinbeheerder. Moet overeenkomen met de eigenschapswaarde van **Gebruikersnaam van domeinbeheerder** in de **Key Vault-sjabloon**.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Domeinnaam|De naam van de Active Directory die door het voorbeeld is gemaakt. De standaardwaarde is _contoso.com_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Gebruikersnaam van domeinbeheerder|Gebruikersnaam voor het AD-beheerdersaccount en voor het toevoegen van apparaten aan het AD-domein. Moet overeenkomen met de eigenschapswaarde van de **AD-beheerder** in de **Key Vault-sjabloon**. De standaardwaarde is _domain-admin-user_.|
|Sjabloon voor Active Directory Domain Services|Resource Manager-sjabloon|Wachtwoord van domeinbeheerder|Stel de details van de Key Vault in voor het opslaan van het wachtwoord. Geen standaardwaarde en mag niet leeg zijn.|

## <a name="next-steps"></a>Volgende stappen

Nu u de stappen voor het implementeren van het blauwdrukvoorbeeld voor ISO 27001 conforme gedeelde services hebt doorgenomen, raadpleegt u de volgende artikelen voor informatie over de architectuur en de toewijzing van besturingselementen:

> [!div class="nextstepaction"]
> [Blauwdruk voor ISO 27001 conforme gedeelde services - overzicht](./index.md)
> [Blauwdruk voor ISO 27001 conforme gedeelde services - toewijzing van beheeropties](./control-mapping.md)

Aanvullende artikelen over blauwdrukken en het gebruik hiervan:

- Meer informatie over de [levenscyclus van een blauwdruk](../../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../../concepts/resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../../how-to/update-existing-assignments.md).
