---
title: Logboekregistratie voor Azure Key Vault | Microsoft Docs
description: Meer informatie over het bewaken van de toegang van uw sleutelkluizen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5fcb3226eebf39ab18fb3bb24f0521a0523748d4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134174"
---
# <a name="azure-key-vault-logging"></a>Logboekregistratie voor Azure Key Vault

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. U kunt dit doen door logboekregistratie voor Azure Key Vault in te schakelen. Hierbij wordt de informatie opgeslagen in een Azure-opslagaccount dat u opgeeft. Zie [Key Vault-logboekregistratie inschakelen](howto-logging.md) voor stapsgewijze instructies voor het instellen hiervan.

U kunt uw logboekgegevens (maximaal) tien minuten nadat de sleutelkluisbewerking is uitgevoerd, bekijken. In de meeste gevallen gaat het echter veel sneller.  Het is aan u om uw logboeken in uw opslagaccount te beheren:

* Gebruik standaardmethoden van Azure voor toegangsbeheer in uw opslagaccount om uw logboeken te beveiligen door het aantal gebruikers te beperken dat toegang heeft tot de logboeken.
* Verwijder de logboeken die u niet meer in uw opslagaccount wilt bewaren.

Zie [Wat is Azure Key Vault?](overview.md) voor algemene informatie over Key Vault. Bekijk de [pagina met prijzen](https://azure.microsoft.com/pricing/details/key-vault/) voor informatie over waar Key Vault beschikbaar is. Zie [Azure Monitor voor Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md) voor informatie over het gebruik ervan.

## <a name="interpret-your-key-vault-logs"></a>Uw Key Vault-logboeken interpreteren

Wanneer u logboekregistratie inschakelt, wordt automatisch een nieuwe container, genaamd **insights-logs-auditevent**, gemaakt voor uw opgegeven opslagaccount. U kunt ditzelfde opslagaccount gebruiken om logboeken voor meerdere sleutelkluizen te verzamelen.

Afzonderlijke blobs worden opgeslagen als tekst, die is opgemaakt als een JSON-blob. Laten we eens naar een voorbeeld van een logboekvermelding kijken. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

De volgende tabel bevat de namen en beschrijvingen van velden:

| Veldnaam | Beschrijving |
| --- | --- |
| **time** |Datum en tijd in UTC. |
| **resourceId** |Azure Resource Manager-resource-id. Voor Key Vault-logboeken is dit altijd de Key Vault-resource-id. |
| **operationName** |Naam van de bewerking, zoals beschreven in de volgende tabel. |
| **operationVersion** |REST-API-versie die door de client is aangevraagd. |
| **category** |Type resultaat. Voor Key Vault-logboeken is `AuditEvent` de enige beschikbare waarde. |
| **resultType** |Resultaat van de REST-API-aanvraag. |
| **resultSignature** |HTTP-status. |
| **resultDescription** |Extra beschrijving van het resultaat, indien beschikbaar. |
| **durationMs** |De tijd die nodig was om de REST-API-aanvraag af te handelen in milliseconden. Hierbij wordt geen rekening gehouden met de netwerklatentie, zodat de tijd die u aan de clientzijde meet mogelijk niet overeenkomt met de tijd die hier wordt weergegeven. |
| **callerIpAddress** |IP-adres van de client die de aanvraag heeft ingediend. |
| **correlationId** |Een optionele GUID die de client kan doorgeven om de logboeken aan de clientzijde te relateren aan (Sleutelkluis-)logboeken aan de servicezijde. |
| **identity** |De identiteit van het token dat is opgegeven in de REST-API-aanvraag. Dit is meestal ‘user’, ‘service principal’ of de combinatie ‘user+appId’, bijvoorbeeld bij een aanvraag via een Azure PowerShell-cmdlet. |
| **properties** |Gegevens die variëren op basis van de bewerking (**operationName**). In de meeste gevallen bevat dit veld clientgegevens (de useragent-tekenreeks die door de client wordt doorgegeven), de exacte URI voor de REST-API-aanvraag en de HTTP-statuscode. Als een object wordt geretourneerd als gevolg van een aanvraag (bijvoorbeeld **KeyCreate** of **VaultGet**), bevat dit veld ook de sleutel-URI (als `id`), kluis-URI of geheim-URI. |

De veldwaarden voor **operationName** hebben de *ObjectVerb*-indeling. Bijvoorbeeld:

* Alle sleutelkluisbewerkingen hebben de `Vault<action>`-indeling, zoals `VaultGet` en `VaultCreate`.
* Alle sleutelbewerkingen hebben de `Key<action>`-indeling, zoals `KeySign` en `KeyList`.
* Alle geheimbewerkingen hebben de `Secret<action>`-indeling, zoals `SecretGet` en `SecretListVersions`.

De volgende tabel bevat de **operationName**-waarden en de bijbehorende REST-API-opdrachten:

### <a name="operation-names-table"></a>Tabel met waarden voor operationName

# <a name="vault"></a>[Kluis](#tab/Vault)

| operationName | REST-API-opdracht |
| --- | --- |
| **Verificatie** |Verifiëren via het Azure Active Directory-eindpunt |
| **VaultGet** |[Informatie over een sleutelkluis ophalen](/rest/api/keyvault/vaults) |
| **VaultPut** |[Een sleutelkluis maken of bijwerken](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Een sleutelkluis verwijderen](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Een sleutelkluis bijwerken](/rest/api/keyvault/vaults) |
| **VaultList** |[Alle sleutelkluizen in een resourcegroep weergeven](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Verwijderde kluis opschonen](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Verwijderde kluis herstellen|
| **VaultGetDeleted** |[Verwijderde kluis ophalen](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Verwijderde kluizen weergeven](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Gebeurtenis gepubliceerd voor wijzigen van toegangsbeleid voor kluis |

# <a name="keys"></a>[Sleutels](#tab/Keys)

| operationName | REST-API-opdracht |
| --- | --- |
| **KeyCreate** |[Een sleutel maken](/rest/api/keyvault/createkey) |
| **KeyGet** |[Informatie over een sleutel ophalen](/rest/api/keyvault/getkey) |
| **KeyImport** |[Een sleutel in een kluis importeren](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Een sleutel verwijderen](/rest/api/keyvault/deletekey) |
| **KeySign** |[Aanmelden met een sleutel](/rest/api/keyvault/sign) |
| **KeyVerify** |[Verifiëren met een sleutel](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Een sleutel inpakken](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Een sleutel uitpakken](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Versleutelen met een sleutel](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Ontsleutelen met een sleutel](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Een sleutel bijwerken](/rest/api/keyvault/updatekey) |
| **KeyList** |[De sleutels in een kluis weergeven](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[De versies van een sleutel weergeven](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Een sleutel opschonen](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Back-up maken van een sleutel](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Een sleutel herstellen](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Een sleutel herstellen](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Een verwijderde sleutel ophalen](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[De verwijderde sleutels in een kluis weergeven](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van sleutel |
| **KeyExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van sleutel |

# <a name="secrets"></a>[Geheimen](#tab/Secrets)

| operationName | REST-API-opdracht |
| --- | --- |
| **SecretSet** |[Een geheim maken](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Een geheim ophalen](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Een geheim bijwerken](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Een geheim verwijderen](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Geheimen in een kluis weergeven](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Versies van een geheim weergeven](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Een geheim opschonen](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Een back-up maken van een geheim](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Een geheim terugzetten](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Een geheim herstellen](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Verwijderd geheim ophalen](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[De verwijderde geheimen in een kluis weergeven](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van geheim |
| **SecretExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van geheim |

# <a name="certificates"></a>[Certificaten](#tab/Cerificates)

| operationName | REST-API-opdracht |
| --- | --- |
| **CertificateGet** |[Informatie over een certificaat ophalen](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Een certificaat maken](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Een certificaat importeren in een kluis](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Een certificaat bijwerken](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[De certificaten in een kluis weergeven](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[De versies van een certificaat weergeven](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Een certificaat verwijderen](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Een certificaat opschonen](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Een back-up maken van een certificaat](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Een certificaat terugzetten](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Een certificaat herstellen](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Verwijderd certificaat ophalen](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[De verwijderde certificaten in een kluis weergeven](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Certificaatbeleid ophalen](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Certificaatbeleid bijwerken](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Certificaatbeleid maken](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Certificaatcontactpersonen ophalen](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Certificaatcontactpersonen instellen](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Certificaatcontactpersonen verwijderen](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Certificaatverlener ophalen](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Certificaatverlener instellen](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Certificaatverlener bijwerken](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Certificaatverlener verwijderen](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[De certificaatverleners weergeven](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Een certificaat inschrijven |
| **CertificateRenew** |Een certificaat verlengen |
| **CertificatePendingGet** |Certificaat ophalen in behandeling |
| **CertificatePendingMerge** |Certificaat samenvoegen in behandeling |
| **CertificatePendingUpdate** |Certificaat bijwerken in behandeling |
| **CertificatePendingDelete** |Certificaat verwijderen in behandeling |
| **CertificateNearExpiryEventGridNotification** |Gebeurtenis gepubliceerd voor bijna verlopen van certificaat |
| **CertificateExpiredEventGridNotification** |Gebeurtenis gepubliceerd voor verlopen van certificaat |

---

## <a name="use-azure-monitor-logs"></a>Azure Monitor-logboeken gebruiken

Met de Key Vault-oplossing in Azure Monitor-logboeken kunt u de `AuditEvent`-logboeken van Key Vault controleren. In Azure Monitor-logboeken kunt u logboekquery’s gebruiken om gegevens te analyseren en de informatie op te halen die u nodig hebt. 

Zie [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Key Vault-logboekregistratie inschakelen](howto-logging.md)
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
- Zie [Azure Key Vault in een webtoepassing gebruiken](tutorial-net-create-vault-azure-web-app.md) voor een zelfstudie over het gebruik van Azure Key Vault in een .NET-webtoepassing.
- Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](developers-guide.md) voor het programmeren van verwijzingen.
- Zie [Cmdlets voor Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) voor een lijst met Azure PowerShell 1.0-cmdlets voor Azure Key Vault.
