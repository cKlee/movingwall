# Introduction

The **Moving Wall Ontology (MWO)** is a vocabulary to express a quantitative limitation to a service event.

In the real world limitations of document services are often called 'retention period', 'access restriction' or 'moving wall'. This Vocabulary aims to be used in conjunction with document services, but it might be used with other services too.

## Status of this document

This document is an early draft. [Feedback](https://github.com/dini-ag-kim/movingwall/issues) is welcome!

## Namespaces and Ontology

The URI namespace of this ontology is ... The namespace prefix `mwo` is recommeded.
The URI of this ontology as a whole is ...

    @prefix mwo: <http://example.org/#> .
    @base        <http://example.org/> .

The following namspace prefixes are used to refer to related ontologies:

	@prefix dso:  <http://purl.org/ontology/dso#> .
	@prefix service: <http://purl.org/ontology/service#> .
	@prefix gr:   <http://purl.org/goodrelations/v1#> .
	@prefix owl:  <http://www.w3.org/2002/07/owl#> .
	@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
	@prefix vann: <http://purl.org/vocab/vann/> .
	@prefix xs:   <http://www.w3.org/2001/XMLSchema#> .
	@prefix dc:   <http://purl.org/dc/elements/1.1/> .
	@prefix skos: <http://www.w3.org/2004/02/skos/core#> .

The Moving Wall Ontology is defined in RDF/Turtle as following:

    <> a owl:Ontology ;
        rdfs:label "Moving Wall Ontology (MWO)" ;
        vann:preferredNamespacePrefix "mwo" ;
        dc:title "Moving Wall Ontology (MWO)" ;
        dc:description "A vocabulary to express a limitation to a document service"@en .

# Overview
``` {.ditaa}
                                             +---------------------------+
                                             | service:ServiceLimitation |
+-------------------+                        |    +----------------+     |
|                   +---service:limitedBy--->|    |                |     |
|  service:Service  |                        |    |   MovingWall   |     |
|                   |<---service:limits------+    |                |     |
+-------------------+                        |    +----------------+     |
                                             |   gr:QuantitativeValue    |
                                             +---------------------------+
```

A [service:Service] might be limited by a [MovingWall], which is a intersection of [service:ServiceLimitation] and [gr:QuantitativeValue].

To state that a [service:Service] is limited by a [MovingWall] use [service:limits].

# Classes

## MovingWall

[MovingWall]: #movingwall

A **moving wall** is some obstacle that may limit the use of a [service:Service]. 

    mwo:MovingWall a owl:Class ;
        rdfs:label "moving wall" ;
        rdfs:comment "A moving wall is some obstacle that may limit the use of a service:Service"@en ;
        rdfs:subClassOf [
            a owl:Class ;
            owl:intersectionOf (service:ServiceLimitation gr:QuantitativeValue)
        ] .

# Properties

## limitedBy

[limitedBy]: #limitedBy

Used to relate a [service:Service] instance that is **limited by** a [MovingWall] instance to this service limitation.

To relate a [MovingWall] to a [dso:DocumentService] use [service:limits]. [service:limits] is defined by the [Service Ontology].

    service:limitedBy a owl:AnnotationProperty , owl:ObjectProperty ;
        rdfs:label "limited by" ;
        skos:scopeNote "Used to relate a service:Service instance that is limited by a moving wall instance to this service limitation."@en ;
        rdfs:isDefinedBy <http://purl.org/ontology/service> .

## hasValue

[hasValue]: #hasvalue

Used to relate a [MovingWall] to its quantitative value. [gr:hasValue] is defined by [GoodRelations].

    gr:hasValue a owl:AnnotationProperty , owl:DatatypeProperty ;
        skos:scopeNote "Used to relate a moving wall to its quantitative value."@en ;
        rdfs:isDefinedBy <http://purl.org/goodrelations/v1> .

## hasUnitOfMeasurement

[hasUnitOfMeasurement]: #hasunitofmeasurement

Used to relates a [MovingWall] to its unit of measurement. [gr:hasUnitOfMeasurement] is defined by [GoodRelations].

    gr:hasUnitOfMeasurement a owl:AnnotationProperty , owl:DatatypeProperty ;
        skos:scopeNote "Used to relate a moving wall to its quantitative value."@en ;
        rdfs:isDefinedBy <http://purl.org/goodrelations/v1> .

# Examples
## Moving Wall and document service
``` {.example}
@prefix mwo: <http://example.org/#> .
@prefix holding:  <http://example.com/#> .
@prefix dso:  <http://purl.org/ontology/dso#> .
@prefix service: <http://purl.org/ontology/service#> .
@prefix gr:   <http://purl.org/goodrelations/v1#> .
@prefix bibo: <http://purl.org/ontology/bibo/> .
@prefix daia: <http://purl.org/ontology/daia/> .
@prefix dct:  <http://purl.org/dc/terms/> .
@prefix ecpo: <http://purl.org/ontology/ecpo#> .
@prefix xs:   <http://www.w3.org/2001/XMLSchema#> .

# The series is a document, consisting of multliple volumes
$series a bibo:Periodical 
    dct:hasPart $volume1, $volume2, $volume3 .

$volume1 a bibo:CollectedDocument ; bibo:volume "1" .
$volume2 a bibo:CollectedDocument ; bibo:volume "2" .
$volume3 a bibo:CollectedDocument ; bibo:volume "3" .

# One chapter in Volume 1
$issue3 a bibo:Document ;
    dct:date "2000"^^dct:W3CDTF ;
    dct:isPartOf $volume1 .

# A copy of the full series
$librarycopies 
    holding:exemplarOf $series ;
    holding:heldBy $libray ;
    ecpo:hasChronology [
        a ecpo:CurrentChronology ;
        ecpo:hasBeginVolumeNumbering "1"  ;
        ecpo:hasBeginIssueNumbering "1"  ;
        ecpo:hasBeginTemporal "1999" ;
        dct:extent [ rdf:value "9" ]
    ] .
    
# The latest volume is available for presentation 
$librarycopies daia:availableFor [
    a dso:Presentation ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "1"^^xs:integer
        gr:hasUnitOfMeasurement "volume"^^xs:string
    ]
] .

# All volumes but the last is available for loan 
$librarycopies daia:availableFor [
    a dso:Loan ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "-1"^^xs:integer ;
        gr:hasUnitOfMeasurement "volume"^^xs:string
    ]
] .

# The latest 10 issues are available for presentation
$librarycopies daia:availableFor [
    a dso:Presentation ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "10"^^xs:integer ;
        gr:hasUnitOfMeasurement "issue"^^xs:string
    ]
] .

# All issues but the latest 10 are available for loan. 
# In this example no issues are currently available for loan because there are only 9 issues in the chronology.
$librarycopies daia:availableFor [
    a dso:Loan ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "-10"^^xs:integer ;
        gr:hasUnitOfMeasurement "issue"^^xs:string
    ]
] .

# The latest two years are available for presentation. 
# Given the current year 2001, in this example all issues before the year 2000 are not avaialable for  presentation. 
$librarycopies daia:availableFor [
    a dso:Presentation ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "P2Y"^^xs:yearMonthDuration
    ]
] .

# All issues but within the last two years are available for loan. 
# Given the current year 2001, all issues after the year 2000 are not available for loan.
$librarycopies daia:availableFor [
    a dso:Loan ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "-P2Y"^^xs:yearMonthDuration
    ]
] .
```
## Moving Wall and other services
```
$exampleService a ex:CoffeForFree ;
    service:limitedBy [
        a mwo:MovingWall ;
        gr:hasValue "1"^^xs:integer ;
        gr:hasUnitOfMeasurement "cup"^^xs:string
    ] .
    
$PreviewGoogleBook daia:availableFor [
    a dso:Presentation ;
    mwo:limitedBy [
        gr:hasValue "5"^^xs:integer ;
        gr:hasUnitOfMeasurement "page"^^xs:string
    ]
] .
```
# References

## Informative References

* [Document Service Ontology]
* [Simple Service Status Ontology (SSSO)]
* [GoodRelations]
* [DAIA Ontology]
* [Bibliographic Ontology]
* [Enumeration and Chronology of Periodicals Ontology (ECPO)]
* [Dublin Core Metadata Terms]
* [XML Schema]

[Document Service Ontology]: http://purl.org/ontology/dso
[dso:DocumentService]: http://purl.org/ontology/dso#DocumentService
[dso:Loan]: http://purl.org/ontology/dso#Loan
[dso:Presentation]: http://purl.org/ontology/dso#Presentation

[Service Ontology]: http://purl.org/ontology/service
[service:limits]: http://purl.org/ontology/service#limits 
[service:limitedBy]: http://purl.org/ontology/service#limitedBy
[service:Service]: http://purl.org/ontology/service#Service
[service:ServiceLimitation]: http://purl.org/ontology/service#ServiceLimitation

[GoodRelations]: http://purl.org/goodrelations/v1
[gr:hasValue]: http://purl.org/goodrelations/v1#hasValue
[gr:hasUnitOfMeasurement]: http://purl.org/goodrelations/v1#hasUnitOfMeasurement
[gr:QuantitativeValue]: http://purl.org/goodrelations/v1#QuantitativeValue

[DAIA Ontology]: http://purl.org/ontology/daia
[daia:availableFor]: http://purl.org/ontology/daia/availableFor 
[daia:availableOf]: http://purl.org/ontology/daia/availableOf 
[daia:unavailableFor]: http://purl.org/ontology/daia/unavailableFor 
[daia:unavailableOf]: http://purl.org/ontology/daia/unavailableOf

[Enumeration and Chronology of Periodicals Ontology (ECPO)]: http://purl.org/ontology/ecpo
[Bibliographic Ontology]: http://purl.org/ontology/bibo
[Dublin Core Metadata Terms]: http://dublincore.org/documents/dcmi-terms/
[XML Schema]: http://www.w3.org/TR/xmlschema-0/
