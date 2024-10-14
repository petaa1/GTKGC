# Geological Temporal Knowledge Graph Construction Assisted by LLMs and Ontologies

This project focuses on constructing a Geological Temporal Knowledge Graph (GTKG) using a combination of large language models (LLMs) and ontologies to extract and organise geological information from unstructured exploration reports. The approach leverages Named Entity Recognition (NER), prompt engineering, and knowledge graph construction techniques to create a structured representation of geological data with temporal context.

## WAMEX Dataset

The main dataset used for the extraction of geological information in this project is the [WAMEX (Western Australia Mineral Exploration)](https://www.dmp.wa.gov.au/Geological-Survey/Mineral-exploration-Reports-1401.aspx) database. WAMEX contains a comprehensive collection of exploration reports that provide insights into mineral deposits, geological formations, and temporal aspects of geological events in Western Australia. 


## Installation

1. **Install the neccessary dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

## Configuration

You may need to set up a `.env` file in the root directory of the project. This file should contain any necessary configuration variables such as API keys and database connections.

You can create a `.env` file in the root directory using the provided `.env.sample` file as a template, then replace the environment variables with your own. To create the file:

1. **Copy the Sample File**:
   ```bash
   cp .env.sample .env
   ```

2. **Edit the .env File**: Open the newly created `.env` file and fill in the required configuration variables. Ensure to replace the placeholders with your actual values:

   ```bash
   api_key=""               # Your API key for gpt-4o mini
   URI_neo4j=""             # The URI for your Neo4j database 
   USER_neo4j=""            # Your Neo4j database username.
   PASSWORD_neo4j=""        # Your Neo4j database password.
   ```

## Project Structure

The main Jupyter notebooks in this repository are the various stages to construct the GTKG. The following files are to be runned in order to achieve the desired results:

### 1. [`6-GeoEntity_NER_Model.ipynb`](https://github.com/petaa1/GTKGC/blob/main/src/NER/6-GeoEntity_NER_Model.ipynb)

- **Description**: Fine-tunes a T5 model for recognising geological entities. The model is trained to identify six main entity types: Rock, Mineral, Ore Deposit, Stratigraphy, Location, and Geological Timescale.
- **Training Data**: The NER model was trained using data sourced from the article **"Auto-labelling entities in low-resource text: a geological case study"** by [Enkhsaikhan et al. (2021)](https://doi.org/10.1007/s10115-020-01532-6). This dataset consists of geological information extracted from WAMEX reports. Additionally, the domain dictionary labels used in this project were also obtained from the same paper.


### 2. [`Combined_NER.ipynb`](https://github.com/petaa1/GTKGC/blob/main/src/NER/Combined_NER.ipynb)

- **Description**: Preprocesses the text and integrates the outputs of three different NER models. It combines them into a unified BIO tagging format to create a single output for each sentence

### 3. [`KGC_LLM_Ontology.ipynb`](https://github.com/petaa1/GTKGC/blob/main/src/KGC/KGC_LLM_Ontology.ipynb)

   - **Description**: This notebook employs prompt engineering combined with an ontology to guide the LLM in extracting subject-relation-object triples. It performs additional cleaning and validation against the ontology and NER results to produce the final set of triples for the knowledge graph.


## Results

### Comparison of Total and Unique Entity Counts

Tables below summarise the counts of entities and relations identified.

- **Table 1** presents the total and unique entity counts across the NER models, ontology-cleaned KG, and the final KG, highlighting the significant reduction in entities during the cleaning process.
  
- **Table 2** presnts the total and unique relation counts for both the ontology-cleaned KG and the final KG, demonstrating a decrease in relations after filtering for relevance and accuracy.

These tables illustrate the effectiveness of the ontology and NER models in refining the geological knowledge graph.

**Column Definitions:**
- **NER Models**: Entities identified by the three NER models.
- **Ontology-Cleaned KG**: LLM-generated KG cleaned only by ontology.
- **Final Model KG**: LLM-generated KG cleaned by both ontology and NER model results.


### Total and Unique Entity Counts

| Entity Type              | NER Models Total | NER Models Unique | Ontology-Cleaned Total | Ontology-Cleaned Unique | Final Model KG Total | Final Model KG Unique |
|--------------------------|------------------|-------------------|------------------------|-------------------------|----------------------|-----------------------|
| Location                 | 2951             | 1174              | 5758                   | 2073                    | 2784                 | 1003                  |
| Rock                     | 2630             | 448               | 7997                   | 1992                    | 3352                 | 578                   |
| Mineral                  | 2123             | 258               | 3055                   | 525                     | 1505                 | 205                   |
| Ore Deposit              | 288              | 63                | 1191                   | 478                     | 279                  | 84                    |
| Stratigraphy             | 718              | 311               | 1337                   | 401                     | 790                  | 252                   |
| Geological Timescale     | 296              | 112               | 439                    | 268                     | 194                  | 65                    |
| Date                     | 1981             | 671               | 1809                   | 637                     | 400                  | 160                   |
| **Total**                | **10729**        | **3017**          | **19676**              | **4674**                | **8350**             | **2862**              |

### Total and Unique Relation Counts

| Relation                 | Ontology-Cleaned Total | Ontology-Cleaned Unique | Final Model KG Total | Final Model KG Unique |
|--------------------------|-------------------------|-------------------------|----------------------|-----------------------|
| foundIn                  | 3310                    | 3060                    | 1360                 | 1209                  |
| locatedNear              | 845                     | 821                     | 444                  | 427                   |
| contains                 | 3863                    | 3395                    | 1889                 | 1559                  |
| LocatedIn                | 379                     | 346                     | 273                  | 248                   |
| foundAt                  | 1809                    | 1647                    | 399                  | 358                   |
| hasAge                   | 439                     | 412                     | 192                  | 167                   |
| isPartOf                 | 53                      | 48                      | 34                   | 34                    |
| underlies                | 46                      | 44                      | 29                   | 27                    |
| overlies                 | 49                      | 45                      | 32                   | 28                    |
| **Total**                | **6930**                | **6319**                | **3838**             | **3018**              |
