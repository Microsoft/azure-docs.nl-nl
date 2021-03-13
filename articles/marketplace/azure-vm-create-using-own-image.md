---
title: Een Azure virtual machine-aanbieding maken op Azure Marketplace met uw eigen installatie kopie
description: Meer informatie over het publiceren van een aanbieding van een virtuele machine naar Azure Marketplace met uw eigen installatie kopie.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200463"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Een virtuele machine maken met uw eigen installatie kopie

In dit artikel wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert.

> [!NOTE]
> Voordat u met deze procedure begint, controleert u de [technische vereisten](marketplace-virtual-machines.md#technical-requirements) voor Azure VM-aanbiedingen, inclusief vereisten voor virtuele harde schijven (VHD).

Als u in plaats daarvan een goedgekeurde basis installatie kopie wilt gebruiken, volgt u de instructies in [een VM-installatie kopie maken van een goedgekeurde basis](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt verg Roten of verkleinen, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie op Azure Marketplace.

### <a name="size-the-vhds"></a>Grootte van de Vhd's

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Meer beveiligings controles uitvoeren

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>De installatie kopie generaliseren

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om dit te verhelpen, moet de VHD van het besturings systeem worden gegeneraliseerd, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

## <a name="bring-your-image-into-azure"></a>Uw installatie kopie naar Azure brengen

Er zijn drie manieren om uw installatie kopie naar Azure te brengen:

1. Upload de VHD naar een (SIG) gedeelde installatie kopie galerie.
1. Upload de VHD naar een Azure Storage-account.
1. De VHD extra heren uit een beheerde installatie kopie (als u gebruikmaakt van services voor het maken van afbeeldingen).

Deze opties worden beschreven in de volgende drie secties.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Optie 1: de VHD uploaden als galerie met gedeelde afbeeldingen

1. Upload VHD (s) naar het opslag account.
2. Zoek op de Azure Portal **een aangepaste sjabloon implementeren**.
3. Selecteer **Bouw uw eigen sjabloon in de editor**.
4. Kopieer de volgende Azure Resource Manager-sjabloon (ARM).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Plak de sjabloon in de editor.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Voorbeeld code scherm voor VM.":::

1. Selecteer **Opslaan**.
1. Gebruik de para meters in deze tabel om de velden in het volgende scherm te volt ooien.

| Parameters | Beschrijving |
| --- | --- |
| sourceStorageAccountResourceId | De resource-ID van het bron opslag account waarin de BLOB-VHD zich bevindt.<br><br>Als u de resource-ID wilt ophalen, gaat u naar uw **opslag account** op **Azure Portal**, gaat u naar **Eigenschappen** en kopieert u de waarde **ResourceID** . |
| sourceBlobUri | BLOB-URI van de VHD-blob van de besturingssysteem schijf (moet in het opslag account worden vermeld).<br><br>Als u de BLOB-URL wilt ophalen, gaat u naar uw **opslag account** op **Azure Portal**, gaat u naar uw **BLOB** en kopieert u de **URL** -waarde. |
| sourceBlobDataDisk0Uri | De BLOB-URI van de VHD-blob van de gegevens schijf (moet in het opslag account worden vermeld). Als u geen gegevens schijf hebt, verwijdert u deze para meter uit de sjabloon.<br><br>Als u de BLOB-URL wilt ophalen, gaat u naar uw **opslag account** op **Azure Portal**, gaat u naar uw **BLOB** en kopieert u de **URL** -waarde. |
| sourceBlobDataDisk1Uri | BLOB-URI van extra gegevens schijf VHD-BLOB (moet in het opslag account worden vermeld). Als u geen extra gegevens schijf hebt, verwijdert u deze para meter uit de sjabloon.<br><br>Als u de BLOB-URL wilt ophalen, gaat u naar uw **opslag account** op **Azure Portal**, gaat u naar uw **BLOB** en kopieert u de **URL** -waarde. |
| galerienaam | Naam van de galerie met gedeelde afbeeldingen |
| galleryImageDefinitionName | Naam van de definitie van de afbeelding |
| galleryImageVersionName | De naam van de afbeeldings versie die moet worden gemaakt, in deze indeling: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Hiermee wordt het aangepaste implementatie venster weer gegeven.":::

8. Selecteer **Controleren + maken**. Wanneer de validatie is voltooid, selecteert u **maken**.

> [!TIP]
> Het uitgevers account moet de ' eigenaar '-toegang hebben om de SIG-installatie kopie te publiceren. Als dat nodig is, volgt u de onderstaande stappen om toegang te verlenen:
>
> 1. Ga naar de galerie met gedeelde afbeeldingen (SIG).
> 2. Selecteer **toegangs beheer** (IAM) in het linkerdeel venster.
> 3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**.
> 4. Selecteer voor **rol** de optie **eigenaar**.
> 5. Selecteer **gebruiker, groep of Service-Principal** voor **toegang toewijzen aan**.
> 6. Voer het Azure-e-mail bericht in van de persoon die de installatie kopie gaat publiceren.
> 7. Selecteer **Opslaan**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Het venster roltoewijzing toevoegen wordt weer gegeven.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Optie 2: de VHD uploaden naar een opslag account

Configureer en bereid de virtuele machine die moet worden geüpload, zoals beschreven in [voorbereiden van een Windows VHD of VHDX voor het uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) of [het maken en uploaden van een virtuele Linux-VHD](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Optie 3: de VHD extra heren uit een beheerde installatie kopie (als u gebruikmaakt van services voor het maken van afbeeldingen)

Als u een installatie kopie voor het maken van afbeeldingen gebruikt, zoals de [pakketer](https://www.packer.io/), moet u mogelijk de VHD extra heren uit de installatie kopie. Er is geen rechtstreekse manier om dit te doen. U moet een virtuele machine maken en de VHD extra heren van de VM-schijf.

## <a name="create-the-vm-on-the-azure-portal"></a>Maak de virtuele machine op de Azure Portal

Volg deze stappen om de basis installatie kopie van de virtuele machine te maken op de [Azure Portal](https://ms.portal.azure.com/).

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).
2. Selecteer **Virtuele machines**.
3. Selecteer **+ toevoegen** om het scherm **een virtuele machine maken** te openen.
4. Selecteer de installatie kopie in de vervolg keuzelijst of selecteer **Bladeren alle open bare en persoonlijke installatie kopieën** om te zoeken of door alle beschik bare installatie kopieën van virtuele machines te bladeren.
5. Als u een virtuele machine van **generatie 2** wilt maken, gaat u naar het tabblad **Geavanceerd** en selecteert u de optie **generatie 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecteer gen 1 of gen 2.":::

6. Selecteer de grootte van de virtuele machine die u wilt implementeren.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecteer een aanbevolen VM-grootte voor de geselecteerde installatie kopie.":::

7. Geef de overige vereiste gegevens op om de virtuele machine te maken.
8. Selecteer **beoordeling + maken** om uw keuzes te controleren. Wanneer het bericht **door gegeven validatie** wordt weer gegeven, selecteert u **maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. Volg de voortgang van de taak door het tabblad **virtual machines** te selecteren in het menu links. Nadat de status van de virtuele machine is gemaakt, wordt deze gewijzigd in **actief**.

## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Raadpleeg de volgende documentatie om verbinding te maken met uw virtuele [Windows](../virtual-machines/windows/connect-logon.md) -of [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) -machine.

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Volgende stappen

- [Test uw VM-installatie kopie](azure-vm-image-test.md) om te controleren of deze voldoet aan de publicatie vereisten voor Azure Marketplace. Dit is optioneel.
- Als u uw VM-installatie kopie niet wilt testen, meldt u zich aan bij [partner centrum](https://partner.microsoft.com/) en publiceert u de SIG-installatie kopie (optie #1).
- Als u de optie #2 of #3 hebt gevolgd, [genereert u de SAS-URI](azure-vm-get-sas-uri.md).
- Zie [Veelgestelde vragen over vm's voor Azure Marketplace](azure-vm-create-faq.md)als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure.
