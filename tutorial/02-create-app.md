<!-- markdownlint-disable MD002 MD041 -->

1. <span data-ttu-id="539ed-101">Ouvrez Visual Studio et sélectionnez **Créer un projet**.</span><span class="sxs-lookup"><span data-stu-id="539ed-101">Open Visual Studio, and select **Create a new project**.</span></span>

1. <span data-ttu-id="539ed-102">Dans la boîte de dialogue Créer un **projet,** choisissez **Application mobile (Xamarin.Forms),** puis sélectionnez **Suivant**.</span><span class="sxs-lookup"><span data-stu-id="539ed-102">In the **Create a new project** dialog, choose **Mobile App (Xamarin.Forms)**, then select **Next**.</span></span>

    ![Visual Studio 2019 : boîte de dialogue Créer un projet](images/new-project-dialog.png)

1. <span data-ttu-id="539ed-104">Dans la **boîte de dialogue** Configurer un nouveau projet, entrez le nom Project et le nom de la `GraphTutorial` **solution,** puis  sélectionnez **Créer.**</span><span class="sxs-lookup"><span data-stu-id="539ed-104">In the **Configure a new project** dialog, enter `GraphTutorial` for the **Project name** and **Solution name**, then select **Create**.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="539ed-105">Veillez à entrer exactement le même nom pour le Visual Studio Project spécifié dans ces instructions d’atelier.</span><span class="sxs-lookup"><span data-stu-id="539ed-105">Ensure that you enter the exact same name for the Visual Studio Project that is specified in these lab instructions.</span></span> <span data-ttu-id="539ed-106">Le nom du projet Visual Studio fait alors partie de l’espace de noms dans le code.</span><span class="sxs-lookup"><span data-stu-id="539ed-106">The Visual Studio Project name becomes part of the namespace in the code.</span></span> <span data-ttu-id="539ed-107">Le code figurant dans ces instructions dépend de l’espace de noms qui correspond au nom de projet Visual Studio spécifié dans celles-ci.</span><span class="sxs-lookup"><span data-stu-id="539ed-107">The code inside these instructions depends on the namespace matching the Visual Studio Project name specified in these instructions.</span></span> <span data-ttu-id="539ed-108">Si vous utilisez un autre nom de projet, celui-ci n’est pas compilé sauf si vous ajustez tous les espaces de noms pour qu’ils correspondent au nom de projet Visual Studio saisi à la création du projet.</span><span class="sxs-lookup"><span data-stu-id="539ed-108">If you use a different project name the code will not compile unless you adjust all the namespaces to match the Visual Studio Project name you enter when you create the project.</span></span>

    ![Visual Studio 2019 configurer la boîte de dialogue nouveau projet](images/configure-new-project-dialog.png)

1. <span data-ttu-id="539ed-110">Dans la boîte de dialogue  Nouvelle **application plateforme,** sélectionnez le modèle Vide, puis sélectionnez les plateformes que vous souhaitez créer sous **Plateformes.**</span><span class="sxs-lookup"><span data-stu-id="539ed-110">In the **New Cross Platform App** dialog, select the **Blank** template, and select the platforms you want to build under **Platforms**.</span></span> <span data-ttu-id="539ed-111">Sélectionnez **OK** pour créer la solution.</span><span class="sxs-lookup"><span data-stu-id="539ed-111">Select **OK** to create the solution.</span></span>

    ![Visual Studio 2019 nouvelle boîte de dialogue d’application plateforme](images/new-cross-platform-app-dialog.png)

## <a name="install-packages"></a><span data-ttu-id="539ed-113">Installer des packages</span><span class="sxs-lookup"><span data-stu-id="539ed-113">Install packages</span></span>

<span data-ttu-id="539ed-114">Avant de passer à autre chose, installez des packages NuGet supplémentaires que vous utiliserez ultérieurement.</span><span class="sxs-lookup"><span data-stu-id="539ed-114">Before moving on, install some additional NuGet packages that you will use later.</span></span>

