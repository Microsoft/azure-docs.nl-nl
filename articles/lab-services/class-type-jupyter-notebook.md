---
title: Stel een lab in om data Science te leren met python-en Jupyter-notebooks | Microsoft Docs
description: Meer informatie over het instellen van een lab om data Science te leren werken met python-en Jupyter-notebooks.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94648018"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Stel een lab in om data Science te leren met python-en Jupyter-notebooks
In dit artikel wordt beschreven hoe u een virtuele machine (VM) in Lab-Services instelt met de hulpprogram ma's die nodig zijn om studenten te leren hoe ze [Jupyter-notebooks](http://jupyter-notebook.readthedocs.io/)gebruiken en hoe studenten verbinding kunnen maken met hun notebooks op hun virtuele machines (vm's).

Jupyter-notebooks is een open-source project waarmee u eenvoudig uitgebreide tekst en uitvoer bare python-bron code kunt combi neren op één canvas dat een notitie blok wordt genoemd. Het uitvoeren van een notitie blok resulteert in een lineaire record met invoer en uitvoer. Deze uitvoer kan tekst, tabel met gegevens, spreidings grafieken en meer zijn.

## <a name="set-up-the-lab"></a>Het lab instellen

### <a name="lab-configuration"></a>Lab-configuratie
Als u dit Lab wilt instellen, moet u toegang hebben tot een Azure-abonnement en een Lab-account. Neem contact op met de beheerder van uw organisatie om te controleren of u toegang kunt krijgen tot een bestaand Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

Zodra u een Azure-abonnement hebt, maakt u een nieuw Lab-account in Azure Lab Services door de volgende instructies in de zelf studie te volgen: [een Lab-account instellen](tutorial-setup-lab-account.md). U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account
Schakel de instellingen in die worden beschreven in de onderstaande tabel voor het lab-account. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](specify-marketplace-images.md)voor meer informatie over het inschakelen van Marketplace-installatie kopieën.

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
| Marketplace-installatiekopie | In uw Lab-account kunt u een van de Azure Marketplace-installatie kopieën inschakelen op basis van de behoeften van uw besturings systeem: <br/><ul><li>Data Science Virtual Machine-Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18,04</li></ul> |

