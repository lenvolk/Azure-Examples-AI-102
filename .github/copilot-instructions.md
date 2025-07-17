# Azure AI-102 Examples - Copilot Instructions

This repository contains hands-on examples for Azure AI services, organized by service category. Each directory demonstrates practical implementations using Jupyter notebooks.

## Repository Structure

- **Service-specific directories**: Each Azure AI service has its own directory (e.g., `azure-ai-search/`, `text-analytics/`, `translator/`)
- **Jupyter notebooks**: Primary development format - all examples are in `.ipynb` files
- **Supporting data**: JSON files (`data.json`, `docVectors.json`) and assets directories contain sample data
- **Prompt Flow**: Advanced AI workflows using Azure Prompt Flow framework in `prompt-flow/math-helper/`

## Key Patterns

### Authentication Pattern
All services use Azure Key Credential authentication with this consistent pattern:
```python
from azure.core.credentials import AzureKeyCredential
credential = AzureKeyCredential(api_key)
client = ServiceClient(endpoint=endpoint, credential=credential)
```

### REST API Fallback Pattern
When Azure SDKs have issues (especially preview versions), use direct REST API calls:
```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': region,
    'Content-type': 'application/json'
}
```
See `translator/translator-failed.ipynb` vs `translator/translator-simple.ipynb` for SDK vs REST examples.

### Data Loading Pattern
Consistent JSON data loading across examples:
```python
import json
with open('data.json', 'r', encoding='utf-8') as file:
    data = json.load(file)
```

## Service-Specific Notes

### Azure AI Search
- Uses vector embeddings with OpenAI integration
- Requires both Azure AI Search and Azure OpenAI credentials
- Data structure: `[{"id": "1", "title": "...", "content": "...", "category": "..."}]`

### Translator Service
- **Avoid** the `azure-ai-translation-text` SDK (has auth issues)
- **Use** direct REST API calls to `https://api.cognitive.microsofttranslator.com`
- Language codes: Use ISO codes (e.g., `"ja"` for Japanese, not `"jp"`)

### Text Analytics
- Works with file collections in `assets/` subdirectories
- Pattern: Load multiple text files and process as batch

### Prompt Flow
- Uses YAML configuration files (`flow.dag.yaml`)
- Jinja2 templates for prompt engineering (`chat.jinja2`)
- Connection management via `pf connection create` commands
- Interactive testing: `pf flow test --flow <folder> --interactive`

## Development Workflow

1. **Credential Setup**: Replace placeholder keys (`###`, `"YOUR_KEY_HERE"`) with actual Azure service keys
2. **Package Installation**: Use `%pip install` in notebooks for dependencies
3. **Data Preparation**: Most examples include sample data files - use these for testing
4. **Error Handling**: Check for both SDK exceptions and HTTP response errors
5. **Testing**: Run notebooks cell-by-cell to verify each step

## Common Issues

- **Authentication errors**: Often SDK issues - try REST API approach
- **Missing imports**: Add `from azure.core.exceptions import HttpResponseError` for proper error handling
- **Language codes**: Use correct ISO language codes in translation services
- **Endpoint formats**: Remove trailing slashes from endpoints when switching between SDK and REST

## File Conventions

- Notebooks follow `service-name.ipynb` or `service-name-variant.ipynb` naming
- Data files are JSON format with consistent structure
- Configuration files use YAML format (Prompt Flow)
- Assets stored in `assets/` subdirectories with descriptive names
