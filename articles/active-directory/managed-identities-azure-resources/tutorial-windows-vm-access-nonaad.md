---
title: Zelfstudie`:` Een beheerde identiteit gebruiken voor toegang tot Azure Key Vault - Windows - Azure AD
description: Een zelfstudie die u helpt bij het doorlopen van het proces voor het gebruiken van een door het Windows-VM-systeem toegewezen beheerde identiteit om toegang te krijgen tot Azure Key Vault.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 668d3cb044512220ff7afbc165c77da704a9a5d7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107512"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Zelfstudie: Een door het Windows-VM-systeem toegewezen beheerde identiteit gebruiken voor toegang tot Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Deze zelfstudie laat zien hoe een virtuele Windows-machine (VM) een door het systeem toegewezen beheerde identiteit kan gebruiken om toegang tot [Azure Key Vault](../../key-vault/general/overview.md) te krijgen. Key Vault fungeert als een bootstrap en maakt het mogelijk voor uw clienttoepassing om met een geheim toegang te krijgen tot resources die niet zijn beveiligd met Azure Active Directory (AD). Beheerde service-identiteiten worden automatisch beheerd in Azure en stellen u in staat om te verifiëren bij services die Azure AD-verificatie ondersteunen, zonder verificatiegegevens in uw code op te nemen.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Uw virtuele machine toegang verlenen tot een geheim dat is opgeslagen in een sleutelkluis
> * Een toegangstoken ophalen met behulp van de identiteit van de virtuele machine en daarmee het geheim uit Key Vault ophalen 

## <a name="prerequisites"></a>Vereisten

