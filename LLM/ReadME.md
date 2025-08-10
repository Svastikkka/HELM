# LLM Deployment Repository


# LLM Models

- unsloth/Llama-4-Scout-17B-16E-Instruct
- hugging-quants/Meta-Llama-3.1-70B-Instruct-AWQ-INT4
- meta-llama/Llama-3.3-70B-Instruct
- Team-ACE/ToolACE-8B
- vital-ai/watt-tool-70B-awq 
- watt-ai/watt-tool-70B
- mistralai/Mistral-7B-Instruct-v0.3
- Salesforce/Llama-xLAM-2-8b-fc-r
- Salesforce/xLAM-2-32b-fc-r
- Salesforce/Llama-xLAM-2-70b-fc-r
- watt-ai/watt-tool-8B


# Commands
### Deploy ToolACE 8B
```bash
helm upgrade --install toolace LLM/ -f LLM/override-gke-toolace-8B.yaml -n dev --wait  --timeout 20m
```

### Deploy Llama-3.3-70B-Instruct
```bash
helm upgrade --install toolace-llama3-3 LLM/ -f LLM/override-gke-meta-llama-Llama-3.3-70B.yaml -n dev --wait --timeout 20m
```

### Deploy Mistral-7B-Instruct-v0.3
```bash
helm upgrade --install toolace-mistral LLM/ -f LLM/override-gke-Mistral-7B-Instruct-v0.3.yaml -n dev --wait --timeout 20m
```

### Deploy Llama-xLAM-2-8b-fc-r
```bash
helm upgrade --install toolace-salesforce LLM/ -f LLM/override-gke-salesforce-xlam8b.yaml -n dev --wait  --timeout 20m
```

### Deploy Qwen/Qwen3-30B-A3B 
```bash
helm upgrade --install toolace-qwen-30b LLM/ -f LLM/override-gke-qwen-qwen3-30b-a3b.yaml -n dev --wait  --timeout 20m
```

### Deploy CohereLabs/c4ai-command-r-v01
```bash
helm upgrade --install toolace-coherelabs LLM/ -f LLM/override-gke-coherelabs-c4ai-command-r-v01.yaml -n dev --wait  --timeout 20m
```