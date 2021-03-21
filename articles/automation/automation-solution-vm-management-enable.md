---
title: Azure Automation VM's buiten bedrijfsuren starten/stoppen inschakelen
description: In dit artikel leest u hoe u de VM's buiten bedrijfsuren starten/stoppen functie voor uw virtuele Azure-machines inschakelt.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593903"
---
# <a name="enable-startstop-vms-during-off-hours"></a>VM's buiten bedrijfsuren starten/stoppen inschakelen

Voer de stappen in dit onderwerp uit om de VM's buiten bedrijfsuren starten/stoppen functie voor Vm's in te scha kelen met behulp van een nieuw of bestaand Automation-account en gekoppelde Log Analytics werk ruimte. Nadat het installatie proces is voltooid, configureert u de variabelen om de functie aan te passen.

>[!NOTE]
>Als u deze functie wilt gebruiken met klassieke Vm's, hebt u een klassiek uitvoeren als-account nodig, dat niet standaard wordt gemaakt. Zie [een klassiek uitvoeren als-account maken](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Resources voor de functie maken

1. Meld u aan bij de Azure [Portal](https://portal.azure.com).
2. Zoek en selecteer **Automation-accounts**.
3. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst.
4. Selecteer in het Automation-account de optie **VM starten/stoppen** onder **gerelateerde resources**. Hier kunt u op meer **informatie klikken en de oplossing inschakelen**. Als u de functie al hebt geïmplementeerd, kunt u klikken op **de oplossing beheren** en deze zoeken in de lijst.

   ![Inschakelen vanuit Automation-account](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > U kunt de resource ook maken vanaf elke locatie in de Azure Portal door te klikken op **een resource maken**. Typ op de pagina Marketplace een tref woord zoals **starten** of **starten/stoppen**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. U kunt ook een of meer tref woorden in de volledige naam van de functie typen en vervolgens op **Enter** drukken. Selecteer **VM's buiten bedrijfsuren starten/stoppen** in de zoek resultaten.

5. Controleer op de pagina VM's buiten bedrijfsuren starten/stoppen voor de geselecteerde implementatie de samenvattings informatie en klik vervolgens op **maken**.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>De functie configureren

Als de resource is gemaakt, wordt de pagina oplossing toevoegen weer gegeven. U wordt gevraagd de functie te configureren voordat u deze kunt importeren in uw Automation-abonnement. Zie [configure VM's buiten bedrijfsuren starten/stoppen](automation-solution-vm-management-config.md).

   ![Pagina oplossing toevoegen van VM-beheer](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Een Log Analytics-werk ruimte selecteren

1. Selecteer op de pagina oplossing toevoegen de optie **werk ruimte**. Selecteer een Log Analytics-werk ruimte die is gekoppeld aan het Azure-abonnement dat wordt gebruikt door het Automation-account. 

2. Als u geen werk ruimte hebt, selecteert u **nieuwe werk ruimte maken**. Voer de volgende stappen uit op de pagina werk ruimte Log Analytics:

   - Geef een naam op voor de nieuwe Log Analytics-werk ruimte, zoals **ContosoLAWorkspace**.
   - Selecteer een **abonnement** om te koppelen door te selecteren in de vervolg keuzelijst, als de standaard optie is geselecteerd, niet geschikt is.
   - Voor **resource groep** kunt u een nieuwe resource groep maken of een bestaande selecteren.
   - Selecteer een **locatie**.
   - Selecteer een **prijs categorie**. Kies de optie **per GB (zelfstandig)** . Azure Monitor-logboeken hebben bijgewerkte [prijzen](https://azure.microsoft.com/pricing/details/log-analytics/) en de laag per GB is de enige optie.

   > [!NOTE]
   > Bij het inschakelen van functies worden alleen bepaalde regio's ondersteund voor het koppelen van een Log Analytics-werk ruimte en een Automation-account. Zie [Regio's toewijzen voor Automation-account en Log Analytics-werkruimte](how-to/region-mappings.md) voor een lijst van alle ondersteunde toewijzingsparen.

3. Nadat u de vereiste gegevens op de pagina Log Analytics werk ruimte hebt opgegeven, klikt u op **maken**. U kunt de voortgang bijhouden onder **meldingen** in het menu, waarmee u wordt teruggebracht naar de pagina oplossing toevoegen wanneer u klaar bent.

## <a name="add-automation-account"></a>Automation-account toevoegen

Open opnieuw de pagina oplossing toevoegen en selecteer **Automation-account**. U kunt een bestaand Automation-account selecteren dat nog niet aan een Log Analytics-werk ruimte is gekoppeld. Als u een nieuwe Log Analytics-werk ruimte maakt, kunt u een nieuw Automation-account maken om hieraan te koppelen. Selecteer een bestaand Automation-account of klik op **een Automation-account maken** en geef op de pagina Automation-account toevoegen de naam op van het Automation-account in het veld **naam** .

Alle andere opties worden automatisch ingevuld op basis van de geselecteerde Log Analytics werk ruimte. U kunt deze opties niet wijzigen. Een uitvoeren als-account voor Azure is de standaard methode voor verificatie voor de runbooks die zijn opgenomen in de functie. 

Nadat u op **OK** hebt geklikt, worden de configuratie opties gevalideerd en wordt het Automation-account gemaakt. U kunt de voortgang bijhouden onder **Meldingen** in het menu.

## <a name="define-feature-parameters"></a>Functie parameters definiëren

1. Selecteer op de pagina oplossing toevoegen de optie **configuratie**. De pagina para meters wordt weer gegeven.

    ![De pagina para meters voor de oplossing](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. Geef een waarde op voor het veld **doel-ResourceGroup namen** . In het veld worden groeps namen gedefinieerd die Vm's bevatten voor de functie die u wilt beheren. U kunt meer dan één naam invoeren en de namen van elkaar scheiden met komma's (waarden zijn niet hoofdletter gevoelig). Jokertekens worden ondersteund als de bewerking moet worden gericht op VM's in alle resourcegroepen in het abonnement. De waarden worden opgeslagen in de `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` variabelen en.

    > [!IMPORTANT]
    > De standaard waarde voor de **namen van doel-ResourceGroup** is een **&ast;** . Met deze instelling worden alle virtuele machines in een abonnement gericht. Als u niet wilt dat de functie alle virtuele machines in uw abonnement heeft, moet u een lijst met namen van resource groepen opgeven voordat u een schema selecteert.
  
3. Geef een waarde op voor het veld voor de **lijst met uitsluitingen van de virtuele machine (teken reeks)** . Deze waarde is de naam van een of meer virtuele machines uit de doel resource groep. U kunt meer dan één naam invoeren en de namen van elkaar scheiden met komma's (waarden zijn niet hoofdletter gevoelig). Het gebruik van een Joker teken wordt ondersteund. Deze waarde wordt opgeslagen in de `External_ExcludeVMNames` variabele.
  
4. Gebruik het veld **planning** om een planning te selecteren voor het beheer van vm's door de functie. Selecteer een begin datum en-tijd voor uw planning om een terugkerend dagelijks schema te maken, beginnend bij het gekozen tijdstip. Het selecteren van een andere regio is niet beschikbaar. Als u de planning wilt configureren voor uw specifieke tijd zone nadat u de functie hebt geconfigureerd, raadpleegt u [de planningen voor opstarten en afsluiten wijzigen](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules).

5. Als u e-mail meldingen van een [actie groep](../azure-monitor/alerts/action-groups.md)wilt ontvangen, accepteert u de standaard waarde **Ja** in het veld  **e-mail meldingen** en geeft u een geldig e-mail adres op. Als u **Nee** selecteert maar besluit op een later tijdstip dat u e-mail meldingen wilt ontvangen, kunt u de actie groep bijwerken die is gemaakt met geldige e-mail adressen gescheiden door komma's. 

6. De volgende waarschuwings regels inschakelen:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Waarschuwingen maken

VM's buiten bedrijfsuren starten/stoppen bevat geen vooraf gedefinieerde set met waarschuwingen. Lees [logboek waarschuwingen maken met Azure monitor](../azure-monitor/alerts/alerts-log.md) voor meer informatie over het maken van mislukte waarschuwingen om uw DevOps of operationele processen en procedures te ondersteunen.

## <a name="deploy-the-feature"></a>De functie implementeren

1. Nadat u de aanvankelijk vereiste instellingen voor de functie hebt geconfigureerd, klikt u op **OK** om de pagina para meters te sluiten.

2. Klik op **Create**. Nadat alle instellingen zijn gevalideerd, wordt de functie geïmplementeerd voor uw abonnement. Het kan enkele seconden duren voordat dit proces is voltooid en u kunt de voortgang bijhouden onder **meldingen** in het menu.

    > [!NOTE]
    > Als u een abonnement voor Azure Cloud Solution Provider (Azure CSP) hebt nadat de implementatie is voltooid, gaat u in uw Automation-account naar **variabelen** onder **gedeelde resources** en stelt u de [External_EnableClassicVMs](automation-solution-vm-management.md#variables) variabele in op **Onwaar**. Zo stopt de oplossing met het zoeken naar klassieke VM-resources.

## <a name="next-steps"></a>Volgende stappen

* Als u de functie wilt instellen, raadpleegt u [virtuele machines stoppen/starten configureren tijdens buiten kantoor uren](automation-solution-vm-management-config.md).
* Raadpleeg [problemen met VM's buiten bedrijfsuren starten/stoppen oplossen](troubleshoot/start-stop-vm.md)om de functie fouten op te lossen.