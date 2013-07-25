# Introduction

The **Moving Wall Ontology (MWO)** is a vocabulary to express a limitation to a document service.

In the real world limitations of document services are often called 'retention period', 'access restriction' or 'moving wall'.

## Status of this document

This document is an early draft. [Feedback](https://github.com/cklee/movingwall/issues) is welcome!

## Namespaces and Ontology

The URI namespace of this ontology is ... The namespace prefix `mwo` is recommeded.
The URI of this ontology as a whole is ...

    @prefix mwo: <http://example.org/#> .
    @base        <http://example.org/> .

The following namspace prefixes are used to refer to related ontologies:

	@prefix dso:  <http://purl.org/ontology/dso#> .
	@prefix ssso: <http://purl.org/ontology/ssso#> .
	@prefix gr:   <http://purl.org/goodrelations/v1#> .
	@prefix owl:  <http://www.w3.org/2002/07/owl#> .
	@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
	@prefix vann: <http://purl.org/vocab/vann/> .
	@prefix xs:   <http://www.w3.org/2001/XMLSchema#> .
	@prefix dc:   <http://purl.org/dc/elements/1.1/> .
	@prefix skos: <http://www.w3.org/2004/02/skos/core#> .
	@prefix rdf:  <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .

The Moving Wall Ontology is defined in RDF/Turtle as following:

    <> a owl:Ontology ;
        rdfs:label "Moving Wall Ontology (MWO)" ;
        vann:preferredNamespacePrefix "mwo" ;
        dc:title "Moving Wall Ontology (MWO)" ;
        dc:description "A vocabulary to express a limitation to a document service"@en .

# Overview
``` {.ditaa}
+-------------------------+                 +------------------------+
|    ssso:ServiceEvent    |                 | ssso:ServiceLimitation |
| +---------------------+ |    limitedBy    |   +----------------+   |
| |                     +---------------------->|                |   |
| | dso:DocumentService | |                 |   |   MovingWall   |   |
| |                     | |<----------------|   |                |   |
| +---------------------+ |   ssso:limits   |   +----------------+   |
|                         |                 |  gr:QuantitativeValue  |
+-------------------------+                 +------------------------+
```

A [dso:DocumentService] is a subclass of [ssso:ServiceEvent] and might be limited through a [MovingWall], which is a subclass of [ssso:ServiceLimitation].

To state that a [dso:DocumentService] is limited by a [MovingWall] use [ssso:limits].

# Classes

## MovingWall

[MovingWall]: #movingwall

A **MovingWall** is some obstacle that may limit the use of a [ssso:ServiceEvent]. 

Instances of [MovingWall] must at least participate in a relation with only one of the properties [numVolumes], [numIssues] or [timePeriod].

	mwo:MovingWall a owl:Class ;
		rdfs:label "moving wall" ;
		rdfs:comment "A moving wall is some obstacle that may limit the use of a ssso:ServiceEvent"@en ;
		rdfs:subClassOf ssso:ServiceLimitation ;
		rdfs:subClassOf [
			a owl:Class ;
			owl:intersectionOf (ssso:ServiceLimitation gr:QuantitativeValue)
		] .

# Properties

## limitedBy

[limitedBy]: #limitedBy

Relates a [dso:DocumentService] instance that is **limited by** a [MovingWall] instance to this service limitation.

To relate a [MovingWall] to a [dso:DocumentService] use [ssso:limits].

	mwo:limitedBy a owl:ObjectProperty ;
		rdfs:label "limited by" ;
		rdfs:comment "Relates a dso:DocumentService instance that is limited by a moving wall instance to this service limitation."@en ;
		rdfs:domain dso:DocumentService ;
		rdfs:range mwo:MovingWall ;
		rdfs:subPropertyOf ssso:limitedBy .

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

``` {.example}
@prefix mwo: <http://example.org/#> .
@prefix holding:  <http://example.com/#> .
@prefix dso:  <http://purl.org/ontology/dso#> .
@prefix ssso: <http://purl.org/ontology/ssso#> .
@prefix gr:   <http://purl.org/goodrelations/v1#> .
@prefix bibo: <http://purl.org/ontology/bibo/> .
@prefix daia: <http://purl.org/ontology/daia/> .
@prefix dct:  <http://purl.org/dc/terms/> .
@prefix ecpo: <http://purl.org/ontology/ecpo#> .

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
	mwo:limitedBy [
		gr:hasValue "1"^^xs:integer
		gr:hasUnitOfMeasurement "volume"^^xs:string
	]
] .

# All volumes but the last is available for loan 
$librarycopies daia:availableFor [
	a dso:Loan ;
	mwo:limitedBy [
		gr:hasValue "-1"^^xs:integer ;
		gr:hasUnitOfMeasurement "volume"^^xs:string
	]
] .

# The latest 10 issues are available for presentation
$librarycopies daia:availableFor [
	a dso:Presentation ;
	mwo:limitedBy [
		gr:hasValue "10"^^xs:integer ;
		gr:hasUnitOfMeasurement "issue"^^xs:string
	]
] .

# All issues but the latest 10 are available for loan. 
# In this example no issues are currently available for loan because there are only 9 issues in the chronology.
$librarycopies daia:availableFor [
	a dso:Loan ;
	mwo:limitedBy [
		gr:hasValue "-10"^^xs:integer ;
		gr:hasUnitOfMeasurement "issue"^^xs:string
	]
] .

# The latest two years are available for presentation. 
# Given the current year 2001, in this example all issues before the year 2000 are not avaialable for  presentation. 
$librarycopies daia:availableFor [
	a dso:Presentation ;
	mwo:limitedBy [
		gr:hasValue "P2Y"^^xs:yearMonthDuration
	]
] .

# All issues but within the last two years are available for loan. 
# Given the current year 2001, all issues before 2000 are available for loan.
$librarycopies daia:availableFor [
	a dso:Loan ;
	mwo:limitedBy [
		gr:hasValue "-P2Y"^^xs:yearMonthDuration
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

[Simple Service Status Ontology (SSSO)]: http://purl.org/ontology/ssso
[ssso:limits]: http://purl.org/ontology/ssso#limits 
[ssso:limitedBy]: http://purl.org/ontology/ssso#limitedBy
[ssso:ServiceEvent]: http://purl.org/ontology/ssso#ServiceEvent
[ssso:ServiceLimitation]: http://purl.org/ontology/ssso#ServiceLimitation

[GoodRelations]: http://purl.org/goodrelations/v1
[gr:hasValue]: http://purl.org/goodrelations/v1#hasValue
[gr:hasUnitOfMeasurement]: http://purl.org/goodrelations/v1#hasUnitOfMeasurement

[DAIA Ontology]: http://purl.org/ontology/daia
[daia:availableFor]: http://purl.org/ontology/daia/availableFor 
[daia:availableOf]: http://purl.org/ontology/daia/availableOf 
[daia:unavailableFor]: http://purl.org/ontology/daia/unavailableFor 
[daia:unavailableOf]: http://purl.org/ontology/daia/unavailableOf

[Enumeration and Chronology of Periodicals Ontology (ECPO)]: http://purl.org/ontology/ecpo
[Bibliographic Ontology]: http://purl.org/ontology/bibo
[Dublin Core Metadata Terms]: http://dublincore.org/documents/dcmi-terms/
[XML Schema]: http://www.w3.org/TR/xmlschema-0/
