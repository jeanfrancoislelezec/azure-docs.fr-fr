---
title: 'Démarrage rapide : créer un cluster Spark dans HDInsight à l’aide d’Azure PowerShell'
description: Ce guide de démarrage rapide montre comment utiliser Azure PowerShell pour créer un cluster Apache Spark dans Azure HDInsight et exécuter une requête Spark SQL simple.
services: azure-hdinsight
author: mumian
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.devlang: na
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: jgao
ms.custom: mvc
ms.openlocfilehash: 321f84e0d56a2bda57e1fbfa2cc562b65c6e1d30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2018
---
# <a name="quickstart-create-a-spark-cluster-in-hdinsight-using-powershell"></a>Démarrage rapide : créer un cluster Spark dans HDInsight à l’aide d’Azure PowerShell
Découvrez comment créer un cluster Apache Spark dans Azure HDInsight et comment exécuter des requêtes Spark SQL sur des tables Apache Hive. Apache Spark permet une analytique des données et un calcul de cluster rapides à l’aide du traitement en mémoire. Pour en savoir plus sur le service Spark sur HDInsight, consultez [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md).

Dans ce guide de démarrage rapide, vous utilisez Azure PowerShell pour créer un cluster HDInsight Spark. Le cluster utilise des objets Azure Storage Blob en tant que stockage du cluster.

