---
title: Geplande gebeurtenissen controleren voor uw Windows-Vm's in azure
description: Meer informatie over het bewaken van uw virtuele Azure-machines voor geplande gebeurtenissen.
author: mysarn
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: 0806c6e0ed89c2c0f4712ec985599810119fcf89
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015516"
---
# <a name="monitoring-scheduled-events"></a>Bewakings Scheduled Events

Updates worden elke dag toegepast op verschillende onderdelen van Azure, om ervoor te zorgen dat de services die worden uitgevoerd, veilig en up-to-date blijven. Naast geplande updates kunnen ook niet-geplande gebeurtenissen optreden. Als er bijvoorbeeld hardware-degradatie of-fouten worden gedetecteerd, moeten Azure-Services mogelijk niet-gepland onderhoud uitvoeren. Het gebruik van Livemigratie, het bewaren van updates en het algemeen houden van een strikte balk over de impact van updates, in de meeste gevallen zijn deze gebeurtenissen bijna transparant voor klanten en ze hebben geen invloed op een paar seconden van het blok keren van virtuele machines. Voor sommige toepassingen kan het echter zelfs enkele seconden duren voordat de blok kering van de virtuele machine gevolgen kan hebben. Het is belang rijk dat u op de hoogte bent van aanstaande onderhouds werkzaamheden van Azure, om ervoor te zorgen dat deze toepassingen optimaal worden ervaren. [Scheduled events-service](scheduled-events.md) biedt u een programmatische interface om op de hoogte te worden gesteld van aanstaande onderhouds werkzaamheden en kunt u het onderhoud op de juiste manier afhandelen. 

In dit artikel laten we zien hoe u geplande gebeurtenissen kunt gebruiken om te worden gewaarschuwd over onderhouds gebeurtenissen die van invloed kunnen zijn op uw Vm's en een eenvoudige automatisering kunnen bouwen die u kan helpen bij het bewaken en analyseren.


## <a name="routing-scheduled-events-to-log-analytics"></a>Door sturen van geplande gebeurtenissen naar Log Analytics

Scheduled Events is beschikbaar als onderdeel van de [Azure-instance metadata service](instance-metadata-service.md)die beschikbaar is op elke virtuele machine van Azure. Klanten kunnen Automation schrijven om een query uit te voeren op het eind punt van hun virtuele machines om geplande onderhouds meldingen te vinden en oplossingen te doen, zoals het opslaan van de status en de virtuele machine uit de rotatie halen. Het is raadzaam om Automation te bouwen om de Scheduled Events te registreren, zodat u een audit logboek van Azure-onderhouds gebeurtenissen kunt hebben. 

In dit artikel wordt stapsgewijs uitgelegd hoe u onderhouds Scheduled Events vastlegt op Log Analytics. Vervolgens worden enkele basis meldings acties geactiveerd, zoals het verzenden van een e-mail naar uw team en het verkrijgen van een historisch overzicht van alle gebeurtenissen die van invloed zijn op uw virtuele machines. Voor de gebeurtenissen aggregatie en automatisering worden [log Analytics](../../azure-monitor/learn/quick-create-workspace.md)gebruikt, maar u kunt elke bewakings oplossing gebruiken om deze logboeken te verzamelen en automatisering te activeren.

![Diagram waarin de levens cyclus van gebeurtenissen wordt weer gegeven](./media/notifications/events.png)

## <a name="prerequisites"></a>Vereisten

Voor dit voor beeld moet u een [virtuele Windows-machine maken in een beschikbaarheidsset](tutorial-availability-sets.md). Scheduled Events geven meldingen over wijzigingen die van invloed kunnen zijn op de virtuele machines in uw beschikbaarheidsset, Cloud service, virtuele-machine Schaalset of zelfstandige Vm's. Er wordt een [service](https://github.com/microsoft/AzureScheduledEventsService) uitgevoerd waarmee geplande gebeurtenissen worden gecontroleerd op een van de virtuele machines die als Collector fungeren, om gebeurtenissen voor alle andere virtuele machines in de beschikbaarheidsset op te halen.    

Verwijder de groeps resource groep niet aan het einde van de zelf studie.

U moet ook [een log Analytics-werk ruimte maken](../../azure-monitor/learn/quick-create-workspace.md) die we gaan gebruiken om informatie te verzamelen van de virtuele machines in de beschikbaarheidsset.

