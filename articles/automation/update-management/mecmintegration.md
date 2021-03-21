---
title: Azure Automation Updatebeheer integreren met micro soft endpoint Configuration Manager
description: In dit artikel wordt beschreven hoe u micro soft endpoint Configuration Manager configureert met Updatebeheer voor het implementeren van software-updates voor Manager-clients.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575780"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Updatebeheer integreren met micro soft endpoint Configuration Manager

Klanten die hebben geïnvesteerd in micro soft endpoint Configuration Manager om Pc's, servers en mobiele apparaten te beheren, zijn ook afhankelijk van de kracht en de verval datum van het beheer van software-updates als onderdeel van hun software-update beheer cyclus.

U kunt beheerde Windows-servers rapporteren en bijwerken door software-update-implementaties te maken en vooraf te gebruiken in micro soft endpoint Configuration Manager, en gedetailleerde status van voltooide update-implementaties te krijgen met behulp van [updatebeheer](overview.md). Als u micro soft endpoint Configuration Manager gebruikt voor het rapporteren van update vereisten, maar niet voor het beheren van update-implementaties met uw Windows-servers, kunt u door gaan met de rapportage aan micro soft endpoint Configuration Manager terwijl beveiligings updates worden beheerd met Azure Automation Updatebeheer.

>[!NOTE]
>Hoewel Updatebeheer update-evaluatie en patching van Windows Server 2008 R2 ondersteunt, biedt het geen ondersteuning voor clients die worden beheerd door micro soft endpoint Configuration Manager het uitvoeren van dit besturings systeem.

## <a name="prerequisites"></a>Vereisten

* U moet [Azure Automation updatebeheer](overview.md) toevoegen aan uw Automation-account.
* Windows-servers die momenteel worden beheerd door uw micro soft endpoint Configuration Manager-omgeving, moeten ook rapporteren aan de Log Analytics-werk ruimte waar ook Updatebeheer is ingeschakeld.
* Deze functie is ingeschakeld in micro soft endpoint Configuration Manager huidige branch versie 1606 en hoger. Als u uw micro soft endpoint Configuration Manager centrale beheer site of een zelfstandige primaire site met Azure Monitor logboeken en import verzamelingen wilt integreren, controleert [u Configuration Manager verbinding maken met Azure monitor logboeken](../../azure-monitor/logs/collect-sccm.md).  
* Windows-agents moeten worden geconfigureerd om te communiceren met een Windows Server Update Services-server (WSUS) of toegang hebben tot Microsoft Update als ze geen beveiligings updates ontvangen van micro soft endpoint Configuration Manager.

Hoe u clients beheert die worden gehost in azure IaaS met uw bestaande micro soft endpoint Configuration Manager-omgeving, is voornamelijk afhankelijk van de verbinding tussen Azure-data centers en uw infra structuur. Deze verbinding is van invloed op de ontwerp wijzigingen die u mogelijk moet aanbrengen in uw micro soft endpoint Configuration Manager-infra structuur en gerelateerde kosten ter ondersteuning van die nood zakelijke wijzigingen. Om te begrijpen welke overwegingen u wilt evalueren voordat u doorgaat, leest u [Configuration Manager on Azure - Frequently Asked Questions](/configmgr/core/understand/configuration-manager-on-azure#networking) (veelgestelde vragen).

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Software-updates van micro soft endpoint Configuration Manager beheren

Voer de volgende stappen uit als u wilt door gaan met het beheren van update-implementaties van micro soft endpoint Configuration Manager. Azure Automation maakt verbinding met micro soft endpoint Configuration Manager om updates toe te passen op de client computers die zijn verbonden met uw Log Analytics-werk ruimte. Update-inhoud is beschikbaar via de cache van de client computer alsof de implementatie door micro soft endpoint Configuration Manager is beheerd.

1. Maak een software-update-implementatie vanaf de site op het hoogste niveau in uw micro soft endpoint Configuration Manager-hiërarchie met behulp van het proces dat wordt beschreven in [software-updates implementeren](/configmgr/sum/deploy-use/deploy-software-updates). De enige instelling die anders moet worden geconfigureerd dan bij een standaardimplementatie, is de optie **Software-updates niet installeren** om het downloadgedrag van het implementatiepakket te bepalen. Dit gedrag wordt in Updatebeheer beheerd door een geplande update-implementatie in de volgende stap te maken.

2. Selecteer **updatebeheer** in azure Automation. Maak een nieuwe implementatie aan de hand van de stappen die worden beschreven in [een update-implementatie maken](deploy-updates.md#schedule-an-update-deployment) en selecteer **geïmporteerde groepen** op de vervolg keuzelijst **type** om de juiste micro soft endpoint-Configuration Manager verzameling te selecteren. Houd rekening met de volgende belangrijke punten:

    a. Als er een onderhouds venster is gedefinieerd voor de geselecteerde micro soft-eindpunt Configuration Manager-Apparaatbeheer, voldoen de leden van de verzameling deze in plaats van **de waarde-** instelling die in de geplande implementatie is gedefinieerd.

    b. Leden van de doel verzameling moeten een verbinding hebben met internet (hetzij direct, via een proxy server of via de gateway van Log Analytics).

Na het volt ooien van de update-implementatie via Azure Automation, zullen de doel computers die lid zijn van de geselecteerde computer groep updates installeren op de geplande tijd van de lokale client cache. U kunt de [status van de update-implementatie bekijken](deploy-updates.md#check-deployment-status) om de resultaten van uw implementatie te bewaken.

## <a name="manage-software-updates-from-azure-automation"></a>Software-updates beheren via Azure Automation

Voor het beheren van updates voor Windows Server-Vm's die micro soft endpoint Configuration Manager-clients zijn, moet u client beleid configureren om de Software Update Management-functie uit te scha kelen voor alle clients die worden beheerd door Updatebeheer. Standaard gelden clientinstellingen voor alle apparaten in de hiërarchie. Lees [hoe u client instellingen in Configuration Manager kunt configureren](/configmgr/core/clients/deploy/configure-client-settings)voor meer informatie over deze beleids instelling en hoe u deze kunt configureren.

Nadat u deze configuratie wijziging hebt uitgevoerd, maakt u een nieuwe implementatie volgens de stappen die worden beschreven in [een update-implementatie maken](deploy-updates.md#schedule-an-update-deployment) en selecteert u **geïmporteerde groepen** in de vervolg keuzelijst **type** om de juiste micro soft endpoint-Configuration Manager verzameling te selecteren.

## <a name="next-steps"></a>Volgende stappen

Zie [een update-implementatie plannen](deploy-updates.md#schedule-an-update-deployment)voor meer informatie over het instellen van een integratie schema.
