---
title: Een Service Fabric cluster omgeving maken in Azure DevTest Labs
description: Leer hoe u een omgeving met een zelfstandig Service Fabric cluster maakt en het cluster start en stopt met planningen.
author: EMaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 530cf3b20820e34913612419d0ffa731a70f6a58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85484006"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Een omgeving met een zelfstandig Service Fabric cluster maken in Azure DevTest Labs
In dit artikel vindt u informatie over het maken van een omgeving met een zelfstandig Service Fabric cluster in Azure DevTest Labs. 

## <a name="overview"></a>Overzicht
DevTest Labs kan op zichzelf staande test omgevingen maken, zoals gedefinieerd door Azure resource management-sjablonen. Deze omgevingen bevatten IaaS resources, zoals virtuele machines, en PaaS-resources, zoals Service Fabric. Met DevTest Labs kunt u virtuele machines in een omgeving beheren door opdrachten te bieden om de virtuele machines te beheren. Deze opdrachten bieden u de mogelijkheid om een virtuele machine op een schema te starten of te stoppen. Op dezelfde manier kan DevTest Labs u ook helpen bij het beheren van Service Fabric clusters in een omgeving. U kunt een Service Fabric cluster in een omgeving hand matig of via een planning starten of stoppen.

## <a name="create-a-service-fabric-cluster"></a>Een Service Fabric-cluster maken
Service Fabric clusters worden gemaakt met omgevingen in DevTest Labs. Elke omgeving wordt gedefinieerd door een Azure Resource Manager sjabloon in een Git-opslag plaats. De [open bare Git-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/Environments/) voor DevTest Labs bevat de Resource Manager-sjabloon voor het maken van een service Fabric cluster in de map [ServiceFabric-cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) . 

1. Maak eerst een lab in Azure DevTest Labs met behulp van instructies in het volgende artikel: [een lab maken](devtest-lab-create-lab.md). U ziet dat de optie **open bare omgevingen** standaard is **ingeschakeld** . 
2. Controleer aan de hand van de volgende stappen of de Service Fabric provider is geregistreerd voor uw abonnement:
    1. Selecteer **abonnementen** in het navigatie menu links en selecteer uw **abonnement**
    2. Selecteer op de pagina **abonnement** de optie **resource providers** in het gedeelte **instellingen** van het menu aan de linkerkant. 
    3. Als **micro soft. ServiecFabric** niet is geregistreerd, selecteert u **registreren**. 
3. Selecteer op de pagina **DevTest Lab** voor uw Lab **+ toevoegen** op de werk balk. 
    
    ![Knop toevoegen op de werk balk](./media/create-environment-service-fabric-cluster/add-button.png)