## <a name="set-up-the-environment"></a>De omgeving instellen

Nu moet u twee initiële Vm's in een beschikbaarheidsset hebben. Nu moet u in dezelfde beschikbaarheidsset een derde VM maken met de naam myCollectorVM. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Down load het zip-bestand van de installatie van het project vanuit [github](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Maak verbinding met **myCollectorVM** en kopieer het zip-bestand naar de virtuele machine en pak alle bestanden uit. Open een Power shell-prompt op de virtuele machine. Verplaats de prompt naar de map met `SchService.ps1` , bijvoorbeeld: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` , en stel de service in.

```powershell
.\SchService.ps1 -Setup
```

Start de service.

```powershell
.\SchService.ps1 -Start
```

De service begint nu elke 10 seconden te pollen voor geplande gebeurtenissen en keurt de gebeurtenissen goed om het onderhoud te versnellen.  Bevriezen, opnieuw opstarten, opnieuw implementeren en voor rang nemen de gebeurtenissen vastgelegd door het plannen van gebeurtenissen.   Houd er rekening mee dat u het script kunt uitbreiden om enkele oplossingen te activeren voordat de gebeurtenis wordt goedgekeurd.

Valideer de status van de service en zorg ervoor dat deze wordt uitgevoerd.

```powershell
.\SchService.ps1 -status  
```

Dit moet worden geretourneerd `Running` .

De service begint nu elke 10 seconden te pollen voor geplande gebeurtenissen en keurt de gebeurtenissen goed om het onderhoud te versnellen.  Bevriezen, opnieuw opstarten, opnieuw implementeren en voor rang nemen gebeurtenissen vastgelegd door het plannen van gebeurtenissen. U kunt het script uitbreiden om enkele oplossingen te activeren voordat u de gebeurtenis goedkeurt.

Wanneer een van de bovenstaande gebeurtenissen wordt vastgelegd door de Schedule Event-service, worden de gebeurtenis status van het toepassings gebeurtenis logboek geregistreerd, gebeurtenis type, resources (namen van virtuele machines) en NotBefore (minimale kennisgevings periode). U kunt de gebeurtenissen met ID 1234 vinden in het logboek voor toepassings gebeurtenissen.

Zodra de service is ingesteld en gestart, worden gebeurtenissen geregistreerd in de Windows-toepassings Logboeken.   U kunt dit controleren door een van de virtuele machines in de beschikbaarheidsset opnieuw op te starten. er wordt een gebeurtenis weer gegeven die wordt vastgelegd in Logboeken in Windows logs > toepassings logboek waarin de VM opnieuw is opgestart. 

![Scherm opname van Logboeken.](./media/notifications/event-viewer.png)

Wanneer gebeurtenissen worden vastgelegd door de Schedule Event-service, wordt het logboek geregistreerd in de toepassing, zelfs met de gebeurtenis status, het gebeurtenis type, de resources (VM-naam) en NotBefore (minimale kennisgevings periode). U kunt de gebeurtenissen met ID 1234 vinden in het logboek voor toepassings gebeurtenissen.

> [!NOTE] 
> In dit voor beeld bevinden de virtuele machines zich in een beschikbaarheidsset, die ons in staat stelt om één virtuele machine toe te wijzen als de collector voor het Luis teren en routeren van geplande gebeurtenissen naar de Works-ruimte log Analytics. Als u zelfstandige virtuele machines hebt, kunt u de service uitvoeren op elke virtuele machine en deze vervolgens afzonderlijk verbinden met uw log Analytics-werk ruimte.
>
> Voor onze installatie hebt u Windows gekozen, maar u kunt een vergelijk bare oplossing voor Linux ontwerpen.

U kunt de geplande gebeurtenis service op elk gewenst moment stoppen/verwijderen met behulp van de Schakel opties `–stop` en `–remove` .

## <a name="connect-to-the-workspace"></a>Verbinding maken met de werk ruimte


We willen nu een Log Analytics-werk ruimte verbinden met de Collector-VM. De werk ruimte Log Analytics fungeert als opslag plaats en er wordt een gebeurtenis logboek verzameling geconfigureerd om de toepassings logboeken van de Collector-VM vast te leggen. 

 Als u de Scheduled Events wilt omleiden naar het gebeurtenissen logboek dat wordt opgeslagen als toepassings logboek door onze service, moet u uw virtuele machine verbinden met uw Log Analytics-werk ruimte.  
 
