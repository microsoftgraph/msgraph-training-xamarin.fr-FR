<!-- markdownlint-disable MD002 MD041 -->

Dans cette section, vous allez ajouter la possibilité de créer des événements sur le calendrier de l’utilisateur.

1. Ajoutez une nouvelle page pour le nouvel affichage d’événement. Cliquez avec le bouton droit **sur le projet GraphTutorial** dans l’Explorateur de solutions et sélectionnez **Ajouter > nouvel élément...**. Choose **Blank Page**, enter in the `NewEventPage.xaml` **Name** field, and select **Add**.

1. Ouvrez **NewEventPage.xaml** et remplacez son contenu par ce qui suit.

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml" id="NewEventPageXamlSnippet":::

1. Ouvrez **NewEventPage.xaml.cs** et ajoutez les `using` instructions suivantes en haut du fichier.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="UsingStatementsSnippet":::

1. Ajoutez **l’interface INotifyPropertyChange** à la **classe NewEventPage.** Remplacez la déclaration de classe existante par ce qui suit.

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

1. Ajoutez les propriétés suivantes à **la classe NewEventPage.**

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="PropertiesSnippet":::

1. Ajoutez le code suivant pour créer l’événement.

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/NewEventPage.xaml.cs" id="CreateEventSnippet":::

1. Enregistrez vos modifications et exécutez l’application. Connectez-vous, sélectionnez **l’élément** de menu  Nouvel événement, remplissez le formulaire et sélectionnez Créer pour ajouter un événement au calendrier de l’utilisateur.

    ![Capture d’écran de la nouvelle page d’événements](images/new-event-page.png)