- Inzicht in beheerde identiteiten. Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](overview.md). 
- Een Azure-account, [meld u aan voor een gratis account](https://azure.microsoft.com/free/).
- 'Eigenaar'-machtigingen voor het juiste bereik (uw abonnement of resourcegroep) om de vereiste stappen voor resource-aanmaak en rolbeheer uit te voeren. Voor hulp bij roltoewijzing gaat u naar [Op rollen gebaseerd toegangsbeheer gebruiken voor het beheer van de toegang tot de resources van uw Azure-abonnement](../../role-based-access-control/role-assignments-portal.md).
- U hebt ook een virtuele Windows-machine nodig waarvoor door het systeem toegewezen beheerde identiteiten zijn ingeschakeld.
  - Als u voor deze zelfstudie een virtuele machine moet maken, kunt u dat doen met behulp van het artikel [Een virtuele machine maken waarvoor door het systeem toegewezen identiteiten ingeschakeld zijn](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)

## <a name="create-a-key-vault"></a>Een sleutelkluis maken  

In deze sectie wordt uitgelegd hoe u uw VM toegang verleent tot een geheim dat is opgeslagen in een sleutelkluis. Met behulp van beheerde identiteiten voor Azure-resources kan uw code toegangstokens ophalen voor verificatie bij resources die ondersteuning bieden voor Microsoft Azure AD-verificatie.  Niet alle Azure-services bieden echter ondersteuning voor Azure AD-verificatie. Als u bij deze services beheerde identiteiten voor Azure-resources wilt gebruiken, slaat u de servicereferenties op in Azure Key Vault en gebruikt u de beheerde identiteit van de VM om toegang te krijgen tot Key Vault en de referenties op te halen.

Eerst moeten we een sleutelkluis maken en de door het systeem toegewezen beheerde identiteit van onze VM toegang tot de sleutelkluis verlenen.

1. Open het Azure-[portaal](https://portal.azure.com/).
1. Selecteer bovenaan de linkernavigatiebalk **Een resource maken**  
1. In het vak **Marketplace doorzoeken** typt u **Key Vault** en drukt u op **Enter**.  
1. Selecteer **Key Vault** in de resultaten.
1. Selecteer **Maken**
1. Geef een **naam** op voor de nieuwe sleutelkluis.

    ![scherm Een sleutelkluis maken](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Vul alle vereiste gegevens in en zorg ervoor dat u het abonnement en de resourcegroep kiest waar u de virtuele machine hebt gemaakt die u voor deze zelfstudie gebruikt.
1. Selecteer **Controleren en maken**
1. Selecteer **Maken**

### <a name="create-a-secret"></a>Een geheim maken

Voeg vervolgens een geheim toe aan de sleutelkluis, zodat u het later kunt ophalen met behulp van code die wordt uitgevoerd op uw virtuele machine. In deze zelfstudie gebruiken we PowerShell, maar dezelfde concepten gelden voor elke andere code die wordt uitgevoerd op deze virtuele machine.

1. Ga naar uw zojuist gemaakte sleutelkluis.
1. Selecteer **Geheimen** en klik op **Toevoegen**.
1. Selecteer **Genereren/importeren**
1. In het scherm **Een geheim maken** laat u bij **Uploadopties** de optie **Handmatig** geselecteerd.
1. Voer een naam en een waarde in voor de geheime sleutel.    De waarde mag elke gewenste waarde zijn. 
1. Laat de activeringsdatum en vervaldatum leeg en laat **Ingeschakeld** ingesteld staan op **Ja**. 
1. Klik op **Maken** om het geheim te maken.

   ![Een geheim maken](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Toegang verlenen

De beheerde identiteit die door de virtuele machine wordt gebruikt, moet toegang worden verleend om het geheim te lezen dat we in de sleutelkluis zullen opslaan.

1. Ga naar uw zojuist aangemaakte sleutelkluis
1. Selecteer **Toegangsbeleid** in het menu aan de linkerkant.
1. Selecteer **Toegangsbeleid toevoegen**

   ![scherm 'Toegangsbeleid maken' in Key Vault](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. In de sectie **Toegangsbeleid toevoegen** onder **Configureren met sjabloon (optioneel)** kiest u **Geheimenbeheer** in het vervolgkeuzemenu.
1. Kies **Principal selecteren**, en voer in het zoekveld de naam in van de virtuele machine die u eerder hebt gemaakt.  Selecteer de virtuele machine in de lijst met resultaten en kies **Selecteren**.
1. Selecteer **Toevoegen**
1. Selecteer **Opslaan**.


## <a name="access-data"></a>Toegang tot gegevens  

In deze sectie wordt uitgelegd hoe u een toegangstoken verkrijgt met behulp van de identiteit van de virtuele machine en daarmee het geheim uit de sleutelkluis ophaalt. Als PowerShell 4.3.1 of hoger niet is geïnstalleerd, moet u [de meest recente versie downloaden en installeren](/powershell/azure/).

Eerst gebruiken we de door het systeem toegewezen beheerde identiteit van de VM om een toegangstoken op te halen voor verificatie bij Key Vault:
 
1. Navigeer in Azure Portal naar **Virtuele machines**, ga naar uw virtuele Windows-machine en klik op de pagina **Overzicht** op **Verbinden**.
2. Voer uw referenties (**gebruikersnaam** en **wachtwoord**) in die u hebt toegevoegd bij het maken van de **virtuele Windows-machine**.  
3. Nu u een **Verbinding met extern bureaublad** met de virtuele machine hebt gemaakt, opent u PowerShell in de externe sessie.  
4. Voer in PowerShell een Invoke-WebRequest-opdracht uit op de tenant om het token voor de lokale host in de specifieke poort voor de virtuele machine op te halen.  

De PowerShell-aanvraag:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

U ziet dat het antwoord er als volgt uitziet:

![aanvraag met tokenantwoord](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Extraheer vervolgens het toegangstoken uit het antwoord.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Gebruik ten slotte de Invoke-WebRequest-opdracht van Powershell om het geheim op te halen dat u eerder in de sleutelkluis hebt gemaakt, waarbij u het toegangstoken doorgeeft in de autorisatie-header.    U hebt de URL van uw sleutelkluis nodig. Deze vindt u in de sectie **Essentials** van de pagina **Overzicht** van de sleutelkluis.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

De reactie ziet er ongeveer als volgt uit: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Zodra u het geheim hebt opgehaald uit de sleutelkluis, kunt u deze gebruiken om te verifiëren bij een service die een gebruikersnaam en wachtwoord vereist.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources wilt opschonen, gaat u naar de [Azure-portal](https://portal.azure.com), selecteert u **Resourcegroepen**, zoekt en selecteert u de resourcegroep die in deze zelfstudie is gemaakt (zoals `mi-test`), en gebruikt u vervolgens de opdracht **Resourcegroep verwijderen**.

U kunt dit ook doen via [PowerShell of CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een door het Windows-VM-systeem toegewezen beheerde identiteit kunt gebruiken voor toegang tot Azure Key Vault.  Zie voor meer informatie over Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)