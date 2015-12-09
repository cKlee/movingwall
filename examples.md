## Moving Wall and document service

<div class="example">

    @prefix mwo: <https://w3id.org/library/mwo#> .
    @prefix holding:  <http://example.com/#> .
    @prefix dso:  <http://purl.org/ontology/dso#> .
    @prefix service: <http://purl.org/ontology/service#> .
    @prefix gr:   <http://purl.org/goodrelations/v1#> .
    @prefix bibo: <http://purl.org/ontology/bibo/> .
    @prefix daia: <http://purl.org/ontology/daia/> .
    @prefix dct:  <http://purl.org/dc/terms/> .
    @prefix ecpo: <http://purl.org/ontology/ecpo#> .
    @prefix xs:   <http://www.w3.org/2001/XMLSchema#> .


The series is a document, consisting of multliple volumes

    $series a bibo:Periodical 
        dct:hasPart $volume1, $volume2, $volume3 .

    $volume1 a bibo:CollectedDocument ; bibo:volume "1" .
    $volume2 a bibo:CollectedDocument ; bibo:volume "2" .
    $volume3 a bibo:CollectedDocument ; bibo:volume "3" .

One chapter in Volume 1

    $issue3 a bibo:Document ;
        dct:date "2000"^^dct:W3CDTF ;
        dct:isPartOf $volume1 .

A copy of the full series

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
    
The latest volume is available for presentation

    $librarycopies daia:availableFor [
        a dso:Presentation ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "1"^^xs:integer
            gr:hasUnitOfMeasurement "volume"^^xs:string
        ]
    ] .


All volumes but the last is available for loan

    $librarycopies daia:availableFor [
        a dso:Loan ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "-1"^^xs:integer ;
            gr:hasUnitOfMeasurement "volume"^^xs:string
        ]
    ] .

The latest 10 issues are available for presentation

    $librarycopies daia:availableFor [
        a dso:Presentation ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "10"^^xs:integer ;
            gr:hasUnitOfMeasurement "issue"^^xs:string
        ]
    ] .

All issues but the latest 10 are available for loan. 

In this example no issues are currently available for loan because there are only 9 issues in the chronology.

    $librarycopies daia:availableFor [
        a dso:Loan ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "-10"^^xs:integer ;
            gr:hasUnitOfMeasurement "issue"^^xs:string
        ]
    ] .

The latest two years are available for presentation. 

Given the current year 2001, in this example all issues before the year 2000 are not avaialable for  presentation. 

    $librarycopies daia:availableFor [
        a dso:Presentation ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "P2Y"^^xs:yearMonthDuration
        ]
    ] .

All issues but within the last two years are available for loan. 

Given the current year 2001, all issues after the year 2000 are not available for loan.

    $librarycopies daia:availableFor [
        a dso:Loan ;
        service:limitedBy [
            a mwo:MovingWall ;
            gr:hasValue "-P2Y"^^xs:yearMonthDuration
        ]
    ] .
</div>

## Moving Wall and other services

<div class="example">

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
</div>
