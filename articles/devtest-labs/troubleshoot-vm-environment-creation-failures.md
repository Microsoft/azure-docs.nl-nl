---
title: Problemen met VM-en omgevings fouten Azure DevTest Labs
description: Meer informatie over het oplossen van problemen met de virtuele machine (VM) en het maken van een omgeving in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476475"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Problemen met de virtuele machine (VM) en het maken van een omgeving oplossen in Azure DevTest Labs
DevTest Labs geeft u waarschuwingen als een computer naam ongeldig is of als u een test beleid schendt. Soms wordt er rood weer geven `X` naast de VM of omgevings status van uw Lab, waarmee u wordt geïnformeerd dat er iets verkeerd is gegaan.  In dit artikel vindt u enkele trucs die u kunt gebruiken om het onderliggende probleem te vinden en om het probleem in de toekomst te voor komen.

## <a name="portal-notifications"></a>Meldingen in portal
Als u de Azure Portal gebruikt, is de eerste plaats om te kijken naar het **deel venster meldingen**.  Het deel venster meldingen, dat beschikbaar is op de hoofd opdracht balk door te klikken op het **klok pictogram**, vertelt u of de test-VM of-omgeving is gemaakt.  Als er een fout is opgetreden, wordt het fout bericht weer gegeven dat is gekoppeld aan het maken van een fout. De details bevatten vaak meer informatie om het probleem op te lossen. In het volgende voor beeld is het maken van de virtuele machine mislukt vanwege het uitvoeren van de kern geheugens. In het gedetailleerde bericht wordt uitgelegd hoe u het probleem kunt oplossen en een kern quotum toename aanvragen.

![Azure Portal-melding](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Virtuele machine in beschadigde status
Als u de status van uw virtuele machine in het Lab als **beschadigd** ziet, is de onderliggende VM mogelijk verwijderd van de pagina met **virtuele machines** waarnaar de gebruiker kan navigeren vanuit de **virtual machines** pagina (niet van de pagina DevTest Labs). Ruim uw Lab op in DevTest Labs door de virtuele machine uit het lab te verwijderen. Maak vervolgens de virtuele machine opnieuw in het lab. 

![VM met beschadigde status](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Activiteitenlogboeken
Bekijk activiteiten Logboeken als u een fout ergens anders onderzoekt nadat u uw VM of omgeving hebt gemaakt. In deze sectie wordt beschreven hoe u Logboeken voor Vm's en omgevingen kunt vinden.

## <a name="activity-logs-for-virtual-machines"></a>Activiteiten logboeken voor virtuele machines

1. Op de start pagina van uw Lab selecteert u de VM om de pagina **virtuele machine** te starten.
2. Selecteer op de pagina **virtuele machine** , in de sectie **bewaking** van het linkermenu, het **activiteiten logboek** om alle logboeken weer te geven die zijn gekoppeld aan de VM.
3. Selecteer in de activiteiten logboek items de bewerking die is mislukt. Normaal gesp roken wordt de mislukte bewerking aangeroepen `Write Virtualmachines` .
4. Ga in het rechterdeel venster naar het tabblad JSON. U ziet de details in de JSON-weer gave van het logboek.

    ![Activiteiten logboek voor een VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Bekijk het JSON-logboek totdat u de eigenschap hebt gevonden `statusMessage` . Het biedt u het belangrijkste fout bericht en gedetailleerde informatie, indien van toepassing. De volgende JSON is een voor beeld van de fout overschrijdt die eerder in dit artikel is aangetroffen.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Activiteiten logboek voor een omgeving

Voer de volgende stappen uit om het activiteiten logboek voor het maken van een omgeving te bekijken:

1. Op de start pagina van uw Lab selecteert u **configuratie en beleid** in het menu links.
2. Selecteer op de pagina **configuratie en beleid** de optie **activiteiten logboeken** in het menu.
3. Zoek naar de fout in de lijst met activiteiten in het logboek en selecteer deze.
4. Ga in het rechterdeel venster naar het tabblad JSON en zoek naar het **statusMessage**.

    ![Activiteiten logboek omgeving](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Implementatie logboeken van Resource Manager-sjabloon
Als uw omgeving of virtuele machine is gemaakt via Automation, is er nog een laatste plaats om fout gegevens te bekijken. Dat is het Azure Resource Manager sjabloon implementatie logboek. Wanneer een Lab-resource wordt gemaakt via Automation, wordt deze vaak uitgevoerd via een Azure Resource Manager sjabloon implementatie. Zie [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) voor voor beelden van Azure Resource Manager sjablonen voor het maken van DevTest Labs-resources.

Voer de volgende stappen uit om de implementatie logboeken van de Lab-sjabloon te bekijken:

1. Start de pagina voor de resource groep waarin het lab zich bevindt.
2. Selecteer **implementaties** in het menu links onder **instellingen**.
3. Zoek naar implementaties met de status mislukt en selecteer deze.
4. Selecteer op de pagina **implementatie** de koppeling **bewerkings Details** voor de bewerking die is mislukt.
5. In het venster **bewerkings Details** vindt u meer informatie over de bewerking die is mislukt.

## <a name="next-steps"></a>Volgende stappen
Zie [problemen met artefacten oplossen](devtest-lab-troubleshoot-artifact-failure.md)
