---
ms.openlocfilehash: f70714a0bc2588fc67d63096b4ab746380bb8e2c
ms.sourcegitcommit: 9d0d10a9e8e5a1d80382d89bc412df287bee03f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "48822329"
---
<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements dans le calendrier de l’utilisateur.

## <a name="create-model"></a>Créer un modèle

1. Créez un fichier nommé **NewEvent.cs** dans le répertoire **./Models** et ajoutez le code suivant.

    :::code language="csharp" source="../demo/GraphTutorial/Models/NewEvent.cs" id="NewEventSnippet":::

## <a name="create-view"></a>Créer un affichage

1. Créez un fichier nommé **New. cshtml** dans le répertoire HE **./views/Calendar** et ajoutez le code suivant.

    :::code language="cshtml" source="../demo/GraphTutorial/Views/Calendar/New.cshtml" id="NewFormSnippet":::

## <a name="add-controller-actions"></a>Ajouter des actions de contrôleur

1. Ouvrez **./Controllers/CalendarController.cs** et ajoutez l’action suivante à la `CalendarController` classe pour afficher le nouveau formulaire d’événement.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewGetSnippet":::

1. Ajoutez l’action suivante à la `CalendarController` classe pour recevoir le nouvel événement à partir du formulaire lorsque l’utilisateur clique sur **Enregistrer** et utilisez Microsoft Graph pour ajouter l’événement au calendrier de l’utilisateur.

    :::code language="csharp" source="../demo/GraphTutorial/Controllers/CalendarController.cs" id="CalendarNewPostSnippet":::

1. Démarrez l’application, connectez-vous, puis cliquez sur le lien **Calendrier**. Cliquez sur le bouton **nouvel événement** , remplissez le formulaire, puis cliquez sur **Enregistrer**.

    ![Capture d’écran du nouveau formulaire d’événement](./images/create-event-01.png)