> [!NOTE]
> In dit artikel wordt gebruikgemaakt van de data Science-installatie kopieën voor virtuele machines die beschikbaar zijn op de Azure Marketplace, omdat deze vooraf zijn geconfigureerd met Jupyter Notebook. Deze installatie kopieën bevatten echter ook tal van andere hulpprogram ma's voor ontwikkeling en modellen voor gegevens wetenschap. Als u deze extra hulpprogram ma's niet wilt en een licht gewicht configuratie met alleen Jupyter-notebooks wilt, maakt u een aangepaste VM-installatie kopie. Voor een voor beeld [installeert u JupyperHub op Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Zodra de aangepaste installatie kopie is gemaakt, kunt u deze uploaden naar een galerie met gedeelde afbeeldingen om de installatie kopie in Azure Lab Services te gebruiken. Meer informatie over het [gebruik van de galerie voor gedeelde afbeeldingen in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Lab-instellingen
Configureer de grootte van de **virtuele machine** en installatie kopie-instellingen van de **virtuele machine** , zoals wordt weer gegeven in de volgende tabel bij het instellen van een leslokaal Lab. Zie [een leslokaal omgeving instellen](tutorial-setup-classroom-lab.md)voor instructies voor het maken van een leslokaal Lab.

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ | 
| Grootte van de virtuele machine | <p>De grootte die u hier kiest, is afhankelijk van de werk belasting die u wilt uitvoeren:</p><ul><li>Klein of gemiddeld – goed voor een eenvoudige installatie van toegang tot Jupyter-notebooks</li><li>Kleine GPU (Compute): het beste geschikt voor computerintensieve en netwerkintensieve toepassingen, zoals kunst matige intelligentie en diep gaande lessen</li></ul> | 
| Installatie kopie van virtuele machine | <p>Kies een van de volgende installatie kopieën op basis van de behoeften van uw besturings systeem:</p><ul><li>[Data Science Virtual Machine-Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18,04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Virtuele machine van sjabloon
Zodra u een Lab hebt gemaakt, wordt er een sjabloon-VM gemaakt op basis van de grootte van de virtuele machine en de installatie kopie die u hebt gekozen. U configureert de sjabloon-VM met alles wat u voor deze klasse wilt leveren aan uw studenten. Zie [hoe u de virtuele machine van de sjabloon beheert](how-to-create-manage-template.md)voor meer informatie. 

De Data Science VM-installatie kopieën worden standaard geleverd met veel data Science frameworks en hulpprogram ma's die vereist zijn voor dit type klasse. De afbeeldingen bevatten bijvoorbeeld:

- [Jupyter-notebooks](http://jupyter-notebook.readthedocs.io/): een webtoepassing waarmee gegevens wetenschappers onbewerkte gegevens kunnen maken, berekeningen kunnen uitvoeren en de resultaten in dezelfde omgeving worden weer gegeven. Het wordt lokaal uitgevoerd in de sjabloon-VM.  
- [Visual Studio code](https://code.visualstudio.com/): een Integrated Development Environment (IDE) die een rijke interactieve ervaring biedt bij het schrijven en testen van een notitie blok. Zie [werken met Jupyter-notebooks in Visual Studio code](https://code.visualstudio.com/docs/python/jupyter-support)voor meer informatie.

### <a name="provide-notebooks-for-the-class"></a>Notitie blokken voor de klasse opgeven
De volgende taak bestaat uit het leveren van studenten met notitie blokken die u wilt gebruiken. Als u uw eigen notitie blokken wilt opgeven, kunt u de notitie blokken lokaal opslaan op de sjabloon-VM. 

Als u voor beelden van notitie blokken van Azure Machine Learning wilt gebruiken, Zie [How to Configure an environment with Jupyter notebooks](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Optioneel: grafisch bureau blad inschakelen voor Linux 
De **Data Science virtual machine-Ubuntu-** installatie kopie is al ingericht met X2GO server en is klaar om client verbindingen te accepteren. Er zijn geen verdere stappen vereist bij het instellen van de sjabloon-VM. 

### <a name="publish-the-template-machine"></a>De sjabloon machine publiceren
Wanneer u de sjabloon publiceert, krijgt elke student die bij uw Lab is geregistreerd, een kopie van de sjabloon-VM met alle lokale hulpprogram ma's en notitie blokken die u hebt ingesteld.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Hoe studenten verbinding maken met Jupyter-notebooks?
Zodra u de sjabloon hebt gepubliceerd, heeft elke student toegang tot een virtuele machine die wordt geleverd met alles wat u vooraf hebt geconfigureerd voor de klasse, met inbegrip van de Jupyter-notebooks. De volgende secties tonen verschillende manieren waarop studenten verbinding kunnen maken met Jupyter-notebooks. 

### <a name="for-windows-vms"></a>Voor Windows-VM's
Als u studenten hebt voorzien van Windows-Vm's, moeten ze verbinding maken met hun Vm's en Jupyter-notebooks gebruiken die lokaal beschikbaar zijn. 

Een student kan een verbinding met een extern bureau blad (RDP) gebruiken om verbinding te maken met een virtuele Windows-machine. Zie [toegang tot een leslokaal Lab](how-to-use-classroom-lab.md)voor gedetailleerde stappen. 

Een student die gebruikmaakt van een Mac of Chromebook kan instructies volgen uit de volgende artikelen om verbinding te maken met de data Science Windows-VM. 

- [Verbinding maken met een virtuele machine met behulp van RDP op een Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Verbinding maken met een virtuele machine met behulp van RDP in een Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Voor Linux-VM's
Als u studenten hebt voorzien van Linux Vm's, zijn er verschillende opties die studenten kunnen gebruiken om verbinding te maken met hun Jupyter-notebooks in de Vm's:

- Lokale toegang tot Jupyter-notebooks nadat u verbinding hebt gemaakt met de virtuele machine
    - SSH naar de virtuele machine voor Terminal sessies
     - X2Go-verbinding met de virtuele machine voor grafische sessies
- Gebruik SSH-Tunneling om vanaf de lokale computer van de student rechtstreeks verbinding te maken met de Jupyter-server op de virtuele machine. 

De volgende secties bevatten informatie over deze manieren om verbinding te maken met Jupyter-notebooks. 

#### <a name="ssh-to-virtual-machine"></a>SSH-naar-virtuele machine
Studenten kunnen via SSH verbinding maken met hun Linux-Vm's via een terminal sessie. Zie [toegang tot een leslokaal Lab](how-to-use-classroom-lab.md)voor gedetailleerde stappen. Als ze een Windows-client computer gebruiken, moeten ze een SSH-client inschakelen door [putty](https://www.putty.org/) te downloaden of [OpenSSH in Windows in](/windows-server/administration/openssh/openssh_install_firstuse) te scha kelen naar SSH vanaf de opdracht prompt. 

1.  Start de virtuele machine.
2.  Zodra de virtuele machine actief is, klikt u op **verbinding maken**. Hiermee wordt een dialoog venster weer gegeven met de SSH-opdracht teken reeks, die er als volgt uitziet:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Ga naar de opdracht prompt of Terminal en plak in deze opdracht en druk op **Enter**.
4.  Voer het wacht woord in om u aan te melden bij de virtuele machine. 

Zodra studenten zijn verbonden met Vm's, kunnen ze Jupyter-notebooks lokaal openen en uitvoeren.

#### <a name="x2go-to-virtual-machine"></a>X2Go naar virtuele machine
De **Data Science virtual machine-Ubuntu-** installatie kopie is al ingericht met X2GO server en is klaar om client verbindingen te accepteren. Om verbinding te maken met het grafische bureau blad van de Linux-computer, moeten studenten deze eenmalige stappen volgen om X2Go op hun client computers in te stellen:

1.  Down load en installeer de [X2Go-client](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) voor uw client platform.
2.  Controleer in de [Azure Lab Services portal](https://labs.azure.com)of de virtuele Linux-machine waarmee u verbinding wilt maken, is gestart.
3.  Zodra de virtuele machine actief is, klikt u op **verbinding maken**. Hiermee wordt een dialoog venster weer gegeven met de SSH-opdracht teken reeks, die er als volgt uitziet:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Zodra u deze informatie hebt, opent u de X2Go-client app en maakt u een nieuwe sessie. 
5.  Vul de volgende waarden in het deel venster **sessie voorkeuren** in:
    - **Sessie naam**: het kan alles wat u wilt, maar u kunt het beste de naam van uw test-VM gebruiken.
     - **Host**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **Aanmelden**: student
     - **SSH-poort**: 12345
     - **Sessie type**: XFce
6. Selecteer **OK**. 

    > [!NOTE]
     > Wanneer u een nieuwe X2Go-sessie maakt, moet u ervoor zorgen dat u de SSH-poort gebruikt, **niet** de RDP-poort.

Voer nu de volgende stappen uit om verbinding te maken met de virtuele machine:    

1.  Dubbel klik in de X2Go-client op de virtuele machine waarmee u verbinding wilt maken. 

    ![X2Go-client](./media/class-type-jupyter-notebook/x2go-client.png)
2. Voer het wacht woord in om verbinding te maken met de virtuele machine. (Mogelijk moet u X2Go toestemming geven om uw firewall te omzeilen om verbinding te maken.)
3.  U ziet nu de grafische interface voor uw Ubuntu-Data Science VM.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>SSH-tunnel naar Jupyter-server op de VM
Sommige studenten willen mogelijk rechtstreeks vanaf hun lokale computer verbinding maken met de Jupyter-server binnen hun Vm's. Het SSH-protocol maakt het mogelijk poort door te sturen tussen de lokale computer en een externe server (in ons geval de Lab-VM van de student), zodat een toepassing die op een bepaalde poort op de server wordt uitgevoerd, wordt **getunneld** naar de toewijzings poort op de lokale computer. Studenten moeten de volgende stappen uitvoeren om SSH-tunnels naar de Jupyter-server op hun Lab-Vm's uit te voeren:

1.  Controleer in de [Azure Lab Services portal](https://labs.azure.com)of de virtuele Linux-machine die u wilt verbinden, is gestart.
2.  Zodra de virtuele machine actief is, klikt u op **verbinding maken**. Hiermee wordt een dialoog venster weer gegeven met de SSH-opdracht teken reeks, die er als volgt uitziet:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. Start een Terminal of opdracht prompt op de lokale computer en kopieer de SSH-connection string ernaar. Voeg vervolgens toe `-L 8888:localhost:8888` aan de opdracht teken reeks, waarmee de **tunnel** tussen de poorten wordt gemaakt. De uiteindelijke teken reeks moet er als volgt uitzien:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Druk op **Enter** om de opdracht uit te voeren. 
5.  Wanneer u hierom wordt gevraagd, geeft u het wacht woord op om verbinding te maken met de VM van het lab. 
6.  Nadat u verbinding hebt gemaakt met de virtuele machine, start u de Jupyter-server met de volgende opdracht: 

    ```bash
     jupyter notebook
     ```
7. Als u de opdracht uitvoert, krijgt u een URL in de terminal of opdracht prompt. De URL moet er als volgt uitzien:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Plak deze URL in een browser op uw lokale computer om verbinding te maken en op uw Jupyter Notebook te werken. 

    > [!NOTE]
    > Met Visual Studio code kunt u ook een fantastische [Jupyter notebook-bewerkings ervaring bieden](https://code.visualstudio.com/docs/python/jupyter-support). U kunt de instructies voor het [maken van een verbinding met een externe Jupyter-server](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) volgen en dezelfde URL uit de vorige stap gebruiken om verbinding te maken vanuit VS code in plaats van vanuit de browser. 


## <a name="cost-estimate"></a>Kosten raming
Laten we een mogelijke schatting van de kosten voor deze klasse beslaan. We gebruiken een klasse van 25 studenten. Er zijn 20 uur geplande tijd voor de klasse. Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd. De VM-grootte die we kiezen was kleine GPU (Compute), 139 Lab-eenheden. Als u de kleine (20 Lab-eenheden) of de normale grootte (42 Lab-eenheden) wilt gebruiken, kunt u het deel van de test eenheid vervangen door de onderstaande vergelijking met het juiste nummer.  

Hier volgt een voor beeld van een mogelijke schatting van de kosten voor deze klasse: 25 studenten * (20 geplande uren + 10 quotum uren) * 139 Lab-eenheden * 0,01 USD per uur = 1042,5 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie
In dit artikel werd uitgelegd hoe de stappen voor het maken van een Lab voor een Jupyter-notitie klasse. U kunt een vergelijk bare installatie gebruiken voor andere machine learning klassen.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)