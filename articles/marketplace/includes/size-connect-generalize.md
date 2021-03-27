---
title: bestand opnemen
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630119"
---
## <a name="generalize-the-image"></a>De installatie kopie generaliseren

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om dit te verhelpen, moet de VHD van het besturings systeem worden gegeneraliseerd, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

### <a name="for-windows"></a>Voor Windows

Windows-besturingssysteem schijven worden gegeneraliseerd met het hulp programma [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Als u het besturings systeem later bijwerkt of opnieuw configureert, moet u Sysprep opnieuw uitvoeren.

> [!WARNING]
> Nadat u Sysprep hebt uitgevoerd, schakelt u de virtuele machine uit totdat deze is geïmplementeerd, omdat updates mogelijk automatisch worden uitgevoerd. Als u dit afsluit, voor komt u dat latere updates exemplaren van specifieke wijzigingen aanbrengen in het besturings systeem of de geïnstalleerde services. Zie [stappen voor het generaliseren van een VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)voor meer informatie over het uitvoeren van Sysprep.

### <a name="for-linux"></a>Voor Linux

Het volgende proces generaliseert een Linux-VM en implementeert deze opnieuw als een afzonderlijke VM. Zie [een installatie kopie van een virtuele machine of VHD maken](../../virtual-machines/linux/capture-image.md)voor meer informatie. U kunt stoppen wanneer u de sectie met de naam ' een virtuele machine maken vanuit de vastgelegde installatie kopie ' bereikt.

1. Verwijder de Azure Linux-agent.
    1. Maak verbinding met uw virtuele Linux-machine met behulp van een SSH-client.
    2. Voer in het SSH-venster de volgende opdracht in: `sudo waagent –deprovision+user` .
    3. Typ j om door te gaan (u kunt de para meter-Force toevoegen aan de vorige opdracht om te voor komen dat de bevestigings stap wordt uitgevoerd).
    4. Nadat de opdracht is voltooid, voert u **Afsluiten** in om de SSH-client te sluiten.
2. Stop de virtuele machine.
    1. Selecteer in de Azure Portal uw resource groep (RG) en de virtuele machine opnieuw toe te wijzen.
    2. Uw VM is nu gegeneraliseerd en u kunt een nieuwe virtuele machine maken met behulp van deze VM-schijf.

### <a name="capture-image"></a>Installatie kopie vastleggen

Zodra uw VM klaar is, kunt u deze vastleggen in een galerie met gedeelde installatie kopieën van Azure. Volg de onderstaande stappen voor het vastleggen van:

1. Ga op [Azure Portal](https://ms.portal.azure.com/)naar de pagina van de virtuele machine.
2. Selecteer **vastleggen**.
3. Selecteer in de **Galerie afbeelding delen naar gedeelde installatie kopie** **de optie Ja en delen deze naar een galerie als afbeeldings versie**.
4. Selecteer gegeneraliseerd onder status van het **besturings systeem** .
5. Selecteer een galerie met doel afbeeldingen of **Maak een nieuwe**.
6. Selecteer een definitie voor de doel installatie kopie of **Maak een nieuwe**.
7. Geef een **versie nummer** op voor de installatie kopie.
8. Selecteer **beoordeling + maken** om uw keuzes te controleren.
9. Nadat de validatie is voltooid, selecteert u **maken**.

Het Azure-abonnement met de SIG moet zich onder dezelfde Tenant benemen als het Publisher-account om te kunnen publiceren. Ook moet de eigenaar van het uitgevers account toegang hebben tot de SIG. 

Toegang verlenen:

1. Ga naar de galerie met gedeelde afbeeldingen.
2. Selecteer **toegangs beheer** (IAM) in het linkerdeel venster.
3. Selecteer **toevoegen** en vervolgens **functie toewijzing toevoegen**.
4. Selecteer een **rol** of **eigenaar**.
5. Onder **toegang toewijzen om** **gebruiker, groep of Service-Principal** te selecteren.
6. Selecteer de Azure-e-mail van de persoon die de installatie kopie gaat publiceren.
7. Selecteer **Opslaan**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Hiermee wordt het venster roltoewijzing toevoegen weer gegeven.":::

> [!NOTE]
> U hoeft geen SAS-Uri's te genereren, omdat u nu een SIG-installatie kopie kunt publiceren in het partner centrum. Als u echter nog steeds moet verwijzen naar de stappen voor het genereren van SAS-URI'S, raadpleegt u [een SAS-URI voor een VM-installatie kopie genereren](../azure-vm-get-sas-uri.md).