3. Op de pagina **Kies een basis** selecteert u **service Fabric Lab-cluster** in de lijst. 

    ![Service Fabric Lab-cluster selecteren in de lijst](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. Voer de volgende stappen uit op de pagina **instellingen configureren** : 
    1. Geef een **naam** op voor uw cluster **omgeving**. Dit is de naam van de resource groep in azure waarin het Service Fabric-cluster wordt gemaakt. 
    2. Selecteer het **besturings systeem (OS)** voor de virtuele cluster machines. De standaard waarde is: **Windows**.
    3. Geef een naam op voor de **beheerder** van het cluster. 
    4. Geef een **wacht woord** op voor de beheerder. 
    5. Voer voor het **certificaat** uw certificaat gegevens in als een base64-gecodeerde teken reeks. Als u een certificaat wilt maken, voert u de volgende stappen uit:
        1. Down load het **Create-ClusterCertificate.ps1** -bestand vanuit de [Git-opslag plaats](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster). U kunt ook de opslag plaats op uw computer klonen. 
        2. Start **PowerShell**. 
        3. Voer het **PS1** -bestand uit met behulp van de opdracht `.\Create-ClusterCertificate.ps1` . U ziet een tekst bestand dat in Klad blok wordt geopend met de informatie die u nodig hebt om de certificaat velden op deze pagina in te vullen. . 
    6. Voer het **wacht woord voor het certificaat in**.
    7. Geef de **vinger afdruk** voor het certificaat op.
    8. Selecteer **toevoegen** op de pagina **instellingen configureren** . 

        ![Cluster instellingen configureren](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. Nadat het cluster is gemaakt, ziet u een resource groep met de naam van de omgeving die u in de vorige stap hebt gegeven. Wanneer u uitvouwt, ziet u het Service Fabric cluster hierin. Als de status van de resource groep is vastgelopen bij **het maken**, selecteert u **vernieuwen** op de werk balk. De **service Fabric cluster** omgeving maakt een cluster met vijf knoop punten van één node op Linux of Windows.

    In het volgende voor beeld is **mysfabricclusterrg** de naam van de resource groep die specifiek is gemaakt voor het service Fabric cluster. Het is belang rijk te weten dat de test omgevingen zich op zichzelf bevinden in de resource groep waarin ze zijn gemaakt. Dit betekent dat de sjabloon die de omgeving definieert, alleen toegang heeft tot resources binnen de zojuist gemaakte resource groep of [virtuele netwerken die zijn geconfigureerd om te worden gebruikt door het lab](devtest-lab-configure-vnet.md). Met dit voor beeld hierboven maakt u alle vereiste resources in dezelfde resource groep.

    ![Cluster gemaakt](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>Het cluster starten of stoppen
U kunt het cluster starten of stoppen vanaf de pagina DevTest Lab zelf of via de Service Fabric cluster pagina die wordt weer gegeven door DevTest Labs. 

### <a name="from-the-devtest-lab-page"></a>Van de pagina DevTest Lab
U kunt het cluster op de DevTest Lab-pagina voor uw Lab starten of stoppen. 

1. Selecteer **drie puntjes (...)** voor het service Fabric-cluster zoals wordt weer gegeven in de volgende afbeelding: 

    ![Start-en stop opdrachten voor het cluster](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. U ziet twee opdrachten in het context menu om het cluster te **starten** en te **stoppen** . Met de start opdracht worden alle knoop punten in een cluster gestart. Met de opdracht stoppen worden alle knoop punten in een cluster gestopt. Zodra een cluster is gestopt, blijft de Service Fabric cluster zelf de status gereed, maar er zijn geen knoop punten beschikbaar totdat de start opdracht opnieuw wordt uitgegeven op het cluster in het lab.

    Er zijn enkele overwegingen waarmee u rekening moet houden wanneer u een Service Fabric cluster in een test omgeving gebruikt. Het kan enige tijd duren voordat het Service Fabric cluster volledig opnieuw is geknoeid nadat de knoop punten opnieuw zijn opgestart. Gegevens moeten worden opgeslagen op een beheerde schijf die is gekoppeld aan de virtuele machine om de gegevens van de afsluit actie te bewaren. Er zijn prestatie implicaties bij het gebruik van een gekoppelde beheerde schijf. dit wordt daarom alleen aanbevolen voor test omgevingen. Als er geen back-up wordt gemaakt van de schijf die wordt gebruikt voor gegevens opslag, gaan de gegevens verloren wanneer de opdracht stoppen op het cluster wordt gegeven.

### <a name="from-the-service-fabric-cluster-page"></a>Van de pagina Service Fabric cluster 
Er is een andere manier om het cluster te starten of te stoppen. 

1. Selecteer uw Service Fabric cluster in de structuur weergave op de pagina DevTest Lab. 

    ![Uw cluster selecteren](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. Op de pagina **service Fabric cluster** voor het cluster ziet u opdrachten op de werk balk om het cluster te starten of te stoppen. 

    ![Opdrachten op de pagina Service Fabric cluster starten of stoppen](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>Planning voor automatisch starten en automatisch afsluiten configureren
Service Fabric clusters kunnen ook worden gestart of gestopt volgens een planning. Deze ervaring is vergelijkbaar met de ervaring voor virtuele machines in een lab. Om geld te besparen, wordt standaard elk cluster dat in een lab wordt gemaakt, automatisch afgesloten op het tijdstip dat is gedefinieerd door het lab- [afsluit beleid](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy). U kunt overschrijven door op te geven of het cluster moet worden afgesloten of door de tijd op te geven waarop het cluster wordt afgesloten. 

![Bestaande planningen voor automatisch starten en automatisch afsluiten](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>Aanmelden bij de automatische start planning
Voer de volgende stappen uit om u aan te melden bij het opstart schema:

1. Selecteer **automatisch starten** in het menu links
2. Selecteer **aan** voor **toestaan dat deze service Fabric-cluster wordt gepland voor automatisch starten**. Deze pagina is alleen ingeschakeld als de eigenaar van het Lab gebruikers toestaat hun virtuele machines of Service Fabric clusters automatisch te starten.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Automatische ster pagina](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>Instellingen voor automatisch afsluiten configureren 
Voer de volgende stappen uit om de instellingen voor afsluiten te wijzigen:

1. Selecteer **automatisch afsluiten** in het menu links. 
2. Op deze pagina kunt u het automatisch afsluiten afmelden door **uit** te scha kelen voor **ingeschakeld**. 
3. Als u **hebt geselecteerd voor** **ingeschakeld**, voert u de volgende stappen uit:
    1. Geef de **tijd** op voor het afsluiten.
    2. Geef de tijd **zone** voor het tijdstip op. 
    3. Geef op of u DevTest Labs **meldingen** wilt laten verzenden voordat het automatisch wordt afgesloten. 
    4. Als u **Ja** hebt geselecteerd voor de optie melding, geeft u de **webhook-URL** en/of het **e-mail adres** op om meldingen te verzenden. 
    5. Selecteer **Opslaan** op de werkbalk.

        ![Pagina automatisch afsluiten](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Knoop punten in het Service Fabric cluster weer geven
De Service Fabric cluster pagina die u eerder hebt bekeken, is specifiek voor de pagina DevTest Labs. De knoop punten in het cluster worden niet weer gegeven. Voer de volgende stappen uit om meer informatie over het cluster weer te geven:

1. Selecteer de **resource groep** in de structuur weergave van de sectie **mijn virtuele machines** op de **DevTest Lab** -pagina voor uw Lab.

    ![Resourcegroep selecteren](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. Op de pagina **resource groep** ziet u alle resources in de resource groep in een lijst. Selecteer uw **service Fabric cluster** in de lijst. 

    ![Selecteer uw cluster in de lijst](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. U ziet de **service Fabric cluster** pagina voor uw cluster. Dit is de pagina die het Service Fabric levert. U ziet alle informatie over de clusters, zoals knoop punten, knooppunt typen, enzovoort.

    ![Start pagina van Service Fabric cluster](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over omgevingen: 

- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Open bare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
