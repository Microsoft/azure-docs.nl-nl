---
title: Azure Managed HSM-logboekregistratie
description: Deze zelfstudie helpt u op weg met de Managed HSM-logboekregistratie.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: mbaldwin
ms.openlocfilehash: 0d5749894fd277ff6a2f77e3db9721e6989d72ac
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109234"
---
# <a name="managed-hsm-logging"></a>Managed HSM-logboekregistratie 

Nadat u één of meer Managed HSM's hebt gemaakt, wilt u wellicht bewaken hoe, wanneer en voor wie uw HSM's toegankelijk zijn. U kunt dit doen door logboekregistratie in te schakelen, waardoor informatie wordt opgeslagen in een Azure-opslagaccount dat u opgeeft. Er wordt automatisch een nieuwe container genaamd **insights-logs-auditevent** gemaakt voor uw opgegeven opslagaccount. U kunt ditzelfde opslagaccount gebruiken om logboeken voor meerdere Managed HSM's te verzamelen.

U kunt uw logboekgegevens (maximaal) tien minuten, nadat de Managed HSM-bewerking is uitgevoerd, bekijken. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden voor Azure-toegangsbeheer om de logboeken te beveiligen door te beperken wie er toegang heeft.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Deze zelfstudie helpt u op weg met de Managed HSM-logboekregistratie. U zult een opslagaccount maken, logboekregistratie inschakelen en de verzamelde logboekgegevens interpreteren.  

> [!NOTE]
> Deze zelfstudie bevat geen instructies voor het maken van Managed HSM's of sleutels. Dit artikel biedt Azure CLI-instructies voor het bijwerken van diagnostische logboeken.

## <a name="prerequisites"></a>Vereisten

U moet over de volgende items beschikken om de stappen in dit artikel uit te kunnen voeren:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI versie 2.12.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).
* Een beheerde HSM in uw abonnement. Zie [Quickstart: Een Managed HSM inrichten en activeren met behulp van de Azure CLI](quick-create-cli.md) om een Managed HSM in te richten en te activeren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Verbinding maken met uw Azure-abonnement

De eerste stap in het instellen van sleutelregistratie is Azure CLI te laten wijzen naar de Managed HSM waarvoor u logboekregistratie wilt inschakelen.

```azurecli-interactive
az login
```

Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over opties voor aanmelding via de CLI

U moet mogelijk het abonnement opgeven dat u hebt gebruikt om uw Managed HSM te maken. Voer de volgende opdracht in als u de abonnementen voor uw account wilt zien:

## <a name="identify-the-managed-hsm-and-storage-account"></a>De Managed HSM en het opslagaccount identificeren

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Logboekregistratie inschakelen

Als u logboekregistratie wilt inschakelen voor Managed HSM, gebruikt u de opdracht **Maak instellingen voor AZ Monitor-diagnostiek**, samen met de variabelen die we voor het nieuwe opslagaccount en de Managed HSM hebben gemaakt. We moeten de markering **-Ingeschakeld** instellen op **$true** en de categorie instellen op **AuditEvent** (de enige categorie voor logboekregistratie van HSM logging):

Deze uitvoer bevestigt dat logboekregistratie nu is ingeschakeld voor uw Managed HSM. Uw Managed HSM slaat informatie op in uw opslagaccount.

U kunt eventueel een retentiebeleid instellen voor uw logboeken, zodat oudere logboeken automatisch worden verwijderd. Stel bijvoorbeeld een retentiebeleid in door de vlag **-RetentionEnabled** in te stellen op **$true**, en stel de parameter **-RetentionInDays** in op **90**, zodat logboeken die ouder zijn dan 90 dagen automatisch worden verwijderd.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Wat wordt in het logboek vastgelegd?

* Alle geverifieerde REST-API-aanvragen, ook mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of ongeldige aanvragen.
* Beheerde vlak bewerkingen voor de beheerde HSM-resource zelf, inclusief het maken, verwijderen en bijwerken van kenmerken zoals Tags.
* Beveiligingsdomein-gerelateerde bewerkingen, zoals initialiseren en downloaden, herstel initialiseren en uploaden
* Volledige HSM-back-ups, herstel en selectieve herstelbewerkingen
* Rollen beheer bewerkingen, zoals het maken/weer geven/verwijderen van roltoewijzingen en het maken/weer geven/verwijderen van aangepaste roldefinities
* Bewerkingen op sleutels, waaronder:
  * Het maken, wijzigen of verwijderen van de sleutels.
  * Ondertekenen, controleren, versleutelen, ontsleutelen, inpakken en uitpakken van sleutels, sleutels weergeven.
  * Sleutelback-up, herstellen, opschonen
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden daarvan zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn, of die een ongeldig token hebben.  

## <a name="access-your-logs"></a>Toegang tot uw logboeken

Managed HSM-logboeken worden opgeslagen in de container **insights-logs-auditevent** in het opslagaccount dat u hebt opgegeven. Als u de logboeken wilt bekijken, moet u blobs downloaden. Zie [Maken, downloaden en weergeven van blobs met Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md) voor meer informatie over Azure Storage.

Afzonderlijke blobs worden opgeslagen als tekst die is opgemaakt als een JSON. Laten we eens naar een voorbeeld van een logboekvermelding kijken. In het onderstaande voorbeeld ziet u de logboekvermelding wanneer een aanvraag voor het maken van een volledige back-up wordt verzonden naar de Managed HSM.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```



## <a name="use-azure-monitor-logs"></a>Azure Monitor-logboeken gebruiken

Met de Key Vault-oplossing in Azure Monitor-logboeken kunt u de **AuditEvent**-logboeken van Managed HSM controleren. In Azure Monitor-logboeken kunt u logboekquery’s gebruiken om gegevens te analyseren en de informatie op te halen die u nodig hebt.

Zie [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [best practices](best-practices.md) voor het inrichten en gebruiken van een Managed HSM
- Meer informatie over [Hoe u een back-up kunt maken van een Managed HSM en een Managed HSM kunt herstellen](backup-restore.md)