> [!IMPORTANT]
> La facturation des clusters HDInsight est calculée au prorata des minutes écoulées, que vous les utilisiez ou non. Veillez à supprimer votre cluster une fois que vous avez fini de l’utiliser. Pour plus d’informations, consultez la section [Nettoyer les ressources](#clean-up-resources) de cet article.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-hdinsight-spark-cluster"></a>Créer un cluster HDInsight Spark

La création d’un cluster HDInsight inclut la création des objets et ressources Azure qui suivent :

- Un groupe de ressources Azure. Un groupe de ressources Azure est un conteneur pour les ressources Azure. 
- Un compte de stockage Azure ou un Azure Data Lake Store.  Chaque cluster HDInsight requiert un stockage de données dépendant. Dans le cadre de ce démarrage rapide, vous créez un compte de stockage.
- Un cluster HDInsight avec différents types de cluster.  Dans ce démarrage rapide, vous allez créer un cluster Spark 2.2.

Vous utilisez un script PowerShell pour créer les ressources.  Lorsque vous exécutez le script, vous êtes invité à entrer les valeurs suivantes :

|Paramètre|Valeur|
|------|------|
|Nom du groupe de ressources Azure | Choisissez un nom unique pour le groupe de ressources.|
|Lieu| Spécifiez la région Azure, par exemple « Centre des États-Unis ». |
|Nom du compte de stockage par défaut | Attribuez un nom unique au compte de stockage. |
|Nom du cluster | Choisissez un nom unique pour le cluster HDInsight Spark.|
|Informations d'identification de connexion au cluster | Vous utilisez ce compte pour vous connecter au tableau de bord du cluster lors du démarrage rapide.|
|Informations d’identification de l’utilisateur SSH | Les clients SSH peuvent être utilisés pour créer une session de ligne de commande à distance avec les clusters HDInsight.|



1. Cliquez sur **Essayer** dans le coin supérieur droit du bloc de code suivant afin d’ouvrir [Azure Cloud Shell](../../cloud-shell/overview.md), puis suivez les instructions pour vous connecter à Azure.
2. Copiez et collez le script PowerShell suivant dans l’interpréteur de commandes du cloud. 

    ```azurepowershell-interactive
    ### Create a Spark 2.2 cluster in Azure HDInsight
        
    # Create the resource group
    $resourceGroupName = Read-Host -Prompt "Enter the resource group name"
    $location = Read-Host -Prompt "Enter the Azure region to create resources in, such as 'Central US'"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    $defaultStorageAccountName = Read-Host -Prompt "Enter the default storage account name"
    
    # Create an Azure storae account and container
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_LRS `
        -Location $location
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    
    # Create a Spark 2.2 cluster
    $clusterName = Read-Host -Prompt "Enter the name of the HDInsight cluster"
    # Cluster login is used to secure HTTPS services hosted on the cluster
    $httpCredential = Get-Credential -Message "Enter Cluster login credentials" -UserName "admin"
    # SSH user is used to remotely connect to the cluster using SSH clients
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"
    
    # Default cluster size (# of worker nodes), version, type, and OS
    $clusterSizeInNodes = "1"
    $clusterVersion = "3.6"
    $clusterType = "Spark"
    $clusterOS = "Linux"
    
    # Set the storage container name to the cluster name
    $defaultBlobContainerName = $clusterName
    
    # Create a blob container. This holds the default data store for the cluster.
    New-AzureStorageContainer `
        -Name $clusterName -Context $defaultStorageContext 
    
    $sparkConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
    $sparkConfig.Add("spark", "2.2")
    
    # Create the HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType $clusterType `
        -OSType $clusterOS `
        -Version $clusterVersion `
        -ComponentVersion $sparkConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials 
    
    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
    ```
La création du cluster prend environ 20 minutes. Le cluster doit être créé pour que vous puissiez passer à la prochaine session.

Si vous rencontrez un problème avec la création de clusters HDInsight, c’est que vous n’avez peut-être pas les autorisations requises pour le faire. Pour plus d’informations, consultez [Exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="create-a-jupyter-notebook"></a>Créer un bloc-notes Jupyter

Jupyter Notebook est un environnement de notebook interactif qui prend en charge plusieurs langages de programmation. Le notebook vous permet d’interagir avec vos données, de combiner du code avec le texte markdown et d’effectuer des visualisations simples. 

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Clusters HDInsight**, puis le cluster que vous avez créé.

    ![ouvrir le cluster HDInsight dans le portail Azure](./media/apache-spark-jupyter-spark-sql/azure-portal-open-hdinsight-cluster.png)

3. À partir du portail, sélectionnez **Tableaux de bord de cluster**, puis **Jupyter Notebook**. Si vous y êtes invité, entrez les informations d’identification pour le cluster.

   ![Ouvrir le bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Ouvrir un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

4. Sélectionnez **Nouveau** > **PySpark** pour créer un notebook. 

   ![Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb).


## <a name="run-spark-sql-statements"></a>Exécuter des instructions Spark SQL

SQL (Structured Query Language) est le langage le plus courant et le plus largement utilisé pour interroger et définir des données. Spark SQL fonctionne en tant qu’extension d’Apache Spark pour le traitement des données structurées, à l’aide de la syntaxe SQL classique.

1. Vérifiez que le noyau est prêt. Le noyau est prêt lorsque vous voyez un cercle vide à côté du nom du noyau dans le bloc-notes. Un cercle plein indique que le noyau est occupé.

    ![Requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Requête Hive dans HDInsight Spark")

    Lorsque vous démarrez le bloc-notes pour la première fois, le noyau effectue certaines tâches en arrière-plan. Attendez que le noyau soit prêt. 
2. Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. La commande répertorie les tables Hive sur le cluster :

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Lorsque vous utilisez un bloc-notes Jupyter avec votre cluster HDInsight Spark, vous obtenez une présélection `sqlContext` que vous pouvez utiliser pour exécuter des requêtes Hive à l’aide de Spark SQL. `%%sql` demande au bloc-notes Jupyter d’utiliser la présélection `sqlContext` pour exécuter la requête Hive. La requête extrait les 10 premières lignes d’une table Hive (**hivesampletable**) qui est disponible par défaut sur tous les clusters HDInsight. Il faut environ 30 secondes pour obtenir les résultats. Le résultat se présente ainsi : 

    ![Requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Requête Hive dans HDInsight Spark")

    À chaque exécution d’une requête dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit.
    
2. Exécutez une autre requête pour afficher les données dans `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    L’écran doit s’actualiser pour afficher la sortie de requête.

    ![Sortie de requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Sortie de requête Hive dans HDInsight Spark")

2. Dans le menu **Fichier** du notebook, sélectionnez **Fermer et arrêter**. L’arrêt du bloc-notes libère les ressources de cluster.

## <a name="clean-up-resources"></a>Supprimer des ressources
HDInsight enregistre vos données dans Stockage Azure ou Azure Data Lake Store, ce qui vous permet de supprimer un cluster de manière sécurisée s’il n’est pas en cours d’utilisation. Vous devez également payer pour un cluster HDInsight, même lorsque vous ne l’utilisez pas. Étant donné que les frais pour le cluster sont bien plus élevés que les frais de stockage, économique, mieux vaut supprimer les clusters lorsqu’ils ne sont pas utilisés. Si vous prévoyez de travailler immédiatement sur le tutoriel répertorié sous [Étapes suivantes](#next-steps), vous souhaiterez peut-être conserver le cluster.

Revenez au portail Azure, puis sélectionnez **Supprimer**.

![Supprimer un cluster HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-azure-portal-delete-cluster.png "Supprimer le cluster HDInsight")

Vous pouvez également sélectionner le nom du groupe de ressources pour ouvrir la page du groupe de ressources, puis sélectionner **Supprimer le groupe de ressources**. En supprimant le groupe de ressources, vous supprimez le cluster HDInsight Spark et le compte de stockage par défaut.

## <a name="next-steps"></a>Étapes suivantes 

Dans ce démarrage rapide, vous avez appris à créer un cluster HDInsight Spark et à exécuter une requête Spark SQL de base. Passez au tutoriel suivant pour apprendre à utiliser un cluster HDInsight Spark pour exécuter des requêtes interactives sur des données test.

> [!div class="nextstepaction"]
>[Exécuter des requêtes interactives sur Spark](./apache-spark-load-data-run-query.md)
