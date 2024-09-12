# MLDCAT-AP for Humans

This is a fork of a DCAT 3 application profile MLDCAT-AP 2.0 from [https://semiceu.github.io/MLDCAT-AP/releases/2.0.0](https://semiceu.github.io/MLDCAT-AP/releases/2.0.0).

MLDCAT-AP -- is an application profile, aimed to extend the use of DCAT-AP, originally envisaged for the description of a machine learning process, developed in collaboration with [OpenML](https://www.openml.org/).

Original repository favors very fine-grained SHACL shapes modularity and the ability to turn on and off single constraints of a shape.

This repository aimed at a little different goals (but in a compatible way: all of this repo enhancements should be compatible with original repo):

1. SHACL shapes should be human-readable and allows handcraft manipulations by humans, not robots.

2. SHACL shapes spec should be IDE-friendly (VS Code).
- camelCase identifiers for properties (spellchecker friendly)

1. Shapes is NOT an "domain-isolated case", it should play nicely with other "non-DCAT" shapes and vocabularies in the same triplestore to allow data integration from different domains.
 - No conflicting prefixes
 - No undefined Classes in shapes

## Quick Readability Example

One quick example of enhancements just for one `publisher` property of `dcat:Catalog` class.

Before:

```turtle
@prefix dc: <http://purl.org/dc/terms/> .

<https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape> a shacl:NodeShape;
  shacl:closed false;
  shacl:property <https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/67dcdb36167ca7969c0532898e11a98e9c2a80f5>,
    <https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/93f73e69bb03d2928fcf758a253ef316becdf9b9>,
    <https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/a0ccdf3bd7f5d161d07f375a26e68c18ca91dc19>,
    <https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/b3ec0655204c62a2531244aaeab12f1a2c5e5b5d>;
  shacl:targetClass dcat:Catalog .

# 'publisher' property shape with maxCount constraint
<https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/67dcdb36167ca7969c0532898e11a98e9c2a80f5> rdfs:seeAlso "https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#Catalogue.publisher";
  shacl:description "An entity (organisation) responsible for making the Catalogue available."@en;
  shacl:maxCount 1;
  shacl:name "publisher"@en;
  shacl:path dc:publisher .

# 'publisher' property shape with nodeKind constraint
<https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/93f73e69bb03d2928fcf758a253ef316becdf9b9> rdfs:seeAlso "https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#Catalogue.publisher";
  shacl:description "An entity (organization) responsible for making the Catalogue available."@en;
  shacl:name "publisher"@en;
  shacl:nodeKind shacl:BlankNodeOrIRI;
  shacl:path dc:publisher .

# 'publisher' property shape with minCount constraint
<https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/a0ccdf3bd7f5d161d07f375a26e68c18ca91dc19> rdfs:seeAlso "https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#Catalogue.publisher";
  shacl:description "An entity (organisation) responsible for making the Catalogue available."@en;
  shacl:minCount 1;
  shacl:name "publisher"@en;
  shacl:path dc:publisher .

# 'publisher' property shape with class constraint
<https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#CatalogShape/b3ec0655204c62a2531244aaeab12f1a2c5e5b5d> rdfs:seeAlso "https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#Catalogue.publisher";
  shacl:class foaf:Agent;
  shacl:description "An entity (organisation) responsible for making the Catalogue available."@en;
  shacl:name "publisher"@en;
  shacl:path dc:publisher .
```

After:

```turtle
# dc -> dcterm replacement for better DCAT 3 compatibility
@prefix dcterm: <http://purl.org/dc/terms/> .
# shacl -> sh replacement for better W3C SHACL Shapes spec compatibility
@prefix sh: <http://www.w3.org/ns/shacl#> .

# Added MLDCAT-AP 2 prefix
@prefix mldcatap2: <https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#> .

mldcatap2:CatalogShape a sh:NodeShape;
  sh:closed false;
  sh:property mldcatap2:CatalogShape.publisher;
  sh:targetClass dcat:Catalog .

# all 'publisher' property shape constraints in one place!!!
mldcatap2:CatalogShape.publisher rdfs:seeAlso "https://semiceu.github.io/MLDCAT-AP/releases/2.0.0#Catalogue.publisher";
  sh:name "publisher"@en;
  sh:description "An entity (organization) responsible for making the Catalogue available."@en;
  sh:path dcterm:publisher;
  #sh:nodeKind sh:BlankNodeOrIRI;
  sh:class foaf:Agent;
  sh:minCount 1;
  sh:maxCount 1 .
```

## Quick Play Nicely Example

Before:

```turtle
@prefix dc: <http://purl.org/dc/terms/> .
@prefix shacl: <http://www.w3.org/ns/shacl#> .
```

After:

```turtle
# Some prefixes replaced for better DCAT 3 compatibility
# dc -> dcterm for DCAT 3 compatibility
@prefix dcterm: <http://purl.org/dc/terms/> .
# shacl -> sh for W3C SHACL recommended prefix
@prefix sh: <http://www.w3.org/ns/shacl#> .
```

According to the prefix lookup service https://prefix.cc

dct -- (best one) has no collisions and resolves only to the correct URL http://purl.org/dc/terms/
dcterms -- (good enough) has some collisions, but according to voters resolves to the correct URL http://purl.org/dc/terms/. It is used internally by Dublin Core specs itself.
dc -- (the worst) has collisions and resolves to the different URL http://purl.org/dc/elements/1.1/

To summarize:

The "dc" prefix should be changed to "dcterms" in MLDCAT-AP for the following reasons:

To avoid the internal collisions with DCAT 3 (if someone loads DCAT 3 ontology and MLDCAT AP shapes into the same triplestore).
To avoid the bloating of a triplestore prefix space for no good reason ("dc" and "dcterms" prefixes refers to the same http://purl.org/dc/terms/). Triplestores tends to save prefixes from uploaded files so its better to have some compatibility here.
And to avoid collisions with http://purl.org/dc/elements/1.1/.


## Proposed Enhancements to boost human-readability and handcraft file manipulations
- more namespace prefixes from MLDCAT-AP HTML Documentation
- multiple 'one-constraint property shapes' for the same shacl:path collapsed into one big property shape
- property shape's IRI changed to more human readable based on 'rdfs:seeAlso'
- prefix 'dc' replaced with 'dcterm' to align with DCAT 3
- added MLDCAT-AP 2 prefix 'mldcatap2'
- broad type constraints (shacl:BlankNodeOrIRI or shacl:Literal) commented out if there are narrower ones
- property shapes statements layout is changed for better readability: name and description -- goes first, then path, then type constraints, then cardinality constraints

## Structure of the repository

Main activity is going in this file https://github.com/agentlab/MLDCAT-AP/blob/main/releases/2.0.0/shacl/mldcat-ap-SHACL.ttl
