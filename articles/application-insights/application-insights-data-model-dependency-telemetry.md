---
title: 'Modèle de données de télémétrie d’Azure Application Insights : télémétrie des dépendances | Microsoft Docs'
description: Modèle de données Application Insights pour la télémétrie des dépendances
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: mbullwin; sergkanz
ms.openlocfilehash: 019b24839c20e7f8f46eeccf4a7b9622d18b0ad6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2018
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Télémétrie des dépendances : modèle de données Application Insights

La télémétrie des dépendances (dans [Application Insights](app-insights-overview.md)) représente une interaction du composant surveillé avec un composant distant tel que SQL ou un point de terminaison HTTP.

## <a name="name"></a>NOM

Nom de la commande lancée par cet appel de dépendance. Valeur de faible cardinalité. Exemples : nom de procédure stockée et modèle de chemin d’accès d’URL.

## <a name="id"></a>ID

Identificateur d’une instance d’appel de dépendance. Utilisé pour la corrélation avec l’élément de télémétrie de demande correspondant à cet appel de dépendance. Pour plus d’informations, consultez la page relative à la [corrélation](application-insights-correlation.md).

## <a name="data"></a>Données

Commande lancée par cet appel de dépendance. Exemples : instruction SQL et URL HTTP avec tous les paramètres de requête.

## <a name="type"></a>type

Nom du type de dépendance. Valeur de faible cardinalité pour le regroupement logique de dépendances et l’interprétation d’autres champs comme commandName et resultCode. Exemples : SQL, table Azure et HTTP.

## <a name="target"></a>Cible

Site cible d’un appel de dépendance. Exemples : nom de serveur, adresse d’hôte. Pour plus d’informations, consultez la page relative à la [corrélation](application-insights-correlation.md).

## <a name="duration"></a>Duration

Durée de la demande au format : `DD.HH:MM:SS.MMMMMM`. Doit être inférieure à `1000` jours.

## <a name="result-code"></a>Code de résultat

Code de résultat d’un appel de dépendance. Exemples : code d’erreur SQL et code d’état HTTP.

## <a name="success"></a>Succès

Indication de la réussite ou non d’un appel.

## <a name="custom-properties"></a>Propriétés personnalisées

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Mesures personnalisées

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>Étapes suivantes

- Configurez le suivi des dépendances pour [.NET](app-insights-asp-net-dependencies.md).
- Configurez le suivi des dépendances pour [Java](app-insights-java-agent.md).
- [Écrire des données de télémétrie des dépendances personnalisées](app-insights-api-custom-events-metrics.md#trackdependency)
- Pour connaître les types et les modèles de données Application Insights, consultez [Modèle de données](application-insights-data-model.md).
- Découvrez quelles [plateformes](app-insights-platforms.md) sont prises en charge par Application Insights.
