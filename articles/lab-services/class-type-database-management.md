---
title: Stel een lab in om database beheer voor relationele data bases te leren | Microsoft Docs
description: Meer informatie over het instellen van een lab om het beheer van relationele data bases te leren kennen.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 32079d6dc31cf3fcad66976737630472129b4271
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462416"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>Stel een lab in om database beheer te leren voor relationele data bases

In dit artikel wordt beschreven hoe u een Lab instelt voor een basis beheer klasse voor data bases in Azure Lab Services. Data bases zijn concepten van een van de in de meeste computer wetenschappen geschoolde opleidingen. Structured Query Language (SQL) is een internationale standaard. SQL is de standaard taal voor het beheer van relationele data bases, zoals het toevoegen, openen en beheren van inhoud in een Data Base.  Het wordt het meest vermeld voor de snelle verwerking, bewezen betrouw baarheid, het gemak en de flexibiliteit van gebruik.

In dit artikel laten we zien hoe u een virtuele-machine sjabloon kunt instellen in een Lab met zowel de MySQL-database server als SQL Server 2019-server.  [MySQL](https://www.mysql.com/) is een vrij beschik bare open-source relationeel database beheersysteem (RDBMS).  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) is de nieuwste versie van de RDBMS van micro soft.

## <a name="lab-configuration"></a>Lab-configuratie

Als u dit Lab wilt instellen, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie [zelf studie voor het instellen van een Lab-account](tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account.  U kunt ook een bestaand Lab-account gebruiken.

### <a name="lab-account-settings"></a>Instellingen van Lab-account

Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Zie [Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers opgeven](./specify-marketplace-images.md)voor meer informatie over het inschakelen van Marketplace-installatie kopieën.

| Account instelling Lab | Instructies |
| ------------------- | ------------ |
|Marketplace-installatiekopie| Schakel de installatie kopie ' SQL Server 2019 Standard op Windows Server 2019 ' in voor gebruik binnen uw Lab-account.|

### <a name="lab-settings"></a>Lab-instellingen

Gebruik de instellingen in de onderstaande tabel bij het instellen van een leslokaal Lab.  Voor meer informatie over het maken van een leslokaal Lab raadpleegt u [een klassikale Lab-zelf studie instellen](tutorial-setup-classroom-lab.md).

| Lab-instellingen | Waarde/instructies |
| ------------ | ------------------ |
|Grootte van de virtuele machine| Gemiddeld. Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse.|
|Installatie kopie van virtuele machine| SQL Server 2019-standaard op Windows Server 2019|

## <a name="template-machine-configuration"></a>Configuratie van de sjabloon machine

Als u MySQL wilt installeren op Windows Server 2019, kunt u de stappen volgen die worden beschreven in [install en run mysql community server op een virtuele machine](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine).

SQL Server 2019 is vooraf geïnstalleerd in de installatie kopie van de virtuele machine die u hebt gekozen bij het maken van het nieuwe lab.

## <a name="cost-estimate"></a>Kosten raming

Laten we een mogelijke schatting van de kosten voor deze klasse beslaan.  We gebruiken een klasse van 25 studenten.  Er zijn 20 uur geplande tijd voor de klasse.  Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten een geplande klasse tijd.  Het formaat van de virtuele machine die we hebben gekozen is gemiddeld, dat wil zeggen 42 Lab-eenheden.

Hier volgt een voor beeld van een mogelijke schatting van de kosten voor deze klasse:

25 studenten \* (20 geplande uren + 10 quotum uren) \* 0,42 USD per uur = 315,00 USD

Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor meer informatie over prijzen.

## <a name="conclusion"></a>Conclusie

In dit artikel werd uitgelegd hoe u stapsgewijs door de stappen die nodig zijn om een lab te maken voor basis concepten voor database beheer met behulp van MySQL en SQL Server. U kunt een vergelijk bare instelling voor andere data base-klassen gebruiken.

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)