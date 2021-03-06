
## <a name="update-app"></a>Mettre à jour l'application pour appeler l'API personnalisée
1. Dans Visual Studio, ouvrez le fichier MainPage.xaml dans votre projet de démarrage rapide, recherchez l'élément **Button** intitulé `ButtonRefresh`, et remplacez-le par le code XAML suivant : 
   
        <StackPanel Orientation="Horizontal">
            <Button Margin="72,0,0,0" Name="ButtonRefresh" 
                    Click="ButtonRefresh_Click">Refresh</Button>
            <Button Margin="12,0,0,0" Name="ButtonCompleteAll" 
                    Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>
   
    Le nouveau bouton est ajouté à la page.
2. Ouvrez le fichier de code MainPage.xaml.cs et ajoutez le code de définition de classe suivant :
   
        public class MarkAllResult
        {
            public int Count { get; set; }
        }
   
    Cette classe permet de conserver la valeur de nombre de lignes renvoyée par l'API personnalisée.
3. Recherchez la méthode **RefreshTodoItems** dans la classe **MainPage** et vérifiez que la requête `query` est définie en utilisant la méthode **Where** suivante :
   
        .Where(todoItem => todoItem.Complete == false)
   
    Les éléments sont filtrés de manière à ce que les éléments terminés ne soient pas renvoyés par la requête.
4. Dans la classe **MainPage**, ajoutez la méthode suivante :
   
        private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
        {
            string message;
            try
            {
                // Asynchronously call the custom API using the POST method. 
                var result = await App.MobileService
                    .InvokeApiAsync<MarkAllResult>("completeAll", 
                    System.Net.Http.HttpMethod.Post, null);
                message =  result.Count + " item(s) marked as complete.";
                RefreshTodoItems();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;                
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
   
    Cette méthode gère l'événement **Click** pour le nouveau bouton. La méthode [InvokeApiAsync](http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx) est appelée sur le client pour envoyer une requête POST à la nouvelle API personnalisée. Le résultat renvoyé par l'API personnalisée apparaît dans la boîte de message, avec les erreurs éventuelles.

## <a name="test-app"></a>Tester l'application
1. Dans Visual Studio, appuyez sur la touche **F5** pour régénérer le projet et démarrer l'application.
2. Dans l'application, tapez du texte dans **Insert a TodoItem**, puis cliquez sur **Enregistrer**.
3. Répétez l'étape précédente jusqu'à ce que vous ayez ajouté plusieurs éléments todo dans la liste.
4. Cliquez sur le bouton **Complete All**.
   
      ![](./media/mobile-services-windows-store-dotnet-call-custom-api/mobile-custom-api-windows-store-completed.png)
   
    Un message s'affiche pour indiquer le nombre d'éléments marqués comme terminés, puis la requête filtrée est de nouveau exécutée pour supprimer tous les éléments de la liste.

<!---HONumber=Oct15_HO3-->