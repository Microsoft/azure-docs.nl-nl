---
title: Certificaten in een Azure Service Fabric-cluster beheren
description: Hierin wordt beschreven hoe u nieuwe certificaten, rollover certificaat en certificaat kunt toevoegen aan of verwijderen uit een Service Fabric-cluster.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6dd4440d76bed9d110c13baab9f4e67b3a5c64c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660896"
---
# <a name="add-or-remove-certificates-for-a-service-fabric-cluster-in-azure"></a>Certificaten voor een Service Fabric-cluster in Azure toevoegen of verwijderen
Het is raadzaam om vertrouwd te raken met de manier waarop Service Fabric X. 509-certificaten gebruikt en dat u vertrouwd bent met de [beveiligings scenario's](service-fabric-cluster-security.md)voor het cluster. Voordat u verder gaat, moet u weten wat een cluster certificaat is en wat wordt gebruikt voor.

De standaard methode voor het laden van het certificaat van de SDK van Azure service fabrics is het implementeren en gebruiken van het gedefinieerde certificaat met de verloop datum die het verst in de toekomst ligt. ongeacht de definitie van de primaire of secundaire configuratie. Terugvallen op het klassieke gedrag is een niet-aanbevolen geavanceerde actie. hiervoor moet de waarde van de para meter "UseSecondaryIfNewer" worden ingesteld op False binnen uw `Fabric.Code` configuratie.

Met Service Fabric kunt u twee cluster certificaten, een primaire en een secundaire, opgeven wanneer u certificaat beveiliging configureert tijdens het maken van het cluster, naast client certificaten. Raadpleeg het [maken van een Azure-cluster via de portal](service-fabric-cluster-creation-via-portal.md) of [het maken van een Azure-cluster via Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) voor meer informatie over het instellen van de clusters tijdens het maken van een tijd. Als u per keer een cluster certificaat opgeeft, wordt dit gebruikt als het primaire certificaat. Nadat het cluster is gemaakt, kunt u een nieuw certificaat toevoegen als secundair.

> [!NOTE]
> Voor een beveiligd cluster hebt u altijd ten minste één geldig (niet ingetrokken en niet-verlopen) cluster certificaat (primair of secundair) geïmplementeerd (als dat niet het geval is, werkt het cluster niet meer). 90 dagen voordat alle geldige certificaten verlopen, genereert het systeem een waarschuwings tracering en een waarschuwings status gebeurtenis op het knoop punt. Dit zijn momenteel de enige meldingen Service Fabric worden verzonden met betrekking tot het verlopen van het certificaat.
> 
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-a-secondary-cluster-certificate-using-the-portal"></a>Een secundair cluster certificaat toevoegen met behulp van de portal
Het secundaire cluster certificaat kan niet worden toegevoegd via de Azure Portal; gebruik Azure PowerShell. Het proces wordt verderop in dit document beschreven.

## <a name="remove-a-cluster-certificate-using-the-portal"></a>Een cluster certificaat verwijderen via de portal
Voor een beveiligd cluster hebt u altijd Mini maal één geldig certificaat (niet ingetrokken en niet verlopen) nodig. Het certificaat dat het meest voor de volgende verval datum is geïmplementeerd, wordt in gebruik en de verwijdering van het cluster werkt niet meer. Zorg ervoor dat u alleen het certificaat verwijdert dat is verlopen of dat een ongebruikt certificaat dat het kortste verloopt.

Als u een niet-gebruikt cluster beveiligings certificaat wilt verwijderen, gaat u naar de sectie Beveiliging en selecteert u de optie verwijderen in het context menu van het ongebruikte certificaat.

Als u het certificaat dat is gemarkeerd als primair wilt verwijderen, moet u een secundair certificaat met een verlopende datum verder in de toekomst implementeren dan het primaire certificaat, waardoor het gedrag voor automatisch rollover wordt ingeschakeld. Verwijder het primaire certificaat nadat de automatische rollover is voltooid.

## <a name="add-a-secondary-certificate-using-azure-resource-manager"></a>Een secundair certificaat toevoegen met Azure Resource Manager

Bij deze stappen wordt ervan uitgegaan dat u bekend bent met het gebruik van Resource Manager en ten minste één Service Fabric cluster met een resource manager-sjabloon hebt geïmplementeerd. u hebt de sjabloon die u hebt gebruikt voor het instellen van het cluster. Ook wordt ervan uitgegaan dat u bent vertrouwd met JSON.

> [!NOTE]
> Als u op zoek bent naar een voorbeeld sjabloon en para meters die u kunt gebruiken om samen te volgen of als uitgangs punt, downloadt u deze vanaf deze [Git-opslag plaats](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample). 
> 
> 

### <a name="edit-your-resource-manager-template"></a>Uw Resource Manager-sjabloon bewerken

Om het volgende te vergemakkelijken, bevat de voorbeeld 5-VM-1-NodeTypes-Secure_Step2.JSop alle bewerkingen die we zullen door voeren. het voor beeld is beschikbaar op [Git-opslag plaats](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample).

**Zorg ervoor dat u alle stappen hebt gevolgd**

1. Open de Resource Manager-sjabloon die u hebt gebruikt voor het implementeren van het cluster. (Als u het voor beeld hebt gedownload van de voor gaande opslag plaats, gebruikt u 5-VM-1-NodeTypes-Secure_Step1.JSin om een beveiligd cluster te implementeren en vervolgens de sjabloon te openen).

2. Voeg **twee nieuwe para meters** ' secCertificateThumbprint ' en ' secCertificateUrlValue ' van het type ' String ' toe aan de para meter-sectie van uw sjabloon. U kunt het volgende code fragment kopiëren en toevoegen aan de sjabloon. Afhankelijk van de bron van uw sjabloon hebt u deze mogelijk al gedefinieerd. als dat het geval is, gaat u verder met de volgende stap. 
 
    ```json
       "secCertificateThumbprint": {
          "type": "string",
          "metadata": {
            "description": "Certificate Thumbprint"
          }
        },
        "secCertificateUrlValue": {
          "type": "string",
          "metadata": {
            "description": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
          }
        },
    
    ```

3. Wijzigingen aanbrengen in de resource van **micro soft. ServiceFabric/clusters** : Zoek de resource definitie ' micro soft. ServiceFabric/clusters ' in uw sjabloon. Onder eigenschappen van die definitie vindt u het JSON-label ' certificaat '. dit moet er ongeveer uitzien als in het volgende JSON-fragment:
   
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Voeg een nieuwe tag ' thumbprintSecondary ' toe en geef deze de waarde ' [para meters (' secCertificateThumbprint ')].  

    De resource definitie moet er nu als volgt uitzien (afhankelijk van de bron van de sjabloon, is dit mogelijk niet precies hetzelfde als het onderstaande fragment). 

    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('certificateThumbprint')]",
              "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

    Als u **het certificaat wilt overnemen**, geeft u het nieuwe certificaat op als primair en verplaatst u de huidige primaire as secundair. Dit resulteert in de overschakeling van uw huidige primaire certificaat in één implementatie stap naar het nieuwe certificaat.
    
    ```JSON
          "properties": {
            "certificate": {
              "thumbprint": "[parameters('secCertificateThumbprint')]",
              "thumbprintSecondary": "[parameters('certificateThumbprint')]",
              "x509StoreName": "[parameters('certificateStoreValue')]"
         }
    ``` 

