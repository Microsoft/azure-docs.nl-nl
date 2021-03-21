---
title: Veelgestelde vragen over schaalsets voor virtuele Azure-machines
description: Krijg antwoorden op veelgestelde vragen over schaal sets voor virtuele machines in Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: faq
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 3bc259f9ee6cb1e6fd927af82a1740403d3ae7d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587954"
---
# <a name="azure-virtual-machine-scale-sets-faqs"></a>Veelgestelde vragen over schaalsets voor virtuele Azure-machines

Krijg antwoorden op veelgestelde vragen over schaal sets voor virtuele machines in Azure.

## <a name="top-frequently-asked-questions-for-scale-sets"></a>Beste Veelgestelde vragen over schaal sets

### <a name="how-many-vms-can-i-have-in-a-scale-set"></a>Hoeveel virtuele machines kan een schaalset bevatten?

Een schaalset kan 0 tot 1.000 Vm's hebben op basis van platform installatie kopieën, of 0 tot 600 Vm's op basis van aangepaste installatie kopieën.

### <a name="are-data-disks-supported-within-scale-sets"></a>Worden gegevensschijven binnen schaalsets ondersteund?

Ja. Een schaalset kan een configuratie voor gekoppelde gegevensschijven definiëren die op alle VM's in de set wordt toegepast. Zie [Schaalsets en gekoppelde gegevensschijven in Azure](virtual-machine-scale-sets-attached-disks.md) voor meer informatie. Andere opties voor het opslaan van gegevens zijn:

* Azure-bestanden (gedeelde SMB-stations)
* Station van het besturingssysteem
* Tijdelijk station (lokaal, niet ondersteund met Azure Storage)
* Azure-gegevensservice (bijvoorbeeld Azure-tabellen, Azure-blobs)
* Externe gegevensservice (bijvoorbeeld externe database)

### <a name="which-azure-regions-support-scale-sets"></a>Welke Azure-regio's ondersteunen schaalsets?

Alle regio's ondersteunen schaalsets.

### <a name="how-do-i-create-a-scale-set-by-using-a-custom-image"></a>Hoe maak ik een schaalset met behulp van een aangepaste installatiekopie?

Maak een VM-installatie kopie en leg deze vast en gebruik deze als de bron voor uw schaalset. Voor een zelf studie over het maken en gebruiken van een aangepaste VM-installatie kopie, kunt u de [Azure cli](tutorial-use-custom-image-cli.md) of [Azure PowerShell](tutorial-use-custom-image-powershell.md) gebruiken

### <a name="if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed"></a>Als ik de capaciteit van mijn schaalset verlaag van 20 naar 15, welke virtuele machines worden er dan verwijderd?

Standaard worden virtuele machines van de schaalset gelijkmatig over de beschikbaarheids zones verwijderd (als de schaalset is geïmplementeerd in de zonegebonden-configuratie) en fout domeinen om de beschik baarheid te maximaliseren. VM's met de hoogste id's worden het eerst verwijderd.

U kunt de volg orde van het verwijderen van de virtuele machine wijzigen door een beleid voor de [schaal in](virtual-machine-scale-sets-scale-in-policy.md) te stellen voor de schaalset.

### <a name="what-if-i-then-increase-the-capacity-from-15-to-18"></a>Wat gebeurt er als ik de capaciteit verhoog van 15 naar 18?

Als u de capaciteit verhoogt naar 18, worden er 3 nieuwe virtuele machines gemaakt. De id van elk VM-exemplaar wordt oplopend gegenereerd vanaf de vorige hoogste waarde (bijvoorbeeld 20, 21, 22). Vm's worden verdeeld over fout domeinen.

### <a name="when-im-using-multiple-extensions-in-a-scale-set-can-i-enforce-an-execution-sequence"></a>Kan ik een uitvoeringsvolgorde toepassen wanneer ik meerdere extensies in een schaalset gebruik?

Ja, u kunt de extensie voor [uitbrei dingen](virtual-machine-scale-sets-extension-sequencing.md)van de schaalset gebruiken.

### <a name="do-scale-sets-work-with-azure-availability-sets"></a>Maken schaalsets gebruik van beschikbaarheidssets van Azure?

Een schaalset voor een regionale (niet-zonegebonden) gebruikt *plaatsings groepen*, die fungeren als een impliciete beschikbaarheidsset met vijf fout domeinen en vijf update domeinen. Schaal sets van meer dan 100 Vm's omvatten meerdere plaatsings groepen. Zie voor meer informatie over de plaatsing van groepen [Werken met grote schaalsets voor virtuele machines](virtual-machine-scale-sets-placement-groups.md). Een beschikbaarheidsset met virtuele machines kan bestaan in hetzelfde virtuele netwerk als een schaalset met virtuele machines. Een veelvoorkomende configuratie is om beheerknooppunt-VM's (waarvoor vaak een unieke configuratie is vereist) in een beschikbaarheidsset te zetten en gegevensknooppunten in de schaalset te zetten.

### <a name="do-scale-sets-work-with-azure-availability-zones"></a>Maken schaal sets gebruik van Azure-beschikbaarheids zones?

Ja. Zie het [document Scale set zone doc](./virtual-machine-scale-sets-use-availability-zones.md)voor meer informatie.


## <a name="autoscale"></a>Automatisch schalen

### <a name="what-are-best-practices-for-azure-autoscale"></a>Wat zijn de aanbevolen procedures voor Azure automatisch schalen?

Zie [Aanbevolen procedures voor het automatisch schalen van virtuele machines](../azure-monitor/autoscale/autoscale-best-practices.md)voor aanbevolen procedures voor automatische schaling.

### <a name="where-do-i-find-metric-names-for-autoscaling-that-uses-host-based-metrics"></a>Waar vind ik metrische namen voor automatisch schalen die gebruikmaken van metrische gegevens op basis van een host?

Zie [ondersteunde metrische gegevens met Azure monitor](../azure-monitor/essentials/metrics-supported.md)voor metrische namen voor automatisch schalen die gebruikmaken van metrische gegevens op basis van een host.

### <a name="are-there-any-examples-of-autoscaling-based-on-an-azure-service-bus-topic-and-queue-length"></a>Zijn er voor beelden van automatisch schalen op basis van een Azure Service Bus onderwerp en wachtrij lengte?

