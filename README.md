# Statistical Data Production Ontology (SDPO)

##### Table of Contents

[Description](#description)  
[Design](#design)  
[Evaluation](#evaluation)

## Description

Statistical Data Production Ontology is an ontology that aim to represent an integrated statistical metadata, focusing on the statistical data production process. The ontology is built based on the existing metadata standards of [Generic Statistical Information Model (GSIM)](https://unece.github.io/GSIM-2.0/), [Generic Statistical Business Process Model (GSBPM)](https://statswiki.unece.org/display/GSBPM/Generic+Statistical+Business+Process+Model), [Generic Activity Model for Statistical Organisations (GAMSO)](https://statswiki.unece.org/display/GAMSO/Generic+Activity+Model+for+Statistical+Organizations), and [Common Statistical Production Architecture (CSPA)](https://statswiki.unece.org/pages/viewpage.action?pageId=112132835). SDPO integrates metadata related to the statistical data production process to provide a comprehensive overview and information on the statistical data production process. In addition to enhancing users’ trust in the statistical data, integrated statistical metadata will also:

- Facilitate the interoperability of metadata between data production support systems;
- Improve the efficiency of statistical data production through the reuse of existing metadata;
- Enable process automation through metadata-driven approaches, simplify metadata management;
- Serve as the foundation for building statistical meta-data knowledge management.

## Design

![alt text](/images/GSBPM%20dan%20GSIM%20relation.png)

This repository provides two files with following description:

- SDPO v1.1.ttl : The Statistical Data Production ontology and the dummy data
- SPARQL.txt : List of SPARQL to answer Competency Question 1 - 8

## Evaluation

This section presents the functional test of SDPO against competency questions using SPARQL queries. The data used for this testing includes the 2020 Indonesia Population Census metadata and the Hungary Retail Trades Statistics Survey metadata.

#### CQ1. What processes are required to conduct a survey?

```SQL
PREFIX : <https://w3id.org/sdp/core#>

SELECT
    ?StatisticalProgram ?StatisticalProgramCycle ?BusinessProcess ?Process
WHERE {
    ?StatisticalProgram :hasSP-SPC ?StatisticalProgramCycle .
    ?StatisticalProgramCycle :includesSPC-BP ?BusinessProcess .
    ?BusinessProcess :hasBP-PS ?Process .
} LIMIT 6
```

Result (_Only a portion of the results is displayed_)
| StatisticalProgram | StatisticalProgramCycle | BusinessProcess | Process |
| -- | -- | -- | -- |
| id-ps:PopulationCensusProgram | id-ps:PopulationCensusProgramCycle2020 | id-ps:PopulationCensusBusinessProcess2020ShortForm | id-ps:SP2020DesignCollectionProcess |
| id-ps:PopulationCensusProgram | id-ps:PopulationCensusProgramCycle2020 | id-ps:PopulationCensusBusinessProcess2020ShortForm | id-ps:SP2020DesignOutputProcess |
| id-ps:PopulationCensusProgram | id-ps:PopulationCensusProgramCycle2020 | id-ps:PopulationCensusBusinessProcess2020ShortForm | id-ps:SP2020DesignProcessingAndAnalysisProcess |
| kshrts:RetailTradeStatistics | kshrts:RetailTradeStatisticsProgramCycle | kshrts:RetailTradeStatisticsBusinessProcess | kshrts:CheckDataAvailabilityProcess |
| kshrts:RetailTradeStatistics | kshrts:RetailTradeStatisticsProgramCycle |kshrts:RetailTradeStatisticsBusinessProcess | kshrts:ConsultAndConfirmNeeds |
| kshrts:RetailTradeStatistics | kshrts:RetailTradeStatisticsProgramCycle | kshrts:RetailTradeStatisticsBusinessProcess | kshrts:CreateFrameAndSampleProcess |

#### CQ2. What metadata is required to create a survey?

```SQL
PREFIX : <https://w3id.org/sdp/core#>
PREFIX gsim-sum: <https://w3id.org/italia/onto/gsim-sum#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX gsim: <http://rdf.unece.org/models/gsim#>

SELECT
    ?StatisticalProgram ?Process ?MetadataInput ?MetadataValue
WHERE {
    ?StatisticalProgram :hasSP-SPC ?StatisticalProgramCycle .
    ?StatisticalProgramCycle :includesSPC-BP ?BusinessProcess .
    ?BusinessProcess :hasBP-PS ?Process .
    ?Process rdf:type :ProcessStep .
    ?Process :hasPS-PI ?ProcessInput .
    ?ProcessInput :refersToCI-IA ?MetadataInput.
    ?MetadataInput ?p ?MetadataValue .
    FILTER(
        CONTAINS(str(?p),"has")
        &&
        !CONTAINS(str(?p),"hasName")
    ).
}
```

Result (_Only a portion of the results is displayed_)
| StatisticalProgram | Process | MetadataInput | MetadataValue |
| -- | -- | -- | --------- |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignCollection Process | id-ps:SP2020 DesignOutput Statistical Program Design | UNIT OF OBSERVATION<br>All individuals (Indonesian citizens and foreigners) residing in the territory of the Unitary State of the Republic of Indonesia for one year or more, or those residing for less than one year but intending to settle for more than one year.<br><br>TYPE OF OBSERVATIONAL STUDY<br>Cross-Sectional<br><br>TYPE OF UNIT OF OBSERVATION<br>Individuals |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignCollection Process | id-ps:SP2020 DesignOutput BusinessCase | To calculate serveral indicator related to Indonesian population in 2020, including:<br><br>- Total population<br>- Total population by administration<br>- Total population migration<br>- Total population by generation |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignOutput Process | id-ps:SP2020 DesignOutput BusinessCase | To calculate serveral indicator related to Indonesian population in 2020, including:<br><br>- Total population<br>- Total population by administration<br>- Total population migration<br>- Total population by generation |
| kshrts:Retail TradeStatistics | kshrts:Check DataAvailability Process | kshrts:Check DataAvailability Information Resources | - Retail Business Registry (Internal Data Source)<br>- Annual Retail Trade Survey (Internal Data Source)<br>- Tax Authority Records (External Data Source)<br>- Point-of-Sale (POS) Transaction Data from Private Retailers (External Data Source)<br>- Consumer Expenditure Survey (Internal Data Source)<br>- Retail Price Index Data (Internal Data Source) |

#### CQ3. What metadata is produced by each process in the statistical data production?

```
PREFIX : <https://w3id.org/sdp/core#>
PREFIX gsim-sum: <https://w3id.org/italia/onto/gsim-sum#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX gsim: <http://rdf.unece.org/models/gsim#>

select ?StatisticalProgram ?Process ?MetadataOutput ?MetadataValue where {
    ?StatisticalProgram :hasSP-SPC ?StatisticalProgramCycle .
    ?StatisticalProgramCycle :includesSPC-BP ?BusinessProcess .
    ?BusinessProcess :hasBP-PS ?Process .
    ?Process rdf:type :ProcessStep .
    ?Process :createsPS-PO ?ProcessOutput .
    ?ProcessOutput :refersToCO-IA ?MetadataOutput.
    ?MetadataOutput ?p ?MetadataValue .
    FILTER(
        CONTAINS(str(?p),"has")
        &&
        !CONTAINS(str(?p),"hasName")
    ).
}
```

Result (_Only a portion of the results is displayed_)
| StatisticalProgram | Process | MetadataOutput | MetadataValue |
| -- | -- | -- | --------- |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignCollection Process | id-ps:SP2020 DesignCollection ProcessMethod Collection | DOES CONDUCT OFFICER TRAINING<br>Yes<br><br>NUMBER OF Enumerators<br>208038<br><br>TYPE OF DATA COLLECTION AREA COVERAGE<br>All Indonesian Territory<br><br>DATA COLLECTION FREQUENCY<br>More than Two Years<br><br>MINIMUM EDUCATIONAL LEVEL OF OFFICERS<br>SMA/SMK<br><br>DATA COLLECTION MODE<br>Paper-Assisted Personal Interviewing (PAPI), Computer-Assisted Personal Interviewing (CAPI), Computer-Aided Web Interviewing (CAWI)<br><br>METHOD OF COLLECTING DATA<br>- Interview (PAPI)<br>- Data Input (CAPI)<br>- Data Input (CAWI)<br><br>DATA COLLECTION OFFICER<br>Organizing Agency Staff and Partners/Contract Personnel<br><br>RESPONSIBLE DIVISION<br>Subdirectorate of Demographic Statistics |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignOutput Process | id-ps:SP2020 DesignOutput StatisticalProgram Design | UNIT OF OBSERVATION<br>All individuals (Indonesian citizens and foreigners) residing in the territory of the Unitary State of the Republic of Indonesia for one year or more, or those residing for less than one year but intending to settle for more than one year.<br><br>TYPE OF OBSERVATIONAL STUDY<br>Cross-Sectional<br><br>TYPE OF UNIT OF OBSERVATION<br>Individuals |
| kshrts:Retail TradeStatistics | kshrts:Check DataAvailability Process | kshrts:Check DataAvailability Assessment | Assessment Summary:<br>1. Internal Data Sources Assessment:<br><br>1.1. Retail Business Register<br>- Availability: Available and regularly updated.<br>- Quality: High coverage of retail businesses, including small and medium enterprises (SMEs).<br>- Completeness: Near-complete coverage of the active retail sector. However, there is a small lag in updating business status (active/inactive), leading to potential inclusion of recently closed businesses.<br>- Suitability: Well-suited for sampling frame development and trend analysis.<br>- Recommendation: Proceed with this source as the primary sampling frame, with updates to inactive businesses done periodically to enhance data quality.<br><br>1.2. Annual Retail Trade Survey<br>- Availability: Available (last conducted in 2023).<br>- Quality: Good historical data for retail sales and employment but subject to some non-response from smaller businesses.<br>- Completeness: Coverage is strong for larger retail chains but less so for micro-enterprises. Data from specific retail subsectors (e.g., online retail) is sparse.<br>- Suitability: Useful for trend analysis and historical comparison, but additional supplementation may be required for niche retail sectors.<br>- Recommendation: Use as a secondary source to verify trends but supplement with external data for better coverage of online and SME sectors.<br><br>1.3. Consumer Expenditure Survey<br>- Availability: Available.<br>- Quality: High-quality, self-reported household expenditure data.<br>- Completeness: Comprehensive data on retail-related purchases but limited granularity on specific retail outlets.<br>- Suitability: Can be used to cross-check retail sales figures by comparing household expenditure on retail goods.<br>- Recommendation: Use this data to validate reported retail sales figures but not as a primary source.<br><br>2. External Data Sources Assessment:<br>2.1. Tax Authority Records<br>- Availability: Available; provision agreement in place with the National Tax Authority.<br>- Quality: High, as it is based on legally filed tax data. Sales turnover is accurately captured.<br>- Completeness: Comprehensive for larger and medium-sized businesses. Some under-reporting may occur for small, informal retail enterprises.<br>- Suitability: Highly suitable for validating sales turnover data, particularly for medium and large enterprises.<br>- Recommendation: Use this source to cross-check and validate sales turnover data. Additional adjustments might be needed for missing or underreported data from smaller retailers.<br><br>2.2. Point-of-Sale (POS) Data from Retail Chains<br>- Availability\*\*: Available; provision agreement in place with major retail chains.<br>- Quality: High-quality transactional data covering daily sales volume and turnover. Provides granular, near real-time data on consumer purchases.<br>- Completeness: Covers most large and mid-sized retail chains but excludes independent retailers and SMEs.<br>- Suitability: Excellent for real-time tracking of sales in fast-moving consumer goods (FMCG) sectors and large retail chains. However, it lacks coverage for the small and micro retail sector.<br>- Recommendation: Use as a primary source for tracking retail activity in major retail chains, but supplement with survey data for SMEs and niche retailers.<br><br>Overall Assessment Conclusion:<br>The assessment of data availability for the Retail Trade Statistics shows that the data sources currently available provide comprehensive coverage for the retail trade sector, with certain limitations. The NSO has sufficient data to proceed with the statistical production process, but adjustments and integrations of various data sources are recommended to fill gaps and ensure complete coverage.<br><br>- Strengths:<br> - Internal Data: The Eetail Business Register provides a reliable sampling frame, and the Annual Retail Trade Survey offers solid historical data.<br> - External Data: Tax Authority Records and POS data from large retail chains are excellent for validating turnover figures and ensuring real-time sales tracking.<br><br>- Weaknesses:<br> - Retail Business Register: Some lag in updating the status of businesses.<br> - Annual Retail Trade Survey: Limited coverage of micro-enterprises and online retailers.<br> - POS Data: Limited to larger retail chains, with insufficient representation of SMEs and independent retailers.<br><br>- Recommendations:<br>1. Integration of Data Sources: Combine Tax Authority Records and POS Data with the Annual Retail Trade Survey to improve coverage for the entire retail sector, including SMEs.<br>2. Supplementation: Conduct targeted surveys or use third-party data sources to better capture data from online retailers and micro-businesses.<br>3. Data Quality Monitoring: Establish a regular data quality monitoring process, especially for external data sources, to ensure consistency and completeness over time.<br>4. Further Collaboration: Enhance collaboration with retail associations to improve data availability from smaller businesses and niche sectors.
|

#### CQ4. Do the production processes occur sequentially in accordance with the order specified by GSBPM?

```SQL
PREFIX : <https://w3id.org/sdp/core#>
PREFIX gsim-sum: <https://w3id.org/italia/onto/gsim-sum#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX gsim: <http://rdf.unece.org/models/gsim#>
SELECT
    ?StatisticalProgram ?Process ?ProcessCode ?Start ?End
WHERE {
    ?StatisticalProgram :hasSP-SPC / :includesSPC-BP / :hasBP-PS ?Process .
    ?ProcessDesign :specifiesPD-PS ?Process .
    ?ProcessDesign :classifiedAs-PD ?ProcessCode .
    ?Process rdf:type :ProcessStep .
    ?Process :hasStartTime-PS ?Start .
    ?Process :hasEndTime-PS ?End .
}
```

Result (Only a portion of the results is displayed)
| StatisticalProgram | Process | ProcessCode | Start | End |
| ------------------ | ------- | ----------- | ----- | --- |
| id-ps:Population CensusProgram | id-ps:SP2020Design CollectionProcess | igsbpm:3.2 | "2018-08-01T00:00:00" ^^xsd:dateTime | "2018-09-12T00:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020Design OutputProcess | igsbpm:2.1 | "2018-05-02T08:00:00" ^^xsd:dateTime | "2018-06-29T20:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020Design ProcessingAndAnalysis Process | igsbpm:2.5 | "2018-09-03T00:00:00" ^^xsd:dateTime | "2018-10-17T00:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020Design VariableDescriptions Process | igsbpm:2.2 | "2018-05-14T07:00:00" ^^xsd:dateTime | "2018-07-13T23:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:CheckData AvailabilityProcess | igsbpm:1.5 | "2018-03-22T08:00:00" ^^xsd:dateTime | "2018-03-01T20:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:ConsultAnd ConfirmNeeds | igsbpm:1.2 | "2018-01-22T08:00:00" ^^xsd:dateTime | "2018-02-16T20:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:CreateFrame AndSampleProcess | igsbpm:4.1 | "2018-07-01T08:00:00" ^^xsd:dateTime | "2018-07-03T20:00:00" ^^xsd:dateTime |

#### CQ5. What metadata falls under the categories of planning, implementation, dissemination, and evaluation?

```SQL
PREFIX : <https://w3id.org/sdp/core#>
PREFIX gsim-sum: <https://w3id.org/italia/onto/gsim-sum#>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX gsim: <http://rdf.unece.org/models/gsim#>
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>

SELECT
    ?StatisticalProgram ?Process ?ProcessCode ?Phase ?StartTime ?EndTime
WHERE {
    ?StatisticalProgram :hasSP-SPC / :includesSPC-BP / :hasBP-PS ?Process .
    ?ProcessDesign :specifiesPD-PS ?Process .
    ?ProcessDesign :classifiedAs-PD ?ProcessCode .
    ?ProcessCode skos:broader / skos:prefLabel ?Phase .
    ?Process rdf:type :ProcessStep .
    ?Process :hasStartTime-PS ?StartTime .
    ?Process :hasEndTime-PS ?EndTime .
}
```

Result (Only a portion of the results is displayed)
| Statistical Program | Process | ProcessCode | Phase | TimeStart | TimeEnd |
| ------------------- | ------- | ----------- | ----- | --------- | ------- |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignCollection Process | igsbpm:3.2 | "Build"@en | "2018-08-01T00:00:00" ^^xsd:dateTime | "2018-09-12T00:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignOutput Process | igsbpm:2.1 | "Design"@en | "2018-05-02T08:00:00" ^^xsd:dateTime | "2018-06-29T20:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020 Design Processing AndAnalysis Process | igsbpm:2.5 | "Design"@en | "2018-09-03T00:00:00" ^^xsd:dateTime | "2018-10-17T00:00:00" ^^xsd:dateTime |
| id-ps:Population CensusProgram | id-ps:SP2020 DesignVariable Descriptions Process | igsbpm:2.2 | "Design"@en | "2018-05-14T07:00:00" ^^xsd:dateTime | "2018-07-13T23:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:Check DataAvailability Process | igsbpm:1.5 | "Specify Needs"@en | "2018-03-22T08:00:00" ^^xsd:dateTime | "2018-03-01T20:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:Consult AndConfirm Needs | igsbpm:1.2 | "Specify Needs"@en | "2018-01-22T08:00:00" ^^xsd:dateTime | "2018-02-16T20:00:00" ^^xsd:dateTime |
| kshrts:Retail TradeStatistics | kshrts:Create FrameAndSample Process | igsbpm:4.1 | "Collect"@en | "2018-07-01T08:00:00" ^^xsd:dateTime | "2018-07-03T20:00:00" ^^xsd:dateTime |
