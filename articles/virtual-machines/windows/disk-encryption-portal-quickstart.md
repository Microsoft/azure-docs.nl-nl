---
title: Een virtuele Windows-machine maken en versleutelen met behulp van de Azure-portal
description: In deze quickstart leert u hoe u de Azure Portal gebruikt om een virtuele Windows-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: e29d1de976a027e32564a258fd5669b1e54c60f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564438"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-with-the-azure-portal"></a>Quickstart: Een virtuele Windows-machine maken en versleutelen met de Azure-portal

Virtuele Azure-machines (VM's) kunnen gemaakt worden via Azure Portal. De Azure-portal is een gebruikersinterface op basis van een browser voor het maken van VM's en alle verwante resources. In deze quickstart gebruikt u de Azure-portal om een virtuele Windows-machine te implementeren, een sleutelkluis te maken voor de opslag van versleutelingssleutels en de VM te versleutelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).


## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Kies in de linkerbovenhoek van de Azure-portal **Een resource maken**.
1. Selecteer op de pagina Nieuw **Windows Server 2016 Datacenter** onder Populair.
1. Controleer op het tabblad Basisinformatie onder Projectgegevens of het juiste abonnement is geselecteerd.
1. Selecteer voor Resourcegroep de optie **Nieuwe maken**. Voer *myResourceGroup* in als naam en selecteer **OK**.
1. Bij **Naam van de virtuele machine** voert u *MyVM* in.
1. Selecteer bij **Regio** *(VS) VS - oost*.
1. Controleer of de **Grootte** *Standard D2s v3* is.
1. Selecteer **Wachtwoord** onder **Beheerdersaccount**. Voer een gebruikersnaam en wachtwoord in.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-windows-vm-creation.png" alt-text="Scherm voor het maken van Windows-VM's":::

    > [!WARNING]
    > Het tabblad 'Schijven' bevat een veld 'Type versleuteling' onder **Schijfopties**. Dit veld wordt gebruikt om versleutelingsopties op te geven voor [Managed Disks](../managed-disks-overview.md) + CMK, en niet voor Azure Disk Encryption.
    >
    > Om verwarring te voorkomen, raden we aan het tabblad *Schijven* helemaal over te slaan tijdens deze zelfstudie.

1. Selecteer het tabblad 'Beheer' en controleer of u een Diagnostics-opslagaccount hebt. Als u geen opslagaccounts hebt, selecteert u 'Nieuw maken', geeft u het nieuwe account een naam en selecteert u 'OK'

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Scherm Resourcegroep maken":::

1. Klik op 'Beoordelen en maken'.
1. Op de pagina **Een virtuele machine maken** ziet u de details van de virtuele machine die u gaat maken. Wanneer u klaar bent, selecteert u **Maken**.

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. Wanneer de implementatie is voltooid, gaat u verder naar de volgende sectie.

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

1. Nadat de VM-implementatie is voltooid, selecteert u **Ga naar resource**.
1. Selecteer **Schijven** in de zijbalk links.
1. Selecteer op de bovenste balk **Extra instellingen**.
1. Onder **Versleutelingsinstellingen** > **Te versleutelen schijven** selecteert u **Besturingssysteem en gegevensschijven**.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Schermopname waarin besturingssysteem- en gegevensschijven zijn gemarkeerd.":::

1. Kies **Selecteer een sleutelkluis en sleutel voor versleuteling** onder **Versleutelingsinstellingen**.
1. Selecteer op het scherm **Sleutel selecteren in Azure Key Vault** de optie **Nieuwe maken**.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Schermopname van de optie Nieuwe maken":::.

1. Selecteer **Klik om een sleutel te selecteren** links van **Sleutelkluis en sleutel**.
1. Selecteer op het scherm **Sleutel selecteren in Azure Key Vault** onder het veld **Sleutelkluis** de optie **Nieuwe maken**.
1. Zorg er op het scherm **Sleutelkluis maken** voor dat de resourcegroep *myResourceGroup* is en geef een naam op voor de sleutelkluis.  Elke sleutelkluis in Azure moet een unieke naam hebben.
1. Schakel op het tabblad **Toegangsbeleid** het selectievakje **Azure Disk Encryption voor volumeversleuteling** in.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="selectie van schijven en versleuteling":::

1. Selecteer **Controleren + maken**.  
1. Selecteer **Maken** nadat de sleutelkluis is gevalideerd. Hierdoor komt u terug op het scherm **Sleutel selecteren in Azure Key Vault**.
1. Laat het veld **Sleutel** leeg en kies **Selecteren**.
1. Klik bovenaan het versleutelingsscherm op **Opslaan**. Er wordt een waarschuwing weergegeven dat de VM opnieuw moet worden opgestart. Klik op **Ja**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de VM niet meer nodig hebt, kunt u de resourcegroep, de machine zelf én alle gerelateerde resources verwijderen. Wanneer u dit wilt doen, selecteert u de resourcegroep voor de virtuele machine en selecteert u Verwijderen. Vervolgens bevestigt u de naam van de resourcegroep die u wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt waarvoor versleutelingssleutels werden ingeschakeld, een virtuele machine gemaakt en versleuteling ingeschakeld voor de virtuele machine.  

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)