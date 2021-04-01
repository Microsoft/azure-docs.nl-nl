---
title: Stel project in voor de manier waarop Labs met Azure Lab Services
description: Meer informatie over het instellen van Labs om project leider te leren hoe klassen.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: ca4fdae2372895c17c4a98dd3959935108846744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024616"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Labs instellen voor project leider de manier waarop klassen

[Project leider (PLTW)](https://www.pltw.org/) is een non-profit organisatie die PreK &ndash; 12 curriculum biedt over de Verenigde Staten in computer wetenschappen, engineering en biomedische wetenschap.  In elke PLTW-klasse gebruiken studenten diverse software toepassingen als onderdeel van hun praktijk ervaring.  Voor veel van de software toepassingen is een snelle CPU of, in sommige gevallen, een GPU vereist.  Dit artikel laat u zien hoe u Labs kunt instellen voor de volgende PLTW-klassen, die doorgaans worden aangeboden aan studenten in kwaliteiten 9 &ndash; 12:

- **Inleiding tot technisch ontwerp**

    Studenten worden geïntroduceerd in het proces van technisch ontwerp, waaronder het gebruik van [auto Desk Inventory computer-aided design (CAD)](https://www.autodesk.com/products/inventor/new-features) -software voor 3D-model lering.

- **Principes van engineering**
    
    Studenten leren meer over technische mechanismen, structurele en materiaal sterkte en automatisering.  Deze klasse maakt gebruik van software zoals [MD solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [westelijke pointer Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)en [de leger simulatie van Amerika](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Civiele techniek en architectuur**

    Studenten leren bouwen en ontwerpen en ontwikkelen met behulp van [auto Desk Revit](https://www.autodesk.com/products/revit/overview) Architecture-ontwerp software voor 3D buil ding Information Modeling (BIM).

- **Computer geïntegreerde productie**

    Studenten verkennen moderne productie processen waarbij Robotics en automatisering betrokken zijn.   In deze klasse gebruiken studenten software van [auto Desk Inventory CAD](https://www.autodesk.com/products/inventor/new-features) en [auto Desk Inventory computer-aided Manufacturing (CAM)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digitale elektronica**

    Studenten onderzoeken elektronische Logic-circuits en-apparaten door gebruik te maken van de MultiSim simulatie-en circuit ontwerp software van [National instrument](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Technisch ontwerp en-ontwikkeling**

    Studenten dragen bij aan een end-to-end oplossing door onderzoek, ontwerp en tests te combi neren die ze aanbieden aan een panel van technici.  In deze klasse gebruiken studenten software van [auto Desk Inventory CAD](https://www.autodesk.com/products/inventor/new-features) .

- **Essentiële computer wetenschappen**

    Studenten worden geïntroduceerd op reken kundige concepten en hulpprogram ma's.  Ze beginnen met Program meren op basis van een blok kering en vervolgens overstappen op op tekst gebaseerde code ring met behulp van code omgevingen zoals [VEXcode V5-blokken](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principes van computer wetenschappen**
    
    Studenten groeien hun programmeer kennis met [python](https://www.python.org/) met behulp van de [ontwikkel omgeving van micro soft Visual Studio code](https://code.visualstudio.com/). 

- **Computer wetenschappen A**

    Studenten breiden hun programmeer competentie in deze klasse uit door de ontwikkeling van mobiele apps te leren.  In deze klasse leren ze [Java](https://www.java.com/) met behulp van de [micro soft Visual Studio code Development Environment](https://code.visualstudio.com/).  Studenten gebruiken ook een emulator waarmee ze hun mobiele app-code kunnen uitvoeren en testen.  [Neem contact op met Azure Lab Services](mailto:AzLabsCOVIDSupport@microsoft.com)voor informatie over het instellen van een emulator in Azure Lab Services.

Voor een volledige lijst met klasse-software gaat u naar de [PLTW-site](https://www.pltw.org/pltw-software) voor elke klasse.

## <a name="lab-configuration"></a>Lab-configuratie

Als u Labs wilt gaan instellen voor PLTW, hebt u een Azure-abonnement en een Lab-account nodig. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

Nadat u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [een Lab-account instellen](./tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account. U kunt ook een bestaand Lab-account gebruiken.

Nadat u een Lab-account hebt ingesteld, moet u een afzonderlijke Lab maken voor elke PLTW-sessie die uw school biedt.  We raden u ook aan om afzonderlijke installatie kopieën te maken voor elk type PLTW-klasse.  Zie het blog bericht over [een fysiek Lab verplaatsen naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)voor meer informatie over het structureren van uw Labs en installatie kopieën.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen voor uw Lab-account in, zoals wordt beschreven in de volgende tabel. Zie voor meer informatie over het inschakelen van installatie kopieën van Azure Marketplace [de Azure Marketplace-installatie kopieën opgeven die beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md).

| Account instelling Lab | Instructies |
| -------------------- | ----- |
| Marketplace-installatiekopie | Schakel de Windows 10 Pro-installatie kopie in voor gebruik binnen uw Lab-account. |

<br>

### <a name="lab-settings"></a>Lab-instellingen
De grootte van de virtuele machine (VM) die wordt aanbevolen voor het gebruik van PLTW-klassen, is afhankelijk van de typen werk belastingen die uw studenten in de klasse uitvoeren.  Voor de eerder weer gegeven klassen raden we u aan om kleine GPU (visualisatie) en grote VM-grootten te gebruiken. Raadpleeg de richt lijnen in de volgende tabel als u Labs voor uw PLTW-klassen instelt:

| Lab-instelling | Waarde en beschrijving | Aanbeveling van klasse |
| ------------ | ------------------ | --- |
| Grootte van de virtuele machine | **Kleine GPU (visualisatie)**<br>Het meest geschikt voor externe visualisatie, streaming, games en code ring met frameworks zoals OpenGL en DirectX. | We raden u aan deze grootte te gebruiken voor de volgende PLTW-klassen: civiele techniek en architectuur, digitale elektronica, computer geïntegreerde productie en technisch ontwerp en ontwikkeling.
| Grootte van de virtuele machine | **Groot**<br>Het meest geschikt voor toepassingen die snellere Cpu's, betere prestaties van lokale schijven, grote data bases en grote geheugen caches nodig hebben. | We raden u aan deze grootte te gebruiken voor de volgende PLTW-klassen: inleiding op technisch ontwerp, principes van technische ontwikkeling, computer wetenschappen, beginselen van computer wetenschappen en computer wetenschappen A. |

<br>

### <a name="license-server"></a>Licentie server
De meeste software die wordt gebruikt in de eerder genoemde PLTW-klassen, vereist *geen* toegang tot een licentie server.  U hebt echter wel toegang tot een licentie server als u van plan bent het netwerk licentie model voor auto Desk te gebruiken voor de volgende software:
-   Revit
-   Vind
-   Voor raden CAM

[PLTW biedt gedetailleerde stappen](https://www.pltw.org/pltw-software) voor het installeren van het netwerk licentie beheer van auto Desk op uw licentie server om netwerk licenties te gebruiken met auto Desk-software.  Deze licentie server bevindt zich gewoonlijk in uw on-premises netwerk of wordt gehost op een virtuele Azure-machine (VM) in het virtuele Azure-netwerk.

Nadat de licentie server is ingesteld, moet u [het virtuele netwerk](./how-to-connect-peer-virtual-network.md) koppelen aan uw [Lab-account](./tutorial-setup-lab-account.md). U moet de netwerk peering uitvoeren *voordat* u het Lab maakt, zodat uw Lab-vm's toegang hebben tot de licentie server en vice versa.

Door auto Desk gegenereerde licentie bestanden het MAC-adres van de licentie server insluiten.  Als u besluit uw licentie server te hosten met behulp van een Azure-VM, is het belang rijk om ervoor te zorgen dat het MAC-adres van uw licentie server niet wordt gewijzigd. Als het MAC-adres wordt gewijzigd, moet u de licentie bestanden opnieuw genereren. Ga als volgt te werk om te voor komen dat uw MAC-adres wordt gewijzigd:

- [Stel een statisch privé-IP en Mac-adres](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) in voor de virtuele Azure-machine die als host fungeert voor uw licentie server.
- Zorg ervoor dat u zowel uw Lab-account als het virtuele netwerk van de licentie server in een regio of locatie met voldoende VM-capaciteit hebt ingesteld, zodat u deze resources niet later hoeft te verplaatsen naar een nieuwe regio of locatie.

Zie [een licentie server instellen als een gedeelde bron](./how-to-create-a-lab-with-shared-resource.md)voor meer informatie.

### <a name="template-machine"></a>Sjabloon machine
Enkele van de installatie bestanden die u nodig hebt voor PLTW zijn groot. Wanneer u de bestanden downloadt naar een test sjabloon-VM, kan het enige tijd duren voordat deze zijn gekopieerd.

In plaats van installatie bestanden te downloaden naar de sjabloon machine en alles daar te installeren, raden we u aan om uw PLTW-installatie kopieën in uw fysieke omgeving te maken.  U kunt de aangepaste installatie kopieën vervolgens importeren in de galerie met gedeelde installatie kopieën, zodat u ze kunt gebruiken om uw Labs te maken.  Zie [een aangepaste installatie kopie uploaden naar de galerie met gedeelde afbeeldingen](./upload-custom-image-shared-image-gallery.md)voor meer informatie.

Let bij het volgen van deze aanbeveling op de belangrijkste taken voor het instellen van een Lab:

1. Maak de installatie kopie voor de klasse in uw fysieke omgeving.

    a.  Gebruik de gedetailleerde stappen van PLTW om de installatie bestanden te downloaden en de vereiste software te installeren.

    > [!NOTE]    
    > Wanneer u de auto Desk-toepassingen installeert, moet de computer waarop u ze installeert, kunnen communiceren met uw licentie server. In de wizard auto Desk installeren wordt u gevraagd de computer naam op te geven van de computer waarop de licentie server wordt gehost.  Als u uw licentie server op een virtuele Azure-machine host, moet u mogelijk wachten op de installatie van auto Desk op de VM van het lab-sjabloon, zodat de installatie wizard toegang heeft tot uw licentie server.

    b.  [Installeer en configureer OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) of andere back-upopties die door uw school kunnen worden gebruikt.
    
    c.  [Windows-updates installeren en configureren](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Upload de aangepaste installatie kopie naar de [Galerie met gedeelde afbeeldingen die is gekoppeld aan uw Lab-account](./how-to-attach-detach-shared-image-gallery.md).

1.  Maak een lab en selecteer vervolgens de aangepaste installatie kopie die u in de voor gaande stap hebt geüpload.

1.  Nadat het lab is gemaakt, start en maakt u verbinding met de VM van de sjabloon om te controleren of de installatie kopie naar verwachting werkt.

1.  Publiceer ten slotte de VM van de sjabloon om de Vm's van de student te maken.

## <a name="student-devices"></a>Studenten apparaten
Studenten kunnen verbinding maken met hun Lab-Vm's van Windows-computers, Mac en Chromebook. Instructies vindt u hier:

- [Verbinding maken vanaf Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Verbinding maken vanaf Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Verbinding maken vanaf Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Kosten
Laten we een voor beeld van een kosten raming voor de PLTW-klassen.  Deze schatting omvat niet de kosten voor het uitvoeren van een licentie server of het gebruik van een galerie met gedeelde installatie kopieën. Stel dat u een klasse van 25 studenten hebt, elk met een periode van 20 uur geplande klasse.  Elke student heeft ook een extra 10 quotum uren voor huis werk of toewijzingen buiten de geplande tijd.  Dit zijn de geschatte kosten:

- **Grote VM**

    25 studenten &times; (20 geplande uren + 10 quotum uren) &times; 70 Lab-eenheden &times; USD 0,01 per uur = USD 525.00

- **Kleine GPU (visualisatie)**

    25 studenten &times; (20 geplande uren + 10 quotum uren) &times; 160 Lab-eenheden &times; USD 0,01 per uur = USD 1200.00

> [!IMPORTANT] 
> De schatting van de kosten is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele prijs informatie.

> [!NOTE] 
> Veel van de PLTW-klassen gebruiken toepassingen die toegankelijk zijn via een browser, zoals MIT app Inventory.  Voor deze browser toepassingen is geen snelle CPU of GPU vereist en u hebt toegang tot de apps vanaf elk apparaat met een Internet verbinding.  Wanneer studenten gebruikmaken van deze typen toepassingen, raden we u aan de browser op hun fysieke apparaat te gebruiken in plaats van de browser op hun Lab-VM. Studenten kunnen helpen kosten te verlagen door hun Lab-VM alleen te gebruiken voor toepassingen waarvoor een snelle CPU of GPU is vereist.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen bij het instellen van uw Lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users) 