Ja. Zie voor voor beelden van automatisch schalen op basis van een Azure Service Bus onderwerp en wachtrij lengte [Azure monitor algemene metrische gegevens automatisch schalen](../azure-monitor/autoscale/autoscale-common-metrics.md).

Gebruik voor een Service Bus wachtrij de volgende JSON:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

Voor een opslag wachtrij gebruikt u de volgende JSON:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

Vervang voorbeeld waarden door de URI (Uniform Resource Identifiers) van uw resource.


### <a name="should-i-autoscale-by-using-host-based-metrics-or-a-diagnostics-extension"></a>Moet ik automatisch schalen met metrische gegevens op basis van een host of een uitbrei ding van diagnostische gegevens?

U kunt een instelling voor automatisch schalen maken op een VM voor het gebruik van metrische gegevens op hostniveau of metrische gegevens op basis van een gast besturingssysteem.

Zie voor een lijst met ondersteunde metrische gegevens [Azure monitor automatisch schalen van algemene metrische gegevens](../azure-monitor/autoscale/autoscale-common-metrics.md).

Zie [Geavanceerde configuratie van automatisch schalen met behulp van Resource Manager-sjablonen voor virtuele-machine schaal sets](../azure-monitor/autoscale/autoscale-virtual-machine-scale-sets.md)voor een volledig voor beeld van virtuele-machine schaal sets.

In het voor beeld wordt gebruikgemaakt van de CPU-metrische gegevens op hostniveau en de metrische gegevens over het aantal berichten.



### <a name="how-do-i-set-alert-rules-on-a-virtual-machine-scale-set"></a>Hoe kan ik waarschuwings regels instellen voor een schaalset voor virtuele machines?

