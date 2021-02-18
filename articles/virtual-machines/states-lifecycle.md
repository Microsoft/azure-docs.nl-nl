---
title: Levens cyclus en statussen van een virtuele machine in azure
description: Overzicht van de levens cyclus van een virtuele machine in azure, inclusief beschrijvingen van de verschillende statussen die een VM op elk gewenst moment kan hebben.
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: vashan
ms.openlocfilehash: 7f91d5eebfae990f60b47f2c98f364046bafbf04
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594352"
---
# <a name="virtual-machines-lifecycle-and-states"></a>Levens cyclus en statussen van virtuele machines

Azure Virtual Machines (Vm's) passeren verschillende statussen die kunnen worden ingedeeld in *inrichting* en *energie* status. Het doel van dit artikel is om deze statussen te beschrijven en te markeren wanneer klanten worden gefactureerd voor het gebruik van een exemplaar. 

## <a name="power-states"></a>Energiestatussen

De energie status vertegenwoordigt de laatste bekende status van de virtuele machine.

![Energie status diagram van de VM](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
De volgende tabel bevat een beschrijving van elke instantie status en geeft aan of deze in rekening wordt gebracht voor het gebruik van exemplaren of niet.

:::row:::
   :::column span="":::

   **Status**
   
   :::column-end:::
   :::column span="":::

   **Beschrijving**

   :::column-end:::
   :::column span="":::

   **Gebruik van exemplaar gefactureerd**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Starten**

   :::column-end:::
   :::column span="":::

   De VM wordt opgestart.

   ```json
   "statuses": [
    {
    "code": "PowerState/starting",
    "level": "Info",
    "displayStatus": "VM starting"
    }
   ]
   ```
   :::column-end:::
   :::column span="":::

   **Niet gefactureerd**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Wordt uitgevoerd**

   :::column-end:::
   :::column span="":::

   Normale werk status voor een virtuele machine

   ```json
   "statuses": [
    {
    "code": "PowerState/running",
    "level": "Info",
    "displayStatus": "VM running"
    }
  ]
  ```
   :::column-end:::
   :::column span="":::

   **Gefactureerd**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Stoppen**

   :::column-end:::
   :::column span="":::

   Dit is een overgangs status. Als deze functie is voltooid, wordt deze weer gegeven als **gestopt**.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopping",
    "level": "Info",
    "displayStatus": "VM stopping"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Gefactureerd**

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Gestopt**

   :::column-end:::
   :::column span="":::

   De virtuele machine is afgesloten vanuit het gast besturingssysteem of met behulp van de uitgeschakeld-Api's.

   Er wordt nog steeds hardware toegewezen aan de virtuele machine en deze blijft op de host.

   ```json
   "statuses": [
    {
    "code": "PowerState/stopped",
    "level": "Info",
    "displayStatus": "VM stopped"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Gefactureerd***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Vrijgeven**

   :::column-end:::
   :::column span="":::

   Overgangs status. Als de VM is voltooid, wordt deze weer gegeven als **opgeheven**.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocating",
    "level": "Info",
    "displayStatus": "VM deallocating"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Niet gefactureerd***

   :::column-end:::
:::row-end:::

:::row:::
   :::column span="":::

   **Toewijzing ongedaan gemaakt**

   :::column-end:::
   :::column span="":::

   De virtuele machine is gestopt en verwijderd van de host.

   ```json
   "statuses": [
    {
    "code": "PowerState/deallocated",
    "level": "Info",
    "displayStatus": "VM deallocated"
    }
   ]
  ```
   :::column-end:::
   :::column span="":::

   **Niet gefactureerd**

   :::column-end:::
:::row-end:::


&#42; sommige Azure-resources, zoals schijven en netwerken, worden er kosten in rekening gebracht. Voor software licenties op het exemplaar worden geen kosten in rekening gebracht.

## <a name="provisioning-states"></a>Inrichtings status

Een inrichtings status is de status van een door de gebruiker geïnitieerde, besturings vlak bewerking op de virtuele machine. Deze statussen zijn gescheiden van de energie status van een virtuele machine.

- **Maken** : VM maken.

- **Update** : Hiermee wordt het model voor een bestaande virtuele machine bijgewerkt. Sommige niet-model wijzigingen voor de VM, zoals starten/opnieuw opstarten, vallen ook onder update.

- **Verwijderen** : VM verwijderen.

- **Toewijzing opheffen** : is de locatie waar een virtuele machine wordt gestopt en verwijderd van de host. Het ongedaan maken van de toewijzing van een virtuele machine wordt beschouwd als een update, zodat de inrichtings status van het bijwerken wordt weer gegeven.



Dit zijn de overgangs statussen nadat het platform een door de gebruiker gestarte actie heeft geaccepteerd:

:::row:::
   :::column span="":::

   **Status**
   
   :::column-end:::
   :::column span="2":::

   **Beschrijving**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Maken**

   :::column-end:::
   :::column span="2":::

  ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating",
    "level": "Info",
    "displayStatus": "Creating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Bijwerken**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/updating",
    "level": "Info",
    "displayStatus": "Updating"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Verwijderen**

   :::column-end:::
   :::column span="2":::

   ```json
   "statuses": [
    {
    "code": "ProvisioningState/deleting",
    "level": "Info",
    "displayStatus": "Deleting"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **Inrichtings status van besturings systeem**
   
   :::column-end:::
   :::column span="2":::

   **Beschrijving**

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::



   :::column-end:::
   :::column span="2":::

   Als een virtuele machine wordt gemaakt met een installatie kopie van een besturings systeem en niet met een gespecialiseerde installatie kopie, kunnen de volgende Substatussen worden waargenomen:

   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningInprogress**

   :::column-end:::
   :::column span="2":::

   De virtuele machine wordt uitgevoerd en de installatie van het gast besturingssysteem wordt uitgevoerd.
 
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningInprogress",
    "level": "Info",
    "displayStatus": "OS Provisioning In progress"
    }
   [
   ```
   :::column-end:::

:::row-end:::

:::row:::
   :::column span="":::

   **OSProvisioningComplete**

   :::column-end:::
   :::column span="2":::
   
   Korte status. De virtuele machine wordt snel overgezet naar **geslaagd** , tenzij er uitbrei dingen moeten worden geïnstalleerd. Het installeren van extensies kan enige tijd duren.
   
   ```json
   "statuses": [
    {
    "code": "ProvisioningState/creating/OSProvisioningComplete",
    "level": "Info",
    "displayStatus": "OS Provisioning Complete"
    }
   [
   ```
   
   **Opmerking**: het inrichten van het besturings systeem kan worden overgezet naar **mislukt** als er een fout in het besturings systeem optreedt of als het besturings systeem niet op tijd wordt geïnstalleerd. Klanten worden gefactureerd voor de geïmplementeerde virtuele machine op de infra structuur.

   :::column-end:::

:::row-end:::

Zodra de bewerking is voltooid, verandert de virtuele machine in een van de volgende statussen:

- **Geslaagd** : de door de gebruiker geïnitieerde acties zijn voltooid.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Mislukt** : geeft een mislukte bewerking aan. Raadpleeg de fout codes voor meer informatie en mogelijke oplossingen.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Weer gave van VM-instantie

De instantie weergave-API biedt informatie over de status van de virtuele machine. Zie de documentatie over de [virtual machines-instantie weergave-](/rest/api/compute/virtualmachines/instanceview) API voor meer informatie.

Azure resource Explorer biedt een eenvoudige gebruikers interface voor het weer geven van de actieve status van de VM: [resource Explorer](https://resources.azure.com/).

De inrichtings statussen zijn zichtbaar in de VM-eigenschappen en de weer gave van exemplaren. Energie statussen zijn beschikbaar in de instantie weergave van de virtuele machine.

Als u de energiestatus van alle VM's in uw abonnement wilt ophalen, gebruikt u de API [Virtual Machines - List All](/rest/api/compute/virtualmachines/listall), waarbij u de parameter **statusOnly** instelt op *true*.

## <a name="next-steps"></a>Volgende stappen

Zie [virtuele machines in azure controleren](../azure-monitor/vm/monitor-vm-azure.md)voor meer informatie over het bewaken van uw VM.