---
title: Azure Integration runtime in Azure Data Factory maken
description: Meer informatie over het maken van Azure Integration runtime in Azure Data Factory, die wordt gebruikt voor het kopiëren van gegevens en trans formatie-activiteiten.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/09/2020
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: ecf2a74a16234084fbac4d1c26157d1703b56a13
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373059"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Azure Integration Runtime maken en configureren
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De Integration Runtime (IR) is de reken infrastructuur die door Azure Data Factory wordt gebruikt om mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Zie [Integration runtime](concepts-integration-runtime.md)(Engelstalig) voor meer informatie over IR.

Azure IR biedt een volledig beheerde Compute voor systeem eigen gegevens verplaatsing en activiteiten voor gegevens transformatie voor het berekenen van services zoals HDInsight. Deze wordt gehost in de Azure-omgeving en ondersteunt het verbinden met bronnen in open bare netwerk omgeving met open bare eind punten.

In dit document wordt uitgelegd hoe u Azure Integration Runtime kunt maken en configureren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Standaard Azure IR
Standaard bevat elke data factory een Azure IR in de back-end die bewerkingen ondersteunt in de Cloud gegevensopslag en Compute-Services in het open bare netwerk. De locatie van de Azure IR automatisch oplossen. Als de eigenschap **connectVia** niet is opgegeven in de definitie van de gekoppelde service, wordt de standaard Azure IR gebruikt. U hoeft alleen expliciet een Azure IR te maken wanneer u de locatie van de IR expliciet wilt definiëren, of als u de uitvoering van de activiteit op verschillende IRs wilt groeperen voor beheer doeleinden. 

## <a name="create-azure-ir"></a>Azure IR maken

U kunt de volgende procedures gebruiken om een Azure IR te maken en in te stellen.

### <a name="create-an-azure-ir-via-azure-powershell"></a>Een Azure IR maken via Azure PowerShell
Integration Runtime kunnen worden gemaakt met de Power shell **-cmdlet Set-AzDataFactoryV2IntegrationRuntime** . Als u een Azure IR wilt maken, geeft u de naam, locatie en het type van de opdracht op. Hier volgt een voor beeld van een opdracht voor het maken van een Azure IR waarbij de locatie is ingesteld op Europa-west:

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Voor Azure IR moet het type worden ingesteld op **beheerd**. U hoeft geen reken gegevens op te geven omdat deze elastisch volledig worden beheerd in de Cloud. Geef reken gegevens, zoals de knooppunt grootte en het aantal knoop punten, op wanneer u Azure-SSIS IR wilt maken. Zie [Azure-SSIS IR maken en configureren](create-azure-ssis-integration-runtime.md)voor meer informatie.

U kunt een bestaande Azure IR configureren om de locatie ervan te wijzigen met behulp van de Set-AzDataFactoryV2IntegrationRuntime Power shell-cmdlet. Zie [Inleiding tot Integration runtime](concepts-integration-runtime.md)voor meer informatie over de locatie van een Azure IR.

### <a name="create-an-azure-ir-via-azure-data-factory-ui"></a>Een Azure IR maken via Azure Data Factory gebruikers interface
Gebruik de volgende stappen om een Azure IR te maken met behulp van Azure Data Factory-gebruikers interface.

1. Selecteer op de pagina **Aan de slag** van de gebruikersinterface van Azure Data Factory het [tabblad Beheren](./author-management-hub.md) in het deelvenster uiterst links.

   ![De knop Beheren op de startpagina](media/doc-common-process/get-started-page-manage-button.png)

1. Selecteer **Integration Runtimes** in het linkerdeelvenster en selecteer vervolgens **+Nieuw**.

   ![Scherm opname van de integratie-Runtimes in het linkerdeel venster en de knop + Nieuw.](media/doc-common-process/manage-new-integration-runtime.png)

1. Selecteer op de pagina **Integration runtime Setup** de optie **Azure, zelf-hostend** en selecteer **door gaan**. 

1. Selecteer op de volgende pagina **Azure** om een Azure IR te maken en selecteer vervolgens **door gaan**.
   ![Een Integration Runtime maken](media/create-azure-integration-runtime/new-azure-integration-runtime.png)

1. Voer een naam in voor uw Azure IR en selecteer **maken**.
   ![Een Azure IR maken](media/create-azure-integration-runtime/create-azure-integration-runtime.png)

1. U ziet een pop-upmelding wanneer het maken is voltooid. Controleer op de pagina **Integration Runtimes** of de ZOJUIST gemaakte IR in de lijst wordt weer geven.

## <a name="use-azure-ir"></a>Azure IR gebruiken

Zodra een Azure IR is gemaakt, kunt u ernaar verwijzen in de definitie van de gekoppelde service. Hieronder ziet u een voor beeld van hoe u kunt verwijzen naar het Azure Integration Runtime dat hierboven is gemaakt op basis van een Azure Storage gekoppelde service:

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=..."
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor meer informatie over het maken van andere typen Integration Runtimes:

- [Een zelf-hostende integratieruntime maken](create-self-hosted-integration-runtime.md)
- [Azure-SSIS-integratie-runtime maken](create-azure-ssis-integration-runtime.md)
