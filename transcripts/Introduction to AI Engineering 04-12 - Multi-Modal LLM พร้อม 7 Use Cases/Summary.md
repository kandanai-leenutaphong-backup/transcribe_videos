# Introduction to AI Engineering 04.11: Multimodal LLMs with 7 Use Cases

**Format:** Thai-language hands-on walkthrough with English multimodal terminology  
**Source:** `transcript.txt` in this lesson directory  
**Primary technology:** Google Gemini multimodal models  
**Thai lesson title:** `Multi-Modal LLM พร้อม 7 Use Cases`

## Overview

This lesson introduces **Multimodal Models**, which can receive data in formats such as text, images, audio, and video, then generate one or more output formats depending on the model.

The walkthrough uses Google Gemini and covers seven practical use cases:

1. Thai receipt extraction
2. Image question answering and captioning
3. Object detection
4. Image generation and editing
5. Audio transcription
6. Direct question answering over audio
7. Video summarization

The instructor also points to real-time voice as an extension and repeatedly warns that a Large Language Model is not always the best engineering choice. OCR, YOLO, and Whisper V3 Turbo may be cheaper or faster for specialized tasks.

## 1. What Is a Multimodal Model?

A multimodal model accepts more than one data format, including:

- Text
- Image
- Audio
- Video

Depending on its training and supported capabilities, it may generate:

- Text
- Image
- Audio
- Video

Not every multimodal model supports every input and output combination. One may accept text plus image and return text only. Another may support image generation or speech.

The lesson focuses on common industry tasks rather than every possible modality combination.

## 2. Exercise Setup

The course exercise is organized into four folders:

- Audio understanding
- Image understanding
- Image generation
- Video understanding

Each folder includes a `requirements.txt`.

General workflow:

1. Install the dependencies for the selected exercise.
2. Obtain a Google Gemini API key.
3. Configure the key.
4. Open the notebook.
5. Run each cell and inspect the output.

The instructor uses Google AI documentation as a reference for text generation, image understanding, image generation, speech, and other multimodal capabilities.

## 3. Use Case 1: Thai Receipt Extraction

The first image-understanding exercise loads a Thai receipt from the internet and asks the model to extract item names and amounts.

The output is forced into a structured format with fields such as:

- `item`
- `amount`

The model returns a list corresponding to the Thai product names and prices on the receipt. Structured output is valuable because later application logic can process the result directly.

Potential Thai extensions include:

- PromptPay slips
- Payment receipts
- Other Thai-language transaction documents

The instructor stresses that **OCR** is another valid technology. The engineer should compare accuracy, price, and operational fit rather than selecting an LLM automatically.

## 4. Use Case 2: Image Q&A and Captioning

The next example uses a blue and yellow advertising poster. The prompt asks for a concise caption without assumptions.

The generated caption identifies visible elements such as:

- A promotion of up to 50 percent off
- A smiling woman
- Social media icons
- A website address

Captions can support:

- Search and embedding
- Document enrichment
- Accessibility
- Direct question answering about an image

The important prompt behavior is to describe visible content while avoiding unsupported assumptions.

## 5. Use Case 3: Object Detection

Gemini 2.5 Flash is asked to find four cars and return structured bounding-box information:

- `x`
- `y`
- Width
- Height
- Confidence

The resulting coordinates are used to draw boxes around the four cars.

This demonstrates that a multimodal LLM can perform approximate object detection, but the instructor does not present it as the default production solution. The call takes roughly **49 seconds**. A smaller specialized model such as **YOLO** can be much faster for large-scale detection.

## 6. Use Case 4: Image Generation and Editing

The image-editing demo starts with a room image and asks the model to replace the existing sofa with a red sofa.

The exercise uses a Gemini Flash preview image-generation model. The generated result changes the sofa, although some parts of the original image may be removed or altered incorrectly.

A practical example is furniture retail:

