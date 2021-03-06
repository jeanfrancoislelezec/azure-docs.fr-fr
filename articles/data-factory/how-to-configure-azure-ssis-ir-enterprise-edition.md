---
title: Enterprise Edition du runtime d’intégration Azure-SSIS | Microsoft Docs
description: Cet article décrit les fonctionnalités et la configuration de la version Enterprise Edition du runtime d’intégration Azure SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 42c0c000ef661a1a256ebf49cd099a4cae2185c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-edition-of-the-azure-ssis-integration-runtime"></a>Enterprise Edition du runtime d’intégration Azure-SSIS

La version Enterprise Edition du runtime d’intégration Azure-SSIS vous donne accès aux fonctionnalités avancées et Premium suivantes :
-   Modification des composants de capture des données (CDC)
-   Connecteurs Oracle, Teradata et SAP BW
-   Connecteurs et transformations SQL Server Analysis Services (SSAS) et Azure Analysis Services (AAS)
-   Transformations par regroupement probable et recherche approximative
-   Transformations par extraction et recherche de termes

Certaines de ces fonctionnalités nécessitent l’installation de composants supplémentaires destinés à personnaliser le runtime d’intégration Azure SSIS. Pour plus d’informations sur l’installation des composants supplémentaires, consultez la section [Configuration personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Fonctionnalités de la version Enterprise Edition

| **Fonctionnalités de la version Enterprise Edition** | **Descriptions** |
|---|---|
| Composants CDC | La source CDC, la tâche de gestion et la transformation par séparateur sont préinstallés sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Pour vous connecter à Oracle, il vous faudra également installer les instances CDC Designer et CDC Service sur un autre ordinateur. |
| Connecteurs Oracle | Le gestionnaire de connexions, la source et la destination Oracle sont préinstallés sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Il vous faudra installer également le pilote de l’interface d’appel Oracle (OCI) et si nécessaire configurer le standard Oracle Transport Network Substrate (TNS) sur le runtime d’intégration Azure-SSIS. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS). |
| Connecteurs Teradata | Vous devez installer le gestionnaire de connexions, la source et la destination Teradata, ainsi que l’API Teradata Parallel Transporter (TPT) et le pilote Teradata ODBC sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Pour plus d’informations, consultez [Custom setup for the Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md) (Configuration personnalisée du runtime d’intégration Azure-SSIS). |
| Connecteurs SAP BW | Le gestionnaire de connexions, la source et la destination SAP BW sont préinstallés sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Vous devez également installer le pilote SAP BW sur le runtime d’intégration Azure-SSIS. Ces connecteurs prennent en charge SAP BW 7.0 ou les versions antérieures. Pour vous connecter à des versions antérieures de SAP BW ou à d’autres produits SAP, vous pouvez acheter et installer des connecteurs SAP d’éditeurs de logiciels indépendants sur le runtime d’intégration Azure-SSIS. Pour plus d’informations sur l’installation des composants supplémentaires, consultez la section [Configuration personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Composants Analysis Services               | La destination de formation du modèle d’exploration de données, la destination du traitement de dimension et la destination du traitement de partition, ainsi que la transformation de requête d’exploration de données sont préinstallées sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Tous ces composants prennent en charge SQL Server Analysis Services (SSAS), mais seule la destination du traitement de partition prend en charge Azure Analysis Services (AAS). Pour vous connecter à SSAS, vous devez également [configurer les informations d’identification de l’authentification Windows dans SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Outre ces composants, la tâche DDL d’exécution d’Analysis Services, la tâche de traitement d’Analysis Services et la tâche de requête d’exploration de données sont également préinstallées sur les versions d’entreprise et standard du runtime d’intégration Azure-SSIS. |
| Transformations par regroupement probable et recherche approximative  | Les transformations par regroupement probable et recherche approximative sont préinstallées sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Ces composants prennent en charge SQL Server et Azure SQL Database pour le stockage des données de référence. |
| Transformations par extraction et recherche de termes | Les transformations par extraction et recherche de termes sont préinstallées sur la version Enterprise Edition du runtime d’intégration Azure-SSIS. Ces composants prennent en charge SQL Server et Azure SQL Database pour le stockage des données de référence. |

## <a name="instructions"></a>Instructions

1.  Téléchargez et installez [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) (version 5.4 ou ultérieure).

2.  Lors du provisionnement ou de la reconfiguration du runtime d’intégration Azure SSIS avec PowerShell, exécutez `Set-AzureRmDataFactoryV2IntegrationRuntime` avec **Enterprise** comme valeur pour le paramètre **Edition** avant de démarrer le runtime d’intégration Azure SSIS. Voici un exemple de script :

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Étapes suivantes

-   [Installation personnalisée du runtime d’intégration Azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Comment développer des composants personnalisés payants, ou sous licence, pour le runtime d’intégration Azure-SSIS](how-to-develop-azure-ssis-ir-licensed-components.md)