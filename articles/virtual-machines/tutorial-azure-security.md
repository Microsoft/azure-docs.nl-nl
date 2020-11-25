---
title: "Zelfstudie: Azure Security Center gebruiken voor VM's in Azure"
description: In deze zelfstudie krijgt u informatie over de functies van Azure Security Center die helpen uw virtuele machines in Azure te beschermen en te beveiligen.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aec223ceca242e37e25f9e7288c8e0c0e2ea7535
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594775"
---
# <a name="tutorial-use-azure-security-center-to-monitor-virtual-machines"></a>Zelfstudie: Azure Security Center gebruiken om virtuele machines te bewaken

Met Azure Security Center kunt u meer inzicht krijgen in de beveiliging van uw Azure-resources. Security Center biedt geïntegreerde beveiligingsbewaking. De service kan bedreigingen detecteren die anders misschien onopgemerkt blijven. In deze zelfstudie krijgt u meer informatie over Azure Security Center en leert u het volgende te doen:
 
> [!div class="checklist"]
> * Gegevensverzameling kunt instellen
> * Beveiligingsbeleid kunt instellen
> * Problemen met de configuratiestatus kunt weergeven en oplossen
> * Gedetecteerde bedreigingen kunt controleren

## <a name="security-center-overview"></a>Overzicht van Security Center

