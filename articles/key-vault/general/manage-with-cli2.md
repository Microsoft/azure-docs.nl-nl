---
title: Azure Key Vault beheren met CLI - Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om algemene taken in Key Vault te automatiseren met behulp van de Azure CLI
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: d4037e66e653bc6e958020a5ef8722f2febb53d0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772176"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Key Vault beheren met behulp van de Azure CLI 

In dit artikel wordt beschreven hoe u aan de slag kunt gaan met Azure Key Vault met behulp van de Azure CLI.  U krijgt informatie over het volgende:

- Een beveiligde container (een kluis) maken in Azure
- Een sleutel, geheim of certificaat toevoegen aan de sleutelkluis
- Een toepassing registreren met Azure Active Directory
- Een toepassing machtigen om een sleutel of geheim te gebruiken
- Geavanceerd toegangsbeleid voor sleutelkluizen instellen
- Werken met HSM's (Hardware Security Modules)
- De sleutelkluis, en de bijbehorende sleutels en geheimen, verwijderen
- Diverse platformoverschrijdende Azure CLI-opdrachten (opdrachtregelinterface)


Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.

> [!NOTE]
> Dit artikel bevat geen instructies voor het schrijven van de Azure-toepassing die in een van de stappen wordt gebruikt, waarin wordt weergegeven hoe u een toepassing toestemming geeft voor het gebruik van een sleutel of geheim in de sleutelkluis.
>

