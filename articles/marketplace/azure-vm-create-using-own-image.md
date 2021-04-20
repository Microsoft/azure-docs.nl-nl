---
title: Een aanbieding voor virtuele Azure-machines maken op Azure Marketplace met behulp van uw eigen afbeelding
description: Meer informatie over het publiceren van een aanbieding voor virtuele machines Azure Marketplace met behulp van uw eigen afbeelding.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 47fe7b42b68ae42f74a74e5fc69c8d1041d3bf8d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727114"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Een virtuele machine maken met uw eigen afbeelding

In dit artikel wordt beschreven hoe u een door de gebruiker geleverde VM-afbeelding (virtuele machine) maakt en implementeert.

> [!NOTE]
> Voordat u met deze procedure begint, bekijkt u de technische [vereisten](marketplace-virtual-machines.md#technical-requirements) voor Azure VM-aanbiedingen, waaronder vereisten voor virtuele harde schijven (VHD).

Als u in plaats daarvan een goedgekeurde basisafbeelding wilt gebruiken, volgt u de instructies in [Een VM-afbeelding maken op basis van een goedgekeurde basis](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt formatteren, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden op de Azure Marketplace.

### <a name="size-the-vhds"></a>Grootte van de VHD's

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Meer beveiligingscontroles uitvoeren

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie- en geplande taken uitvoeren

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>De afbeelding generaliseren

Alle afbeeldingen in de Azure Marketplace moeten op een algemene manier opnieuw kunnen worden gebruikt. Hiervoor moet de VHD van het besturingssysteem worden ge generaliseerd, een bewerking waarmee alle instantiespecifieke id's en software-stuurprogramma's van een VM worden verwijderd.

## <a name="bring-your-image-into-azure"></a>Uw afbeelding naar Azure brengen

> [!NOTE]
> Het Azure-abonnement met de SIG moet onder dezelfde tenant als het uitgeversaccount vallen om te kunnen publiceren. Daarnaast moet het uitgeversaccount ten minste inzenderstoegang hebben tot het abonnement met SIG.

Er zijn drie manieren om uw afbeelding naar Azure te brengen:

1. Upload de vhd naar een Shared Image Gallery (SIG).
1. Upload de VHD naar een Azure-opslagaccount.
1. Extrahd uit een beheerde afbeelding (als u services voor het bouwen van afbeeldingen gebruikt).

In de volgende drie secties worden deze opties beschreven.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Optie 1: upload de VHD als Shared Image Gallery

1. VHD('s) uploaden naar opslagaccount.
2. Zoek op Azure Portal naar **Een aangepaste sjabloon implementeren.**
3. Selecteer **Bouw uw eigen sjabloon in de editor**.
4. Kopieer de volgende ARM Azure Resource Manager sjabloon.

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

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Voorbeeldcodescherm voor VM.":::

1. Selecteer **Opslaan**.
1. Gebruik de parameters in deze tabel om de velden in het volgende scherm in te vullen.

| Parameters | Description |
| --- | --- |
| sourceStorageAccountResourceId | Resource-id van het bronopslagaccount waarin de blob-vhd zich bevindt.<br><br>Als u de resource-id  wilt op halen, gaat u naar uw opslagaccount op **Azure Portal**, gaat u naar **Eigenschappen** en kopieert u de **waarde resource-id.** |
| sourceBlobUri | Blob-URI van de VHD-blob van de besturingssysteemschijf (moet zich in het opgegeven opslagaccount).<br><br>Als u de blob-URL  wilt op halen, gaat u naar uw opslagaccount op **Azure Portal,** gaat u naar **uw blob** en kopieert u **de URL-waarde.** |
| sourceBlobDataDisk0Uri | Blob-URI van de vhd-blob van de gegevensschijf (moet zich in het opgegeven opslagaccount). Als u geen gegevensschijf hebt, verwijdert u deze parameter uit de sjabloon.<br><br>Als u de blob-URL  wilt op halen, gaat u naar uw opslagaccount op **Azure Portal,** gaat u naar **uw blob** en kopieert u **de URL-waarde.** |
| sourceBlobDataDisk1Uri | Blob-URI van de VHD-blob van de extra gegevensschijf (moet zich in het opgegeven opslagaccount). Als u geen extra gegevensschijf hebt, verwijdert u deze parameter uit de sjabloon.<br><br>Als u de blob-URL  wilt op halen, gaat u naar uw opslagaccount **op Azure Portal,** gaat u naar **uw blob** en kopieert u de **URL-waarde.** |
| galleryName | Naam van de Shared Image Gallery |
| galleryImageDefinitionName | Naam van de definitie van de afbeelding |
| galleryImageVersionName | Naam van de versie van de afbeelding die moet worden gemaakt, in deze indeling: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Toont het venster voor aangepaste implementatie.":::

8. Selecteer **Controleren + maken**. Zodra de validatie is uitgevoerd, selecteert **u Maken.**

> [!TIP]
> Het uitgeversaccount moet 'Eigenaar'-toegang hebben om de SIG-afbeelding te publiceren. Volg indien nodig de onderstaande stappen om toegang te verlenen:
>
> 1. Ga naar de Shared Image Gallery (SIG).
> 2. Selecteer **Toegangsbeheer** (IAM) in het linkerpaneel.
> 3. Selecteer **Toevoegen** en vervolgens **Roltoewijzing toevoegen.**
> 4. Selecteer **bij Rol** de optie **Eigenaar.**
> 5. Selecteer **voor Toegang toewijzen aan** de optie **Gebruiker, groep of service-principal.**
> 6. Voer het Azure-e-mailadres in van de persoon die de afbeelding gaat publiceren.
> 7. Selecteer **Opslaan**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Het venster Roltoewijzing toevoegen wordt weergegeven.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Optie 2: de VHD uploaden naar een opslagaccount

Configureer en bereid de VM voor die moet worden geüpload, zoals beschreven in Prepare [a Windows VHD or VHDX to upload to Azure (Een Windows-VHD of VHD](../virtual-machines/windows/prepare-for-upload-vhd-image.md) voorbereiden voor uploaden naar Azure) of Create and Upload a Linux [VHD (Een Linux-VHD](../virtual-machines/linux/create-upload-generic.md)maken en uploaden).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Optie 3: de VHD uit de beheerde afbeelding extraheren (als u services voor het bouwen van afbeeldingen gebruikt)

Als u een service voor het bouwen van afbeeldingen gebruikt, zoals [Packer,](https://www.packer.io/)moet u mogelijk de VHD uit de afbeelding extraheren. Er is geen directe manier om dit te doen. U moet een VM maken en de VHD van de VM-schijf extraheren.

## <a name="create-the-vm-on-the-azure-portal"></a>Maak de VM op de Azure Portal

Volg deze stappen om de basis-VM-afbeelding te maken op [de Azure Portal](https://ms.portal.azure.com/).

1. Meld u aan bij [Azure Portal](https://ms.portal.azure.com/).
2. Selecteer **Virtuele machines**.
3. Selecteer **+ Toevoegen om** het scherm Een virtuele machine maken **te** openen.
4. Selecteer de afbeelding in de vervolgkeuzelijst of selecteer **Door** alle openbare en persoonlijke afbeeldingen bladeren om alle beschikbare virtuele-machine-afbeeldingen te zoeken of te doorzoeken.
5. Als u een **gen 2-VM** wilt maken, gaat u naar **het tabblad Geavanceerd** en selecteert u de optie Gen **2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecteer Gen 1 of Gen 2.":::

6. Selecteer de grootte van de VM die u wilt implementeren.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecteer een aanbevolen VM-grootte voor de geselecteerde afbeelding.":::

7. Geef de overige vereiste gegevens op om de VM te maken.
8. Selecteer **Beoordelen en maken om** uw keuzes te controleren. Wanneer het **bericht Validatie** geslaagd wordt weergegeven, selecteert u **Maken.**

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. Houd de voortgang bij door het tabblad **Virtual Machines** selecteren in het menu links. Nadat deze is gemaakt, verandert de status van Virtuele machine in **Wordt uitgevoerd.**

## <a name="connect-to-your-vm"></a>Verbinding maken met uw VM

Raadpleeg de volgende documentatie om verbinding te maken met uw [Windows-](../virtual-machines/windows/connect-logon.md) of [Linux-VM.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Volgende stappen

- [Test uw VM-afbeelding om](azure-vm-image-test.md) te controleren of deze voldoet Azure Marketplace publicatievereisten. Dit is optioneel.
- Als u uw VM-afbeelding niet wilt testen, meldt u zich aan bij [Partner Center](https://partner.microsoft.com/) en publiceert u de SIG-afbeelding (optie #1).
- Als u de optie #2 of #3, [genereert u de SAS-URI](azure-vm-get-sas-uri.md).
- Als u problemen ondervindt bij het maken van uw nieuwe op Azure gebaseerde VHD, zie Veelgestelde vragen over [VM's Azure Marketplace](azure-vm-create-faq.md).
