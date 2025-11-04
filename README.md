# NER Proof of Concept (German) – with Semantic Enrichment

This repository contains a Jupyter Notebook that evaluates Named Entity Recognition (NER) on German text using:

- spaCy pretrained model: `de_core_news_lg`
- GLiNER multi-language model: `urchade/gliner_multi-v2.1` (via gliner-spacy)
- (Experiment 3) GLiNER + BERT DE NER: `fhswf/bert_de_ner`

It also integrates semantic enrichment to attach external knowledge to entities:

- PERSON → DBpedia (birth date, birth place, short description, URI)
- PLACE (GPE/LOC/place) → GeoNames (coordinates, population, feature codes, country, ID)


## Repository contents

- `NER_proof_of_concept.ipynb` – the main notebook with 3 experiments and the enrichment section
- `LICENSE` – license file


## Quick start (VS Code + Notebook)

1. Open the repository in VS Code

   - Install the Python and Jupyter extensions if prompted.
   - Select a Python interpreter (a virtual environment is recommended).

2. Open the notebook

   - Open `NER_proof_of_concept.ipynb`.
   - If asked, select the desired Python kernel.

3. Install dependencies from inside the notebook

   - Run the “Install dependencies…” cell near the top; it installs spaCy, transformers, torch, gliner-spacy and downloads the German model.

4. Run one NER experiment (to produce entities)

   - Experiment 1 (spaCy): creates variable `doc_de`.
   - Experiment 2 (GLiNER) or Experiment 3 (GLiNER + BERT): creates variable `doc`.

5. Configure semantic enrichment

   - Scroll to the “Semantic enrichment: DBpedia + GeoNames” section.
   - Run the import and function definition cells (DBpedia and GeoNames).
   - Optionally set a GeoNames username to enrich places:

     - In Windows PowerShell (current session):

       ```powershell
       $env:GEONAMES_USERNAME = "<your_geonames_username>"
       ```

     - Or in the notebook Usage cell:

       ```python
       GEONAMES_USERNAME = "<your_geonames_username>"
       ```

6. Run enrichment and view results

   - Run the enrichment display cell (added at the end):

     ```python
     enrich_and_display(geonames_username=GEONAMES_USERNAME)
     ```

   - You’ll see:
     - Original normalized NER results (entity, label, source)
     - PERSON rows enriched from DBpedia
     - PLACE rows enriched from GeoNames
     - A combined table with all enriched attributes


## Alternative setup (command line)

If you prefer to install dependencies up front (outside the notebook):

```powershell
# Create and activate a virtual environment (Windows PowerShell)
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# Install packages
pip install spacy transformers torch gliner-spacy SPARQLWrapper requests pandas accelerate

# Download German spaCy model
python -m spacy download de_core_news_lg
```

Then open the notebook and start from step 4 above.


## Notes, benefits, and challenges

- Benefits
  - Adds factual context to NER spans for people and places (e.g., bio info, coordinates).
  - Useful for downstream tasks: search faceting, geo-visualization, disambiguation.
  - Modular enrichment: functions are cached and easy to extend (e.g., ORG/Wikidata).

- Challenges
  - Ambiguity: name-only lookup can return the wrong entity; consider entity linking/disambiguation.
  - Rate limits/availability: public endpoints (DBpedia SPARQL, GeoNames) may throttle; rely on caching and backoff.
  - Credentials: GeoNames requires a username; use an environment variable rather than hard-coding in code.
  - Performance: network calls incur latency; for large corpora, consider batching/async and persistent caches.


## Troubleshooting

- No enrichment output
  - Ensure you ran at least one NER experiment so `doc` or `doc_de` exists.
  - Run the enrichment import and definition cells before the display call.
  - Execute `enrich_and_display(geonames_username=GEONAMES_USERNAME)` at the end.

- GeoNames warnings / missing place details
  - Set `GEONAMES_USERNAME` and re-run the enrichment cells and display call.

- displaCy visualization errors (IPython import issues)
  - The notebook uses an HTML rendering path that avoids known IPython 9 issues. If you modified it, set `jupyter=False` and render via `IPython.display.HTML` + `display`.


## Acknowledgments

- [spaCy](https://spacy.io)
- [GLiNER](https://github.com/urchade/GLiNER)
- [DBpedia](https://www.dbpedia.org)
- [GeoNames](https://www.geonames.org) (requires a free username for API)


## License

See `LICENSE` for details.
