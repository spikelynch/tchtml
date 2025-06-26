# TinyCrate HTML generator

Python library to generate HTML previews for RO-Crates.

This uses tinycrate to 

Based on the Javascript Based [ro-crate-html-lite](https://github.com/Language-Research-Technology/ro-crate-html-lite)

ATM this makes a single HTML page for each *entire* crate and uses very minimal Javascript though you can pass in any Jinja2 template you like programmatically. Future versions may allow:

- Chunking into more pages based on heuristics like page-per entity or page per type of entity (complicated cos of multiple types)
- Static versions of maps
- Special rendering for stuff like CSV schemas

This works by creating a data structure from an RO-Crate that makes it easy to display each entity in a crate and then feeding that to a Jinja template to display. Each property for example has both its resolved URI (so it can be linked to the definition) and its label and references to other entities are pre-populated with the `name` of the target entity to make it trivial to display.


## Installation

Install [uv](https://docs.astral.sh/uv/), then

    > git clone this rep
    > cd tchtml
    > uv run src/rocrate_tabular/rocrate_tabular.py -h

`uv run` should create a local venv and install the dependencies

## Usage

    > uv run  src/tchtml/tchtml.py tests/crates/languageFamily <-- will make a preview file in the `languageFamily` dir 



### How to call from your code:
```python
from tinycrate.tinycrate import TinyCrate
import src.tchtml.tchtml as tchtml
from pathlib import Path

# Load a crate from a directory
crate_path = Path('/path/to/your/crate')
crate = TinyCrate(crate_path)

# Option 1: Generate HTML using default template and layout
html = tchtml.generate_html(crate)

# Option 2: Generate HTML with custom template and layout
template_path = Path('/path/to/custom/template.html')
layout = [
    {
        "name": "Basic Information",
        "inputs": [
            "http://schema.org/name", 
            "http://schema.org/description"
        ]
    },
    {
        "name": "Files",
        "inputs": [
            "http://schema.org/hasPart"
        ]
    }
]

html = tchtml.generate_html(crate, template_path=template_path, layout=layout)

# Option 3: Write HTML directly to a file
output_path = crate_path / 'ro-crate-preview.html'
tchtml.write_html_preview(crate, output_path)