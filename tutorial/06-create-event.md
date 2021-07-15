<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="92041-101">Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92041-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="92041-102">Ouvrez **NewEventPage.xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="92041-102">Open **NewEventPage.xaml** and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml":::

1. <span data-ttu-id="92041-103">Ouvrez **NewEventPage.xaml.cs** et ajoutez les `using` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="92041-103">Open **NewEventPage.xaml.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using Microsoft.Graph;
    ```

1. <span data-ttu-id="92041-104">Ajoutez **l’interface INotifyPropertyChange** à la **classe NewEventPage.**</span><span class="sxs-lookup"><span data-stu-id="92041-104">Add the **INotifyPropertyChange** interface to the **NewEventPage** class.</span></span> <span data-ttu-id="92041-105">Remplacez la déclaration de classe existante par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="92041-105">Replace the existing class declaration with the following.</span></span>

    ```csharp
    [XamlCompilation(XamlCompilationOptions.Compile)]
    public partial class NewEventPage : ContentPage, INotifyPropertyChanged
    {
        public NewEventPage()
        {
            InitializeComponent();
            BindingContext = this;
        }
    }
    ```

1. <span data-ttu-id="92041-106">Ajoutez les propriétés suivantes à **la classe NewEventPage.**</span><span class="sxs-lookup"><span data-stu-id="92041-106">Add the following properties to the **NewEventPage** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. <span data-ttu-id="92041-107">Ajoutez le code suivant pour créer l’événement.</span><span class="sxs-lookup"><span data-stu-id="92041-107">Add the following code to create the event.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. <span data-ttu-id="92041-108">Enregistrez vos modifications et exécutez l’application.</span><span class="sxs-lookup"><span data-stu-id="92041-108">Save your changes and run the app.</span></span> <span data-ttu-id="92041-109">Connectez-vous, sélectionnez **l’élément** de menu  Nouvel événement, remplissez le formulaire et sélectionnez Créer pour ajouter un événement au calendrier de l’utilisateur.</span><span class="sxs-lookup"><span data-stu-id="92041-109">Sign in, select the **New event** menu item, fill in the form, and select **Create** to add an event to the user's calendar.</span></span>

    ![Capture d’écran de la nouvelle page d’événements](images/new-event-page.png)
