---
title: Een versleutelings certificaat instellen op Windows-clusters
description: Meer informatie over het instellen van een versleutelings certificaat en het versleutelen van geheimen op Windows-clusters.
ms.topic: conceptual
ms.date: 01/04/2019
ms.openlocfilehash: eb4909d62a2627c368f24dab572b25c6f1df30ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82583290"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-windows-clusters"></a>Een versleutelings certificaat instellen en geheimen op Windows-clusters versleutelen
In dit artikel wordt beschreven hoe u een versleutelings certificaat instelt en hoe u deze gebruikt om geheimen te versleutelen op Windows-clusters. Voor Linux-clusters raadpleegt [u een versleutelings certificaat instellen en geheimen op Linux-clusters versleutelen.][secret-management-linux-specific-link]

[Azure Key Vault][key-vault-get-started] wordt hier gebruikt als een veilige opslag locatie voor certificaten en als manier om certificaten te verkrijgen die zijn geïnstalleerd op service Fabric clusters in Azure. Als u niet in azure implementeert, hoeft u Key Vault niet te gebruiken om geheimen in Service Fabric toepassingen te beheren. Het *gebruik* van geheimen in een toepassing is echter het Cloud platform-neutraal zodat toepassingen kunnen worden geïmplementeerd in een cluster dat overal wordt gehost. 

## <a name="obtain-a-data-encipherment-certificate"></a>Een certificaat voor gegevens codering verkrijgen
Een gegevens versleuteling certificaat wordt uitsluitend gebruikt voor het versleutelen en ontsleutelen van [para meters][parameters-link] in de Settings.xml en [omgevings variabelen][environment-variables-link] van een service in de ServiceManifest.xml van een service. Het wordt niet gebruikt voor verificatie of ondertekening van versleutelings tekst. Het certificaat moet aan de volgende vereisten voldoen:

* Het certificaat moet een persoonlijke sleutel bevatten.
* Het certificaat moet worden gemaakt voor sleutel uitwisseling, exporteerbaar naar een pfx-bestand (Personal Information Exchange).
* Het gebruik van de certificaat sleutel moet gegevens codering (10) bevatten en mag geen server verificatie of client verificatie omvatten. 
  
  Wanneer u bijvoorbeeld een zelfondertekend certificaat maakt met behulp van Power shell, `KeyUsage` moet de vlag worden ingesteld op `DataEncipherment` :
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Het certificaat in uw cluster installeren
Dit certificaat moet worden geïnstalleerd op elk knoop punt in het cluster. Zie [een cluster maken met Azure Resource Manager][service-fabric-cluster-creation-via-arm] voor installatie-instructies. 

## <a name="encrypt-application-secrets"></a>Toepassings geheimen versleutelen
De volgende Power shell-opdracht wordt gebruikt voor het versleutelen van een geheim. Met deze opdracht wordt alleen de waarde versleuteld. de versleutelings tekst wordt **niet** ondertekend. U moet hetzelfde versleuteling certificaat gebruiken dat is geïnstalleerd in uw cluster voor het maken van gecodeerde tekst voor geheime waarden:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

De resulterende teken reeks met base-64 bevat zowel de geheime code ring als informatie over het certificaat dat is gebruikt om het te versleutelen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [opgeven van versleutelde geheimen in een toepassing.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[key-vault-get-started]:../key-vault/general/overview.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
