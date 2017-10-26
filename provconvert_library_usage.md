# ProvToolbox Template Usage

A short tutorial about creating, reading and expanding  Provenance Templates with the ProvToolbox created by Luc Moreau. The **provconvert** tool will not be used, the expansion is done with the internal library class ***Expand***.

All presented templates and bindings are special activitys for the provenance model for Quantified Self data.

The used Provenance Java Library can be found here: http://lucmoreau.github.io/ProvToolbox/

## Example locations
- Tested templates can be found in the *template/*
- Bindings for this templates are located in *examples/*


## Prerequisites
- Setup a java project with the correct ProvToolbox dependencies (Instructions can be found on this page: [Weblink](http://lucmoreau.github.io/ProvToolbox/)
- The usage of building framework **maven** is recommended.
- All relevant functions for template expensions are in the *org.openprovenance.prov.template*-package

## Realisation
- create a new instance *provFactory* of the class **ProvFactory**: You can use the static function `InteropFramework.newXMLProvFactory()`
- create a new instance *fw* of the class **InteropFramework**: You have to use the **ProvFactory**-instance as parameter for the constructor - `new InteropFramework(provFactory)` 
- create a new instance *ep* of the class **Expand**, you need the **ProvFactory**-instance again. This object will later expand your template with matching bindings: `new Expand(p, false, true)`
- read the template Document with the **InteropFramework**-instance and the `fw.readDocumentFromFile(String path)`-function
- read the binding Document with the **InteropFramework**-instance and the `fw.readDocumentFromFile(String path)`-function 
- expand the template with the bindings and save the new Document. For this you can use your created **Expand**-instance: `ep.expander(doc, binding)`
- The resulting document is a filled out template, it can be printed with the usual *fw* functions.

## Reference Implementation

~~~~java
import org.openprovenance.prov.interop.InteropFramework;
import org.openprovenance.prov.interop.InteropFramework.ProvFormat;
import org.openprovenance.prov.model.Document;
import org.openprovenance.prov.model.ProvFactory;
import org.openprovenance.prov.template.Expand;

public class App 
{
    ProvFactory p;
    InteropFramework fw;
    Expand ep;

    public App() {
        p = InteropFramework.newXMLProvFactory();
        fw = new InteropFramework(p);
        ep = new Expand(p, false, true);
    }

    public static void main( String[] args )
    {
        App app = new App();
        Document input = app.expandDocument(app.getPath("input.provn"), 
                                            app.getPath("input_binding.ttl"));
        app.printDocument(input, "Input");
    }

    public Document expandDocument(String pathTmpl, String pathBinding) {
        Document doc = fw.readDocumentFromFile(pathTmpl);
        Document binding = fw.readDocumentFromFile(pathBinding);
        return ep.expander(doc, binding);
    }

    public void printDocument(Document doc, String name) {
        fw.writeDocument(System.out, ProvFormat.PROVN, doc);
        fw.writeDocument(name + ".png", doc);  
    }

    public String getPath(String filename) {
       return this.getClass().getClassLoader().getResource(filename).getPath();
    }
}
~~~~

