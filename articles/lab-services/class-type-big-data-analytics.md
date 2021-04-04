---
title: Stel een lab in om big data Analytics te leren gebruiken met behulp van Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen van een Lab voor het leren van de big data Analytics met behulp van docker-implementatie van Hortonworks data platform (HDP).
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 5eb9cd00350c41645d4427e30a6f25a6c163358c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659893"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>Een Lab instellen voor big data Analytics met docker-implementatie van HortonWorks-gegevens platform

In dit artikel wordt beschreven hoe u een Lab instelt om een big data Analytics-klasse te leren.  Met dit type klasse leren cursisten hoe u grote hoeveel heden gegevens kunt verwerken en machine-en statistische leer algoritmen kunt Toep assen om gegevens inzichten te verkrijgen.  Een belang rijke doel stelling voor studenten is het leren gebruiken van hulpprogram ma's voor gegevens analyse, zoals [het open-source software pakket van Apache Hadoop](https://hadoop.apache.org/) waarmee u Big data kunt opslaan, beheren en verwerken.

In dit lab gebruiken studenten een populaire commerciële versie van Hadoop, die wordt verschaft door [Cloudera](https://www.cloudera.com/), [Hortonworks data platform (hdp)](https://www.cloudera.com/products/hdp.html).  In het bijzonder gebruiken studenten [HDP sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) . Dit is een vereenvoudigde, gebruiks vriendelijke versie van het platform die gratis is en bedoeld is voor leren en experimenteren.  Hoewel deze klasse kan gebruikmaken van virtuele Windows-of Linux-machines (VM) met HDP sandbox geïmplementeerd, wordt in dit artikel weer gegeven hoe u Windows gebruikt.

Een ander interessant aspect van dit lab is dat we HDP sandbox implementeren op de Lab-Vm's met behulp van [docker](https://www.docker.com/) -containers.  Elke docker-container biedt een eigen geïsoleerde omgeving voor het uitvoeren van software toepassingen.  Docker-containers zijn in het algemeen als geneste Vm's en kunnen worden gebruikt om eenvoudig een breed scala aan software toepassingen te implementeren en uit te voeren op basis van container installatie kopieën die worden meegeleverd op [docker hub](https://www.docker.com/products/docker-hub).  Het implementatie script van Cloudera voor HDP-sandbox haalt automatisch de [HDP sandbox 3.0.1 docker-installatie kopie](https://hub.docker.com/r/hortonworks/sandbox-hdp) uit vanuit docker hub en voert twee docker-containers uit:
  - sandbox-HDP
  - sandbox-proxy

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account.  U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](./specify-marketplace-images.md)voor meer informatie over het inschakelen van Marketplace-installatie kopieën.

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
|Marketplace-installatiekopie| Schakel de Windows 10 Pro-installatie kopie in voor gebruik binnen uw Lab-account.|

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een leslokaal Lab.  Voor meer informatie over het maken van een leslokaal Lab raadpleegt u [een klassikale Lab-zelf studie instellen](tutorial-setup-classroom-lab.md).

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Grootte van de virtuele machine| Gemiddeld (geneste virtualisatie). Deze VM-grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse.  Deze grootte biedt ook ondersteuning voor geneste virtualisatie.|  
|Installatie kopie van virtuele machine| Windows 10 Pro|

> [!NOTE] 
> We moeten gemiddeld (geneste virtualisatie) gebruiken omdat het implementeren van HDP sandbox met docker vereist is:
>   - Windows Hyper-V met geneste virtualisatie
>   - Mini maal 10 GB aan RAM-geheugen

## <a name="template-machine-configuration"></a>Configuratie van de sjabloon machine

Als u de sjabloon machine wilt instellen, doet u het volgende:
- Docker installeren
- HDP sandbox implementeren
- Power shell en Windows Task Scheduler gebruiken om automatisch de docker-containers te starten

### <a name="install-docker"></a>Docker installeren

De stappen in deze sectie zijn gebaseerd op [de instructies van Cloudera voor de implementatie met docker-containers](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html). 

Als u docker-containers wilt gebruiken, moet u eerst docker Desktop installeren op de sjabloon-VM:

1. Volg de stappen in de [sectie vereisten](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) om [docker voor Windows](https://docs.docker.com/docker-for-windows/install/)te installeren. 

    > [!IMPORTANT] 
    > Zorg ervoor dat de configuratie optie **Windows-containers gebruiken in plaats van Linux-containers** is uitgeschakeld.

1. Zorg ervoor dat **Windows-containers en de Hyper-V-functies** zijn ingeschakeld.
   ![Windows-onderdelen in- of uitschakelen](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. Volg de stappen in de sectie [geheugen voor Windows](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) om de geheugen configuratie van de docker te configureren.

    > [!WARNING]
    > Als u per ongeluk de optie **Windows-containers gebruiken in plaats van Linux-containers** hebt ingeschakeld tijdens de installatie van docker, worden de configuratie-instellingen van het geheugen niet weer geven.  Om dit probleem op te lossen, kunt u overschakelen naar het gebruik van Linux-containers door [te klikken op het docker-pictogram in het Windows](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)-systeemvak. Wanneer het menu van docker Desktop wordt geopend, selecteert **u overschakelen naar Linux-containers**.
 
### <a name="deploy-hdp-sandbox"></a>HDP sandbox implementeren

In deze sectie implementeert u HDP sandbox en opent u vervolgens ook toegang tot HDP sandbox via de browser.

1. Zorg ervoor dat u [Git Bash](https://gitforwindows.org/) hebt geïnstalleerd zoals vermeld in de [sectie vereisten](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) van de hand leiding, aangezien dit wordt aanbevolen voor het volt ooien van de volgende stappen.

1. Volg de stappen in de volgende secties [om de implementatie-en installatie handleiding van Cloudera te gebruiken voor docker](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html):
   
   -    HDP sandbox implementeren
   -    HDP sandbox controleren

    > [!WARNING] 
    > Wanneer u het meest recente zip-bestand voor HDP downloadt, moet u ervoor zorgen dat u het zip-bestand *niet* in een mappad opslaat dat witruimte bevat.

    > [!NOTE] 
    > Als u een uitzonde ring ontvangt tijdens de implementatie waarbij het **station niet is gedeeld**, moet u uw C-station met docker delen zodat de Linux-containers van HDP toegang hebben tot lokale Windows-bestanden.  Om dit probleem op te lossen, klikt u op [het pictogram docker op de Windows-systeem balk](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) om het menu docker-bureau blad te openen en **instellingen** te selecteren.  Wanneer **het dialoog venster instellingen van docker** wordt geopend, selecteert u **resources > het delen van bestanden** en controleert u het station **C** .  Vervolgens kunt u de stappen voor het implementeren van HDP sandbox herhalen.

1. Zodra de docker-containers voor HDP sandbox zijn geïmplementeerd en worden uitgevoerd, kunt u toegang krijgen tot de omgeving door uw browser te starten en de instructies van Cloudera te volgen voor het openen van de [welkomst pagina](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) van de sandbox en het HDP-dash board te starten.

    > [!NOTE] 
    > Bij deze instructies wordt ervan uitgegaan dat u het lokale IP-adres van de sandbox-omgeving eerst hebt toegewezen aan de sandbox-hdp.hortonworks.com in het hostbestand op de VM van de sjabloon.  Als u deze toewijzing **niet** doet, kunt u toegang krijgen tot de welkomst pagina van de sandbox door te navigeren naar `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>Docker-containers automatisch starten wanneer studenten zich aanmelden

We gaan een Power shell-script gebruiken dat automatisch wordt gebruikt voor het maken van een gebruiks ervaring:
  - Start de HDP sandbox docker-containers wanneer een student wordt gestart en verbinding maakt met hun Lab-VM.
  - Hiermee opent u de browser en gaat u naar de welkomst pagina van de sandbox.
We gebruiken Windows Task Scheduler ook om dit script automatisch uit te voeren wanneer een student zich aanmeldt bij de VM.
Voer de volgende stappen uit om deze in te stellen: [Big Data Analytics scripting](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/).

## <a name="cost-estimate"></a>Kosten raming

Als u de kosten van dit Lab wilt schatten, kunt u het volgende voor beeld gebruiken.

Voor een klasse van 25 studenten met 20 uur geplande en tien uur aan quota voor huis werk of-toewijzingen is de prijs voor het Lab:
  - 25 studenten * (20 + 10) uur * 55 Lab-eenheden * 0,01 USD per uur = 412,50 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie

In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen die nodig zijn voor het maken van een Lab voor een big data Analytics-klasse die gebruikmaakt van Hortonworks-gegevens platform dat is geïmplementeerd met docker.  De instellingen voor dit klassetype kunnen worden gebruikt voor soort gelijke gegevens analyse klassen.  Deze installatie kan ook van toepassing zijn op andere typen klassen die gebruikmaken van docker voor implementatie.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)