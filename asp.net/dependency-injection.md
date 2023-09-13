### Dependency Injection in an ASP.NET Core App

Dependency Injection is creating a well-defined relationship between two objects so that the object being created can use the object being injected in the way it was designed to be used. This allows for methods to be abstracted to a seperate class and those methods will be availiable in any object dependent on that class. Some of the Benefits of using Dependency Injection are: you can inject dependency of an interface and give any derivation of that interface to the constructor allowing polymorphism; if you replace the class you are using with a new sibling class you don’t need to change the dependent class to use the new class in the constructor; different objects can share dependency of a single object; it helps ensure that the code you write can only be used in the way you intended it to be used; and it makes things that are difficult to test much easier. Some of the disadvantages are that like inheritance and interfaces by making code in different files more connected it can become difficult to understand and debug much easier if you’re not careful, and it can lead to an increased number of unnecessary classes and interfaces. 
When using an interface in the dependency injection you can use modular code on one class instead of needing modular code for that class. In the following example the user class is dependent to the IPrompter interface. Each child of the IPropter can deliver the propts in a diferent language so depending on which prompter is sent to the user constructor the user will deliver propts in that language. This way we don't need to write a user class for each language and can add a new language just by adding a new IPrompter class.

using System;
using System.Collections.Generic;

// the interface to base the prompters on
public interface IPrompter
{
  public void Welcome();
  public void Menu();
  public void Add();
  public void View(List<string> items);
}

// the prompter in english
public class EnglishPrompter : IPrompter
{
  void IPrompter.Welcome(){
    Console.WriteLine("Welcome to my program");
  }
  void IPrompter.Menu(){
    Console.WriteLine("Enter a for add item or enter v to view youre items");
  }
  void IPrompter.Add(){
    Console.WriteLine("Enter the name of the Item Youre adding");
  }
  void IPrompter.View(List<string> items){
    Console.WriteLine("Your items are: " + string.Join(", ", items));
  }
}

// the prompter in spanish
public class SpanishPrompter : IPrompter
{
  void IPrompter.Welcome(){
    Console.WriteLine("Bienvenida a mi programa");
  }
  void IPrompter.Menu(){
    Console.WriteLine("Ingrese a para agregar artículo o ingrese v para ver sus artículos");
  }
  void IPrompter.Add(){
    Console.WriteLine("Ingresa el nombre del artículo que estás agregando");
  }
  void IPrompter.View(List<string> items){
    Console.WriteLine("Tus artículos son: " + string.Join(", ", items));
  }
}

// the prompter in french
public class FrenchPrompter : IPrompter
{
  void IPrompter.Welcome(){
    Console.WriteLine("Bienvenue dans mon programme");
  }
  void IPrompter.Menu(){
    Console.WriteLine("Entrez a pour ajouter un article ou entrez v pour afficher vos articles");
  }
  void IPrompter.Add(){
    Console.WriteLine("Entrez le nom de l'élément que vous ajoutez");
  }
  void IPrompter.View(List<string> items){
    Console.WriteLine("Vos articles sont: " + string.Join(", ", items));
  }
}

public class User{

  private IPrompter _prompter {get; set;}
  public List<string> Items {get; set;} = new List<string>();
  
  // here we inject dependency on an IPrompter child
  public User(IPrompter prompter){
    _prompter = prompter;
  }

  //uses given propter to post prompts in requested language
  public void Welcome(){
    _prompter.Welcome();
  }
  public void Menu(){
    _prompter.Menu();
  }
  public void Add(){
    _prompter.Add();
  }
  public void View(){
    _prompter.View(Items);
  }
}




class Program {
  public static void Main (string[] args) {
    Console.WriteLine ("Enter 'en' for english");
    Console.WriteLine ("ingresa 'es' para español");
    Console.WriteLine ("entrez 'fr' pour le français");
    string input = Console.ReadLine();

    // create a prompter of the desired language
    IPrompter prompter = null;
    if (input == "en"){
      prompter = new EnglishPrompter();
    }
    else if (input == "es"){
      prompter = new SpanishPrompter();
    }
    else if (input == "fr"){
      prompter = new FrenchPrompter();
    }
    if (prompter != null){

      // gives the user that prompter
      User user = new User(prompter);

      // the "program"
      user.Welcome();
      while (true){
        user.Menu();
        input = Console.ReadLine();
        if(input == "a"){
          user.Add();
          input = Console.ReadLine();
          user.Items.Add(input);
        }
        else if (input == "v"){
          user.View();
        }
      }      
    }
  }
}

some of the referenced I found to be useful were:
the microsoft documentation
https://learn.microsoft.com/en-us/dotnet/core/extensions/dependency-injection
c-sharp corner
https://www.c-sharpcorner.com/blogs/dependency-injection-di-c-sharp
Berin Loritsch's answer on stack exchange
https://softwareengineering.stackexchange.com/questions/371722/criticism-and-disadvantages-of-dependency-injection