Raadpleeg [Wat is Azure Key Vault?](overview.md) voor een overzicht van Azure Key Vault. Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-opdrachten in dit artikel wilt gebruiken, hebt u het volgende nodig:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI (opdrachtregelinterface) 2.0 of hoger. Zie [De Azure CLI installeren](/cli/azure/install-azure-cli) om de meest recente versie te installeren.
* Een toepassing die wordt geconfigureerd voor het gebruik van de sleutel of het wachtwoord dat u in dit artikel maakt. Er is een voorbeeldtoepassing beschikbaar in het [Microsoft Downloadcentrum](https://www.microsoft.com/download/details.aspx?id=45343). Raadpleeg het meegeleverde Leesmij-bestand voor instructies.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Hulp krijgen voor de platformoverschrijdende Azure CLI (opdrachtregelinterface)

In dit artikel wordt ervan uitgegaan dat u bekend bent met de opdrachtregelinterface (bash, terminal, opdrachtprompt).

De parameter --help of -h kan worden gebruikt om Help-documentatie voor specifieke opdrachten te bekijken. U kunt ook de indeling voor Azure-Help [opdracht] [opties] gebruiken. Raadpleeg de Help-documentatie als u niet zeker weet welke parameters nodig zijn voor een opdracht. De volgende opdrachten retourneren bijvoorbeeld allemaal dezelfde informatie:

```azurecli
az account set --help
az account set -h
```

U kunt ook de volgende artikelen lezen om vertrouwd te raken met Azure Resource Manager in de platformoverschrijdende Azure CLI (opdrachtregelinterface):

* [Azure-CLI installeren](/cli/azure/install-azure-cli)
* [Aan de slag met de Azure CLI](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Een beveiligde container (een kluis) maken in Azure

Kluizen zijn beveiligde containers ondersteund met HSM's (Hardware Security Modules). Kluizen verminderen de kans op onbedoeld verlies van beveiligingsinformatie door de opslag van toepassingsgeheimen te centraliseren. Sleutelkluizen regelen en registreren ook de toegang tot alles wat erin is opgeslagen. Azure Key Vault kan het aanvragen en vernieuwen van Transport Layer Security-certificaten (TLS) verwerken, met behulp van de functies die vereist zijn voor een robuuste oplossing voor het beheren van de levenscyclus van certificaten. In de volgende stappen maakt u een kluis.

### <a name="connect-to-your-subscriptions"></a>Verbinding maken met uw abonnementen

Gebruik de volgende opdracht om u interactief aan te melden:

```azurecli
az login
```
Als u zich wilt aanmelden met een organisatieaccount, kunt u uw gebruikersnaam en wachtwoord opgeven.

```azurecli
az login -u username@domain.com -p password
```

Als u meer dan één abonnement hebt en wilt opgeven welk abonnement moet worden gebruikt, typt u het volgende om de abonnementen voor uw account te zien:

```azurecli
az account list
```

Geef een abonnement op met de abonnementsparameter.

```azurecli
az account set --subscription <subscription name or ID>
```

Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor meer informatie over het configureren van de platformoverschrijdende Azure CLI (opdrachtregelinterface).

### <a name="create-a-new-resource-group"></a>Een nieuwe resourcegroep maken

Als u Azure Resource Manager gebruikt, worden alle gerelateerde resources gemaakt binnen een resourcegroep. U kunt een sleutelkluis maken in een bestaande resourcegroep. Als u een nieuwe resourcegroep wilt gebruiken, kunt u een nieuwe maken.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

De eerste parameter is de naam van de resourcegroep en de tweede parameter is de locatie. Een lijst met alle mogelijke locatietypen ophalen:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>De Key Vault-resourceprovider registreren

 Wanneer u een nieuwe sleutelkluis wilt maken, kan het volgende foutbericht worden weergegeven: Het abonnement is niet geregistreerd om de naamruimte Microsoft.KeyVault te gebruiken. Als dit bericht verschijnt, controleert u of de Key Vault-resourceprovider is geregistreerd in uw abonnement. Dit is een eenmalige bewerking voor elk abonnement.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Een sleutelkluis maken

Gebruik de opdracht `az keyvault create` om een sleutelkluis te maken. Het script heeft drie verplichte parameters: een resourcegroepnaam, een sleutelkluisnaam, en de geografische locatie.

Als u een nieuwe kluis wilt maken met de naam **ContosoKeyVault** in de resourcegroep **ContosoResourceGroup**, met als locatie **Azië - oost**, typt u: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

In de uitvoer van deze opdracht worden de eigenschappen weergegeven van de sleutelkluis die u hebt gemaakt. De twee belangrijkste eigenschappen zijn:

* **name**: In het voorbeeld is de naam ContosoKeyVault. U gebruikt deze naam voor andere Key Vault-opdrachten.
* **vaultUri**: in het voorbeeld is de URI https://contosokeyvault.vault.azure.net. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze sleutelkluis uit te voeren. Op dit moment is nog niemand anders gemachtigd.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Een sleutel, geheim of certificaat toevoegen aan de sleutelkluis

Als u wilt dat Azure Key Vault een softwarematig beveiligde sleutel voor u maakt, gebruikt u de opdracht `az key create`.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Als u een bestaande sleutel hebt in een PEM-bestand, kunt u deze uploaden naar Azure Key Vault. U kunt ervoor kiezen om de sleutel te beveiligen met software of HSM. In dit voorbeeld wordt de sleutel geïmporteerd uit het PEM-bestand en beveiligd met software, met het wachtwoord hVFkk965BuUv.

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

U kunt nu de URI van de sleutel gebruiken om te verwijzen naar de sleutel die u hebt gemaakt of geüpload naar Azure Key Vault. Gebruik `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` om altijd de meest recente versie op te halen. Gebruik https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] om deze specifieke versie op te halen. Bijvoorbeeld `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Voeg een geheim toe aan de kluis, wat een wachtwoord is met de naam SQLPassword, en die de waarde hVFkk965BuUv heeft voor Azure-sleutelkluizen. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Verwijs naar dit wachtwoord door de bijbehorende URI te gebruiken. Gebruik **https://ContosoVault.vault.azure.net/secrets/SQLPassword** om altijd de meest recente versie op te halen, en https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] om deze specifieke versie op te halen. Bijvoorbeeld **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Importeer een certificaat in de kluis met behulp van een PEM- of PFX-bestand.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Laten we kijken naar de sleutel, het geheim of het certificaat dat u hebt gemaakt:

* Als u de sleutels wilt weergeven, typt u: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Als u de geheimen wilt weergeven, typt u: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Als u certificaten wilt weergeven, typt u: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Een toepassing registreren met Azure Active Directory

Deze stap wordt doorgaans uitgevoerd door een ontwikkelaar op een afzonderlijke computer. De stap is niet specifiek voor Azure Key Vault maar is hier ter informatie opgenomen. Uw account, de kluis en de toepassing moeten zich in dezelfde Azure-map bevinden om de app-registratie te voltooien.

Toepassingen die gebruikmaken van een sleutelkluis moeten een token van Azure Active Directory gebruiken om zich te verifiëren.  De eigenaar van de toepassing moet deze eerst registreren in Azure Active Directory. Aan het eind van het registratieproces ontvangt de eigenaar de volgende waarden:

- Een **Toepassings-id** (ook bekend als de AAD-client-id of appID)
- Een **verificatiesleutel** (ook wel het gedeelde geheim genoemd). 

De toepassing moet beide waarden aan Azure Active Directory verstrekken om een token te verkrijgen. Hoe een toepassing is geconfigureerd om een token op te halen, is afhankelijk van de toepassing. In de [Key Vault-voorbeeldtoepassing](https://www.microsoft.com/download/details.aspx?id=45343) moet de eigenaar van de toepassing deze waarden instellen in het bestand app.config.

Voor gedetailleerde stappen voor het registreren van een toepassing bij Azure Active Directory raadpleegt u de artikelen [Toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-register-app.md), [De portal gebruiken om een Azure Active Directory-toepassing en service-principal te maken die toegang hebben tot resources](../../active-directory/develop/howto-create-service-principal-portal.md) en [Een Azure-service-principal maken met de Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Een toepassing registreren in Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Een toepassing machtigen om een sleutel of geheim te gebruiken

Gebruik de opdracht `az keyvault set-policy` om de toepassing toegang te verlenen tot de sleutel of het geheim in de kluis.

Als uw kluisnaam bijvoorbeeld ContosoKeyVault is, en de toepassing een appID 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed heeft, gebruikt u de volgende opdracht om de toepassing te machtigen om te ontsleutelen en te ondertekenen met sleutels in uw kluis:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Als u dezelfde toepassing wilt machtigen om geheimen in uw kluis te lezen, typt u de volgende opdracht:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Geavanceerd toegangsbeleid voor sleutelkluizen instellen.

Gebruik [az keyvault update](/cli/azure/keyvault#az_keyvault_update) om geavanceerd toegangsbeleid voor de sleutelkluis in te stellen.

 Key Vault inschakelen voor implementatie: Stelt virtuele machines in staat certificaten uit de kluis op te halen die zijn opgeslagen als geheimen.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Key Vault inschakelen voor schijfversleuteling: Vereist als u de kluis gebruikt voor Azure Disk Encryption.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Key Vault inschakelen voor sjabloonimplementatie: Stelt Resource Manager in staat geheimen uit de kluis op te halen.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Werken met HSM's (Hardware Security Modules)

Voor de zekerheid kunt u sleutels in HSM's (Hardware Security Module) importeren of genereren die de HSM-grens nooit verlaten. De HSM's zijn FIPS 140-2 Level 2-gevalideerde modules. Als deze vereiste niet van toepassing is op u, kunt u deze sectie overslaan en gaat u naar: De sleutelkluis en de bijbehorende sleutels en geheimen verwijderen.

Als u deze met HSM beveiligde sleutels wilt maken, moet u beschikken over een kluisabonnement dat ondersteuning biedt voor sleutels die zijn beveiligd met HSM.

Wanneer u de sleutelkluis maakt, voegt u de parameter sku toe:

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

U kunt softwarematige beveiligde sleutels (zoals hiervoor) en met HSM beveiligde sleutels toevoegen aan deze kluis. Als u een sleutel wilt maken die is beveiligd met HSM, stelt u de parameter Destination in op HSM:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

U kunt de volgende opdracht gebruiken om een sleutel uit een PEM-bestand naar de computer te importeren. Met deze opdracht wordt de sleutel geïmporteerd in HSM's in de Sleutelkluis-service:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

Met de volgende opdracht wordt een BYOK-pakket (Bring Your Own Key) geïmporteerd. Hiermee kunt u de sleutel in uw lokale HSM genereren en overdragen naar HSM's in de Sleutelkluis-service, zonder dat de sleutel de HSM-grens verlaat:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Zie [Met HSM beveiligde sleutels gebruiken met Azure Key Vault](../keys/hsm-protected-keys.md) voor gedetailleerde instructies over het genereren van dit BYOK-pakket.

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>De sleutelkluis, en de bijbehorende sleutels en geheimen, verwijderen

Als u de sleutelkluis, en de bijbehorende sleutels of geheimen, niet meer nodig hebt, kunt u de sleutelkluis verwijderen met behulp van de opdracht `az keyvault delete`:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

U kunt ook een volledige Azure-resourcegroep verwijderen. Deze bevat de sleutelkluis en alle andere resources die u hebt opgenomen in de groep:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diverse platformoverschrijdende Azure CLI-opdrachten (opdrachtregelinterface)

Overige opdrachten die handig kunnen zijn voor het beheren van Azure Key Vault:

Met deze opdracht worden alle sleutels en geselecteerde eigenschappen weergegeven in een tabel:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Met deze opdracht wordt een volledige lijst met eigenschappen voor de opgegeven sleutel weergegeven:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Met deze opdracht worden alle geheime namen en geselecteerde eigenschappen weergegeven in een tabel:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Hier is en voorbeeld van hoe u een specifieke sleutel verwijdert:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Hier is een voorbeeld van hoe u een specifiek geheim verwijdert:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Naslaginformatie voor Key Vault CLI](/cli/azure/keyvault) voor de volledige naslaginformatie van Azure CLI over sleutelkluisopdrachten.

- Raadpleeg [de Ontwikkelaarshandleiding voor Azure Key Vault](developers-guide.md) voor naslaginformatie over programmeren.

- Raadpleeg [Met HSM beveiligde sleutels gebruiken met Azure Key Vault](../keys/hsm-protected-keys.md) voor informatie over Azure Key Vault en HSM's.
