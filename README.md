
# JAXBTutorial
## What is JAXB?
JAXB stands for Java Architecture XML Binding. It is a tool that is used by Java developers to map Java to XML representations. Today, JAXB  is also  known as Jakarta XML binding.<br/><br/>

## The two main features/operations of JAXB are: 
- **````Marshalling```` -> converting a Java Object into XML** 
- **```` Unmarshalling```` -> converting XML to a Java Object** 
<br/><br/>  
## Removal of JAXB from the Java JDK:
JAXB became a part of the JDK in Java 6. In Java 11 (2018), JAXB was removed from the JDK. 
- JAXB is still a part of the JDK in Java 6,7, and 8.
- JAXB is still a part of Java 9 and 10 but it has been disabled or not included in the module path. 
- In Java 11 it is completely removed.

Versions exist:  JAXB 1.0 &  JAXB 2.0, Jakarta 3.0<br/><br/>

## Since it has been removed:
JAXB (Jakarta XML Binding) can still be utilized. To do so, you will need to use the following dependency. Using older versions of the dependencies require different import statements. 

```Java

<!-- JAXB API only -->
  <dependency>
      <groupId>jakarta.xml.bind</groupId>
      <artifactId>jakarta.xml.bind-api</artifactId>
      <version>3.0.0</version>
  </dependency>

  <!-- JAXB RI, Jakarta XML Binding -->
  <dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-impl</artifactId>
      <version>3.0.0</version>
      <scope>runtime</scope>
  </dependency>

``` 
<br/>

## Simple XML File (simple)
#### Marshalling (Java Object to XML)
  - POJO code
  - Main code

  ```Java
import jakarta.xml.bind.JAXBContext;
import jakarta.xml.bind.JAXBException;
import jakarta.xml.bind.Marshaller;
import jakarta.xml.bind.Unmarshaller;
import java.io.File;

/**
 * This app demonstrates how to use JAXB to turn a simple xml file into an object (unmarshall)
 * and then a java object into an xml file (marshall)
 */
 
  public class SimpleXMLDemo
{
    public static void main( String[] args ) {

        //try-catch catches JAXBException
        try {
            //This is an example of unmarshalling with a simple XML
            File simpleXML = new File("zz_woz.xml");
            JAXBContext jaxbContext = JAXBContext.newInstance(Credentials.class);
            Unmarshaller jaxbUnmarshaller = jaxbContext.createUnmarshaller();
            Credentials credentials = (Credentials) jaxbUnmarshaller.unmarshal(simpleXML);
            System.out.println(credentials);

            //This is an example of taking that same object and marshalling it into an xml file with a simple XML
            Password password = new Password();
            password.setPass("Bad Pass");
            password.setXhint("Best hint ever");
            Credentials newCredentials = new Credentials(
                "missouriwestern.edu",
                "587398",
                "Team Awesome",
                password
            );
            jaxbContext = JAXBContext.newInstance(Credentials.class);
            Marshaller jaxbMarshaller = jaxbContext.createMarshaller();
            jaxbMarshaller.setProperty( Marshaller.JAXB_FORMATTED_OUTPUT, true );
            jaxbMarshaller.marshal( newCredentials, new File ("newCredentials.xml"));
            jaxbMarshaller.marshal( newCredentials, System.out);

        } catch (JAXBException e) {
            e.printStackTrace();
            System.exit(1);
        }
    }
}
```
  - Output in console
#### Unmarshalling (XML to Java Object)
  - Local XML file 
  - POJO code
  - Main code

## Nested XML File (complex)
#### Marshalling (Java Object to XML)
  - POJO code
  - Main code
  - Output in console
#### Unmarshalling (XML to Java Object)
  - URL link
  - POJO code
  - Main code