Security Center identificeert mogelijke configuratieproblemen voor virtuele machine (VM's) en gerichte beveiligingsrisico's. Dit kan het volgende omvatten: virtuele machines waarbij netwerkbeveiligingsgroepen ontbreken, niet-versleutelde schijven en RDP-beveiligingsaanvallen (Remote Desktop Protocol). De informatie wordt op het Security Center-dashboard in duidelijke grafieken weergegeven.

Als u het Security Center-dashboard wilt openen, selecteert u in het menu van Azure Portal de optie **Security Center**. Op het dashboard kunt u de beveiligingsstatus van uw Azure-omgeving bekijken, een aantal huidige aanbevelingen zien en de huidige status van bedreigingswaarschuwingen weergeven. U kunt elke algemene grafiek uitvouwen voor gedetailleerdere gegevens.

![Dashboard van Security Center](./media/tutorial-azure-security/asc-dash.png)

Security Center biedt niet alleen gegevensdetectie maar ook aanbevelingen voor gedetecteerde problemen. Als een VM bijvoorbeeld zonder een gekoppelde netwerkbeveiligingsgroep is geïmplementeerd, geeft Security Center een aanbeveling weer inclusief herstelstappen. U krijgt automatisch herstelstappen zonder de context van Security Center te verlaten.  

![Schermopname toont de pagina met aanbevelingen, met daarop een beschrijving, resource, status en de ernst van de aanbevelingen.](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>Gegevensverzameling kunt instellen

Voordat u VM-beveiligingsconfiguraties kunt inzien, moet u eerst gegevensverzameling instellen in Security Center. Dit omvat het inschakelen van gegevensverzameling, waardoor de Microsoft Monitoring Agent automatisch wordt geïnstalleerd voor alle VM's in uw abonnement.

1. Klik in het Security Center-dashboard op **Beveiligingsbeleid** en selecteer vervolgens uw abonnement. 
2. Voor **gegevensverzameling** selecteert u in **Automatische inrichting** de optie **Aan**.
3. Voor **Configuratie van de standaardwerkruimte** laat u **Werkruimten gebruiken die zijn gemaakt door Security Center (standaard)** staan.
4. Bij **Beveiligingsgebeurtenissen** laat u de standaardoptie **Algemeen** staan.
4. Klik bovenaan de pagina op **Opslaan**. 

Vervolgens wordt de Security Center-agent voor gegevensverzameling op alle VM's geïnstalleerd en wordt de gegevensverzameling gestart. 

## <a name="set-up-a-security-policy"></a>Een beveiligingsbeleid instellen

Een beveiligingsbeleid wordt gebruikt voor het definiëren van de items waarvoor Security Center gegevens verzamelt en aanbevelingen doet. U kunt verschillende soorten beveiligingsbeleid toepassen op verschillende sets van Azure-resources. Hoewel Azure-resources standaard voor alle beleidsitems worden geëvalueerd, kunt u afzonderlijke beleidsitems uitschakelen voor alle Azure-resources of voor een resourcegroep. Zie [Beveiligingsbeleid instellen in Azure Security Center](../security-center/tutorial-security-policy.md) voor gedetailleerde informatie over Security Center-beveiligingsbeleid. 

Een beveiligingsbeleid voor een volledig abonnement instellen:

1. Selecteer in het Security Center-dashboard **Beveiligingsbeleid** en selecteer vervolgens uw abonnement.
2. Selecteer op de blade **Beveiligingsbeleid** de optie **Beveiligingsbeleid**. 
3. Op de blade **Beveiligingsbeleid - Beveiligingsbeleid** schakelt u de beleidsitems in die u wilt toepassen op het abonnement.
4. Wanneer u klaar bent met instellen, selecteert u bovenaan de blade de optie **Opslaan**. 

![Uniek beleid](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>Configuratiestatus van VM weergeven

Zodra u gegevensverzameling hebt ingeschakeld en een beveiligingsbeleid hebt ingesteld, begint Security Center meldingen en aanbevelingen te geven. Wanneer virtuele machines worden geïmplementeerd, wordt de agent voor gegevensverzameling geïnstalleerd. Security Center wordt vervolgens gevuld met gegevens voor de nieuwe VM's. Zie [Protect your VMs in Security Center](../security-center/asset-inventory.md) (Uw VM's beschermen in Security Center) voor gedetailleerde informatie over de configuratiestatus van VM's. 

Terwijl er gegevens worden verzameld, worden de resourcestatus voor elke VM en de gerelateerde Azure-resource verzameld. De informatie wordt weergegeven in een duidelijk diagram. 

Resourcestatus bekijken:

1.  Selecteer op het Security Center-dashboard onder **Preventie** de optie **Compute**. 
2.  Selecteer op de blade **Compute** de optie **VM's en computers**. Deze weergave biedt een samenvatting van de configuratiestatus van alle VM's.

![Status berekenen](./media/tutorial-azure-security/compute-health.png)

Selecteer een VM als u alle aanbevelingen voor deze VM wilt bekijken. 

## <a name="remediate-configuration-issues"></a>Configuratieproblemen oplossen

Wanneer Security Center begint met het invullen van configuratiegegevens, worden er aanbevelingen gemaakt op basis van het ingestelde beveiligingsbeleid. Als een VM bijvoorbeeld zonder een gekoppelde netwerkbeveiligingsgroep is ingesteld, wordt aanbevolen er een te maken. 

Een lijst met alle aanbevelingen weergeven: 

1. Selecteer in het Security Center-dashboard de optie **Aanbevelingen**.
2. Selecteer een specifieke aanbeveling. Er wordt een lijst weergegeven met alle resources waarop de aanbeveling van toepassing is.
3. Als u een aanbeveling wilt toepassen, selecteert u de benodigde resource. 
4. Volg de instructies voor herstelstappen. 

In veel gevallen biedt Security Center stappen om een aanbeveling uit te voeren zonder Security Center te verlaten. In het volgende voorbeeld detecteert Security Center een netwerkbeveiligingsgroep met een onbeperkte binnenkomende regel. Op de aanbevelingenpagina kunt u de knop **Binnenkomende regels bewerken** selecteren. De gebruikersinterface die nodig is voor het wijzigen van de regel wordt weergegeven. 

![Aanbevelingen](./media/tutorial-azure-security/remediation.png)

Wanneer aanbevelingen worden doorgevoerd, worden ze als opgelost gemarkeerd. 

## <a name="view-detected-threats"></a>Gedetecteerde bedreigingen bekijken

Security Center geeft naast aanbevelingen voor resourceconfiguratie ook meldingen voor geconstateerde bedreigingen weer. De functie voor beveiligingsmeldingen combineert de verzamelde gegevens van elke VM, Azure-netwerklogboeken en gekoppelde partneroplossingen om bedreigingen voor Azure-resources te detecteren. Lees [How does Security Center detect threats?](../security-center/security-center-alerts-overview.md#detect-threats) (Hoe detecteert Security Center bedreigingen?) voor gedetailleerdere informatie over de detectiemogelijkheden van Security Center.

De functie voor beveiligingsmeldingen vereist dat de Security Center-prijscategorie wordt verhoogd van *Gratis* naar *Standard*. Er is een **gratis proefversie** beschikbaar wanneer u naar deze hogere prijscategorie overstapt. 

De prijscategorie wijzigen:  

1. Klik in het Security Center-dashboard op **Beveiligingsbeleid** en selecteer vervolgens uw abonnement.
2. Selecteer **Prijscategorie**.
3. Selecteer **Standard** en klik vervolgens op **Opslaan** boven in de blade.


Nadat u de prijscategorie hebt gewijzigd, wordt de grafiek voor beveiligingsmeldingen ingevuld wanneer er beveiligingsbedreigingen worden gedetecteerd.

![Beveiligingswaarschuwingen](./media/tutorial-azure-security/security-alerts.png)

Selecteer een waarschuwing om informatie weer te geven. U kunt bijvoorbeeld een beschrijving van de bedreiging, het tijdstip van de detectie, alle bedreigingspogingen en de aanbevolen herstelstappen bekijken. In het volgende voorbeeld wordt een RDP-beveiligingsaanval gedetecteerd, met 294 mislukte RDP-pogingen. Er wordt een aanbevolen oplossing gegeven.

![RDP-aanval](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u Azure Security Center ingesteld en vervolgens VM's in Security Center gecontroleerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Gegevensverzameling kunt instellen
> * Beveiligingsbeleid kunt instellen
> * Problemen met de configuratiestatus kunt weergeven en oplossen
> * Gedetecteerde bedreigingen kunt controleren

Ga naar de volgende zelfstudie voor meer informatie over het maken van een CI/CD-pijplijn met Jenkins, GitHub en Docker.

> [!div class="nextstepaction"]
> [Een CI/CD-infrastructuur maken met Jenkins, GitHub en Docker](/azure/developer/jenkins/pipeline-with-github-and-docker)