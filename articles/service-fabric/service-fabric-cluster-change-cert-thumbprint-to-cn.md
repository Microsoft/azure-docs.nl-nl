---
title: Een cluster bijwerken voor het gebruik van de algemene naam van het certificaat
description: Meer informatie over het converteren van een Azure Service Fabric-cluster certificaat van declaraties op basis van een vinger afdruk naar algemene namen.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: f719b1eb39da776827c6babec61e9e6701bb4602
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900787"
---
# <a name="convert-cluster-certificates-from-thumbprint-based-declarations-to-common-names"></a>Cluster certificaten van declaraties op basis van vinger afdruk naar algemene namen converteren

De hand tekening van een certificaat (vaak bekend als een vinger afdruk) is uniek. Een cluster certificaat dat is gedeclareerd met de vinger afdruk verwijst naar een specifiek exemplaar van een certificaat. Deze specificiteit maakt certificaat overschakeling en beheer in het algemeen, moeilijk en expliciet. Elke wijziging vereist het organiseren van upgrades van het cluster en de onderliggende computing hosts.

Het converteren van de certificaat declaraties van een Azure Service Fabric-cluster van een vinger afdruk naar de declaraties op basis van het certificaat onderwerp common name (CN) vereenvoudigt het beheer aanzienlijk. Met name voor het uitvoeren van een certificaat is niet langer een cluster upgrade vereist. In dit artikel wordt beschreven hoe u een bestaand cluster kunt converteren naar CN-gebaseerde declaraties zonder uitval tijd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="move-to-certificate-authority-signed-certificates"></a>Verplaatsen naar door certificerings instantie ondertekende certificaten

De beveiliging van een cluster waarvan het certificaat is gedeclareerd door de vinger afdruk, is van het feit dat het onmogelijk is of niet haalbaar is om een certificaat met dezelfde hand tekening als een andere te vervalsen. In dit geval is de herkomst van het certificaat minder belang rijk, waardoor zelfondertekende certificaten adequaat zijn.

Daarentegen, de beveiliging van een cluster waarvan de certificaten zijn gedeclareerd door de CN-stromen van de impliciete vertrouwens relatie die de cluster eigenaar heeft in de certificaat provider. De provider is de open bare-sleutel infrastructuur (PKI) die het certificaat heeft uitgegeven. Vertrouwen is gebaseerd op de certificerings praktijken van de PKI, op basis van andere factoren, of hun operationele beveiliging wordt gecontroleerd en goedgekeurd door nog andere vertrouwde partijen, enzovoort.