- <span data-ttu-id="539ed-115">[Microsoft.Identity.Client pour gérer](https://www.nuget.org/packages/Microsoft.Identity.Client/) l’authentification Azure AD et la gestion des jetons.</span><span class="sxs-lookup"><span data-stu-id="539ed-115">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) to handle Azure AD authentication and token management.</span></span>
- <span data-ttu-id="539ed-116">[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph/) pour appeler le microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="539ed-116">[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph/) for making calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="539ed-117">[TimeZoneConverter pour](https://www.nuget.org/packages/TimeZoneConverter/) la gestion des fuseaux horaires sur plusieurs plateformes.</span><span class="sxs-lookup"><span data-stu-id="539ed-117">[TimeZoneConverter](https://www.nuget.org/packages/TimeZoneConverter/) for handling time zones cross-platform.</span></span>

1. <span data-ttu-id="539ed-118">Sélectionnez **Outils > Gestionnaire de package NuGet > Console Gestionnaire de package**.</span><span class="sxs-lookup"><span data-stu-id="539ed-118">Select **Tools > NuGet Package Manager > Package Manager Console**.</span></span>

1. <span data-ttu-id="539ed-119">Dans le menu Console du Gestionnaire de package, saisissez les commandes suivantes.</span><span class="sxs-lookup"><span data-stu-id="539ed-119">In the Package Manager Console, enter the following commands.</span></span>

    ```Powershell
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.Android
    Install-Package Microsoft.Identity.Client -Version 4.34.0 -Project GraphTutorial.iOS
    Install-Package Microsoft.Graph -Version 4.0.0 -Project GraphTutorial
    Install-Package TimeZoneConverter -Project GraphTutorial
    ```

## <a name="design-the-app"></a><span data-ttu-id="539ed-120">Concevoir l’application</span><span class="sxs-lookup"><span data-stu-id="539ed-120">Design the app</span></span>

<span data-ttu-id="539ed-121">Commencez par mettre à jour la classe pour ajouter des variables pour suivre l’état `App` d’authentification et l’utilisateur qui est inscrit.</span><span class="sxs-lookup"><span data-stu-id="539ed-121">Start by updating the `App` class to add variables to track the authentication state and the signed-in user.</span></span>

1. <span data-ttu-id="539ed-122">Dans **l’Explorateur** de solutions, développez **le projet GraphTutorial,** puis développez le fichier **App.xaml.**</span><span class="sxs-lookup"><span data-stu-id="539ed-122">In **Solution Explorer**, expand the **GraphTutorial** project, then expand the **App.xaml** file.</span></span> <span data-ttu-id="539ed-123">Ouvrez **le fichier App.xaml.cs** et ajoutez les `using` instructions suivantes en haut du fichier.</span><span class="sxs-lookup"><span data-stu-id="539ed-123">Open the **App.xaml.cs** file and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using System.ComponentModel;
    using System.IO;
    using System.Reflection;
    using System.Threading.Tasks;
    ```

1. <span data-ttu-id="539ed-124">Ajoutez `INotifyPropertyChanged` l’interface à la déclaration de classe.</span><span class="sxs-lookup"><span data-stu-id="539ed-124">Add the `INotifyPropertyChanged` interface to the class declaration.</span></span>

    ```csharp
    public partial class App : Application, INotifyPropertyChanged
    ```

1. <span data-ttu-id="539ed-125">Ajoutez les propriétés suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="539ed-125">Add the following properties to the `App` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/App.xaml.cs" id="GlobalPropertiesSnippet":::

1. <span data-ttu-id="539ed-126">Ajoutez les fonctions suivantes à la `App` classe.</span><span class="sxs-lookup"><span data-stu-id="539ed-126">Add the following functions to the `App` class.</span></span> <span data-ttu-id="539ed-127">Les `SignIn` fonctions et les fonctions ne sont que des `SignOut` `GetUserInfo` espaces réservé pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="539ed-127">The `SignIn`, `SignOut`, and `GetUserInfo` functions are just placeholders for now.</span></span>

    ```csharp
    public async Task SignIn()
    {
        await GetUserInfo();

        IsSignedIn = true;
    }

    public async Task SignOut()
    {
        UserPhoto = null;
        UserName = string.Empty;
        UserEmail = string.Empty;
        IsSignedIn = false;
    }

    private async Task GetUserInfo()
    {
        UserPhoto = ImageSource.FromStream(() => GetUserPhoto());
        UserName = "Adele Vance";
        UserEmail = "adelev@contoso.com";
    }

    private Stream GetUserPhoto()
    {
        // Return the default photo
        return Assembly.GetExecutingAssembly().GetManifestResourceStream("GraphTutorial.no-profile-pic.png");
    }
    ```

1. <span data-ttu-id="539ed-128">La `GetUserPhoto` fonction renvoie une photo par défaut pour l’instant.</span><span class="sxs-lookup"><span data-stu-id="539ed-128">The `GetUserPhoto` function returns a default photo for now.</span></span> <span data-ttu-id="539ed-129">Vous pouvez fournir votre propre fichier ou télécharger celui utilisé dans l’exemple à partir [de GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span><span class="sxs-lookup"><span data-stu-id="539ed-129">You can either supply your own file, or you can download the one used in the sample from [GitHub](https://github.com/microsoftgraph/msgraph-training-xamarin/blob/master/tutorial/images/no-profile-pic.png).</span></span> <span data-ttu-id="539ed-130">Si vous utilisez votre propre fichier, renommez-le **no-profile-pic.png**.</span><span class="sxs-lookup"><span data-stu-id="539ed-130">If you use your own file, rename it to **no-profile-pic.png**.</span></span>

1. <span data-ttu-id="539ed-131">Copiez le fichier dans le répertoire **./GraphTutorial/GraphTutorial.**</span><span class="sxs-lookup"><span data-stu-id="539ed-131">Copy the file to the **./GraphTutorial/GraphTutorial** directory.</span></span>

1. <span data-ttu-id="539ed-132">Cliquez avec le bouton droit sur le fichier dans **l’Explorateur de** solutions et sélectionnez **Propriétés.**</span><span class="sxs-lookup"><span data-stu-id="539ed-132">Right-click the file in **Solution Explorer** and select **Properties**.</span></span> <span data-ttu-id="539ed-133">Dans la **fenêtre Propriétés,** modifiez la valeur de **l’action** de build en **ressource incorporée.**</span><span class="sxs-lookup"><span data-stu-id="539ed-133">In the **Properties** window, change the value of **Build Action** to **Embedded resource**.</span></span>

    ![Capture d’écran de la fenêtre Propriétés du fichier PNG](./images/png-file-properties.png)

### <a name="app-navigation"></a><span data-ttu-id="539ed-135">Navigation dans l’application</span><span class="sxs-lookup"><span data-stu-id="539ed-135">App navigation</span></span>

<span data-ttu-id="539ed-136">Dans cette section, vous allez modifier la page principale de l’application en [page volante.](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage)</span><span class="sxs-lookup"><span data-stu-id="539ed-136">In this section, you'll change the application's main page to a [FlyoutPage](/xamarin/xamarin-forms/app-fundamentals/navigation/flyoutpage).</span></span> <span data-ttu-id="539ed-137">Cela fournit un menu de navigation pour basculer d’une vue à l’autre dans l’application.</span><span class="sxs-lookup"><span data-stu-id="539ed-137">This will provide a navigation menu to switch between view in the app.</span></span>

1. <span data-ttu-id="539ed-138">Ouvrez **le fichier MainPage.xaml** dans le projet **GraphTutorial** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="539ed-138">Open the **MainPage.xaml** file in the **GraphTutorial** project and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml":::

#### <a name="implement-the-menu"></a><span data-ttu-id="539ed-139">Implémenter le menu</span><span class="sxs-lookup"><span data-stu-id="539ed-139">Implement the menu</span></span>

1. <span data-ttu-id="539ed-140">Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouveau dossier.**</span><span class="sxs-lookup"><span data-stu-id="539ed-140">Right-click the **GraphTutorial** project and select **Add**, then **New Folder**.</span></span> <span data-ttu-id="539ed-141">Nommer le dossier `Models`.</span><span class="sxs-lookup"><span data-stu-id="539ed-141">Name the folder `Models`.</span></span>

1. <span data-ttu-id="539ed-142">Cliquez avec le bouton droit sur le dossier **Modèles** et **sélectionnez Ajouter,** puis **Classe...**. Nommez la classe `NavMenuItem` et sélectionnez **Ajouter.**</span><span class="sxs-lookup"><span data-stu-id="539ed-142">Right-click the **Models** folder and select **Add**, then **Class...**. Name the class `NavMenuItem` and select **Add**.</span></span>

1. <span data-ttu-id="539ed-143">Ouvrez **le fichier NavMenuItem.cs** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="539ed-143">Open the **NavMenuItem.cs** file and replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/Models/NavMenuItem.cs" id="NavMenuItemSnippet":::

1. <span data-ttu-id="539ed-144">Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `MenuPage` .</span><span class="sxs-lookup"><span data-stu-id="539ed-144">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `MenuPage`.</span></span> <span data-ttu-id="539ed-145">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="539ed-145">Select **Add**.</span></span>

1. <span data-ttu-id="539ed-146">Ouvrez **le fichier MenuPage.xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="539ed-146">Open the **MenuPage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml":::

1. <span data-ttu-id="539ed-147">Développez **MenuPage.xaml dans** l’Explorateur **de** solutions et ouvrez le fichier **MenuPage.xaml.cs.**</span><span class="sxs-lookup"><span data-stu-id="539ed-147">Expand **MenuPage.xaml** in **Solution Explorer** and open the **MenuPage.xaml.cs** file.</span></span> <span data-ttu-id="539ed-148">Remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="539ed-148">Replace its contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MenuPage.xaml.cs" id="MenuPageSnippet":::

    > [!NOTE]
    > <span data-ttu-id="539ed-149">Visual Studio signalera des erreurs dans **MenuPage.xaml.cs.**</span><span class="sxs-lookup"><span data-stu-id="539ed-149">Visual Studio will report errors in **MenuPage.xaml.cs**.</span></span> <span data-ttu-id="539ed-150">Ces erreurs seront résolues dans une étape ultérieure.</span><span class="sxs-lookup"><span data-stu-id="539ed-150">These errors will be resolved in a later step.</span></span>

#### <a name="implement-the-welcome-page"></a><span data-ttu-id="539ed-151">Implémenter la page d’accueil</span><span class="sxs-lookup"><span data-stu-id="539ed-151">Implement the welcome page</span></span>

1. <span data-ttu-id="539ed-152">Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `WelcomePage` .</span><span class="sxs-lookup"><span data-stu-id="539ed-152">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `WelcomePage`.</span></span> <span data-ttu-id="539ed-153">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="539ed-153">Select **Add**.</span></span> <span data-ttu-id="539ed-154">Ouvrez **le fichier WelcomePage.xaml** et remplacez son contenu par ce qui suit.</span><span class="sxs-lookup"><span data-stu-id="539ed-154">Open the **WelcomePage.xaml** file and replace its contents with the following.</span></span>

    :::code language="xaml" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml":::

1. <span data-ttu-id="539ed-155">Développez **WelcomePage.xaml dans** l’Explorateur **de** solutions et ouvrez le fichier **WelcomePage.xaml.cs.**</span><span class="sxs-lookup"><span data-stu-id="539ed-155">Expand **WelcomePage.xaml** in **Solution Explorer** and open the **WelcomePage.xaml.cs** file.</span></span> <span data-ttu-id="539ed-156">Ajoutez la fonction suivante à la classe `WelcomePage`.</span><span class="sxs-lookup"><span data-stu-id="539ed-156">Add the following function to the `WelcomePage` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/WelcomePage.xaml.cs" id="OnSignInSnippet":::

#### <a name="add-calendar-and-new-event-pages"></a><span data-ttu-id="539ed-157">Ajouter un calendrier et de nouvelles pages d’événements</span><span class="sxs-lookup"><span data-stu-id="539ed-157">Add calendar and new event pages</span></span>

<span data-ttu-id="539ed-158">Maintenant, ajoutez une page de calendrier et une nouvelle page d’événements.</span><span class="sxs-lookup"><span data-stu-id="539ed-158">Now add a calendar page and a new event page.</span></span> <span data-ttu-id="539ed-159">Ces espaces seront simplement des espaces réservé pour le moment.</span><span class="sxs-lookup"><span data-stu-id="539ed-159">These will just be placeholders for now.</span></span>

1. <span data-ttu-id="539ed-160">Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `CalendarPage` .</span><span class="sxs-lookup"><span data-stu-id="539ed-160">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `CalendarPage`.</span></span> <span data-ttu-id="539ed-161">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="539ed-161">Select **Add**.</span></span>

1. <span data-ttu-id="539ed-162">Cliquez avec le bouton droit **sur le projet GraphTutorial** et **sélectionnez Ajouter,** puis **Nouvel élément...**. Choose **Content Page** and name the page `NewEventPage` .</span><span class="sxs-lookup"><span data-stu-id="539ed-162">Right-click the **GraphTutorial** project and select **Add**, then **New Item...**. Choose **Content Page** and name the page `NewEventPage`.</span></span> <span data-ttu-id="539ed-163">Cliquez sur **Ajouter**.</span><span class="sxs-lookup"><span data-stu-id="539ed-163">Select **Add**.</span></span>

#### <a name="update-mainpage-code-behind"></a><span data-ttu-id="539ed-164">Mettre à jour le code-behind MainPage</span><span class="sxs-lookup"><span data-stu-id="539ed-164">Update MainPage code-behind</span></span>

<span data-ttu-id="539ed-165">Maintenant que toutes les pages sont en place, mettez à jour le code-behind **pour MainPage.xaml**.</span><span class="sxs-lookup"><span data-stu-id="539ed-165">Now that all of the pages are in place, update the code-behind for **MainPage.xaml**.</span></span>

1. <span data-ttu-id="539ed-166">Développez **MainPage.xaml** dans l’Explorateur de solutions, ouvrez le fichier **MainPage.xaml.cs** et remplacez tout son contenu par ce qui suit. </span><span class="sxs-lookup"><span data-stu-id="539ed-166">Expand **MainPage.xaml** in **Solution Explorer** and open the **MainPage.xaml.cs** file and replace its entire contents with the following.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/GraphTutorial/MainPage.xaml.cs" id="MainPageSnippet":::

1. <span data-ttu-id="539ed-167">Enregistrez toutes vos modifications.</span><span class="sxs-lookup"><span data-stu-id="539ed-167">Save all of your changes.</span></span> <span data-ttu-id="539ed-168">Cliquez avec le bouton droit sur le projet que vous **souhaitez** exécuter (Android, iOS ou UWP) et sélectionnez Définir comme Project .</span><span class="sxs-lookup"><span data-stu-id="539ed-168">Right-click the project that you want to run (Android, iOS, or UWP) and select **Set as StartUp Project**.</span></span> <span data-ttu-id="539ed-169">Appuyez **sur F5** ou **sélectionnez Déboguer > démarrer le débogage** dans Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="539ed-169">Press **F5** or select **Debug > Start Debugging** in Visual Studio.</span></span>

    ![Captures d’écran des versions Android, iOS et UWP de l’application](./images/welcome-page.png)