4. Breng wijzigingen aan  in alle **micro soft. Compute/virtualMachineScaleSets** resource definities-Zoek de resource definitie micro soft. Compute/virtualMachineScaleSets. Ga naar ' Publisher ': ' micro soft. Azure. ServiceFabric ', onder ' virtualMachineProfile '.

    In de instellingen voor de Service Fabric uitgever ziet u iets als in.
    
    ![Json_Pub_Setting1][Json_Pub_Setting1]
    
    De nieuwe certificaat vermeldingen toevoegen
    
    ```json
                   "certificateSecondary": {
                        "thumbprint": "[parameters('secCertificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    
    ```

    De eigenschappen moeten er nu als volgt uitzien
    
    ![Json_Pub_Setting2][Json_Pub_Setting2]
    
    Als u **het certificaat wilt overnemen**, geeft u het nieuwe certificaat op als primair en verplaatst u de huidige primaire as secundair. Dit resulteert in de overschakeling van uw huidige certificaat in één implementatie stap naar het nieuwe certificaat.     

    ```json
                   "certificate": {
                       "thumbprint": "[parameters('secCertificateThumbprint')]",
                       "x509StoreName": "[parameters('certificateStoreValue')]"
                         },
                   "certificateSecondary": {
                        "thumbprint": "[parameters('certificateThumbprint')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                        }
                      },
    ```

    De eigenschappen moeten er nu als volgt uitzien    
    ![Json_Pub_Setting3][Json_Pub_Setting3]

