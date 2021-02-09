---
title: Een toepassing uitvoeren met Fortanix vertrouwelijk computing Manager
description: Meer informatie over het gebruik van Fortanix vertrouwelijk computing Manager voor het converteren van uw gecontainerde installatie kopieën
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: 41e47e29553b3e4d9cd0e0e8f280b3648f224bf9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832301"
---
# <a name="how-to-run-an-application-with-fortanix-confidential-computing-manager"></a>Procedure: een toepassing uitvoeren met Fortanix vertrouwelijk computing Manager

Start de uitvoering van uw toepassing in azure vertrouwelijk Computing met behulp van [Fortanix vertrouwelijk computing Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) en de [Fortanix-knooppunt agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) van [Fortanix](https://www.fortanix.com/).


Fortanix is een software leverancier van derden met producten en services die zijn gebouwd boven op de Azure-infra structuur. Er zijn andere providers van derden die vergelijk bare Services voor vertrouwelijke computing aanbieden op Azure.

> [!Note]
 > De producten waarnaar in dit document wordt verwezen, vallen niet onder het beheer van micro soft. Micro soft levert deze informatie alleen als een gemak en de verwijzing naar deze niet-micro soft-producten impliceert geen goed keuring door micro soft.



In deze zelf studie leert u hoe u de installatie kopie van uw toepassing kunt converteren naar een met een vertrouwelijk image beveiligde afbeelding. In deze omgeving wordt gebruikgemaakt van [Fortanix](https://www.fortanix.com/) -software, die wordt ondersteund door de DCsv2-Series Intel SGX-virtual machines van Azure. Deze oplossing coördineert essentiële beveiligings beleidsregels, zoals identiteits verificatie en gegevens toegangs beheer.

 Voor Fortanix-specifieke ondersteuning moet u lid worden van de [Fortanix-marge Community](https://fortanix.com/community/) en de kanaal #enclavemanager gebruiken.


## <a name="prerequisites"></a>Vereisten

1. Als u geen Fortanix vertrouwelijk computing Manager-account hebt, [meldt u zich](https://em.fortanix.com/auth/sign-up) aan voordat u begint.
1. Een persoonlijk [docker](https://docs.docker.com/) -REGI ster om geconverteerde installatie kopieën van toepassingen te pushen.
1. Als u geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) voordat u begint.

> [!NOTE]
> Accounts voor gratis proefversies hebben geen toegang tot de virtuele machines die in deze zelfstudie worden gebruikt. U moet upgraden naar een abonnement met betalen per gebruik.

## <a name="add-an-application-to-fortanix-confidential-computing-manager"></a>Een toepassing toevoegen aan Fortanix vertrouwelijk computing Manager
1. Meld u aan bij [Fortanix vertrouwelijk computing Manager (FORTANIX em)](https://em.fortanix.com)
1. Navigeer naar de pagina **accounts** en selecteer **account toevoegen** om een nieuw account te maken.

![Een account maken](media/how-to-fortanix-enclave-manager/create-account.png)

1. Nadat uw account is gemaakt, klikt u op **selecteren** om het zojuist gemaakte account te selecteren. Nu kunnen we de reken knooppunten registreren en toepassingen maken.
1. Selecteer de knop **+ toepassing** om een toepassing toe te voegen. In dit voor beeld voegen we een kolf server enclave-besturingssysteem toepassing toe.

1. Selecteer de knop **toevoegen** voor de enclave-besturings systeem toepassing.

    ![Toepassing toevoegen](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > In deze zelf studie wordt beschreven hoe u alleen enclave OS-toepassingen toevoegt. [Meer](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Confidential-Computing-Manager) informatie over het aanbrengen van EDP-roest-toepassingen naar Fortanix vertrouwelijk computing Manager.

6. In deze zelf studie gebruiken we het docker-REGI ster van Fortanix voor de voorbeeld toepassing. Vul de gegevens in op basis van de volgende gegevens. Gebruik uw persoonlijke docker-REGI ster om de uitvoer installatie kopie te bedenken.

    - **Toepassings naam**: python-toepassings server
    - **Beschrijving**: python-fles server
    - **Naam van de invoer installatie kopie**: fortanix/python-kolf
    - **Naam van uitvoer installatie kopie**: fortanx-privé/python-kolf-SGX
    - **ISVPRODID**: 1
    - **ISVSVM**: 1
    - **Geheugen grootte**: 1 GB
    - **Aantal threads**: 128

    *Optioneel*: Voer de toepassing uit.
    - **Docker-hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **App**: fortanix/python-kolf

        Voer de volgende opdracht uit:
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. Voeg een certificaat toe. Vul de gegevens in met behulp van de onderstaande gegevens en selecteer **volgende**:
    - **Domein**: Mijntoep. domain. dom
    - **Type**: certificaat uitgegeven door vertrouwelijk computing Manager
    - **Sleutelpad:/appkey.pem**
    - **Sleutel type**: RSA
    - **Certificaatpad**:/appcert.pem
    - **RSA-sleutel grootte**: 2048 bits


## <a name="create-an-image"></a>Een installatie kopie maken
Een Fortanix CCM-installatie kopie is een software versie of-versie van een toepassing. Elke installatie kopie is gekoppeld aan één enclave-hash (MRENCLAVE).
1. Voer op de pagina **afbeelding toevoegen** de **register referenties** voor de **naam van de uitvoer installatie kopie** in. Deze referenties worden gebruikt om toegang te krijgen tot het privé-docker-REGI ster waarin de installatie kopie wordt gepusht.

    ![installatie kopie maken](media/how-to-fortanix-enclave-manager/create-image.png)
1. Geef de afbeeldings code op en selecteer **maken**.

    ![tag toevoegen](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>Vermelding van domein en installatie kopie toestaan
Een toepassing waarvan het domein wordt toegevoegd aan de lijst met toegestane apps, ontvangt een TLS-certificaat van Fortanix vertrouwelijk computing Manager. Wanneer een toepassing wordt uitgevoerd vanuit de geconverteerde afbeelding, probeert deze ook contact op te nemen met Fortanix vertrouwelijk computing Manager. De toepassing wordt vervolgens gevraagd om een TLS-certificaat.

Ga naar het tabblad **taken** aan de linkerkant en goed keuring van de in behandeling zijnde aanvragen om het domein en de installatie kopie toe te staan.

## <a name="enroll-compute-node-agent-in-azure"></a>De agent voor reken knooppunten registreren in azure

### <a name="generate-and-copy-join-token"></a>Token voor samen voegen genereren en kopiëren
In Fortanix vertrouwelijk computing Manager maakt u een token. Met dit token kan een reken knooppunt in azure zichzelf verifiëren. U moet dit token verlenen aan uw Azure virtual machine.
1. Selecteer de knop **+ registratie knooppunt** in de beheer console.
1. Selecteer **token genereren** om het token voor samen voegen te genereren. Kopieer het token.

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Knoop punten registreren bij de Fortanix-knooppunt agent in azure Marketplace

Als u een Fortanix-knoop punt agent maakt, worden een virtuele machine, netwerk interface, virtueel netwerk, netwerk beveiligings groep en een openbaar IP-adres geïmplementeerd in uw Azure-resource groep. Uw Azure-abonnement wordt per uur gefactureerd voor de virtuele machine. Voordat u een Fortanix-knooppunt agent maakt, controleert u de pagina met prijzen voor Azure [virtual machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor de DCsv2-serie. Verwijder Azure-resources wanneer deze niet worden gebruikt.

1. Ga naar de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) en meld u aan met uw Azure-referenties.
1. Typ in de zoek balk **Fortanix vertrouwelijk computing node agent**. Selecteer de app die wordt weer gegeven in het zoekvak met de naam **Fortanix vertrouwelijk computing node agent** om naar de start pagina van de aanbieding te navigeren.
     ![Marketplace zoeken](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. Selecteer **nu downloaden**, Vul zo nodig uw gegevens in en selecteer **door gaan**. U wordt omgeleid naar de Azure Portal.
1. Selecteer **maken** om de implementatie pagina van de Fortanix vertrouwelijk computing-knoop punt in te voeren.
1. Op deze pagina voert u informatie in voor het implementeren van een virtuele machine. Deze VM is met name een DCsv2-Series Intel SGX-ingeschakelde virtuele machine van Azure met de Fortanix-knooppunt agent software geïnstalleerd. Met de knooppunt agent kan de geconverteerde afbeelding veilig worden uitgevoerd op Intel SGX-knoop punten in Azure.  Selecteer het **abonnement** en de **resource groep** waar u de virtuele machine en de bijbehorende resources wilt implementeren.

    > [!NOTE]
    > Er gelden beperkingen bij het implementeren van DCsv2-Series virtuele machines in Azure. Mogelijk moet u een quotum aanvragen voor extra kern geheugens. Lees meer informatie over de [oplossingen voor vertrouwelijke Computing op Azure-vm's](./virtual-machine-solutions.md) .

1. Selecteer een beschikbare regio.
1. Voer in het veld **knooppunt naam** een naam in voor de virtuele machine.
1. Voer de gebruikers naam en het wacht woord (of de SSH-sleutel) in voor verificatie bij de virtuele machine.
1. Wijzig de standaard grootte van de besturingssysteem schijf als 200 en selecteer een VM-grootte (Standard_DC4s_v2 is voldoende voor deze zelf studie)
1. Plak het token dat eerder is gegenereerd in het veld **deelname token** .

     ![resource implementeren](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent1.png)

1. Selecteer **Controleren + maken**. Zorg ervoor dat de validatie is geslaagd en selecteer vervolgens **maken**. Zodra alle resources zijn geïmplementeerd, wordt het reken knooppunt nu geregistreerd in Fortanix vertrouwelijk computing Manager.

## <a name="run-the-application-image-on-the-compute-node"></a>De installatie kopie van de toepassing uitvoeren op het reken knooppunt
Voer de toepassing uit door de volgende opdracht uit te voeren. Zorg ervoor dat u het IP-adres, de poort en de geconverteerde installatie kopie naam van het knoop punt wijzigt als invoer voor uw specifieke toepassing.

In deze zelf studie is de opdracht die moet worden uitgevoerd:     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

positie
- *52.152.206.164* is het host-IP-adres van de node-agent
- *9092* is de poort die door de knooppunt agent wordt geluisterd
- *fortanix-private/python-kolf-SGX* is de geconverteerde app die u kunt vinden op het tabblad Afbeeldingen onder de kolom **afbeeldings naam** in de tabel met installatie **kopieën** in de Fortanix vertrouwelijk computing Manager-webportal.

## <a name="verify-and-monitor-the-running-application"></a>De actieve toepassing controleren en bewaken
1. Ga terug naar de [Fortanix vertrouwelijk computing Manager](https://em.fortanix.com/console)
1. Zorg ervoor dat u werkt in het **account** waar u het knoop punt hebt Inge schreven
1. Ga naar de **beheer console** door het bovenste pictogram in het navigatie deel venster aan de linkerkant te selecteren.
1. Selecteer het tabblad **toepassing**
1. Controleren of er een actieve toepassing is met een bijbehorend reken knooppunt


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, kunt u de resource groep, de virtuele machine en de bijbehorende resources verwijderen. Als u de resource groep verwijdert, wordt de registratie van de knoop punten die zijn gekoppeld aan de geconverteerde afbeelding ongedaan gemaakt

Selecteer de resourcegroep voor de virtuele machine en klik op **Verwijderen**. Bevestig de naam van de resourcegroep om het verwijderen van de resources te voltooien.

Als u het Fortanix vertrouwelijk computing Manager-account wilt verwijderen dat u hebt gemaakt, gaat u naar de [pagina accounts](https://em.fortanix.com/accounts) in Fortanix vertrouwelijk computing Manager. Beweeg de muis aanwijzer over het account dat u wilt verwijderen. Selecteer de verticale zwarte stippen in de rechter bovenhoek en selecteer **account verwijderen**.

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u Fortanix tool gebruikt om de installatie kopie van uw toepassing te converteren zodat deze wordt uitgevoerd op een computer met een vertrouwelijke computing. Zie [Oplossingen op Virtual Machines](virtual-machine-solutions.md) voor meer informatie over virtuele machines met Confidential Computing in Azure.

Zie [overzicht van Azure vertrouwelijk computing](overview.md) voor meer informatie over de aanbiedingen van Azure vertrouwelijk computing

 Meer informatie over het uitvoeren van soort gelijke taken met andere aanbiedingen van derden op Azure, zoals [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) en [Scone](https://sconedocs.github.io).