1. A customer uploads a room photo.
2. The system applies a product or color change.
3. The customer previews how the room could look.
4. The generated image is reviewed for artifacts.

This can improve customer imagination, but generated edits require visual quality checks.

## 7. Use Case 5: Audio Transcription

The instructor records a short Thai audio clip with QuickTime and sends it to Gemini with:

```text
Please transcribe the audio file.
```

The model receives both the text instruction and the audio file, then returns Thai text.

A common Thai use case is call-center transcription, where spoken conversations are converted into text for later analysis.

The instructor compares the multimodal LLM with specialized speech-to-text systems such as **Whisper V3 Turbo**. A dedicated model may offer a better price, speed, or deployment fit.

## 8. Use Case 6: Direct Q&A over Audio

Instead of transcribing first and sending the transcript to another LLM, the user can send the audio and a question directly to Gemini.

Two-step design:

```text
Audio -> Speech-to-Text -> Transcript -> LLM -> Answer
```

Direct multimodal design:

```text
Audio + Question -> Gemini -> Answer
```

The direct design may answer faster because it removes an intermediate step. Transcription remains useful when the text must be stored, searched, audited, or analyzed later.

The lesson also mentions Gemini and ChatGPT real-time audio APIs as a path toward live voice conversations.

## 9. Use Case 7: Video Summarization

The final demonstration sends Gemini a YouTube link for a video approximately titled `What Is Hugging Face in 9 Minutes?`

The prompt asks the model to:

- Summarize the content comprehensively.
- Mention only original video content.
- Avoid assumptions.

Gemini returns a structured summary of the source. The model can also receive an uploaded video file instead of only a YouTube URL.

Possible applications include:

- Video summaries
- Search indexing
- Content discovery
- Question answering over recordings

Faithfulness instructions are important because the summary should remain grounded in the original video.

## 10. Selecting a Generalist or Specialist

| Task | Demonstrated generalist | Specialized alternative | Key decision factors |
|---|---|---|---|
| Receipt extraction | Gemini multimodal | OCR | Thai accuracy, schema, cost |
| Image caption | Gemini multimodal | Vision caption model | Quality and grounding |
| Object detection | Gemini 2.5 Flash | YOLO | Latency and scale |
| Image editing | Gemini image generation | Specialized image model | Artifacts and visual quality |
| Transcription | Gemini multimodal | Whisper V3 Turbo | Accuracy, speed, storage needs |
| Audio Q&A | Gemini multimodal | STT plus LLM | One-step speed versus reusable transcript |
| Video summary | Gemini multimodal | Video pipeline plus LLM | Source faithfulness and input support |

## Practical Exercises

- Run each notebook cell after installing its folder's requirements.
- Extract `item` and `amount` from another Thai receipt and verify every field.
- Extend the receipt schema only for fields actually visible in the source.
- Caption a new poster with an explicit `do not make assumptions` instruction.
- Detect objects and inspect coordinates, confidence, and elapsed time.
- Compare Gemini object detection latency with YOLO.
- Edit furniture color and list all unintended visual changes.
- Record Thai audio and compare Gemini transcription with Whisper V3 Turbo.
- Ask a question directly over audio and compare it with a two-step transcription pipeline.
- Summarize a YouTube link or uploaded video while restricting the answer to source content.

## Takeaways / Action Items

- Define required input and output modalities before choosing a model.
- Verify the exact modality combinations supported by each model.
- Use structured output when visual extraction feeds application logic.
- Preserve Thai item names, payment context, and spoken language accurately.
- Compare multimodal LLMs with OCR, YOLO, and Whisper V3 Turbo.
- Measure latency, cost, accuracy, and scale instead of relying on novelty.
- Review generated image edits for removed details and other artifacts.
- Use direct audio Q&A when a persistent transcript is unnecessary.
- Keep transcription when search, audit, storage, or later analysis requires text.
- Prompt video summaries to use only original content and avoid assumptions.
