---
title: Azure VMware-oplossing door CloudSimple-Vm's in azure te beheren
description: Hierin wordt beschreven hoe u virtuele CloudSimple-Vm's beheert in de Azure Portal, inclusief het toevoegen van schijven, het wijzigen van de VM-capaciteit en het toevoegen van netwerk interfaces
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895186"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Uw virtuele CloudSimple-Cloud machines beheren in azure

Als u de virtuele machines die u hebt [gemaakt voor uw CloudSimple-privécloud](azure-create-vm.md)wilt beheren, meldt u zich aan bij de [Azure Portal](https://portal.azure.com). Zoek en selecteer de virtuele server (Zoek in **alle services** of **virtual machines** in het menu aan de zijkant).

## <a name="control-virtual-machine-operation"></a>Bewerking van virtuele machine beheren

De volgende besturings elementen zijn beschikbaar op de pagina **overzicht** voor de geselecteerde virtuele machine.

| Beheer | Beschrijving |
| ------------ | ------------- |
| Verbinding maken | Verbinding maken met de opgegeven virtuele machine.  |
| Starten | Start de opgegeven VM.  |
| Opnieuw starten | Sluit af en schakel vervolgens de opgegeven virtuele machine uit.  |
| Stoppen | Sluit de specifieke VM af.  |
| Vastleggen | Leg een installatie kopie van de opgegeven virtuele machine vast zodat deze kan worden gebruikt als een installatie kopie om andere Vm's te maken. Zie [een beheerde installatie kopie maken van een gegeneraliseerde vm in azure](../virtual-machines/windows/capture-image-resource.md).   |
| Verplaatsen | Verplaatsen naar de opgegeven virtuele machine.  |
| Verwijderen | Verwijder de opgegeven virtuele machine.  |
| Vernieuwen | De gegevens in de weer gave vernieuwen.  |

### <a name="view-performance-information"></a>Prestatie gegevens weer geven

De grafieken in het onderste gedeelte van de pagina **overzicht** presen teren prestatie gegevens voor het geselecteerde interval (laatste uur tot laatste 30 dagen; standaard is het laatste uur). Binnen elke grafiek kunt u de numerieke waarden voor elk tijdstip binnen het interval weer geven door de cursor terug en weer over de grafiek heen te bewegen.

De volgende grafieken worden weer gegeven.

| Item | Beschrijving |
| ------------ | ------------- |
| CPU (gemiddeld) | Gemiddeld CPU-gebruik in procenten gedurende het geselecteerde interval.   |
| Netwerk | Verkeer binnen en buiten het netwerk (MB) over het geselecteerde interval.  |
| Schijf bytes | Het totale aantal gelezen gegevens van de schijf en geschreven naar schijf (MB) voor het geselecteerde interval.  |
| Schijf bewerkingen | Het gemiddelde aantal schijf bewerkingen (bewerkingen/seconde) voor het geselecteerde interval. |

## <a name="manage-vm-disks"></a>VM-schijven beheren

Als u een VM-schijf wilt toevoegen, opent u de pagina **schijven** voor de geselecteerde VM. Als u een schijf wilt toevoegen, klikt u op **schijf toevoegen**. Configureer elk van de volgende instellingen door een inline-optie in te voeren of te selecteren. Klik op **Opslaan**.

   | Item | Beschrijving |
   | ------------ | ------------- |
   | Naam | Voer een naam in om de schijf aan te duiden.  |
   | Grootte | Selecteer een van de beschik bare grootten.  |
   | SCSI-controller | Selecteer een SCSI-controller. De beschik bare controllers variëren voor de verschillende ondersteunde besturings systemen.  |
   | Modus | Hiermee wordt bepaald hoe de schijf deel uitmaakt van moment opnamen. Kies een van de volgende opties: <br> -Onafhankelijk permanent: alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> -Onafhankelijk, niet-persistent: wijzigingen die naar de schijf worden geschreven, worden genegeerd wanneer u de virtuele machine uitschakelt of opnieuw instelt.  In deze modus kunt u de virtuele machine in dezelfde staat altijd opnieuw opstarten. Raadpleeg de [VMware-documentatie](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)voor meer informatie. |

Als u een schijf wilt verwijderen, selecteert u deze en klikt u op **verwijderen**.

## <a name="change-the-capacity-of-the-vm"></a>De capaciteit van de virtuele machine wijzigen

Als u de capaciteit van de virtuele machine wilt wijzigen, opent u de pagina **grootte** voor de geselecteerde virtuele machine. Geef een van de volgende op en klik op **Opslaan**.

| Item | Beschrijving |
| ------------ | ------------- |
| Aantal kerngeheugens | Het aantal kern geheugens dat aan de virtuele machine is toegewezen.  |
| Hardwarevirtualisatie | Schakel het selectie vakje in om de hardware-virtualisatie beschikbaar te maken voor het gast besturingssysteem. Zie het VMware-artikel een door [VMware ondersteunde virtualisatie beschikbaar](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)maken. |
| Geheugen grootte | Selecteer de hoeveelheid geheugen die aan de virtuele machine moet worden toegewezen.  

## <a name="manage-network-interfaces"></a>Netwerk interfaces beheren

Als u een interface wilt toevoegen, klikt u op **netwerk interface toevoegen**. Configureer elk van de volgende instellingen door een inline-optie in te voeren of te selecteren. Klik op **Opslaan**.

   | Beheer | Beschrijving |
   | ------------ | ------------- |
   | Naam | Voer een naam in om de interface te identificeren.  |
   | Netwerk | Selecteer in de lijst met geconfigureerde netwerken in de vSphere van uw Privécloud.  |
   | Adapter | Selecteer een vSphere-adapter in de lijst met beschik bare typen die voor de virtuele machine zijn geconfigureerd. Zie het artikel over VMware Knowledge Base voor meer informatie. [Kies een netwerk adapter voor de virtuele machine](https://kb.vmware.com/s/article/1001805). |
   | Inschakelen bij opstarten | Kies of u de NIC-hardware wilt inschakelen wanneer de virtuele machine wordt opgestart. De standaard instelling is **ingeschakeld**. |

Als u een netwerk interface wilt verwijderen, selecteert u deze en klikt u op **verwijderen**.
