---
title: bestand opnemen
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727115"
---
## <a name="generalize-the-image"></a>De afbeelding generaliseren

Alle afbeeldingen in de Azure Marketplace moeten op een algemene manier opnieuw kunnen worden gebruikt. Hiervoor moet de VHD van het besturingssysteem worden ge generaliseerd, een bewerking waarmee alle instantiespecifieke id's en software-stuurprogramma's van een VM worden verwijderd.

### <a name="for-windows"></a>Voor Windows

Windows-besturingssysteemschijven worden gegeneraliseerd met [het hulpprogramma sysprep.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Als u het besturingssysteem later updatet of opnieuw configureert, moet u sysprep opnieuw uitvoeren.

> [!WARNING]
> Nadat u sysprep hebt uitgevoerd, schakelt u de VM uit totdat deze is geïmplementeerd, omdat updates automatisch kunnen worden uitgevoerd. Door het afsluiten wordt voorkomen dat volgende updates instantiespecifieke wijzigingen aanbrengen in het besturingssysteem of de geïnstalleerde services. Zie Stappen voor het generaliseren van een [VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)voor meer informatie over het uitvoeren van sysprep.

### <a name="for-linux"></a>Voor Linux

Het volgende proces generaliseert een virtuele Linux-VM en de virtuele Linux-VM opnieuw als een afzonderlijke VM. Zie How [to create an image of a virtual machine or VHD (Een afbeelding van een virtuele machine of VHD maken) voor meer informatie.](../../virtual-machines/linux/capture-image.md) U kunt stoppen wanneer u de sectie 'Een VM maken van de vastgelegde afbeelding' bereikt.

1. Verwijder de Azure Linux-agent.
    1. Maak verbinding met uw Linux-VM met behulp van een SSH-client.
    2. Voer in het SSH-venster de volgende opdracht in: `sudo waagent –deprovision+user` .
    3. Typ Y om door te gaan (u kunt de parameter -force toevoegen aan de vorige opdracht om de bevestigingsstap te vermijden).
    4. Nadat de opdracht is voltooid, voert u **Afsluiten in om** de SSH-client te sluiten.
2. Stop de virtuele machine.
    1. Selecteer in Azure Portal resourcegroep (RG) en maak de toewijzing van de VM op.
    2. Uw VM is nu ge generaliseerd en u kunt een nieuwe VM maken met behulp van deze VM-schijf.

### <a name="capture-image"></a>Afbeelding vastleggen

> [!NOTE]
> Het Azure-abonnement met de SIG moet onder dezelfde tenant als het uitgeversaccount vallen om te kunnen publiceren. Daarnaast moet het uitgeversaccount ten minste inzenderstoegang hebben tot het abonnement met SIG.

Zodra uw VM gereed is, kunt u deze vastleggen in een galerie met gedeelde Azure-afbeeldingen. Volg de onderstaande stappen om het volgende vast te leggen:

1. Ga [Azure Portal](https://ms.portal.azure.com/)naar de pagina van uw virtuele machine.
2. Selecteer **Vastleggen.**
3. Selecteer **onder Share image to Shared image gallery** de optie **Yes, share it to a gallery as an image version**.
4. Selecteer **onder Status van besturingssysteem** de optie Ge generaliseerd.
5. Selecteer een galerie met doelafbeeldingen of **Nieuwe maken.**
6. Selecteer een Definitie van doelafbeelding of **Nieuwe maken.**
7. Geef een **versienummer op** voor de afbeelding.
8. Selecteer **Beoordelen en maken om** uw keuzes te controleren.
9. Zodra de validatie is geslaagd, selecteert u **Maken.**

Toegang verlenen:

1. Ga naar de Shared Image Gallery.
2. Selecteer **Toegangsbeheer** (IAM) in het linkerpaneel.
3. Selecteer **Toevoegen** en vervolgens **Roltoewijzing toevoegen.**
4. Selecteer een **Rol** of **Eigenaar.**
5. Selecteer **onder Toegang toewijzen de** optie **Gebruiker, groep of service-principal.**
6. Selecteer het Azure-e-mailadres van de persoon die de afbeelding gaat publiceren.
7. Selecteer **Opslaan**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Geeft het venster Roltoewijzing toevoegen weer.":::

> [!NOTE]
> U hoeft geen SAS-URI's te genereren, omdat u nu een SIG-afbeelding op uw Partner Center. Als u echter nog steeds de stappen voor het genereren van SAS-URI's moet volgen, raadpleegt u How [to generate a SAS URI for a VM image (Een SAS-URI genereren voor een VM-afbeelding).](../azure-vm-get-sas-uri.md)
