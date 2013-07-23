% Moving Wall Ontology (MWO)
% Carsten Klee (ZDB)
% 2013-07-23 15:04:27 +0200

# Introduction

The **Moving Wall Ontology (MWO)** is a vocabulary to express the retention period of an item for a specific service.

## Status of this document

This document is an early draft. [ Feedback ](https://github.com/cklee/movingwall/issues) is welcome!

## Namespaces and Ontology

The URI namespace of this ontology is ... The namespace prefix `mwo` is recommeded.
The URI of this ontology as a whole is ...

    @prefix mwo: <http://example.org/#> .
    @base        <http://example.org/> .

The following namspace prefixes are used to refer to related ontologies:

    @prefix bibo: <http://purl.org/ontology/bibo/> .
    @prefix daia: <http://purl.org/ontology/daia/> .
    @prefix dct:  <http://purl.org/dc/terms/> .
    @prefix dso:  <http://purl.org/ontology/dso#> .
    @prefix ssso:  <http://purl.org/ontology/ssso#> .
    @prefix ecpo: <http://purl.org/ontology/ecpo#> .
    @prefix owl:  <http://www.w3.org/2002/07/owl#> .
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix vann: <http://purl.org/vocab/vann/> .
	@prefix xs:  <http://www.w3.org/2001/XMLSchema#> .

The Holding Ontology is defined in RDF/Turtle as following:

    <> a owl:Ontology ;
        rdfs:label "Moving Wall Ontology (MWO)" ;
        vann:preferredNamespacePrefix "mwo" .

# Classes

## DocumentService

[DocumentService]: #documentservice

A **DocumentService** is a service event that is related to one or more documents. The DocumentService class is defined by the [Document Service Ontology] and is a subclass of [ssso:ServiceEvent].

    dso:DocumentService a owl:Class ;
        rdfs:label "DocumentService" ;
        rdfs:isDefinedBy <http://purl.org/ontology/dso> .

Typical document services within the scope of holdings ontology involve a loan event ([dso:Loan]) and a presentation event ([dso:Presentation]). 
To express the availability of items for selected services, one SHOULD use the properties [daia:availableFor] and [daia:unavailableFor] from the [DAIA Ontology].

[daia:availableFor]: http://purl.org/ontology/daia/availableFor 
[daia:availableOf]: http://purl.org/ontology/daia/availableOf 
[daia:unavailableFor]: http://purl.org/ontology/daia/unavailableFor 
[daia:unavailableOf]: http://purl.org/ontology/daia/unavailableOf 

[dso:Loan]: http://purl.org/ontology/dso#Loan
[dso:Presentation]: http://purl.org/ontology/dso#Presentation

## ServiceLimitation

[ServiceLimitation]: #servicelimitation

A **service limitation** is some obstacle that may limit the use of a
[ssso:ServiceEvent]. For instance the purchase of guns and drugs is limited to
consumers with special permission. Another example is providing a different
product or activity than originally requested. Services and limitations are
connected to each other with properties [ssso:limits] and [ssso:limitedBy].

    ssso:ServiceLimitation a owl:Class ;
        rdfs:label "ServiceLimitation" ;
        rdfs:isDefinedBy <http://purl.org/ontology/ssso> .
		
[ssso:limits]: http://purl.org/ontology/ssso#limits 
[ssso:limitedBy]: http://purl.org/ontology/ssso#limitedBy
[ssso:ServiceEvent]: http://purl.org/ontology/ssso#ServiceEvent


# Properties

## limitedBy

[limitedBy]: #limitedBy

Relates a [ssso:ServiceEvent] instance that is **limited by** a [ssso:ServiceLimitation]
instance to this service limitation. The property [limitedBy] is defined by the [Simple Service Status Ontology (SSSO)].

    ssso:limitedBy a owl:ObjectProperty ;
        rdfs:label "limitedBy" ;
        rdfs:isDefinedBy <http://purl.org/ontology/ssso> .

## numVolumes

[numVolumes]: #numvolumes

A limitation to the [DocumentService] in the form of number of volumes. The number MUST be of the datatype **xs:integer** and thus can be positive or negative.

	mwo:numVolumes a owl:DatatypeProperty ;
		rdfs:label "number of volumes" ;
		rdfs:comment "A limitation to the DocumentService in the form of number of volumes." ;
		rdfs:domain dso:DocumentService ;
		rdfs:range xs:integer .

## numIssues

[numIssues]: #issues

A limitation to the [DocumentService] in the form of number of issues. The number MUST be of the datatype **xs:integer** and thus can be positive or negative.

	mwo:numIssues a owl:DatatypeProperty ;
		rdfs:label "number of issues" ;
		rdfs:comment "A limitation to the DocumentService in the form of number of volumes." ;
		rdfs:domain dso:DocumentService ;
		rdfs:range xs:integer .

## timePeriod

[timePeriod]: #timePeriod

A limitation to the [DocumentService] in the form of a period of time. The period MUST be of the datatype **xs:duration** and thus can be positive or negative.

	mwo:timePeriod a owl:DatatypeProperty ;
		rdfs:label "period of time" ;
		rdfs:comment "A limitation to the DocumentService in the form of a period of time." ;
		rdfs:domain dso:DocumentService ;
		rdfs:range xs:duration .


# Examples

``` {.example}
# The series is a document, consisting of multliple volumes
$series a bibo:Periodical 
    dcterms:hasPart $volume1, $volume2, $volume3 .

$volume1 a bibo:CollectedDocument ; bibo:volume "1" .
$volume2 a bibo:CollectedDocument ; bibo:volume "2" .
$volume3 a bibo:CollectedDocument ; bibo:volume "3" .

# One chapter in Volume 1
$issue3 a bibo:Document ;
	dcterms:date "2000"^^dcterms:W3CDTF ;
    dcterms:isPartOf $volume1 .

# A copy of the full series
$librarycopies 
    holding:exemplarOf $series ;
    holding:heldBy $libray ;
    ecpo:hasChronology [
        a ecpo:CurrentChronology ;
        ecpo:hasBeginVolumeNumbering "1"  ;
        ecpo:hasBeginIssueNumbering "1"  ;
		ecpo:hasBeginTemporal "1999" ;
		dcterms:extent [ rdf:value "9" ]	
    ] .
	
# The latest volume is available for presentation 
$librarycopies daia:availableFor [
	a dso:Presentation ;
	ssso:limitedBy [
		mwo:numVolumes "1"^^xs:integer
	]
] .

# All volumes but the last is available for loan 
$librarycopies daia:availableFor [
	a dso:Loan ;
	ssso:limitedBy [
		mwo:numVolumes "-1"^^xs:integer
	]
] .

# The latest 10 issues are available for presentation
$librarycopies daia:availableFor [
	a dso:Presentation ;
	ssso:limitedBy [
		mwo:numIssues "10"^^xs:integer
	]
] .

# All issues but the latest 10 are available for loan. In this example no issues are currently available for loan because there are only 9 issues in the chronology.
$librarycopies daia:availableFor [
	a dso:Loan ;
	ssso:limitedBy [
		mwo:numIssues "-10"^^xs:integer
	]
] .

# The latest two years are available for presentation. Given the current year 2001, in this example all issues before the year 2000 are not avaialable for  presentation. 
$librarycopies daia:availableFor [
	a dso:Presentation ;
	ssso:limitedBy [
		mwo:timePeriod "P2Y"^^^xs:duration
	]
] .

# All issues but within the last two years are available for loan. Given the current year 2001, all issues before 2000 are available for loan.
$librarycopies daia:availableFor [
	a dso:Loan ;
	ssso:limitedBy [
		mwo:timePeriod "-P2Y"^^^xs:duration
	]
] .

```

# References

## Normative References

...

## Informative References

* [Enumeration and Chronology of Periodicals Ontology] (ECPO)
* [Document Service Ontology]
* [Simple Service Status Ontology (SSSO)]

[Document Service Ontology]: http://purl.org/ontology/dso
[DAIA Ontology]: http://purl.org/ontology/daia
[Enumeration and Chronology of Periodicals Ontology]: http://purl.org/ontology/ecpo
[Simple Service Status Ontology (SSSO)]: http://purl.org/ontology/ssso



