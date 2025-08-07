# GPT-OSS Model Integration

A Python implementation for running OpenAI's gpt-oss open-source models locally using Ollama, with support for chat completions, function calling, and chain-of-thought reasoning.

## Features

- **Local Model Deployment**: Install and run gpt-oss models using Ollama
- **OpenAI-Compatible API**: Use familiar OpenAI client syntax for chat completions
- **Function Calling**: Built-in support for tool use and function calling
- **Chain of Thought**: Access detailed reasoning traces from the models
- **Variable Reasoning Levels**: Configure reasoning effort (low/medium/high)
- **Performance Monitoring**: Track token usage, response times, and reasoning quality

## Quick Start

### 1. Install Ollama and Dependencies

```bash
# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Install system dependencies
sudo apt update && sudo apt install -y pciutils lshw

# Pull the 20B parameter model
ollama pull gpt-oss:20b

# Install Python dependencies
pip install openai
```

### 2. Basic Chat Example

```python
from openai import OpenAI

client = OpenAI(
    base_url="http://localhost:11434/v1",
    api_key="ollama"  # Dummy key
)

response = client.chat.completions.create(
    model="gpt-oss:20b",
    messages=[
        {"role": "system", "content": "You are a helpful assistant. Reasoning: low"},
        {"role": "user", "content": "Explain quantum computing in simple terms."}
    ]
)

print(response.choices[0].message.content)
```

### 3. Function Calling Example

```python
# Define tools
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current weather in a given city",
        "parameters": {
            "type": "object",
            "properties": {
                "city": {
                    "type": "string",
                    "description": "The city name"
                }
            },
            "required": ["city"]
        }
    }
}]

# Make request with tools
response = client.chat.completions.create(
    model="gpt-oss:20b",
    messages=[{"role": "user", "content": "What's the weather in New York?"}],
    tools=tools,
    tool_choice="auto"
)

# Handle function calls
if response.choices[0].message.tool_calls:
    # Process tool calls and provide results back to model
    # See notebook for complete implementation
```

## Advanced Features

### Chain of Thought Reasoning

Access detailed reasoning traces:

```python
response = client.chat.completions.create(
    model="gpt-oss:20b",
    messages=[
        {"role": "system", "content": "Reasoning: high"},
        {"role": "user", "content": "Solve this step by step: 2x + 5 = 13"}
    ]
)

# Access reasoning trace
reasoning = response.choices[0].message.reasoning
print(f"Reasoning: {reasoning}")
```

### Performance Monitoring

Track model performance with the included statistics function:

```python
def chat_with_stats(model, messages):
    start_time = time.time()
    response = client.chat.completions.create(model=model, messages=messages)
    
    # Print performance metrics
    usage = response.usage
    print(f"🔢 Tokens: {usage.prompt_tokens} + {usage.completion_tokens} = {usage.total_tokens}")
    print(f"🤖 Model: {response.model}")
    print(f"✅ Status: {response.choices[0].finish_reason}")
    
    return response
```

## Model Specifications

### Available Models
- **gpt-oss-20b**: 20.9B parameters (12.8GB checkpoint)
- **gpt-oss-120b**: 116.8B parameters (60.8GB checkpoint)

### Key Capabilities
- **Reasoning**: Variable effort reasoning with chain-of-thought
- **Tool Use**: Web browsing, Python execution, custom functions
- **Coding**: Strong performance on programming tasks
- **Math**: Excellent mathematical reasoning capabilities
- **Multilingual**: Support for 14+ languages

### System Requirements
- **RAM**: Minimum 16GB for gpt-oss-20b, 80GB for gpt-oss-120b
- **GPU**: Optional but recommended for faster inference
- **Storage**: ~15GB for 20B model, ~65GB for 120B model

## Configuration

### Reasoning Levels
Control the depth of reasoning by setting the system prompt:

```python
# Low reasoning (faster, less detailed)
{"role": "system", "content": "You are a helpful assistant. Reasoning: low"}

# High reasoning (slower, more thorough)
{"role": "system", "content": "You are a helpful assistant. Reasoning: high"}
```

### Harmony Chat Format
The models use a custom chat format with special roles:
- `System`: Highest priority instructions
- `Developer`: Function definitions and developer instructions  
- `User`: User messages
- `Assistant`: Model responses
- `Tool`: Tool execution results

## Examples

The repository includes a Jupyter notebook (`gptoss.ipynb`) with comprehensive examples:

1. **Basic Setup**: Installing Ollama and pulling models
2. **Simple Chat**: Basic question-answering
3. **Chain of Thought**: Detailed reasoning examples
4. **Function Calling**: Weather API integration example
5. **Performance Analysis**: Token usage and timing statistics

## Model Performance

Based on evaluations in the model card:

| Benchmark | gpt-oss-20b | gpt-oss-120b | Comparison |
|-----------|-------------|--------------|------------|
| AIME 2025 | 98.7% | 97.9% | Competitive with frontier models |
| GPQA Diamond | 71.5% | 80.1% | Strong scientific reasoning |
| MMLU | 85.3% | 90.0% | Excellent general knowledge |
| SWE-Bench | 60.7% | 62.4% | Good coding capabilities |

## License

The gpt-oss models are released under the Apache 2.0 license, making them suitable for both research and commercial use.

## Contributing

Feel free to submit issues and enhancement requests! This implementation focuses on practical usage of the gpt-oss models with clean, documented examples.

## Acknowledgments

- OpenAI for releasing the gpt-oss models
- Ollama team for the excellent local model serving infrastructure
- The open-source community for making this possible

---

*This implementation demonstrates the capabilities of OpenAI's gpt-oss models in a practical, easy-to-use format. The models show impressive performance across reasoning, coding, and general knowledge tasks while being fully open-source.*