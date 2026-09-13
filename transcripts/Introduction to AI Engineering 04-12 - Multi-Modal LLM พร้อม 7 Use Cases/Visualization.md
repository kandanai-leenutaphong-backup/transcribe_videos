# Multimodal LLMs with 7 Use Cases

**Visual goal:** Map text, image, audio, and video inputs to seven demonstrated Gemini workflows and the specialist models that may be better for production.

[Read the detailed summary](./Summary.md)

## Multimodal input and output map

```mermaid
flowchart LR
    T["Text instruction"] --> M["Multimodal model"]
    I["Image"] --> M
    A["Audio"] --> M
    V["Video or YouTube URL"] --> M
    M --> TO["Text output"]
    M --> IO["Image output"]
    TO --> E["Extraction, caption, Q and A, transcript, summary"]
    IO --> G["Image generation or editing"]
```

A model is multimodal because it handles multiple data formats, but each model supports its own specific input and output combinations.

## Seven use cases at a glance

```mermaid
flowchart TB
    M["Gemini multimodal lesson"]
    M --> I["Image"]
    M --> A["Audio"]
    M --> V["Video"]
    I --> U1["1 Thai receipt extraction"]
    I --> U2["2 Image Q and A or caption"]
    I --> U3["3 Object detection"]
    I --> U4["4 Image editing"]
    A --> U5["5 Thai transcription"]
    A --> U6["6 Direct audio Q and A"]
    V --> U7["7 Video summarization"]
```

| Use case | Input | Output | Important engineering check |
|---|---|---|---|
| Thai receipt extraction | Image plus prompt | `item` and `amount` list | Verify Thai text and prices |
| Image caption | Poster plus prompt | Concise grounded caption | Avoid assumptions |
| Object detection | Car image | Boxes and confidence | Compare roughly 49-second LLM call with YOLO |
| Image editing | Room image plus instruction | Red-sofa image | Inspect missing details and artifacts |
| Audio transcription | Thai audio plus instruction | Thai transcript | Compare with Whisper V3 Turbo |
| Direct audio Q&A | Audio plus question | Direct answer | Decide whether reusable text is needed |
| Video summary | YouTube URL or video | Source-grounded summary | Restrict output to original content |

> **Mental model:** A multimodal LLM is a generalist with several senses. OCR, YOLO, and Whisper are focused specialists. Select the generalist when flexibility helps, and select the specialist when speed, cost, or scale dominates.

## Image workflow choices

```mermaid
flowchart TD
    A["Receive an image"] --> B{"What is the goal?"}
    B -->|Extract fields| C["Prompt with structured schema"]
    C --> D["Validate against source"]
    B -->|Describe image| E["Request concise grounded caption"]
    E --> F["Use for search or Q and A"]
    B -->|Detect objects| G["Return boxes and confidence"]
    G --> H["Benchmark against YOLO"]
    B -->|Edit image| I["Generate requested visual change"]
    I --> J["Review artifacts"]
```

## Audio design decision

```mermaid
flowchart TD
    A["Thai audio"] --> B{"What does the application need?"}
    B -->|Stored searchable text| C["Transcribe"]
    C --> D["Save and analyze transcript"]
    B -->|Answer one question| E["Direct audio Q and A"]
    E --> F["One multimodal model call"]
    B -->|Live conversation| G["Real-time audio API"]
    C --> H["Compare Gemini with Whisper V3 Turbo"]
```

## Video summarization sequence

```mermaid
sequenceDiagram
    participant U as User
    participant G as Gemini
    participant V as VideoSource
    U->>G: URL plus source-only summary prompt
    G->>V: Read video content
    V-->>G: Audio and visual information
    G->>G: Organize original content
    G-->>U: Comprehensive grounded summary
```

## Model-selection path

```mermaid
flowchart TD
    A["Define input and required output"] --> B["Check model modality support"]
    B --> C["Run a representative sample"]
    C --> D["Measure accuracy"]
    C --> E["Measure latency"]
    C --> F["Estimate cost and scale"]
    D --> G{"Generalist or specialist?"}
    E --> G
    F --> G
    G --> H["Gemini multimodal"]
    G --> I["OCR, YOLO, Whisper, or another specialist"]
    H --> J["Add source and artifact verification"]
    I --> J
```

## Visual learning path

1. Identify the input modality and the exact output the application needs.
2. Walk through the seven use cases from image extraction to video summary.
3. Notice where structured output connects perception to software.
4. Compare one-step multimodal workflows with specialist pipelines.
5. Add validation for Thai text, coordinates, generated artifacts, and source faithfulness.

## Check your understanding

1. Why does multimodal not mean that every model supports every input and output?
2. What does structured output add to Thai receipt extraction?
3. Why might YOLO be preferable to Gemini for object detection at scale?
4. When is direct audio Q&A better than creating a transcript first?
5. Which prompt constraint helps prevent unsupported claims in a video summary?