5. Breng wijzigingen aan  in alle **micro soft. Compute/virtualMachineScaleSets** resource definities-Zoek de resource definitie micro soft. Compute/virtualMachineScaleSets. Ga naar ' vaultCertificates ':, onder ' OSProfile '. Dit ziet er ongeveer als volgt uit.

    ![Json_Pub_Setting4][Json_Pub_Setting4]
    
    Voeg het secCertificateUrlValue toe. Gebruik het volgende code fragment:
    
    ```json
                      {
                        "certificateStore": "[parameters('certificateStoreValue')]",
                        "certificateUrl": "[parameters('secCertificateUrlValue')]"
                      }
    
    ```
    De resulterende JSON moet er nu ongeveer als volgt uitzien.
    ![Json_Pub_Setting5][Json_Pub_Setting5]


> [!NOTE]
> Zorg ervoor dat u stap 4 en 5 hebt herhaald voor alle Nodetypes/micro soft. Compute/virtualMachineScaleSets-resource definities in uw sjabloon. Als u een van deze opties mist, wordt het certificaat niet geïnstalleerd op de schaalset van die virtuele machine en hebt u onvoorspelbare resultaten in uw cluster, inclusief het cluster (als u er geen geldige certificaten voor hebt die het cluster kan gebruiken voor beveiliging. Controleer daarom dubbele controle voordat u doorgaat.
> 
> 

### <a name="edit-your-template-file-to-reflect-the-new-parameters-you-added-above"></a>Bewerk uw sjabloon bestand om de nieuwe para meters weer te geven die u hierboven hebt toegevoegd
Als u het voor beeld van het [Git-opslag plaats](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Cert-Rollover-Sample) gebruikt om samen te volgen, kunt u beginnen met het maken van wijzigingen in de voorbeeld 5-VM-1-NodeTypes-Secure.parameters_Step2.JSop 

Bewerk het parameter bestand voor de Resource Manager-sjabloon, voeg de twee nieuwe para meters voor secCertificateThumbprint en secCertificateUrlValue toe. 

```JSON
    "secCertificateThumbprint": {
      "value": "thumbprint value"
    },
    "secCertificateUrlValue": {
      "value": "Refers to the location URL in your key vault where the certificate was uploaded, it is should be in the format of https://<name of the vault>.vault.azure.net:443/secrets/<exact location>"
     },

```

### <a name="deploy-the-template-to-azure"></a>De sjabloon implementeren in Azure

- U bent nu klaar om uw sjabloon te implementeren in Azure. Open een opdracht prompt van Azure PS versie 1 +.
- Meld u aan bij uw Azure-account en selecteer het specifieke Azure-abonnement. Dit is een belang rijke stap voor mensen die toegang hebben tot meer dan één Azure-abonnement.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId <Subscription ID> 

```

Test de sjabloon voordat u deze implementeert. Gebruik dezelfde resource groep als waarin uw cluster momenteel is geïmplementeerd.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

Implementeer de sjabloon in uw resource groep. Gebruik dezelfde resource groep als waarin uw cluster momenteel is geïmplementeerd. Voer de New-AzResourceGroupDeployment opdracht uit. U hoeft de modus niet op te geven, omdat de standaard waarde **Incrementeel** is.

> [!NOTE]
> Als u de modus instelt op voltooid, kunt u per ongeluk resources verwijderen die zich niet in uw sjabloon bevinden. In dit scenario gebruikt u dit dus niet.
> 
> 

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

Hier volgt een ingevuld voor beeld van dezelfde Power shell.

```powershell
$ResourceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResourceGroup2

```

Zodra de implementatie is voltooid, maakt u verbinding met het cluster met behulp van het nieuwe certificaat en voert u enkele query's uit. Als u dit kunt doen. Vervolgens kunt u het oude certificaat verwijderen. 

Als u een zelfondertekend certificaat gebruikt, vergeet dan niet om deze te importeren in uw lokale TrustedPeople-certificaat archief.

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
Raadpleeg de opdracht om verbinding te maken met een beveiligd cluster voor naslag informatie 

```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
Raadpleeg de opdracht voor het ophalen van de cluster status voor naslag informatie

```powershell
Get-ServiceFabricClusterHealth 
```

## <a name="deploying-client-certificates-to-the-cluster"></a>Client certificaten implementeren op het cluster.

U kunt dezelfde stappen gebruiken als in de voor gaande stappen 5 om de certificaten te implementeren van een sleutel kluis naar de knoop punten. U hoeft alleen verschillende para meters te definiëren en te gebruiken.


## <a name="adding-or-removing-client-certificates"></a>Client certificaten toevoegen of verwijderen

Naast de cluster certificaten kunt u client certificaten toevoegen om beheer bewerkingen uit te voeren op een Service Fabric cluster.

U kunt twee soorten client certificaten toevoegen: beheerder of alleen-lezen. Deze kunnen vervolgens worden gebruikt om de toegang tot de beheer bewerkingen en query bewerkingen op het cluster te beheren. Standaard worden de cluster certificaten toegevoegd aan de lijst met toegestane beheerders certificaten.

U kunt een wille keurig aantal client certificaten opgeven. Elke toevoeging/verwijdering resulteert in een configuratie-update voor het Service Fabric cluster.


### <a name="adding-client-certificates---admin-or-read-only-via-portal"></a>Client certificaten toevoegen-beheerder of Read-Only via Portal

1. Ga naar de sectie Beveiliging en selecteer de knop + verificatie boven in het gedeelte Beveiliging.
2. Kies in de sectie verificatie toevoegen de optie verificatie type-' alleen-lezen client ' of ' admin-client '.
3. Kies nu de autorisatie methode. Dit geeft aan Service Fabric of dit certificaat moet worden opgezocht met behulp van de onderwerpnaam of de vinger afdruk. Over het algemeen is het geen goede beveiligings procedure om de autorisatie methode van de object naam te gebruiken. 

![Client certificaat toevoegen][Add_Client_Cert]

### <a name="deletion-of-client-certificates---admin-or-read-only-using-the-portal"></a>Client certificaten verwijderen: beheerder of Read-Only met behulp van de portal

Als u een secundair certificaat wilt verwijderen dat wordt gebruikt voor cluster beveiliging, gaat u naar de sectie Beveiliging en selecteert u de optie verwijderen in het context menu van het specifieke certificaat.

## <a name="adding-application-certificates-to-a-virtual-machine-scale-set"></a>Toepassings certificaten toevoegen aan een schaalset voor virtuele machines

Zie [Dit Power shell-voorbeeld script](scripts/service-fabric-powershell-add-application-certificate.md)voor het implementeren van een certificaat dat u voor uw toepassingen gebruikt voor uw cluster.

## <a name="next-steps"></a>Volgende stappen
Lees deze artikelen voor meer informatie over Cluster beheer:

* [Upgrade proces en verwachtingen van de Cluster Service Fabric](service-fabric-cluster-upgrade.md)
* [Op rollen gebaseerde toegang voor clients instellen](service-fabric-cluster-security-roles.md)

<!--Image references-->
[Add_Client_Cert]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_13.PNG
[Json_Pub_Setting1]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_14.PNG
[Json_Pub_Setting2]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_15.PNG
[Json_Pub_Setting3]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_16.PNG
[Json_Pub_Setting4]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_17.PNG
[Json_Pub_Setting5]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_18.PNG


