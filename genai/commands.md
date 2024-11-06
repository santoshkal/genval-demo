# Commands to interact with genval GenAI features

- Generate a Dockerfile for a simple web Nginx web serve:

`genval genai -c ./genAI-config/dockerfile-config.yaml`

## Note: Regex implementation is in testing phase in pre-main branch

- Generate Regex polcies for GenAI:

`genval genai -c ./genAI-config/regex-config.yaml`

- Generation of Rego Policies using Genai

`genval genai -c ./genAI-config/rego-config.yaml`

- Generation of Cuelang Definitions using Genai

`genval genai -c ./genAI-config/cue-config.yaml`

- Generation of CEL policies using Genai

`genval genai -c ./genAI-config/cel-config.yaml`