## Example 3: Array Expressed as XML (complex)
#### Unmarshalling (XML to Java Object)
  - We will be loading the XML from this [url](https://civilserviceusa.github.io/us-states/data/states.xml). The url contains a collection of states in the form of nested XML.
  - **Step 1:** We will need to create a limited-type POJO for each individual state. 
  ````Java
  /**
 * This class represents a single state object.
 */

public class State {

    /* --- DATA --- */
    //These data fields directly correspond to the XML tags found in the above url.
    private String state,slug,code,nickname,website,
            admission_date,admission_number,capital_city,
            capital_url,population,population_rank,constitution_url,
            state_flag_url,state_seal_url,map_image_url,
            landscape_background_url, skyline_background_url,
            twitter_url, facebook_url;

    /* --- TO STRING --- */
    //I would suggest making a toString so that you can easily read & print the object.
    ...

    /* --- ALL GETTERS & SETTERS --- */
    ...
 
} //End of State class.
  ````
  - **Step 2:**  We will also need a class that represents the collection of States. This class will contain the @XMLAnnotations. Because ArrayList is a part of the collection framework, and does not have any JAXB annotations, we need to have this seperate class to represent the set of objects.
   ````Java
import jakarta.xml.bind.annotation.*;
import java.util.List;

@XmlRootElement(name = "states")
@XmlAccessorType(XmlAccessType.FIELD)

public class States {

    @XmlElement(name = "state")
    private List<State> states = null;

   /* --- GETTER FOR LIST --- */
    public List<State> getStates() {
        return states;
    } //End of getStates

    /* --- SETTER FOR LIST --- */
    public void setStates(List<State> states) {
        this.states = states;
    } //End of setStates.

} //End of States class.
  ````
  - **Step 3:**  Now let's get to unmarshalling! First let us create a function that will take in the address, load and unmarshall the content from the url, and give us an ArrayList of the State Objects. 
  ````Java
    public static ArrayList<State> loadToObject(String address) {
        ArrayList<State> stList = new ArrayList<State>();
        try {
            //Setting to the new instance of the XML Annotated class.
            JAXBContext jaxbContext = JAXBContext.newInstance(States.class);
            //Creating the URL to read from address.
            URL url = new URL(address);
            //Creating the Unmarshaller.
            Unmarshaller jaxbUnmarshaller = jaxbContext.createUnmarshaller();
            //Unmarshalling the URL and mapping it to the XML Annotated class States.
            States sts = (States) jaxbUnmarshaller.unmarshal(url);
            //For each state inside the States List, sts.getStates(), print out its corresponding data and add to ArrayList.
            for(State st : sts.getStates()) {
                System.out.println(st);
                stList.add(st);
            } //End of for/each.
            //JAXBContext.newInstance() throws an Exception.
        } catch (Exception ex) {
            ex.printStackTrace();
            System.exit(1);
        } //End of generic catch.
        return stList;
    } //End of loadToObject.
  ````
  - **Step 4:** : Setting the address in main and calling our newly created function.
   ````Java
    public static void main(String[] args) {
        //Putting the web address into a String.
        String address = "https://civilserviceusa.github.io/us-states/data/states.xml";
        //Loading from the website and printing the ArrayList of State objects we created.
        loadToObject(address);
     } //End of main.
  ````
  - Output in console (Note: the above code will print all State objects added to the ArrayList, which is all 50, I will only be showing one of the printed state objects printed in the console).
  ````Java
  //Remember that the way it is printed into the console will be determined by your toString().
 State{state='Alabama', slug='alabama', code='AL', nickname='Yellowhammer State', website='http://www.alabama.gov/', admission_date='1819-12-14', admission_number='22', capital_city='Montgomery', capital_url='http://www.montgomeryal.gov/', population='4833722', population_rank='23', constitution_url='http://alisondb.legislature.state.al.us/alison/default.aspx', state_flag_url='https://cdn.civil.services/us-states/flags/alabama-large.png', state_seal_url='https://cdn.civil.services/us-states/seals/alabama-large.png', map_image_url='https://cdn.civil.services/us-states/maps/alabama-large.png', landscape_background_url='https://cdn.civil.services/us-states/backgrounds/1280x720/landscape/alabama.jpg', skyline_background_url='https://cdn.civil.services/us-states/backgrounds/1280x720/skyline/alabama.jpg', twitter_url='https://twitter.com/alabamagov', facebook_url='https://www.facebook.com/alabamagov%27%7D}
  ````
#### Marshalling (Java Object to XML)
  - POJO code
  - Main code
  - Output in file
