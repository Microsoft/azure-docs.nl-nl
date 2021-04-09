---
title: Bestandsintegriteit bewaken in Azure Security Center
description: Meer informatie over het vergelijken van basis lijnen met de bewaking van bestands integriteit in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f160948a06d8a3175158e0100345ee2a330048c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634691"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Basislijnen vergelijken met behulp van bewaking bestandsintegriteit (FIM)

Met de File Integrity Monitoring (FIM) wordt u geïnformeerd wanneer er wijzigingen optreden in gevoelige gebieden in uw resources, zodat u niet-geautoriseerde activiteiten kunt onderzoeken en oplossen. FIM bewaakt Windows-bestanden, Windows-registers en Linux-bestanden.

In dit onderwerp wordt uitgelegd hoe u FIM op de bestanden en registers inschakelt. Zie [File Integrity Monitoring in azure Security Center](security-center-file-integrity-monitoring.md)voor meer informatie over FIM.

## <a name="why-use-fim"></a>Waarom FIM gebruiken?

Besturings systeem, toepassingen en gekoppelde configuraties bepalen het gedrag en de beveiligings status van uw resources. Daarom bereiken aanvallers de bestanden die uw resources beheren, om het besturings systeem van een resource te overtake en/of om activiteiten uit te voeren zonder te worden gedetecteerd.

In feite moeten veel nalevings normen zoals PCI-DSS & ISO 17799 de implementatie van FIM-besturings elementen vereisen.  

## <a name="enable-built-in-recursive-registry-checks"></a>Ingebouwde recursieve register controles inschakelen

De standaard instellingen van de FIM-register onderdelen bieden een handige manier om recursieve wijzigingen in algemene beveiligings gebieden te bewaken.  Een kwaadwillende persoon kan bijvoorbeeld een script configureren om in LOCAL_SYSTEM context uit te voeren door een uitvoering te configureren bij het opstarten of afsluiten.  Als u de wijzigingen van dit type wilt controleren, schakelt u de ingebouwde controle in.  

![Register](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Recursieve controles zijn alleen van toepassing op aanbevolen beveiligings componenten en niet op aangepaste register paden.  

## <a name="add-a-custom-registry-check"></a>Een aangepaste register controle toevoegen

FIM-basis lijnen worden gestart door kenmerken te identificeren van een bekende goede status voor het besturings systeem en de ondersteunende toepassing.  In dit voor beeld richten we zich op de wachtwoord beleids configuraties voor Windows Server 2008 en hoger.


|Policy Name                 | Register instelling|
|---------------------------------------|-------------|
|Domeincontroller: wijzigen van wachtwoorden van computeraccounts weigeren| MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RefusePasswordChange|
|Lid van domein: gegevens in beveiligd kanaal digitaal versleutelen of ondertekenen (altijd)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireSignOrSeal|
|Lid van domein: gegevens in beveiligd kanaal digitaal versleutelen (indien mogelijk)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SealSecureChannel|
|Lid van domein: gegevens in beveiligd kanaal digitaal ondertekenen (indien mogelijk)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\SignSecureChannel|
|Lid van domein: geen systeemonderhoud van wachtwoord van computeraccount|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DisablePasswordChange|
|Lid van domein: het wachtwoord van het machineaccount heeft de maximale leeftijd bereikt|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\MaximumPasswordAge|
|Lid van domein: sterke sessiesleutel verplicht (Windows 2000 of hoger)|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RequireStrongKey|
|Netwerk beveiliging: NTLM beperken: NTLM-authenticatie in dit domein|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\RestrictNTLMInDomain|
|Netwerkbeveiliging: NTLM beperken: uitzonderingen voor servers in dit domein toevoegen|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\DCAllowedNTLMServers|
|Netwerk beveiliging: NTLM beperken: NTLM-authenticatie in dit domein controleren|MACHINE\System\CurrentControlSet\Services \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Raadpleeg voor meer informatie over register instellingen die worden ondersteund door de verschillende versies van het besturings systeem, het [werk blad Groepsbeleid instellingen referentie](https://www.microsoft.com/download/confirmation.aspx?id=25250).

FIM configureren voor het bewaken van register basislijnen:

1. In het venster **Windows-REGI ster toevoegen voor wijzigingen bijhouden** , in het tekstvak **Windows-register sleutel** , voert u de volgende register sleutel in:

    ```
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    ```

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png" alt-text="FIM inschakelen in een REGI ster":::

## <a name="track-changes-to-windows-files"></a>Wijzigingen in Windows-bestanden bijhouden

1. In het venster **Windows-bestand toevoegen voor wijzigingen bijhouden** , in het tekstvak **pad invoeren** , voert u de map in die de bestanden bevat die u wilt bijhouden. In het voor beeld in de volgende afbeelding bevindt **Contoso web app** zich in de D:\ station in de mapstructuur van de **ContosWebApp** .  
1. Maak een aangepaste vermelding voor een Windows-bestand door een naam op te geven van de instellings klasse, recursie in te scha kelen en de bovenste map met een Joker teken (*) op te geven.

    :::image type="content" source="./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png" alt-text="FIM inschakelen voor een bestand":::

## <a name="retrieve-change-data"></a>Wijzigings gegevens ophalen

Bewakings gegevens voor bestands integriteit bevinden zich in de Azure Log Analytics/ConfigurationChange-tabelset.  

 1. Stel een tijds bereik in om een samen vatting van wijzigingen per resource op te halen.
In het volgende voor beeld worden alle wijzigingen in de afgelopen veer tien dagen in de volgende categorieën van het REGI ster en de bestanden opgehaald:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. Details van de register wijzigingen weer geven:

    1. **Bestanden** verwijderen uit de component **where** , 
    1. Verwijder de samenvattings regel en vervang deze door een bestel component:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Rapporten kunnen worden geëxporteerd naar CSV voor archivering en/of gekanaald naar een Power BI-rapport.  

![FIM-gegevens](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)