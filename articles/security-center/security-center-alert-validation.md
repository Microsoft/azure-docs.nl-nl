---
title: Validatie van waarschuwingen in Azure Security Center | Microsoft Docs
description: Meer informatie over hoe u kunt controleren of uw beveiligings waarschuwingen correct zijn geconfigureerd in Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b90ca39d7bf01b84400464240bb581a5e7bc922a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602380"
---
# <a name="alert-validation-in-azure-security-center"></a>Validatie van waarschuwingen in Azure Security Center
In dit document leest u hoe u kunt controleren of uw systeem op de juiste manier is geconfigureerd voor waarschuwingen van Azure Security Center.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Waarschuwingen zijn de meldingen die Security Center genereert wanneer het bedreigingen op uw resources detecteert. Er worden prioriteiten gegeven en de waarschuwingen worden vermeld samen met de informatie die nodig is om snel het probleem te onderzoeken. Security Center geeft ook aanbevelingen voor hoe u een aanval kunt oplossen.
Zie [beveiligings waarschuwingen in Security Center](security-center-alerts-overview.md) en [beheren en reageren op beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) voor meer informatie.


## <a name="generate-sample-azure-defender-alerts"></a>Voorbeelden van Azure Defender-waarschuwingen genereren

Als u de nieuwe preview-versie van waarschuwingen gebruikt, zoals beschreven in [beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md), kunt u in een paar klikken voorbeeld waarschuwingen maken via de pagina beveiligings waarschuwingen in de Azure Portal.

Gebruik voorbeeld waarschuwingen voor het volgende:

- de waarde en mogelijkheden van Azure Defender evalueren
- Controleer alle configuraties die u hebt gemaakt voor uw beveiligings waarschuwingen (zoals SIEM-integraties, werk stroom automatisering en e-mail meldingen)

Voorbeeld waarschuwingen maken:

1. Als gebruiker met de rol **beveiligings beheerder** of **mede werker** van het abonnement, selecteert u in de werk balk op de pagina waarschuwingen de optie **voorbeeld waarschuwingen maken**.
1. Selecteer het abonnement.
1. Selecteer de relevante Azure Defender-plannen/s waarvoor u waarschuwingen wilt weer geven. 
1. Selecteer **voorbeeld waarschuwingen maken**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Stappen voor het maken van voorbeeld waarschuwingen in Azure Security Center":::
    
    Er wordt een melding weer gegeven dat u weet dat de voorbeeld waarschuwingen worden gemaakt:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Melding dat de voorbeeld waarschuwingen worden gegenereerd.":::

    Na enkele minuten worden de waarschuwingen weer gegeven op de pagina beveiligings waarschuwingen. Ze worden ook ergens anders weer gegeven die u hebt geconfigureerd voor het ontvangen van uw Azure Security Center beveiligings waarschuwingen (verbonden Siem's, e-mail meldingen, enzovoort).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Voorbeeld waarschuwingen in de lijst met beveiligings waarschuwingen":::

    > [!TIP]
    > De waarschuwingen zijn voor gesimuleerde resources.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Waarschuwingen op uw virtuele Azure-machines simuleren (Windows) <a name="validate-windows"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:

1. Kopieer een uitvoerbaar bestand (bijvoorbeeld **calc.exe**) naar het bureau blad van de computer of een andere map met uw gemak en wijzig de naam ervan in **ASC_AlertTest_662jfi039N.exe**.
1. Open de opdracht prompt en voer dit bestand uit met een argument (alleen een valse argument naam), zoals: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven.

> [!NOTE]
> Wanneer u deze test waarschuwing voor Windows bekijkt **, moet u** controleren of de controle van de veld **argumenten is ingeschakeld** . Als deze **False** is, moet u controle van opdracht regel argumenten inschakelen. Gebruik de volgende opdracht om dit in te schakelen: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Waarschuwingen op uw virtuele Azure-machines simuleren (Linux) <a name="validate-linux"></a>

Nadat Security Center agent op uw computer is geïnstalleerd, voert u de volgende stappen uit vanaf de computer waar u de aangevallen bron van de waarschuwing wilt ontvangen:
1. Kopieer een uitvoerbaar bestand naar een handige locatie en wijzig de naam in **./asc_alerttest_662jfi039n**, bijvoorbeeld:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Open de opdracht prompt en voer dit bestand uit:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Wacht 5 tot 10 minuten en open Security Center. Er moet een waarschuwing worden weer gegeven.


## <a name="simulate-alerts-on-kubernetes"></a>Waarschuwingen simuleren op Kubernetes <a name="validate-kubernetes"></a>

Als u Azure Kubernetes service hebt geïntegreerd met Security Center, kunt u testen of uw waarschuwingen werken met de volgende kubectl-opdracht:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Zie [Inleiding tot Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md) voor meer informatie over het beschermen van uw Kubernetes-knoop punten en-clusters.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebben we aandacht besteed aan het valideren van waarschuwingen. Raadpleeg de volgende artikelen als u meer over dit onderwerp wilt weten:

* [Azure Key Vault Threat Detection in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md) -meer informatie over het beheren van waarschuwingen en het reageren op beveiligings incidenten in Security Center.
* [Beveiligings waarschuwingen in azure Security Center](./security-center-alerts-overview.md) -meer informatie over de verschillende typen beveiligings waarschuwingen.