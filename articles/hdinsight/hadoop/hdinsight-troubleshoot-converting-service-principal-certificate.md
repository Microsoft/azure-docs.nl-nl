---
title: Certificaat inhoud converteren naar base-64-Azure HDInsight
description: De inhoud van een Service-Principal-certificaat converteren naar een met base 64 gecodeerde teken reeks indeling in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944413"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>De inhoud van een Service-Principal-certificaat converteren naar een met base 64 gecodeerde teken reeks notatie in HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Er wordt een fout bericht weer gegeven met de melding dat de invoer geen geldige base-64-teken reeks is, omdat deze een niet-base-64 teken bevat, meer dan twee opvullings tekens of een niet-spatie teken tussen de opvul tekens.

## <a name="cause"></a>Oorzaak

Wanneer u een Power shell-of Azure-sjabloon implementatie gebruikt om clusters te maken met Data Lake als primaire of extra opslag ruimte, is de inhoud van het Service-Principal-certificaat voor toegang tot het Data Lake Storage-account in de basis-64-indeling. Een onjuiste conversie van de PFX-certificaat inhoud naar een met base 64 gecodeerde teken reeks kan leiden tot deze fout.

## <a name="resolution"></a>Oplossing

Als u het certificaat van de Service-Principal in PFX-indeling hebt (Zie [hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) voor voor beelden van de stappen voor het maken van een service-principal), gebruikt u de volgende Power shell-opdracht of C#-code om de certificaat inhoud te converteren naar de indeling base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]