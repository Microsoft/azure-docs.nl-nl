---
title: Beheerde identiteiten configureren in batch-Pools
description: Meer informatie over het inschakelen van door de gebruiker toegewezen beheerde identiteiten in batch-Pools en het gebruik van beheerde identiteiten binnen de knoop punten.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7fab213ac1545c0bff9b74bc46504717b6038e8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950158"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Beheerde identiteiten configureren in batch-Pools

[Beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md) komen dat ontwikkel aars referenties hoeven te beheren door een identiteit voor de Azure-resource in azure Active Directory (Azure AD) op te geven en deze te gebruiken om Azure Active Directory (Azure AD)-tokens te verkrijgen.

In dit onderwerp wordt uitgelegd hoe u door de gebruiker toegewezen beheerde identiteiten kunt inschakelen voor batch-Pools en hoe u beheerde identiteiten binnen de knoop punten kunt gebruiken.

> [!IMPORTANT]
> Ondersteuning voor Azure Batch Pools met door de gebruiker toegewezen beheerde identiteiten is momenteel beschikbaar in de open bare Preview voor de volgende regio's: VS-West 2, Zuid-Centraal VS, VS-Oost, US Gov-Arizona en US Gov-Virginia.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="create-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit maken

Maak eerst [uw door de gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) in dezelfde Tenant als uw batch-account. Deze beheerde identiteit hoeft zich niet in dezelfde resource groep te benemen of zelfs in hetzelfde abonnement.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Een batch-pool maken met door de gebruiker toegewezen beheerde identiteiten

Nadat u een of meer door de gebruiker toegewezen beheerde identiteiten hebt gemaakt, kunt u een batch-pool met die beheerde identiteit maken met behulp van de [batch .net-beheer bibliotheek](/dotnet/api/overview/azure/batch#management-library).

> [!IMPORTANT]
> Pools moeten worden geconfigureerd met [virtuele-machine configuratie](nodes-and-pools.md#virtual-machine-configuration) om beheerde identiteiten te kunnen gebruiken.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> Het maken van Pools met beheerde identiteiten wordt momenteel niet ondersteund in de [batch .net-client bibliotheek](/dotnet/api/overview/azure/batch#client-library).

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Door de gebruiker toegewezen beheerde identiteiten gebruiken in batch knooppunten

Nadat u uw Pools hebt gemaakt, hebben de door de gebruiker toegewezen beheerde identiteiten toegang tot de groeps knooppunten via Secure Shell (SSH) of Extern bureaublad (RDP). U kunt uw taken ook zo configureren dat de beheerde identiteiten rechtstreeks toegang hebben tot [Azure-resources die beheerde identiteiten ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

Binnen de batch knooppunten kunt u beheerde identiteits tokens ophalen en gebruiken om te verifiëren via Azure AD-verificatie via de [Azure-instance metadata service](../virtual-machines/windows/instance-metadata-service.md).

Het Power shell-script voor Windows om een toegangs token op te halen voor verificatie is:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Voor Linux is het bash-script:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

Zie [Managed Identities voor Azure resources on an Azure VM](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)(Engelstalig) voor meer informatie over het verkrijgen van een toegangs token.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).
- Meer informatie over het gebruik [van door de klant beheerde sleutels met door gebruikers beheerde identiteiten](batch-customer-managed-key.md).
- Meer informatie over het [inschakelen van automatische rotatie van certificaten in een batch-pool](automatic-certificate-rotation.md).