1. Open de pagina voor de werk ruimte die u hebt gemaakt.
1. Onder **verbinding maken met een gegevens bron selecteert u** **Azure virtual machines (vm's)**.

    ![Verbinding maken met een virtuele machine als gegevens bron](./media/notifications/connect-to-data-source.png)

1. Zoek en selecteer **myCollectorVM**. 
1. Selecteer op de pagina nieuw voor **myCollectorVM** de optie **verbinding maken**.

Hiermee wordt de [micro soft Monitoring Agent](../extensions/oms-windows.md) op uw virtuele machine geïnstalleerd. Het duurt enkele minuten om uw virtuele machine te verbinden met de werk ruimte en de uitbrei ding te installeren. 

## <a name="configure-the-workspace"></a>De werk ruimte configureren

1. Open de pagina voor uw werk ruimte en selecteer **Geavanceerde instellingen**.
1. Selecteer **gegevens** in het menu links en selecteer vervolgens **Windows-gebeurtenis logboeken**.
1. In **verzamelen uit de volgende gebeurtenis logboeken** begint u met het typen van de *toepassing* en selecteert u vervolgens **toepassing** in de lijst.

    ![Geavanceerde instellingen selecteren](./media/notifications/advanced.png)

1. Geef **fout**, **waarschuwing** en **informatie** ingeschakeld en selecteer **Opslaan** om de instellingen op te slaan.


> [!NOTE]
> Er kan enige vertraging optreden en het kan tot tien minuten duren voordat het logboek beschikbaar is. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Een waarschuwings regel maken met Azure Monitor 


Zodra de gebeurtenissen naar Log Analytics zijn gepusht, kunt u de volgende [query](../../azure-monitor/log-query/get-started-portal.md) uitvoeren om de plannings gebeurtenissen te zoeken.

1. Selecteer aan de bovenkant van de pagina **Logboeken** en plak het volgende in het tekstvak:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Selecteer **Opslaan** en typ *logQuery* voor de naam, geef **query** op als type, typ *VMLogs* als de **categorie** en selecteer vervolgens **Opslaan**. 

    ![De query opslaan](./media/notifications/save-query.png)

1. Selecteer **Nieuwe waarschuwingsregel**. 
1. Op de pagina **regel maken** , gaat u `collectorworkspace` naar de **resource**.
1. Onder **voor waarde** selecteert u de vermelding *wanneer het <login undefined> zoeken naar Logboeken van de klant*. De pagina **signaal logica configureren** wordt geopend.
1. Voer bij **drempel waarde** *0* in en selecteer vervolgens **gereed**.
1. Onder **acties**, selecteer **actie groep maken**. De pagina **actie groep toevoegen** wordt geopend.
1. Typ *myActionGroup* in de naam van de **actie groep**.
1. Typ **myActionGroup** in **short name**.
1. Selecteer **resourcegroupavailability** in de **resource groep**.
1. Onder acties, Typ in **actie naam** **e-mail** en selecteer vervolgens **e-mail/SMS/push/Voice**. De pagina **e-mail/SMS/push/Voice** wordt geopend.
1. Selecteer **e-mail**, typ uw e-mail adres en selecteer vervolgens **OK**.
1. Selecteer **OK** op de pagina **actie groep toevoegen** . 
1. Typ *myAlert* voor de naam van de **waarschuwings regel** in de pagina **regel maken** **, en** Typ vervolgens *e-mail waarschuwings regel* voor de **Beschrijving**.
1. Wanneer u klaar bent, selecteert u **waarschuwings regel maken**.
1. Start een van de virtuele machines in de beschikbaarheidsset opnieuw op. Binnen een paar minuten ontvangt u een e-mail bericht dat de waarschuwing is geactiveerd.

Als u uw waarschuwings regels wilt beheren, gaat u naar de resource groep, selecteert u **waarschuwingen** in het linkermenu en selecteert u vervolgens **waarschuwings regels beheren** boven aan de pagina.

     
## <a name="next-steps"></a>Volgende stappen

Zie de pagina [geplande gebeurtenissen service](https://github.com/microsoft/AzureScheduledEventsService) op github voor meer informatie.
