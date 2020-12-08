---
title: Wat u moet doen als een onderbreking van de Azure-service invloed heeft op beheerde HSM - Azure Key Vault | Microsoft Docs
description: Meer informatie over wat u moet doen als een onderbreking van de Azure-service invloed heeft op beheerde HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 69a0272061d8518119114e8fe7b023c889639844
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96171555"
---
# <a name="managed-hsm-disaster-recovery"></a>Herstel na noodgeval van beheerde HSM

U kunt een exacte replica van uw HSM maken als het origineel verloren is gegaan of niet beschikbaar is vanwege een van de volgende redenen:

- Het is verwijderd en vervolgens opgeschoond.
- Een onherstelbare fout in de regio heeft tot gevolg dat alle ledenpartities zijn vernietigd.

U kunt het HSM-exemplaar in dezelfde of een andere regio opnieuw maken als u over het volgende beschikt:
- Het [beveiligingsdomein](security-domain.md) van de bron-HSM.
- De persoonlijke sleutels (ten minste het quorumnummer) waarmee het beveiligingsdomein wordt versleuteld.
- De meest recente, volledige [back-up](backup-restore.md) van de bron-HSM.

Dit zijn de stappen van de procedure voor herstel na noodgeval:

1. Maak een nieuw exemplaar.
1. Activeer Herstel van beveiligingsdomein. Er wordt een nieuw RSA-sleutelpaar (uitwisselingssleutel voor beveiligingsdomeinen) gegenereerd voor de overdracht van het beveiligingsdomein. Deze wordt als reactie verzonden, waarna een SecurityDomainExchangeKey (openbare sleutel) wordt gedownload.
1. Maak en upload het bestand voor de overdracht van het beveiligingsdomein. U hebt de persoonlijke sleutels nodig waarmee het beveiligingsdomein wordt versleuteld. De persoonlijke sleutels worden lokaal gebruikt en nooit in dit proces overgedragen.
1. Maak een back-up van de nieuwe HSM. Er is een back-up vereist voordat een herstelbewerking kan worden uitgevoerd, ook wanneer de HSM leeg is. Met back-ups kan er eenvoudig worden teruggedraaid.
1. De recente back-up van de bron-HSM herstellen

Met deze stappen kunt u de inhoud van de HSM handmatig repliceren naar een andere regio. De HSM-naam (en de URI van het service-eindpunt) wijken af, dus u moet mogelijk de configuratie van de toepassing aanpassen om deze sleutels vanaf een andere locatie te gebruiken.

## <a name="create-a-new-managed-hsm"></a>Een nieuwe beheerde HSM maken

Gebruik de opdracht `az keyvault create` om een beheerde HSM te maken. Het script heeft drie verplichte parameters: een resourcegroepnaam, een HSM-naam, en de geografische locatie.

U dient de volgende invoer op te geven om een beheerde HSM-resource te maken:

- De naam van de HSM.
- De resourcegroep waar de HSM in uw abonnement wordt geplaatst.
- De Azure-locatie.
- Een lijst met initiële beheerders.

In het volgende voorbeeld wordt een HSM met de naam **ContosoMHSM** gemaakt in resourcegroep **ContosoResourceGroup**, die zich op locatie **US - oost 2** bevindt met **de huidige aangemelde gebruiker** als enige beheerder.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Het kan enkele minuten duren voor de maakopdracht is uitgevoerd. Zodra de opdracht is voltooid, bent u klaar om uw HSM te activeren.

In de uitvoer van deze opdracht worden de eigenschappen weergegeven van de beheerde HSM die u hebt gemaakt. De twee belangrijkste eigenschappen zijn:

* **name**: In het voorbeeld is ContosoMHSM de naam. U gebruikt deze naam voor andere Key Vault-opdrachten.
* **hsmUri**: In het voorbeeld is de URI https://contosohsm.managedhsm.azure.net. Toepassingen die via de REST API gebruikmaken van uw HSM, moeten deze URI gebruiken.

Uw Azure-account is nu gemachtigd om alle bewerkingen op deze beheerde HSM uit te voeren. Op dit moment is nog niemand anders gemachtigd.