De cluster eigenaar moet ook gedetailleerde kennis hebben van de certificerings instanties (Ca's) die hun certificaten verlenen, omdat dit een fundamenteel aspect is van het valideren van certificaten op basis van het onderwerp. Dit betekent ook dat zelfondertekende certificaten volledig ongeschikt zijn voor dit doel. Letterlijk iedereen kan een certificaat met een bepaald onderwerp genereren.

Een certificaat dat is gedeclareerd door CN wordt doorgaans als geldig beschouwd als:

* De keten kan nu worden gebouwd.
* Het onderwerp heeft het verwachte CN-element.
* De verlener (direct of hoger in de keten) wordt vertrouwd door de agent die de validatie uitvoert.

Service Fabric ondersteunt het declareren van certificaten door CN op twee manieren:

* Met *impliciete* verleners, wat betekent dat de keten moet eindigen op een vertrouwens anker.
* Met verleners die door de vinger afdruk zijn gedeclareerd, wordt de verlener genoemd.

Zie [certificaat validatie declaraties op basis van algemene namen](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)voor meer informatie.

Voor het converteren van een cluster met behulp van een zelfondertekend certificaat dat is gedeclareerd door een vinger afdruk naar CN, het doel, het CA-ondertekende certificaat moet eerst worden geïntroduceerd in het cluster via de vinger afdruk. Alleen vervolgens wordt de conversie van de vinger afdruk naar CN mogelijk.

Voor test doeleinden *kan* een zelfondertekend certificaat worden GEDECLAREERD door CN, maar alleen als de uitgever is vastgemaakt aan een eigen vinger afdruk. Uit veiligheids oogpunt is deze actie bijna gelijk aan het declareren van hetzelfde certificaat door de vinger afdruk. Een geslaagde conversie van dit type garandeert niet dat een geslaagde conversie van de vinger afdruk naar CN met een CA-ondertekend certificaat is mislukt. U wordt aangeraden om de conversie te testen met een goed CA-ondertekend certificaat. Voor deze tests bestaan gratis opties.

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>Upload het certificaat en installeer het in de schaalset

In azure omvat het aanbevolen mechanisme voor het verkrijgen en inrichten van certificaten Azure Key Vault en het bijbehorende hulp programma. Een certificaat dat overeenkomt met de certificaat declaratie van het cluster moet worden ingericht op elk knoop punt van de virtuele-machine schaal sets waaruit het cluster bestaat. Zie [geheimen op schaal sets voor virtuele machines](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-securely-ship-a-certificate-to-the-vm)voor meer informatie.

Het is belang rijk om zowel het huidige als het doel cluster certificaat te installeren op de virtuele machines van elk knooppunt type van het cluster voordat u wijzigingen aanbrengt in de certificaat verklaringen van het cluster. Het traject van certificaat uitgifte tot inrichting op een Service Fabric knoop punt wordt in [de weg van een certificaat](cluster-security-certificate-management.md#the-journey-of-a-certificate)toegelicht.

## <a name="bring-the-cluster-to-an-optimal-starting-state"></a>Het cluster naar een optimale start status brengen

Het converteren van een certificaat declaratie van een vinger afdruk naar op CN gebaseerde effecten:

- Hoe elk knoop punt in het cluster de referenties naar andere knoop punten zoekt en presenteert.
- Hoe elk knoop punt de referenties van de bijbehorende tegen hanger valideert bij het tot stand brengen van een beveiligde verbinding.

Bekijk de [presentatie-en validatie regels voor beide configuraties](cluster-security-certificates.md#certificate-configuration-rules) voordat u doorgaat. De belangrijkste overweging bij het uitvoeren van een vinger afdruk-naar-CN-conversie is dat de knoop punten die zijn bijgewerkt en nog niet zijn bijgewerkt (dat wil zeggen, knoop punten die tot verschillende upgrade domeinen behoren) op elk gewenst moment tijdens de upgrade een geslaagde wederzijdse verificatie kunnen uitvoeren. De aanbevolen manier om dit gedrag te bereiken is door de vinger afdruk te declareren van het doel-of doel certificaat in een eerste upgrade. Voltooi vervolgens de overgang naar CN in een volgende. Als het cluster al een aanbevolen start status heeft, kunt u deze sectie overs Laan.

Er zijn meerdere geldige start statussen voor een conversie. De invariantie is dat het cluster al het doel certificaat gebruikt (aangegeven door de vinger afdruk) aan het begin van de upgrade naar CN. We overwegen `GoalCert` , `OldCert1` en `OldCert2` in dit artikel.

#### <a name="valid-starting-states"></a>Geldige start statussen

- `Thumbprint: GoalCert, ThumbprintSecondary: None`
- `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, waarbij `GoalCert` een latere `NotBefore` datum is dan die van `OldCert1`
- `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, waarbij `GoalCert` een latere `NotBefore` datum is dan die van `OldCert1`

> [!NOTE]
> Voorafgaand aan versie 7.2.445 (7,2 CU4) Service Fabric het meest recente verlopende certificaat (het certificaat met het meest recente ' NotAfter-eigendom) geselecteerd, zodat GoalCert een latere datum moet hebben voor de bovenstaande start statussen vóór 7,2 CU4 `NotAfter``OldCert1`

Als uw cluster zich niet in een van de geldige statussen bevindt, raadpleegt u informatie over het bereiken van die status in de sectie aan het einde van dit artikel.

## <a name="select-the-desired-cn-based-certificate-validation-scheme"></a>Selecteer het gewenste certificaat validatie schema op basis van CN

Zoals eerder beschreven, ondersteunt Service Fabric het declareren van certificaten door CN met een impliciet vertrouwens anker of het expliciet vastmaken van de uitgevers vingerafdrukken. Zie [certificaat validatie declaraties op basis van algemene namen](cluster-security-certificates.md#common-name-based-certificate-validation-declarations)voor meer informatie.

Zorg ervoor dat u een goed idee hebt van de verschillen en de implicaties van het kiezen van een van beide mechanismen. Syntactisch, dit verschil of keuze wordt bepaald door de waarde van de `certificateIssuerThumbprintList` para meter. Leeg houdt in dat een vertrouwde basis certificerings instantie (vertrouwens anker) moet worden vertrouwd, terwijl een set met vinger afdrukken de toegestane directe verleners van cluster certificaten beperkt.

   > [!NOTE]
   > `certificateIssuerThumbprint`In het veld kunt u de verwachte directe verleners opgeven van certificaten die zijn gedeclareerd door het onderwerp cn. Acceptabele waarden zijn een of meer door komma's gescheiden SHA1-vinger afdrukken. Met deze actie wordt de certificaat validatie versterkt.
   >
   > Als er geen verleners zijn opgegeven of als de lijst leeg is, wordt het certificaat geaccepteerd voor verificatie als de keten kan worden samengesteld. Het certificaat wordt vervolgens beëindigd in een hoofd niveau dat wordt vertrouwd door de validator. Als er een of meer verleners vingerafdrukten zijn opgegeven, wordt het certificaat geaccepteerd als de vinger afdruk van de directe verlener, zoals geëxtraheerd uit de keten, overeenkomt met een van de waarden die zijn opgegeven in dit veld. Het certificaat wordt geaccepteerd, ongeacht of het toegangs punt al dan niet wordt vertrouwd.
   >
   > Een PKI kan gebruikmaken van verschillende certificerings instanties (ook bekend als *verleners*) voor het ondertekenen van certificaten met een bepaald onderwerp. Daarom is het belang rijk om alle verwachte uitgevers vingerafdrukten voor dat onderwerp op te geven. Met andere woorden: het vernieuwen van een certificaat is niet gegarandeerd door dezelfde verlener als het certificaat dat wordt vernieuwd.
   >
   > Het opgeven van de uitgever wordt beschouwd als een best practice. Als u de certificaat verlener weglaat, blijft het werken met certificaten die zijn gekoppeld aan een vertrouwd basis certificaat, maar dit gedrag heeft beperkingen en kan in de nabije toekomst worden verlopen. Clusters die zijn geïmplementeerd in azure, beveiligd met x509-certificaten die zijn uitgegeven door een persoonlijke PKI en die zijn gedefinieerd door het onderwerp, kunnen mogelijk niet worden gevalideerd door Service Fabric (voor communicatie met cluster-naar-Service). Voor validatie moet het certificaat beleid van de PKI detecteerbaar, beschikbaar en toegankelijk zijn.

## <a name="update-the-clusters-azure-resource-manager-template-and-deploy"></a>De Azure Resource Manager-sjabloon van het cluster bijwerken en implementeren

Uw Service Fabric-clusters beheren met Azure Resource Manager-sjablonen (ARM). Een alternatieve, die ook gebruikmaakt van JSON-artefacten, is de [Azure resource Explorer (preview)](https://resources.azure.com). Er is op dit moment geen equivalente ervaring beschikbaar in de Azure Portal.

Als de oorspronkelijke sjabloon die overeenkomt met een bestaand cluster niet beschikbaar is, kunt u een gelijkwaardige sjabloon verkrijgen in de Azure Portal. Ga naar de resource groep die het cluster bevat en selecteer **sjabloon exporteren** in het menu **automatisering** aan de linkerkant. Selecteer vervolgens de gewenste resources. Ten minste de virtuele-machine schaalset en cluster bronnen moeten worden geëxporteerd. De gegenereerde sjabloon kan ook worden gedownload. Voor deze sjabloon zijn mogelijk wijzigingen vereist voordat deze volledig kan worden geïmplementeerd. De sjabloon komt ook mogelijk niet exact overeen met het oorspronkelijke exemplaar. Het is een reflectie van de huidige status van de cluster bron.

De benodigde wijzigingen zijn als volgt:

- De definitie van de extensie van het Service Fabric knooppunt (onder de resource van de virtuele machine) bijwerken. Als het cluster meerdere knooppunt typen definieert, moet u de definitie van elke overeenkomende schaalset voor virtuele machines bijwerken.
- De cluster bron definitie wordt bijgewerkt.

Hier vindt u meer gedetailleerde voor beelden.

### <a name="update-the-virtual-machine-scale-set-resources"></a>De resources voor de schaalset voor virtuele machines bijwerken
Van:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "thumbprint": "[parameters('certificateThumbprint')]",
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```
Aan:
```json
"virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            ...
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            ...
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        ...
                    }
                },
```

### <a name="update-the-cluster-resource"></a>De cluster bron bijwerken

Voeg in de resource **micro soft. ServiceFabric/clusters** een eigenschap **certificateCommonNames** met de instelling **commonNames** toe en verwijder de **certificaat** eigenschap helemaal (alle instellingen).

Van:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```
Aan:
```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            ...
        ],
        "properties": {
            "addonFeatures": [
                ...
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprintList')]"
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
```

Zie [een service Fabric cluster implementeren dat de algemene naam van een certificaat gebruikt in plaats van een vinger afdruk](./service-fabric-create-cluster-using-cert-cn.md)voor meer informatie.

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren

Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="achieve-a-valid-starting-state-for-converting-a-cluster-to-cn-based-certificate-declarations"></a>Een geldige start status krijgen voor het converteren van een cluster naar CN-gebaseerde certificaat declaraties

| Begin status | Upgrade 1 | Upgrade 2 |
| :--- | :--- | :--- |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` en `GoalCert` heeft een latere `NotBefore` datum dan `OldCert1` | `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: None` en `OldCert1` heeft een latere `NotBefore` datum dan `GoalCert` | `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1` | `Thumbprint: GoalCert, ThumbprintSecondary: None` |
| `Thumbprint: OldCert1, ThumbprintSecondary: GoalCert`, waarbij `OldCert1` een latere `NotBefore` datum is dan `GoalCert` | Upgrade uitvoeren naar `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: GoalCert, ThumbprintSecondary: OldCert1`, waarbij `OldCert1` een latere `NotBefore` datum is dan `GoalCert` | Upgrade uitvoeren naar `Thumbprint: GoalCert, ThumbprintSecondary: None` | - |
| `Thumbprint: OldCert1, ThumbprintSecondary: OldCert2` | Verwijder een van `OldCert1` of `OldCert2` om de status te verkrijgen `Thumbprint: OldCertx, ThumbprintSecondary: None` | Door gaan vanaf de nieuwe begin status |

> [!NOTE]
> Voor een cluster met een versie die ouder is dan versie 7.2.445 (7,2 CU4), vervangt u door `NotBefore` `NotAfter` de bovenstaande status.

Zie [certificaten beheren in een Azure service Fabric-cluster](service-fabric-cluster-security-update-certs-azure.md)voor instructies over het uitvoeren van deze upgrades.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [cluster beveiliging](service-fabric-cluster-security.md).
* Meer informatie over het uitvoeren [van een cluster certificaat op basis van een algemene naam](service-fabric-cluster-rollover-cert-cn.md).
* Meer informatie over het [configureren van een cluster voor automatische rollover](cluster-security-certificate-management.md).

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
