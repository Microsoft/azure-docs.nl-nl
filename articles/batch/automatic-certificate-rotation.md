---
title: Automatische rotatie van certificaten in een batch-pool inschakelen
description: U kunt een batch-pool maken met een beheerde identiteit en een certificaat dat automatisch wordt vernieuwd.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962508"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Automatische rotatie van certificaten in een batch-pool inschakelen

 U kunt een batch-pool maken met een certificaat dat automatisch wordt vernieuwd. Hiervoor moet uw pool worden gemaakt met een door de [gebruiker toegewezen beheerde identiteit](managed-identity-pools.md) die toegang heeft tot het certificaat in [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> Ondersteuning voor Azure Batch Pools met door de gebruiker toegewezen beheerde identiteiten is momenteel beschikbaar in de open bare Preview voor de volgende regio's: VS-West 2, Zuid-Centraal VS, VS-Oost, US Gov-Arizona en US Gov-Virginia.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="create-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit maken

Maak eerst [uw door de gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) in dezelfde Tenant als uw batch-account. Deze beheerde identiteit hoeft zich niet in dezelfde resource groep te benemen of zelfs in hetzelfde abonnement.

Noteer de **client-id** van de door de gebruiker toegewezen beheerde identiteit. U hebt deze waarde later nog nodig.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Scherm afbeelding met de client-ID van een door de gebruiker toegewezen beheerde identiteit in de Azure Portal.":::

## <a name="create-your-certificate"></a>Uw certificaat maken

Vervolgens moet u een certificaat maken en toevoegen aan Azure Key Vault. Als u nog geen sleutel kluis hebt gemaakt, moet u dat eerst doen. Zie voor instructies [Quick Start: een certificaat instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../key-vault/certificates/quick-create-portal.md).

Wanneer u uw certificaat maakt, moet u ervoor zorgen dat het **levensduur actie type** is ingesteld op automatisch verlengen en geeft u het aantal dagen op waarna het certificaat moet worden vernieuwd.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Screen shot van het scherm voor het maken van certificaten in de Azure Portal.":::

Nadat het certificaat is gemaakt, noteert u de **geheime id**. U hebt deze waarde later nog nodig.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Scherm afbeelding met de geheime id van een certificaat.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Een toegangs beleid toevoegen in Azure Key Vault

Wijs in uw sleutel kluis een Key Vault toegangs beleid toe waarmee uw door de gebruiker toegewezen beheerde identiteit toegang kan krijgen tot geheimen en certificaten. Zie [toegangs beleid voor Key Vault toewijzen met behulp van de Azure Portal](../key-vault/general/assign-access-policy-portal.md)voor gedetailleerde instructies.

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Een batch-pool maken met een door de gebruiker toegewezen beheerde identiteit

Maak een batch-pool met uw beheerde identiteit met behulp van de [batch .net-beheer bibliotheek](/dotnet/api/overview/azure/batch#management-library). Zie [Managed Identities in batch-Pools configureren](managed-identity-pools.md)voor meer informatie.

In het volgende voor beeld wordt de REST API voor batch beheer gebruikt voor het maken van een groep. Zorg ervoor dat u de **geheime id** van uw certificaat gebruikt voor `observedCertificates` en de **client-id** van uw beheerde identiteit voor `msiClientId` , waarbij u de onderstaande voorbeeld gegevens vervangt.

REST API-URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Aanvraagtekst

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Het certificaat valideren

Meld u aan bij het reken knooppunt om te controleren of het certificaat is geïmplementeerd. De uitvoer ziet er als volgt uit:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).
- Meer informatie over het gebruik [van door de klant beheerde sleutels met door gebruikers beheerde identiteiten](batch-customer-managed-key.md).