## <a name="activate-the-security-domain-recovery-mode"></a>De herstelmodus van het beveiligingsdomein activeren

In het normale maakproces wordt op dit moment een nieuw beveiligingsdomein geïnitialiseerd en gedownload. Aangezien er echter een noodherstelprocedure wordt uitgevoerd, wordt bij de HSM aangevraagd om de herstelmodus voor het beveiligingsdomein in te voeren en een uitwisselingssleutel voor beveiligingsdomeinen te downloaden. De uitwisselingssleutel voor beveiligingsdomeinen is een openbare RSA-sleutel die wordt gebruikt voor het versleutelen van het beveiligingsdomein voordat het naar de HSM wordt geüpload. De bijbehorende persoonlijke sleutel wordt in de HSM beveiligd, zodat de inhoud van het beveiligingsdomein tijdens de overdracht veilig blijft.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Beveiligingsdomein uploaden naar doel-HSM

Voor deze stap hebt u het volgende nodig:
- De uitwisselingssleutel van het beveiligingsdomein die u in de vorige stap hebt gedownload.
- Het beveiligingsdomein van de bron-HSM.
- Ten minste het quorumnummer van de persoonlijke sleutels waarmee het beveiligingsdomein is versleuteld.

Met de opdracht `az keyvault security-domain upload` worden de volgende bewerkingen uitgevoerd:

- Ontsleutelen van het beveiligingsdomein van de bron-HSM met de persoonlijke sleutels die u opgeeft. 
- Maken van een uploadblob van het beveiligingsdomein die is versleuteld met de uitwisselingssleutel van het beveiligingsdomein die in de vorige stap is gedownload en vervolgens
- Uploaden van de uploadblob van het beveiligingsdomein naar de HSM om het herstel van het beveiligingsdomein te voltooien

In het onderstaande voorbeeld wordt gebruikgemaakt van het beveiligingsdomein van de **ContosoMHSM**, de 2 van de bijbehorende persoonlijke sleutels. Deze worden geüpload naar **ContosoMHSM2**, die wacht op het ontvangen van een beveiligingsdomein. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

De bron-HSM (ContosoMHSM) en de doel-HSM (ContosoMHSM2) hebben nu hetzelfde beveiligingsdomein. Er kan nu een volledige back-up van de bron-HSM in de doel-HSM worden hersteld.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Een back-up maken (als herstelpunt) van de nieuwe HSM

Het is altijd een goed idee om een volledige back-up te maken voordat u een volledige HSM-herstelbewerking uitvoert. U beschikt dan namelijk over een herstelpunt voor het geval er iets mis gaat met de herstelbewerking.

Als u een back-up van de HSM wilt maken, hebt u het volgende nodig
- Een opslagaccount waarin de back-up wordt opgeslagen
- Een container voor blob-opslag in dit opslagaccount, waar door het back-upproces een nieuwe map wordt gemaakt voor de versleutelde back-up

We gebruiken de opdracht `az keyvault backup` voor de back-up van de HSM in de opslagcontainer **mhsmbackupcontainer**, die zich voor het onderstaande voorbeeld in het opslagaccount **ContosoBackup** bevindt. Er wordt een SAS-token gemaakt dat binnen dertig minuten verloopt. Dit token wordt aan de beheerde HSM verstrekt om de back-up te schrijven.

```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Back-up herstellen vanuit bron-HSM

Voor deze stap hebt u het volgende nodig:

- Het opslagaccount en de blob-container waar de back-ups van de bron-HSM zijn opgeslagen.
- De naam van de map waaruit u de back-up wilt terugzetten. Als u regelmatig back-ups maakt, bevat deze container een groot aantal mappen.


```azurecli-interactive
end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

U hebt nu een volledig herstelproces voor noodgevallen voltooid. De inhoud van de bron-HSM toen de back-up werd gemaakt, wordt gekopieerd naar de doel-HSM, inclusief alle sleutels, versies, kenmerken, tags en roltoewijzingen.

## <a name="next-steps"></a>Volgende stappen

- Zie [Over het beveiligingsdomein van de beheerde HSM](security-domain.md) voor meer informatie over het beveiligingsdomein
- Volg [Best practices voor beheerde HSM](best-practices.md)