U kunt waarschuwingen over metrische gegevens voor virtuele-machine schaal sets maken via Power shell of Azure CLI. Zie Azure Monitor voor beelden van [Power shell-Quick](../azure-monitor/powershell-samples.md#create-metric-alerts) start en [Azure monitor-voor beelden van cross-platform cli Quick](../azure-monitor/cli-samples.md#work-with-alerts)start voor meer informatie.

De TargetResourceId van de virtuele-machine schaalset ziet er als volgt uit:

/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname

U kunt elk VM-prestatie meter item kiezen als metriek voor het instellen van een waarschuwing voor. Zie voor meer informatie de metrische gegevens van het [gast besturingssysteem voor Windows-vm's op basis van Resource Manager](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-for-resource-manager-based-windows-vms) en [metrische gegevens voor het gast besturingssysteem voor virtuele Linux-machines](../azure-monitor/autoscale/autoscale-common-metrics.md#guest-os-metrics-linux-vms) in het artikel [Azure monitor automatisch schalen van algemene gegevens](../azure-monitor/autoscale/autoscale-common-metrics.md) .

### <a name="how-do-i-set-up-autoscale-on-a-virtual-machine-scale-set-by-using-powershell"></a>Hoe kan ik automatisch schalen instellen voor een schaalset voor virtuele machines met behulp van Power shell?

Als u automatisch schalen wilt instellen voor een schaalset voor een virtuele machine met behulp van Power shell, raadpleegt u een schaalset voor [een virtuele machine](tutorial-autoscale-powershell.md). U kunt automatisch schalen ook configureren met de [Azure cli](tutorial-autoscale-cli.md) -en [Azure-sjablonen](tutorial-autoscale-template.md)


### <a name="if-i-have-stopped-deallocated-a-vm-is-that-vm-started-as-part-of-an-autoscale-operation"></a>Als een virtuele machine is gestopt (toewijzing opgeheven), is de VM gestart als onderdeel van een bewerking voor automatisch schalen?

Nee. Als voor regels voor automatisch schalen extra VM-exemplaren zijn vereist als onderdeel van een schaalset, wordt een nieuw VM-exemplaar gemaakt. VM-instanties die zijn gestopt (toewijzing opgeheven) worden niet gestart als onderdeel van een automatische schaal gebeurtenis. Deze gestopte (niet-toegewezen) Vm's kunnen echter worden verwijderd als onderdeel van een functie voor automatisch schalen die in het aantal instanties wordt geschaald, op dezelfde manier als een VM-exemplaar kan worden verwijderd op basis van de volg orde van de ID van de VM-instantie.



## <a name="certificates"></a>Certificaten

### <a name="how-do-i-securely-ship-a-certificate-to-the-vm"></a>Hoe kan ik een certificaat veilig naar de virtuele machine verzenden?

Als u een certificaat op een veilige manier naar de virtuele machine wilt verzenden, kunt u een klant certificaat rechtstreeks in een Windows-certificaat archief installeren vanuit de sleutel kluis van de klant.

Gebruik de volgende JSON:

```json
"secrets": [
    {
        "sourceVault": {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
        },
        "vaultCertificates": [
            {
                "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                "certificateStore": "certificateStoreName"
            }
        ]
    }
]
```

De code ondersteunt Windows en Linux.

Zie [een schaalset voor virtuele machines maken of bijwerken](/rest/api/compute/virtualmachinescalesets/createorupdate)voor meer informatie.


### <a name="how-do-i-use-self-signed-certificates-provisioned-for-azure-service-fabric-clusters"></a>Hoe kan ik zelfondertekende certificaten gebruiken die zijn ingericht voor Azure Service Fabric-clusters?
Voor het meest recente voor beeld gebruikt u de volgende Azure CLI-instructie in azure shell, lees de voor beeld van een CLI-module voor service fabrics, die wordt afgedrukt op stdout:

```azurecli
az sf cluster create -h
```

Zelfondertekende certificaten kunnen niet worden gebruikt voor gedistribueerde vertrouwens relaties die worden verstrekt door een certificerings instantie en mogen niet worden gebruikt voor een Service Fabric cluster dat is bedoeld voor het hosten van bedrijfs productie oplossingen. Raadpleeg de [Aanbevolen procedures voor Azure service Fabric Security](../security/fundamentals/service-fabric-best-practices.md) en [service Fabric scenario's voor cluster beveiliging](../service-fabric/service-fabric-cluster-security.md)voor aanvullende service Fabric beveiligings richtlijnen.

### <a name="can-i-specify-an-ssh-key-pair-to-use-for-ssh-authentication-with-a-linux-virtual-machine-scale-set-from-a-resource-manager-template"></a>Kan ik een SSH-sleutel paar opgeven dat moet worden gebruikt voor SSH-verificatie met een virtuele Linux-machine schaalset van een resource manager-sjabloon?

Ja. De REST API voor **osProfile** is vergelijkbaar met de standaard-VM rest API.

**OsProfile** in uw sjabloon insluiten:

```json
"osProfile": {
    "computerName": "[variables('vmName')]",
    "adminUsername": "[parameters('adminUserName')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "ssh": {
            "publicKeys": [
                {
                    "path": "[variables('sshKeyPath')]",
                    "keyData": "[parameters('sshKeyData')]"
                }
            ]
        }
    }
}
```

Dit JSON-blok wordt gebruikt in [deze Azure Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)start-sjabloon.

Zie [een schaalset voor virtuele machines maken of bijwerken](/rest/api/compute/virtualmachinescalesets/createorupdate#linuxconfiguration)voor meer informatie.

### <a name="how-do-i-remove-deprecated-certificates"></a>Afgeschafte certificaten Hoe kan ik verwijderen?

Als u afgeschafte certificaten wilt verwijderen, verwijdert u het oude certificaat uit de lijst met kluis certificaten. Zorg ervoor dat alle certificaten die u wilt blijven op uw computer in de lijst staan. Hiermee wordt het certificaat niet van alle virtuele machines verwijderd. Het certificaat wordt ook niet toegevoegd aan nieuwe virtuele machines die in de schaalset voor virtuele machines worden gemaakt.

Als u het certificaat van bestaande Vm's wilt verwijderen, gebruikt u een aangepaste script extensie om de certificaten hand matig uit het certificaat archief te verwijderen.

### <a name="how-do-i-inject-an-existing-ssh-public-key-into-the-virtual-machine-scale-set-ssh-layer-during-provisioning"></a>Hoe kan ik een bestaande open bare SSH-sleutel in de VM-laag van de virtuele machine injecteren tijdens het inrichten?

Als u de virtuele machines alleen met een open bare SSH-sleutel levert, hoeft u de open bare sleutels niet in Key Vault te plaatsen. Open bare sleutels zijn niet geheim.

U kunt open bare SSH-sleutels in tekst zonder opmaak opgeven wanneer u een virtuele Linux-machine maakt:

```json
"linuxConfiguration": {
    "ssh": {
        "publicKeys": [
            {
                "path": "path",
                "keyData": "publickey"
            }
        ]
    }
}
```

linuxConfiguration element naam | Vereist | Type | Beschrijving
--- | --- | --- | ---
SSH | Nee | Verzameling | Hiermee geeft u de configuratie van de SSH-sleutel voor een Linux-besturings systeem op
leertraject | Ja | Tekenreeks | Hiermee geeft u het pad naar het Linux-bestand op waar de SSH-sleutels of het certificaat zich bevinden
Gegevens | Ja | Tekenreeks | Hiermee geeft u een open bare SSH-sleutel met base64-code ring op

Zie [de sjabloon 101-VM-Sshkey github Quick](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json)start voor een voor beeld.

### <a name="when-i-run-update-azvmss-after-adding-more-than-one-certificate-from-the-same-key-vault-i-see-the-following-message"></a>Wanneer ik voer `Update-AzVmss` na het toevoegen van meer dan één certificaat uit dezelfde sleutel kluis, wordt het volgende bericht weer gegeven:

>Update-AzVmss: List Secret bevat herhaalde exemplaren van/Subscriptions/ \<my-subscription-id> /resourceGroups/Internal-RG-dev/providers/Microsoft.KeyVault/vaults/Internal-keyvault-dev. Dit is niet toegestaan.

Dit kan gebeuren als u probeert dezelfde kluis opnieuw toe te voegen in plaats van een nieuw kluis certificaat te gebruiken voor de bestaande bron kluis. De `Add-AzVmssSecret` opdracht werkt niet goed als u extra geheimen toevoegt.

Als u meer geheimen wilt toevoegen uit dezelfde sleutel kluis, werkt u de $vmss. Properties. osProfile. geheimen [0]. vaultCertificates-lijst bij.

Zie [een virtuele-machineset maken of bijwerken](/rest/api/compute/virtualmachinescalesets/createorupdate)voor de verwachte invoer structuur.

Zoek het geheim in het object voor virtuele-machine schaal sets dat zich in de sleutel kluis bevindt. Voeg vervolgens uw certificaat verwijzing (de URL en de naam van het geheime archief) toe aan de lijst die is gekoppeld aan de kluis.

> [!NOTE]
> Op dit moment kunt u geen certificaten verwijderen van Vm's met behulp van de virtuele-machine Scale set API.
>

Nieuwe Vm's hebben niet het oude certificaat. Vm's met het certificaat en die al zijn geïmplementeerd, hebben echter het oude certificaat.

### <a name="can-i-push-certificates-to-the-virtual-machine-scale-set-without-providing-the-password-when-the-certificate-is-in-the-secret-store"></a>Kan ik certificaten pushen naar de schaalset voor virtuele machines zonder het wacht woord op te geven wanneer het certificaat zich in het archief van de geheime sleutel bevindt?

U hoeft geen wacht woorden in scripts vast te maken. U kunt wacht woorden dynamisch ophalen met de machtigingen die u gebruikt om het implementatie script uit te voeren. Als u een script hebt waarmee een certificaat uit de sleutel kluis van het geheime archief wordt verplaatst, wordt met de opdracht voor een geheim archief `get certificate` ook het wacht woord van het pfx-bestand uitgevoerd.

### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-for-a-virtual-machine-scale-set-work-why-do-i-need-the-sourcevault-value-when-i-have-to-specify-the-absolute-uri-for-a-certificate-by-using-the-certificateurl-property"></a>Hoe werkt de eigenschap geheimen van virtualMachineProfile. osProfile voor een schaalset voor virtuele machines? Waarom heb ik de waarde sourceVault nodig wanneer ik de absolute URI voor een certificaat moet opgeven met behulp van de eigenschap certificateUrl?

Er moet een WinRM-certificaat verwijzing (Windows Remote Management) aanwezig zijn in de eigenschap geheimen van het besturingssysteem profiel.

Het doel om aan te geven dat de bron kluis toegangs beheer lijst (ACL) afdwingt die voor komt in het Azure-Cloud service model van een gebruiker. Als de bron kluis niet is opgegeven, kunnen gebruikers die geen machtigingen hebben voor het implementeren of openen van geheimen in een sleutel kluis, een compute resource provider (CRP) hebben. Er bestaan ook Acl's voor bronnen die niet bestaan.

Als u een onjuiste ID voor de bron kluis opgeeft, maar een geldige sleutel kluis-URL, wordt er een fout melding weer gegeven wanneer u de bewerking aanroept.

### <a name="if-i-add-secrets-to-an-existing-virtual-machine-scale-set-are-the-secrets-injected-into-existing-vms-or-only-into-new-ones"></a>Als ik geheimen toevoeg aan een bestaande schaalset voor virtuele machines, zijn de geheimen in bestaande Vm's opgenomen of alleen in nieuwe?

Certificaten worden toegevoegd aan al uw Vm's, zelfs vooraf bestaande. Als uw eigenschap Scale set upgrade Policy van virtuele machines is ingesteld op **hand matig**, wordt het certificaat toegevoegd aan de VM wanneer u een hand matige update uitvoert op de VM.

### <a name="where-do-i-put-certificates-for-linux-vms"></a>Waar kan ik certificaten voor Linux Vm's plaatsen?

Zie [certificaten implementeren op vm's vanuit een door de klant beheerde sleutel kluis](/archive/blogs/kv/deploy-certificates-to-vms-from-customer-managed-key-vault)voor meer informatie over het implementeren van certificaten voor Linux-vm's.

### <a name="how-do-i-add-a-new-vault-certificate-to-a-new-certificate-object"></a>Hoe kan ik een nieuw kluis certificaat toevoegen aan een nieuw certificaat object?

Als u een kluis certificaat wilt toevoegen aan een bestaand geheim, raadpleegt u het volgende Power shell-voor beeld. Gebruik slechts één geheim object.

```powershell
$newVaultCertificate = New-AzVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809

$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)

Update-AzVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```

### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>Wat gebeurt er met certificaten als u de installatie kopie van een virtuele machine wilt terugzetten?

Als u de installatie kopie van een virtuele machine opnieuw maakt, worden de certificaten verwijderd. Als u de installatie kopie verwijdert, wordt de hele besturingssysteem schijf verwijderd.

### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Wat gebeurt er als u een certificaat verwijdert uit de sleutel kluis?

Als het geheim wordt verwijderd uit de sleutel kluis en vervolgens wordt uitgevoerd `stop deallocate` voor alle virtuele machines en vervolgens weer wordt gestart, treedt er een fout op. De fout treedt op omdat de CRP de geheimen van de sleutel kluis moet ophalen, maar dit kan niet. In dit scenario kunt u de certificaten uit het model voor virtuele-machine schaal sets verwijderen.

Het CRP-onderdeel houdt geen klant geheimen persistent. Als u uitvoert `stop deallocate` voor alle vm's in de schaalset voor virtuele machines, wordt de cache verwijderd. In dit scenario worden geheimen opgehaald uit de sleutel kluis.

U ondervindt dit probleem niet bij het uitschalen, omdat er een kopie van het geheim in de cache is opgeslagen in azure Service Fabric (in het Tenant model met één infra structuur).

### <a name="why-do-i-have-to-specify-the-certificate-version-when-i-use-key-vault"></a>Waarom moet ik de certificaat versie opgeven wanneer ik Key Vault gebruik?

Het doel van de Key Vault vereiste om de certificaat versie op te geven, is om dit duidelijk te maken voor de gebruiker welk certificaat op hun Vm's wordt geïmplementeerd.

Als u een virtuele machine maakt en vervolgens uw geheim in de sleutel kluis bijwerkt, wordt het nieuwe certificaat niet gedownload naar uw Vm's. Maar uw Vm's lijken ernaar te verwijzen en nieuwe Vm's krijgen het nieuwe geheim. Als u dit wilt voor komen, moet u verwijzen naar een geheime versie.

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-for-deploying-these-certificates-to-a-virtual-machine-scale-set"></a>Mijn team werkt met verschillende certificaten die naar ons worden gedistribueerd als. CER-open bare sleutels. Wat is de aanbevolen benadering voor het implementeren van deze certificaten voor een schaalset voor virtuele machines?

Als u open bare CER-sleutels wilt implementeren in een schaalset voor virtuele machines, kunt u een. pfx-bestand genereren dat alleen CER-bestanden bevat. Gebruik hiervoor `X509ContentType = Pfx` . Laad bijvoorbeeld het CER-bestand als een x509Certificate2-object in C# of Power shell en roep vervolgens de methode aan.

Zie [X509Certificate. export Method (X509ContentType, String)](/dotnet/api/system.security.cryptography.x509certificates.x509certificate.export?view=netcore-3.1#system_security_cryptography_x509certificates_x509certificate_export_system_security_cryptography_x509certificates_x509contenttype_system_string_)voor meer informatie.

### <a name="how-do-i-pass-in-certificates-as-base64-strings"></a>Certificaten Hoe kan ik door geven als base64-teken reeksen?

Als u wilt emuleren dat een certificaat als base64-teken reeks wordt door gegeven, kunt u de meest recente versie-URL in een resource manager-sjabloon extra heren. Neem de volgende JSON-eigenschap op in uw Resource Manager-sjabloon:

```json
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```

### <a name="do-i-have-to-wrap-certificates-in-json-objects-in-key-vaults"></a>Moet ik certificaten laten teruglopen in JSON-objecten in sleutel kluizen?

In virtuele-machine schaal sets en Vm's moeten certificaten in JSON-objecten worden verpakt.

We ondersteunen ook het inhouds type application/x-pkcs12/pfx-profiel.

We ondersteunen momenteel geen CER-bestanden. Als u CER-bestanden wilt gebruiken, exporteert u ze naar. pfx-containers.



## <a name="compliance-and-security"></a>Naleving en beveiliging

### <a name="are-virtual-machine-scale-sets-pci-compliant"></a>Zijn de virtuele-machine schaal sets PCI-compatibel?

Schaalsets van virtuele machines zijn een thin-API-laag bovenop de CRP. Beide componenten maken deel uit van het computerplatform in de boomstructuur van de Azure-service.

Vanuit het perspectief van naleving zijn schaalsets van virtuele machines een fundamenteel onderdeel van het Azure-computerplatform. Ze delen een team, hulpprogramma's, processen, implementatiemethode, beveiligingsmechanismen, JIT-compilatie (just-in-time), bewaking, waarschuwingen enz. met de CRP zelf. Schaalsets van virtuele machines zijn compatibel met PCI (Payment Card Industry) omdat de CRP deel uitmaakt van het huidige PCI DSS-attestation (Data Security Standard).

Zie [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/Compliance/PCI) voor meer informatie.

### <a name="does-managed-identities-for-azure-resources-work-with-virtual-machine-scale-sets"></a>Werken [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) met schaal sets voor virtuele machines?

Ja. U ziet enkele voor beelden van MSI-sjablonen in azure Quick Start-sjablonen voor [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi) en [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-msi).

## <a name="deleting"></a>Verwijderen

### <a name="will-the-locks-i-set-in-place-on-virtual-machine-scale-set-instances-be-respected-when-deleting-instances"></a>Worden de vergren delingen die ik heb ingesteld voor instanties van virtuele-machine schaal sets gerespecteerd bij het verwijderen van instanties?

In azure Portal kunt u een afzonderlijk exemplaar of bulksgewijs verwijderen verwijderen door meerdere exemplaren te selecteren. Als u probeert een enkele instantie te verwijderen die een vergren deling heeft, wordt de vergren deling gerespecteerd en kunt u het exemplaar niet verwijderen. Als u echter meerdere instanties tegelijk selecteert en een van deze instanties een vergren deling heeft, worden de vergren delingen niet geëerbiedigd en worden alle geselecteerde exemplaren verwijderd.

In azure CLI hebt u alleen de mogelijkheid om een afzonderlijk exemplaar te verwijderen. Als u probeert om één exemplaar te verwijderen dat een vergren deling heeft, wordt de vergren deling gerespecteerd en kunt u dat exemplaar niet verwijderen.

## <a name="extensions"></a>Uitbreidingen

### <a name="how-do-i-delete-a-virtual-machine-scale-set-extension"></a>Wilt u de extensie van een virtuele-machine schaalset Hoe kan ik verwijderen?

Als u een extensie voor virtuele-machine schaal sets wilt verwijderen, gebruikt u het volgende Power shell-voor beeld:

```powershell
$vmss = Get-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName"

$vmss=Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```

U kunt de waarde van de Extensienaam vinden in `$vmss` .

### <a name="is-there-a-virtual-machine-scale-set-template-example-that-integrates-with-azure-monitor-logs"></a>Is er een voor beeld van een sjabloon voor virtuele-machine schaal sets die kan worden geïntegreerd met Azure Monitor-logboeken?

Voor een voor beeld van een sjabloon voor virtuele-machine schaal sets die kan worden geïntegreerd met Azure Monitor-logboeken, raadpleegt u het tweede voor beeld in [een Azure service Fabric-cluster implementeren en bewaking inschakelen met behulp van Azure monitor logboeken](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric).

### <a name="how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set"></a>Hoe kan ik een uitbrei ding toevoegen aan alle Vm's in mijn schaalset voor virtuele machines?

Als update beleid is ingesteld op **automatisch**, waarbij de sjabloon opnieuw wordt geïmplementeerd met de nieuwe extensie-eigenschappen, worden alle virtuele machines bijgewerkt.

Als update beleid is ingesteld op **hand matig**, moet u eerst de extensie bijwerken en vervolgens alle exemplaren in uw vm's hand matig bijwerken.

### <a name="if-the-extensions-associated-with-an-existing-virtual-machine-scale-set-are-updated-are-existing-vms-affected"></a>Als de uitbrei dingen die zijn gekoppeld aan een bestaande schaalset voor virtuele machines worden bijgewerkt, zijn bestaande Vm's betrokken?

Als de extensie definitie in het model voor virtuele-machine schaal sets wordt bijgewerkt en de eigenschap upgrade Policy is ingesteld op **automatisch**, worden de virtuele machines bijgewerkt. Als de eigenschap upgrade Policy is ingesteld op **hand matig**, worden uitbrei dingen gemarkeerd als niet-overeenkomend met het model.

### <a name="are-extensions-run-again-when-an-existing-machine-is-service-healed-or-reimaged"></a>Worden uitbrei dingen opnieuw uitgevoerd wanneer een bestaande machine een service opnieuw heeft hersteld of een installatie kopie heeft hersteld?

Als een bestaande virtuele machine wordt hersteld, wordt deze weer gegeven als opnieuw opstarten en de uitbrei dingen worden niet opnieuw uitgevoerd. Als er een installatie kopie van een virtuele machine wordt uitgevoerd, is het proces vergelijkbaar met het vervangen van het station van het besturings systeem met de bron installatie kopie. Elke specialisatie van het meest recente model, zoals uitbrei dingen, wordt opnieuw uitgevoerd.

### <a name="how-do-i-join-a-virtual-machine-scale-set-to-an-active-directory-domain"></a>Hoe kan ik een schaalset voor een virtuele machine toevoegen aan een Active Directory domein?

Als u een schaalset voor virtuele machines wilt toevoegen aan een Active Directory (AD)-domein, kunt u een uitbrei ding definiëren.

Als u een uitbrei ding wilt definiëren, gebruikt u de eigenschap JsonADDomainExtension:

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "joindomain",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "JsonADDomainExtension",
                "typeHandlerVersion": "1.3",
                "settings": {
                    "Name": "[parameters('domainName')]",
                    "OUPath": "[variables('ouPath')]",
                    "User": "[variables('domainAndUsername')]",
                    "Restart": "true",
                    "Options": "[variables('domainJoinOptions')]"
                },
                "protectedsettings": {
                    "Password": "[parameters('domainJoinPassword')]"
                }
            }
        }
    ]
}
```

### <a name="my-virtual-machine-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot"></a>De extensie voor de virtuele-machine schaalset probeert iets te installeren waarvoor opnieuw moet worden opgestart.

Als de extensie van de virtuele-machine schaalset probeert om iets te installeren waarvoor opnieuw opstarten is vereist, kunt u de extensie Azure Automation desired state Configuration (Automation DSC) gebruiken. Als het besturings systeem Windows Server 2012 R2 is, haalt Azure de Windows Management Framework (WMF) 5,0 Setup opnieuw op en gaat vervolgens door met de configuratie.

### <a name="how-do-i-turn-on-antimalware-in-my-virtual-machine-scale-set"></a>Antimalware in mijn schaalset voor virtuele machines Hoe kan ik inschakelen?

Gebruik het volgende Power shell-voor beeld om antimalware in te scha kelen voor de schaalset van de virtuele machine:

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'

# Retrieve the most recent version number of the extension.
$allVersions= (Get-AzVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]

$VMSS = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS
```

### <a name="how-do-i-execute-a-custom-script-thats-hosted-in-a-private-storage-account"></a>Hoe kan ik een aangepast script uitvoeren dat wordt gehost in een privé opslag account?

Als u een aangepast script wilt uitvoeren dat wordt gehost in een privé-opslag account, stelt u beveiligde instellingen in met de sleutel en de naam van het opslag account. Zie [Custom Script extension](../virtual-machines/extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json#property-managedidentity)(Engelstalig) voor meer informatie.

## <a name="passwords"></a>Wachtwoorden

### <a name="how-do-i-reset-the-password-for-vms-in-my-virtual-machine-scale-set"></a>Hoe kan ik het wacht woord voor Vm's in mijn schaalset voor virtuele machines opnieuw instellen?

Er zijn twee belang rijke manieren om het wacht woord voor Vm's in schaal sets te wijzigen.

- Wijzig het model van de virtuele-machine schaalset rechtstreeks. Beschikbaar met API 2017-12-01 en hoger.

    Werk de beheerders referenties rechtstreeks bij in het model met de schaalset (bijvoorbeeld met behulp van de Azure Resource Explorer, Power shell of CLI). Zodra de schaalset is bijgewerkt, hebben alle nieuwe virtuele machines de nieuwe referenties. Bestaande Vm's hebben alleen de nieuwe referenties als ze worden geimageeerd.

- Stel het wacht woord opnieuw in met behulp van de toegangs uitbreidingen van de VM. Zorg ervoor dat u de wachtwoord vereisten volgt, zoals [hier](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)wordt beschreven.

    Gebruik het volgende PowerShell-voorbeeld:

    ```powershell
    $vmssName = "myvmss"
    $vmssResourceGroup = "myvmssrg"
    $publicConfig = @{"UserName" = "newuser"}
    $privateConfig = @{"Password" = "********"}

    $extName = "VMAccessAgent"
    $publisher = "Microsoft.Compute"
    $vmss = Get-AzVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
    $vmss = Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
    Update-AzVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName -VirtualMachineScaleSet $vmss
    ```

## <a name="networking"></a>Netwerken

### <a name="is-it-possible-to-assign-a-network-security-group-nsg-to-a-scale-set-so-that-it-applies-to-all-the-vm-nics-in-the-set"></a>Is het mogelijk een netwerk beveiligings groep (NSG) toe te wijzen aan een schaalset, zodat deze van toepassing is op alle VM-Nic's in de set?

Ja. Een netwerk beveiligings groep kan rechtstreeks worden toegepast op een schaalset door ernaar te verwijzen in de sectie Networkinterfaceconfigurations schaalset van het netwerk profiel. Voorbeeld:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

### <a name="how-do-i-do-a-vip-swap-for-virtual-machine-scale-sets-in-the-same-subscription-and-same-region"></a>Hoe kan ik een VIP-swap voor schaal sets voor virtuele machines in hetzelfde abonnement en dezelfde regio?

Als u twee virtuele-machine schaal sets met Azure Load Balancer front-ends hebt, en deze zich in hetzelfde abonnement en dezelfde regio bevinden, kunt u de toewijzing van de open bare IP-adressen van elke computer ongedaan maken en toewijzen aan de andere. Zie [VIP swap: Blue-groen implementatie in azure Resource Manager](https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/) bijvoorbeeld. Dit impliceert een vertraging, hoewel de toewijzing van resources ongedaan wordt gemaakt/toegewezen op netwerk niveau. Een snellere optie is het gebruik van Azure-toepassing gateway met twee back-end-Pools en een routerings regel. U kunt uw toepassing ook hosten met [Azure-app-service](https://azure.microsoft.com/services/app-service/) die ondersteuning biedt voor snelle overstap tussen staging-en productie sleuven.

### <a name="how-do-i-specify-a-range-of-private-ip-addresses-to-use-for-static-private-ip-address-allocation"></a>Hoe kan ik een bereik van privé-IP-adressen opgeven die moeten worden gebruikt voor de toewijzing van statische privé-IP-adressen?

IP-adressen worden geselecteerd in een subnet dat u opgeeft.

De toewijzings methode van IP-adressen voor virtuele-machine schaal sets is altijd ' dynamisch ', maar dat betekent niet dat deze IP-adressen kunnen worden gewijzigd. In dit geval betekent ' dynamisch ' alleen dat u het IP-adres in een PUT-aanvraag opgeeft. Geef de statische set op met behulp van het subnet.

### <a name="how-do-i-deploy-a-virtual-machine-scale-set-to-an-existing-azure-virtual-network"></a>Hoe kan ik een schaalset voor virtuele machines implementeren naar een bestaand virtueel Azure-netwerk?

Als u een schaalset voor virtuele machines wilt implementeren in een bestaand virtueel Azure-netwerk, raadpleegt u [een virtuele-machine schaalset implementeren in een bestaand virtueel netwerk](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet).

### <a name="can-i-use-scale-sets-with-accelerated-networking"></a>Kan ik schaal sets gebruiken met versneld netwerken?

Ja. Als u versneld netwerken wilt gebruiken, stelt u enableAcceleratedNetworking in op waar in de Networkinterfaceconfigurations schaalset instellingen van uw schaalset. Bijvoorbeeld

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "niconfig1",
            "properties": {
                "primary": true,
                "enableAcceleratedNetworking" : true,
                "ipConfigurations": [
                ]
            }
        }
    ]
}
```

### <a name="how-can-i-configure-the-dns-servers-used-by-a-scale-set"></a>Hoe kan ik de DNS-servers configureren die worden gebruikt door een schaalset?

Als u een schaalset voor virtuele machines met een aangepaste DNS-configuratie wilt maken, voegt u een dnsSettings JSON-pakket toe aan de sectie Scale set Networkinterfaceconfigurations schaalset. Voorbeeld:

```json
    "dnsSettings":{
        "dnsServers":["10.0.0.6", "10.0.0.5"]
    }
```

### <a name="how-can-i-configure-a-scale-set-to-assign-a-public-ip-address-to-each-vm"></a>Hoe kan ik een schaalset configureren om een openbaar IP-adres toe te wijzen aan elke VM?

Als u een schaalset voor virtuele machines wilt maken waarmee een openbaar IP-adres aan elke virtuele machine wordt toegewezen, moet u ervoor zorgen dat de API-versie van de micro soft. Compute/virtualMachineScaleSets-resource 2017-03-30 is en een _publicipaddressconfiguration toe_ JSON-pakket toevoegen aan de sectie ipConfigurations van schaal sets. Voorbeeld:

```json
    "publicipaddressconfiguration": {
        "name": "pub1",
        "properties": {
        "idleTimeoutInMinutes": 15
        }
    }
```

### <a name="can-i-configure-a-scale-set-to-work-with-multiple-application-gateways"></a>Kan ik een schaalset configureren om te werken met meerdere toepassings gateways?

Ja. U kunt de resource-Id's voor meerdere Application Gateway back-end-adres groepen toevoegen aan de lijst _applicationGatewayBackendAddressPools_ in het gedeelte _ipConfigurations_ van het netwerk profiel van de schaalset.

## <a name="scale"></a>Schalen

### <a name="in-what-case-would-i-create-a-virtual-machine-scale-set-with-fewer-than-two-vms"></a>In welke gevallen maakt u een schaalset voor virtuele machines met minder dan twee Vm's?

Een reden voor het maken van een schaalset voor virtuele machines met minder dan twee Vm's zou de elastische eigenschappen van een schaalset voor virtuele machines moeten gebruiken. U kunt bijvoorbeeld een schaalset voor virtuele machines met nul Vm's implementeren om uw infra structuur te definiëren zonder dat de kosten voor het uitvoeren van VM'S worden betaald. Wanneer u klaar bent voor het implementeren van Vm's, verhoogt u de capaciteit van de schaalset voor virtuele machines naar het aantal productie-exemplaren.

Een andere reden voor het maken van een schaalset voor virtuele machines met minder dan twee Vm's is als u minder ervaring hebt dan bij het gebruik van een beschikbaarheidsset met discrete Vm's. Met schaal sets voor virtuele machines kunt u werken met niet-gedifferentieerde reken eenheden die vervangbaar zijn. Deze uniformiteit is een Key onderscheid voor virtuele-machine schaal sets versus beschikbaarheids sets. Veel stateless werk belastingen volgen geen afzonderlijke eenheden. Als de werk belasting daalt, kunt u omlaag schalen naar één reken eenheid en vervolgens omhoog schalen tot veel wanneer de werk belasting toeneemt.

### <a name="how-do-i-change-the-number-of-vms-in-a-virtual-machine-scale-set"></a>Hoe kan ik het aantal Vm's in een schaalset voor virtuele machines gewijzigd?

Als u het aantal virtuele machines in een VM-schaalset in de Azure Portal wilt wijzigen, klikt u in de sectie eigenschappen van virtuele-machine schaalset op de Blade schalen en gebruikt u de schuif regelaar.

### <a name="how-do-i-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>Hoe kan ik aangepaste waarschuwingen definiëren wanneer bepaalde drempel waarden worden bereikt?

U hebt een zekere mate van flexibiliteit bij het verwerken van waarschuwingen voor opgegeven drempel waarden. U kunt bijvoorbeeld aangepaste webhooks definiëren. Het volgende voor beeld van een webhook is afkomstig uit een resource manager-sjabloon:

```json
{
    "type": "Microsoft.Insights/autoscaleSettings",
    "apiVersion": "[variables('insightsApi')]",
    "name": "autoscale",
    "location": "[parameters('resourceLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
    ],
    "properties": {
        "name": "autoscale",
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
        "enabled": true,
        "notifications": [
            {
                "operation": "Scale",
                "email": {
                    "sendToSubscriptionAdministrator": true,
                    "sendToSubscriptionCoAdministrators": true,
                    "customEmails": [
                        "youremail@address.com"
                    ]
                },
                "webhooks": [
                    {
                        "serviceUri": "<service uri>",
                        "properties": {
                            "key1": "custommetric",
                            "key2": "scalevmss"
                        }
                    }
                ]
            }
        ]
    }
}
```


## <a name="patching-and-operations"></a>Patches en bewerkingen

### <a name="can-i-create-a-scale-set-in-an-existing-resource-group"></a>Kan ik een schaalset maken in een bestaande resource groep?

Ja, u kunt een schaalset maken in een bestaande resource groep.

### <a name="can-i-move-a-scale-set-to-another-resource-group"></a>Kan ik een schaalset verplaatsen naar een andere resource groep?

Ja, u kunt resources van schaal sets verplaatsen naar een nieuw abonnement of een nieuwe resource groep.

### <a name="how-to-i-update-my-virtual-machine-scale-set-to-a-new-image-how-do-i-manage-patching"></a>Hoe kan ik mijn schaalset voor virtuele machines bijwerken naar een nieuwe installatie kopie? Patching Hoe kan ik beheren?

Als u de schaalset van de virtuele machine wilt bijwerken naar een nieuwe installatie kopie en u patching wilt beheren, raadpleegt u [een upgrade van een virtuele-machine schaalset](./virtual-machine-scale-sets-upgrade-scale-set.md).

### <a name="can-i-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-i-want-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>Kan ik de bewerking voor het opnieuw instellen van het image gebruiken om een virtuele machine opnieuw in te stellen zonder de installatie kopie te wijzigen? (Dat wil zeggen, ik wil een virtuele machine opnieuw instellen op de fabrieks instellingen in plaats van naar een nieuwe installatie kopie.)

Ja, u kunt de bewerking opnieuw instellen gebruiken om een virtuele machine opnieuw in te stellen zonder de installatie kopie te wijzigen. Als uw virtuele-machine schaalset echter verwijst naar een platform installatie kopie met `version = latest` , kan uw VM worden bijgewerkt naar een latere installatie kopie van het besturings systeem wanneer u aanroept `reimage` .

### <a name="is-it-possible-to-integrate-scale-sets-with-azure-monitor-logs"></a>Is het mogelijk om schaal sets te integreren met Azure Monitor-logboeken?

Ja, u kunt de Azure Monitor-extensie installeren op de schaal sets Vm's. Hier volgt een Azure CLI-voor beeld:

```azurecli
az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group Team-03 --vmss-name nt01 --settings "{'workspaceId': '<your workspace ID here>'}" --protected-settings "{'workspaceKey': '<your workspace key here'}"
```

U kunt de vereiste workspaceId en workspaceKey vinden in de werk ruimte Log Analytics van Azure Portal. Klik op de pagina overzicht op de tegel instellingen. Klik op het tabblad verbonden bronnen bovenaan.

> [!NOTE]
> Als uw schaalset _upgrade Policy_ is ingesteld op hand matig, moet u de uitbrei ding Toep assen op alle virtuele machines in de set door de upgrade hiervoor aan te roepen. In CLI zou dit _AZ vmss update-instances_ zijn.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="how-do-i-turn-on-boot-diagnostics"></a>Diagnostische gegevens over opstarten Hoe kan ik inschakelen?

Als u Diagnostische gegevens over opstarten wilt inschakelen, moet u eerst een opslag account maken. Plaats vervolgens dit JSON-blok in uw virtuele- **virtualMachineProfile** en werk de schaalset voor virtuele machines bij:

```json
"diagnosticsProfile": {
    "bootDiagnostics": {
        "enabled": true,
        "storageUri": "http://yourstorageaccount.blob.core.windows.net"
    }
}
```

Wanneer er een nieuwe virtuele machine wordt gemaakt, worden in de eigenschap InstanceView van de VM de details van de scherm opname weer gegeven, enzovoort. Hier volgt een voorbeeld:

```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
}
```

## <a name="virtual-machine-properties"></a>Eigenschappen van de virtuele machine

### <a name="how-do-i-get-property-information-for-each-vm-without-making-multiple-calls-for-example-how-would-i-get-the-fault-domain-for-each-of-the-100-vms-in-my-virtual-machine-scale-set"></a>Hoe kan ik informatie over de eigenschap voor elke VM ophalen zonder meerdere aanroepen uit te voeren? Hoe krijg ik bijvoorbeeld het fout domein voor elk van de 100 Vm's in mijn schaalset voor virtuele machines?

Als u informatie over de eigenschappen voor elke VM wilt ophalen zonder meerdere aanroepen te maken, kunt u aanroepen `ListVMInstanceViews` door een rest API te doen `GET` op de volgende bron-URI:

/Subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines? $expand = instanceView&$select = instanceView

### <a name="can-i-pass-different-extension-arguments-to-different-vms-in-a-virtual-machine-scale-set"></a>Kan ik verschillende extensie argumenten door geven aan verschillende Vm's in een schaalset voor virtuele machines?

Nee, u kunt geen andere extensie argumenten door geven aan verschillende virtuele machines in een schaalset met virtuele machines. Uitbrei dingen kunnen echter wel reageren op basis van de unieke eigenschappen van de virtuele machine waarop ze worden uitgevoerd, zoals op de computer naam. Uitbrei dingen kunnen ook meta gegevens van exemplaren opvragen voor http://169.254.169.254 meer informatie over de virtuele machine.

### <a name="why-are-there-gaps-between-my-virtual-machine-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>Waarom zijn er hiaten tussen de VM-computer namen en VM-Id's van de virtuele-machine schaal sets? Bijvoorbeeld: 0, 1, 3...

Er zijn hiaten tussen de VM-computer namen en VM-Id's van de virtuele machine. de eigenschap Scale set van de virtuele machine **is ingesteld op** de standaard waarde **waar**. Als overinrichting is ingesteld op **True**, worden er meer vm's gemaakt dan aangevraagd. Extra Vm's worden vervolgens verwijderd. In dit geval krijgt u een grotere betrouw baarheid van de implementatie, maar ten koste van aaneengesloten en aaneengesloten regels voor netwerkadresomzetting (NAT).

U kunt deze eigenschap instellen op **Onwaar**. Voor kleine virtuele-machine schaal sets heeft dit geen invloed op de betrouw baarheid van de implementatie.

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-virtual-machine-scale-set-and-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>Wat is het verschil tussen het verwijderen van een virtuele machine in een VM-schaalset en het ongedaan toewijzen van de toewijzing van de VM? Wanneer moet ik een van de andere kiezen?

Het belangrijkste verschil tussen het verwijderen van een virtuele machine in een VM-schaalset en het ongedaan maken van de toewijzing van de VM is dat de `deallocate` virtuele harde schijven (vhd's) niet worden verwijderd. Er zijn opslag kosten gekoppeld aan het uitvoeren van `stop deallocate` . U kunt een van de volgende redenen gebruiken:

- U wilt stoppen met het betalen van reken kosten, maar u wilt de schijf status van de Vm's blijven gebruiken.
- U wilt een serie Vm's sneller starten dan het schalen van een schaalset voor virtuele machines.
  - Met betrekking tot dit scenario hebt u mogelijk uw eigen engine voor automatisch schalen gemaakt en wilt u een snelle end-to-end-schaal.
- U hebt een schaalset voor virtuele machines die onevenredig wordt verdeeld over fout domeinen of update domeinen. Dit kan zijn omdat u een selectief verwijderde Vm's hebt gemaakt, of omdat de virtuele machines zijn verwijderd na de inrichting. Wordt uitgevoerd `stop deallocate` , gevolgd door `start` de schaalset van de virtuele machine, verdeelt de vm's gelijkmatig over fout domeinen of update domeinen.

### <a name="how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance"></a>Hoe kan ik een moment opname van een virtuele-machine Scale set-exemplaar maken?
Maak een moment opname van een exemplaar van een virtuele-machine schaalset.

```azurepowershell-interactive
$rgname = "myResourceGroup"
$vmssname = "myVMScaleSet"
$Id = 0
$location = "East US"

$vmss1 = Get-AzVmssVM -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $Id     
$snapshotconfig = New-AzSnapshotConfig -Location $location -AccountType Standard_LRS -OsType Windows -CreateOption Copy -SourceUri $vmss1.StorageProfile.OsDisk.ManagedDisk.id
New-AzSnapshot -ResourceGroupName $rgname -SnapshotName 'mySnapshot' -Snapshot $snapshotconfig
```

Een beheerde schijf maken op basis van de moment opname.

```azurepowershell-interactive
$snapshotName = "mySnapshot"
$snapshot = Get-AzSnapshot -ResourceGroupName $rgname -SnapshotName $snapshotName  
$diskConfig = New-AzDiskConfig -AccountType Premium_LRS -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
$osDisk = New-AzDisk -Disk $diskConfig -ResourceGroupName $rgname -DiskName ($snapshotName + '_Disk')
```
