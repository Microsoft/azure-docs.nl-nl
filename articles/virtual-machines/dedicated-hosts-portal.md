---
title: Implementeer uw eigen Azure-hosts met behulp van de Azure Portal
description: Implementeer Vm's en schaal sets op toegewezen hosts met behulp van de Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 3bc7909f38e63256d7d708ec189c628662cf8837
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667326"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Vm's en schaal sets implementeren op toegewezen hosts met behulp van de portal 

Dit artikel begeleidt u bij het maken van een toegewezen Azure- [host](dedicated-hosts.md) voor het hosten van uw virtuele machines (vm's). 


## <a name="limitations"></a>Beperkingen

- De grootten en typen hardware die beschikbaar zijn voor toegewezen hosts variëren per regio. Raadpleeg de pagina met [prijzen](https://aka.ms/ADHPricing) voor de host voor meer informatie.

## <a name="create-a-host-group"></a>Een hostgroep maken

Een **hostgroep** is een resource die een verzameling toegewezen hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheids zone en voegt hierop hosts toe. Bij het plannen van hoge Beschik baarheid zijn er extra opties. U kunt een of beide van de volgende opties gebruiken met uw toegewezen hosts: 
- Beschik over meerdere beschikbaarheids zones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Over meerdere fout domeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal fout domeinen voor uw hostgroep opgeven. Als u geen fout domeinen in uw groep wilt beslaan, gebruikt u het aantal fouten domein 1. 

U kunt er ook voor kiezen om zowel beschikbaarheids zones als fout domeinen te gebruiken. 

In dit voor beeld maken we een hostgroep met 1 beschikbaarheids zone en twee fout domeinen. 


1. Open Azure [Portal](https://portal.azure.com). 
1. Selecteer in de linkerbovenhoek **een resource maken** .
1. Zoek naar **hostgroep** en selecteer **hostgroepen** in de resultaten.
1. Selecteer op de pagina **hostgroepen** de optie **maken**.
1. Selecteer het abonnement dat u wilt gebruiken en selecteer vervolgens **Nieuw maken** om een nieuwe resource groep te maken.
1. Typ *myDedicatedHostsRG* als **naam** en selecteer **OK**.
1. Typ *myHostGroup* voor de naam van de **hostgroep**.
1. Selecteer bij **Locatie** **VS - oost**.
1. Selecteer voor de **beschikbaarheids zone** de optie **1**.
1. Selecteer **2** bij **aantal fout domeinen**.
1. Selecteer **automatische plaatsing** om automatisch vm's en schaal sets toe te wijzen aan een beschik bare host in deze groep.
1. Selecteer **controleren + maken** en wacht vervolgens op validatie.
1. Zodra u het bericht **validatie is voltooid** ziet, selecteert u **maken** om de hostgroep te maken.

Het kan even duren voordat de hostgroep is gemaakt.


## <a name="create-a-dedicated-host"></a>Een toegewezen host maken

Maak nu een toegewezen host in de hostgroep. Naast een naam voor de host, moet u de SKU voor de host opgeven. Host SKU legt de ondersteunde VM-serie en de generatie van de hardware voor uw specifieke host vast.

Zie voor meer informatie over de Sku's en prijzen van de host de [Azure dedicated host prijzen](https://aka.ms/ADHPricing).

Als u het aantal fout domeinen voor uw hostgroep instelt, wordt u gevraagd om het fout domein voor uw host op te geven.  

1. Selecteer in de linkerbovenhoek **een resource maken** .
1. Zoek naar een **specifieke host** en selecteer vervolgens **toegewezen hosts** uit de resultaten.
1. Selecteer op de pagina **gereserveerde hosts** de optie **maken**.
1. Selecteer het abonnement dat u wilt gebruiken.
1. Selecteer *myDedicatedHostsRG* als de **resource groep**.
1. In **Details** van het exemplaar typt u *MyHost* voor de **naam** en selecteert u *VS-Oost* voor de locatie.
1. Selecteer in **hardwareprofiel** *standaard Es3 Family-type 1* voor de **grootte familie**, selecteer *myHostGroup* voor de **hostgroep** en selecteer vervolgens *1* voor het **fout domein**. Laat de standaard waarden voor de rest van de velden ongewijzigd.
1. Wanneer u klaar bent, selecteert u **controleren + maken** en wacht u op validatie.
1. Zodra u het bericht **validatie is voltooid** ziet, selecteert u **maken** om de host te maken.

## <a name="create-a-vm"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. In het zoekvak boven de lijst met resources van Azure Marketplace zoekt en selecteert u de installatie kopie die u wilt gebruiken en kiest u **maken**.
1. Controleer op het tabblad **basis beginselen** onder **Project Details** of het juiste abonnement is geselecteerd en selecteer vervolgens *myDedicatedHostsRG* als de **resource groep**. 
1. Typ onder **Exemplaardetails***myVM* als **Naam van de virtuele machine** en kies *VS - oost* als de **Locatie**.
1. Selecteer in **beschikbaarheids opties** **beschikbaarheids zone** selecteren, selecteer *1* in de vervolg keuzelijst.
1. Voor de grootte selecteert u **grootte wijzigen**. Kies in de lijst met beschik bare grootten een van de Esv3-reeksen, zoals **Standard E2s v3**. Mogelijk moet u het filter wissen om alle beschik bare grootten weer te geven.
1. Vul zo nodig de overige velden op het tabblad **basis** .
1. Als u wilt opgeven welke host moet worden gebruikt voor uw virtuele machine, selecteert u het tabblad **Geavanceerd** in de sectie **host** en selecteert u *MyHostGroup* voor **hostgroep** en *myHost* voor de **host** aan de bovenkant van de pagina. Anders wordt uw virtuele machine automatisch op een host met capaciteit geplaatst.
    ![Hostgroep en host selecteren](./media/dedicated-hosts-portal/advanced.png)
1. Laat de resterende standaardwaarden staan ​​en selecteer vervolgens de knop **Beoordelen en maken** aan de onderkant van de pagina.
1. Wanneer u het bericht ziet dat de validatie is voltooid, selecteert u **maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd.

## <a name="create-a-scale-set"></a>Een schaalset maken 

Wanneer u een schaalset implementeert, geeft u de hostgroep op.

1. Zoek naar *schaalset* en selecteer **virtuele-machine schaal sets** in de lijst.
1. Selecteer **toevoegen** om een nieuwe schaalset te maken.
1. Vul de velden op het tabblad **basis beginselen** in zoals gebruikelijk, maar zorg ervoor dat u een VM-grootte selecteert uit de reeks die u voor uw specifieke host hebt gekozen, zoals **Standard E2s v3**.
1. Op het tabblad **Geavanceerd** , voor het **spreidings algoritme** , selecteert u **maximale sprei ding**.
1. Selecteer in **hostgroep** de hostgroep in de vervolg keuzelijst. Als u de groep onlangs hebt gemaakt, kan het een minuut duren voordat deze wordt toegevoegd aan de lijst.

## <a name="add-an-existing-vm"></a>Een bestaande VM toevoegen 

U kunt een afgesloten VM toevoegen aan een specifieke host, maar de virtuele machine moet eerst worden Stop\Deallocated. Voordat u een virtuele machine naar een toegewezen host verplaatst, moet u ervoor zorgen dat de VM-configuratie wordt ondersteund:

- De VM-grootte moet zich in dezelfde grootte familie bestaan als de toegewezen host. Als uw toegewezen host bijvoorbeeld DSv3 is, kan de grootte van de virtuele machine worden Standard_D4s_v3, maar kan deze niet Standard_A4_v2. 
- De virtuele machine moet zich in dezelfde regio bevinden als de toegewezen host.
- De virtuele machine kan geen deel uitmaken van een proximity-plaatsings groep. Verwijder de virtuele machine uit de plaatsings groep voor nabijheid voordat u deze naar een specifieke host verplaatst. Zie [een virtuele machine uit een proximity-plaatsings groep verplaatsen](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group) voor meer informatie
- De virtuele machine kan zich niet in een beschikbaarheidsset bevinden.
- Als de virtuele machine zich in een beschikbaarheids zone bevindt, moet deze dezelfde beschikbaarheids zone zijn als de hostgroep. De instellingen voor de beschikbaarheids zone voor de virtuele machine en de hostgroep moeten overeenkomen.

Verplaats de virtuele machine naar een speciale host met behulp van de [Portal](https://portal.azure.com).

1. Open de pagina voor de virtuele machine.
1. Selecteer **stoppen** om de virtuele machine te stop\deallocate.
1. Selecteer **configuratie** in het menu links.
1. Selecteer een hostgroep en een host in de vervolg keuzelijst.
1. Wanneer u klaar bent, selecteert u **Opslaan** boven aan de pagina.
1. Nadat de virtuele machine is toegevoegd aan de host, selecteert u **overzicht** in het menu links.
1. Selecteer boven aan de pagina **Start** om de virtuele machine opnieuw op te starten.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie het overzicht [gespecialiseerde hosts](dedicated-hosts.md) .

- [Hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)vindt u een voor beeld van een sjabloon, die zowel zones als fout domeinen gebruikt voor maximale tolerantie in een regio.

- U kunt ook een speciale host implementeren met behulp van [Azure cli](./linux/dedicated-hosts-cli.md) of [Power shell](./windows/dedicated-hosts-powershell.md).
