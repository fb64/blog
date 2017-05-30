+++
author = "Florian Bernard"
categories = ["programmation"]
date = "2017-05-30T17:57:22+02:00"
description = "desérialisation générique en xml"
draft = true
tags = ["xml","java"]
title = "Desérialisation générique en xml"

+++


## Problématique

Il m'arrive souvent de consomer des données au format [XML](https://fr.wikipedia.org/wiki/Extensible_Markup_Language) en java, pour faciliter la serialisation / desérialisation j'utilise régulièrement l'[API](https://fr.wikipedia.org/wiki/Interface_de_programmation) [JAXB](https://docs.oracle.com/javase/8/docs/technotes/guides/xml/jaxb/) qui est l'api fourni par défaut avec java pour traiter les données au format xml.

Exemple de modélisation :

Format xml
```xml
<voiture>
	<marque>renault</marque>
	<couleur>rouge</couleur>
	<prix>10000.00</prix>
</voiture>
``` 

Classe Java correspondante
```java
@XmlRootElement(name = "voiture") //indication du noeud parent xml représentant l'objet
@XmlAccessorType(XmlAccessType.FIELD) // méthode d'accès au propriété de l'objet
public class Voiture{
	
	private String marque;
	private String couleur;
	private Double prix;

	//Getters et Setters
	//	.....
}
```

Assez simple non ? Cela dit il arrive souvent parfois que tous les objets d'une même source soit englobés dans un élément parent de la manière suivante : 

```xml
<monApi>
	<voiture>
		<marque>renault</marque>
		<couleur>rouge</couleur>
		<prix>10000.00</prix>
	</voiture>
</monApi>
``` 

Ceci implique de créer une classe spécifique permettant de contenir le noeud parent et le sous objet qu'il contient. Par exemple :

```java
@XmlRootElement(name = "monApi")
@XmlAccessorType(XmlAccessType.FIELD)
public class VoitureContainer{
	
	private Voiture voiture;
	//Getters et Setters
	//.....
}
```

Quand il y a beaucoup d'objets à modéliser, cette méthode peut être fastidieuse et répétitive.

## Solution

Grâce à l'utilisation des [generics](https://docs.oracle.com/javase/tutorial/java/generics/) et de l'annotation [JAXB](https://docs.oracle.com/javase/8/docs/technotes/guides/xml/jaxb/) [@XmlAnyElement](https://docs.oracle.com/javase/8/docs/api/javax/xml/bind/annotation/XmlAnyElement.html) il est possible de définir une classe conteneur permettant de stocker n'importe qu'elle sous type de manière générique. 

```java
@XmlRootElement(name = "monApi")
@XmlAccessorType(XmlAccessType.FIELD)
public class ApiContainer<T> {

    @XmlAnyElement(lax = true)
    private T objet;

    public T getObjet() {
        return objet;
    }

}
```

Exemple d'utilisation :

```java
public class Deserializer{

public static final String XML = "<monApi> <voiture><marque>renault</marque><couleur>rouge</couleur><prix>10000.00</prix></voiture>< /monApi>";

 public static void main(String[] args) {
        try {
            JAXBContext jaxbContext = JAXBContext.newInstance(AddressModel.class, PrestashopEntityWrapper.class);
            PrestashopEntityWrapper<Voiture> conteneur = (PrestashopEntityWrapper<Voiture>) jaxbContext.createUnmarshaller().unmarshal(new StringReader(XML));
            Voiture voiture = conteneur.getEntity();
            System.out.println("*** Voiture ***");
            System.out.println("marque: " + voiture.getMarque());
            System.out.println("couleur: " + voiture.getCouleur());
            System.out.println("Prix: " + voiture.getPrix());
            System.out.println("******");

        } catch (JAXBException e) {
            System.err.print("Erreur pendant la desérialisation xml: " + e.getMessage());
        }
    }
}
```

Cette solution permet d'utiliser toujours la même classe de façon générique pour tout les objets contenu dans le même élément parent.











