# Introduction to AI Engineering 04.11: Introduction to Multimodalities

**Format:** Thai-language lecture with English technical terminology  
**Source:** `introduction_to_ai_engineering_0411_-_introduction_to_multimodalities (360p).mp4`

## Overview

This session introduces **multimodal models**, which can receive and generate information in more than one data format. The examples use Google Gemini to work with text, images, audio, and video.

Four exercise areas are demonstrated:

1. Image understanding
2. Image generation and editing
3. Audio understanding
4. Video understanding and summarization

The instructor repeatedly emphasizes that a Large Language Model is not always the only or best solution. OCR, object detection, and speech-to-text models may be faster or cheaper depending on the use case.

## 1. What Is a Multimodal Model?

A multimodal model can accept inputs such as:

- Text
- Images
- Audio
- Video

Depending on how it was trained, it may generate:

- Text
- Images
- Audio
- Video

Not every model supports every combination. One model might accept text and images but produce only text, while another may generate images or audio.

The session focuses on common industry patterns rather than attempting every possible input and output combination.

## 2. Exercise Setup

The course materials contain four exercise folders:

- Audio understanding
- Image understanding
- Image generation
- Video understanding

Each folder contains its own `requirements.txt`. The general setup is:

1. Install the dependencies for the selected exercise.
2. Obtain and configure a Google Gemini API key.
3. Open the corresponding notebook.
4. Run the notebook cell by cell and inspect the outputs.

The instructor points learners to Google AI documentation for further examples such as text generation, image understanding, image generation, and speech-related capabilities.

## 3. Image Understanding

### Thai Receipt Extraction

The first example uses a Thai receipt downloaded from the internet. The prompt asks Gemini to extract item names and amounts.

The result is constrained to a structured output containing fields such as:

- `item`
- `amount`

The model returns a list that corresponds to the receipt's Thai item names and prices. Structured output is important because it makes the extraction easier to pass into later application logic.

Possible extensions include extracting information from Thai payment slips or PromptPay receipts.

The instructor notes that a multimodal LLM is not the only technology for this task. Traditional or cloud-based **OCR** may be cheaper or otherwise more suitable, so the engineering choice should compare accuracy, cost, and operational needs.

### Image Question Answering and Captioning

The next example sends an advertising poster to the model and asks for a concise caption. The returned description identifies details such as:

- Blue and yellow visual design
- A promotion of up to 50 percent off
- A smiling woman
- Social media icons
- A website address

Image captions can support document enrichment, embeddings, search, accessibility, or direct question answering about visual content.

### Object Detection

Gemini 2.5 Flash is asked to detect four cars in an image and return structured bounding-box information such as:

- `x`
- `y`
- Width
- Height
- Confidence

The result identifies the four cars and draws boxes around them.

This demonstrates that an LLM can perform basic object detection, but the instructor does not recommend it as the default large-scale solution. A smaller specialized model such as **YOLO** can be much faster. The demonstrated LLM call takes roughly 49 seconds, which highlights the performance tradeoff.

## 4. Image Generation and Editing

The image editing example provides a room photo and asks the model to replace the sofa with a red sofa.

The exercise uses a Gemini Flash preview image-generation model. The output changes the sofa color, although some visual errors or removed details may appear.

A practical business use case is furniture sales. A customer could preview how a room might look with a different sofa color, helping them imagine the product before purchasing.

The example shows the basic pattern:

1. Provide the original image.
2. Add a text instruction describing the desired edit.
3. Ask the model to generate the modified image.
4. Review the result for artifacts or unintended changes.

## 5. Audio Understanding

### Transcription

The instructor records a short Thai audio clip using QuickTime and asks the model:

```text
Please transcribe the audio file.
```

The model receives both the instruction and audio file, then returns a Thai transcript. This is presented as a common use case in Thailand, particularly for transcribing call-center conversations so the text can be analyzed later.

The instructor again compares alternatives. A dedicated speech-to-text model such as **Whisper V3 Turbo** may be appropriate instead of a general multimodal LLM.

### Direct Q&A over Audio

The same audio file can be sent directly to Gemini together with a user question. This avoids a two-step pipeline where the system first transcribes the entire file and then sends the transcript to another model.

For some questions, direct audio Q&A can respond faster because it uses one model interaction rather than separate transcription and reasoning stages.

### Real-Time Voice

The session briefly points to real-time audio APIs from Gemini and ChatGPT. These APIs can extend the same ideas into live spoken conversations rather than offline audio-file analysis.

## 6. Video Understanding and Summarization

The final demonstration asks Gemini to summarize a YouTube video titled approximately "What Is Hugging Face in 9 Minutes?"

The prompt instructs the model to:

- Summarize the video comprehensively.
- Mention only content from the original video.
- Avoid assumptions.

Gemini accepts the YouTube link and returns a structured explanation of the video's content. The same approach can also work with an uploaded video file rather than a YouTube URL.

This pattern can support:

- Video summaries
- Content indexing
- Search preparation
- Question answering over recorded material

As with the other examples, prompts should explicitly prohibit unsupported assumptions when factual faithfulness matters.

## Practical Exercises

- Run Thai receipt extraction and verify every item and amount against the source image.
- Change the structured output schema to include additional receipt fields when present.
- Generate a concise caption for another image and compare it with direct image Q&A.
- Run object detection and inspect bounding-box coordinates and confidence values.
- Edit a product image, such as changing furniture color, and review visual artifacts.
- Record a short Thai audio file and compare direct Gemini transcription with a specialized speech-to-text model.
- Ask questions directly about an audio file without first creating a transcript.
- Summarize a YouTube link or uploaded video with instructions not to make assumptions.

## Takeaways / Action Items

- Choose a multimodal model based on its supported input and output formats.
- Use structured output when extracted image information will feed another system.
- Preserve Thai text and context when processing Thai receipts, payment slips, calls, or recordings.
- Compare multimodal LLMs with OCR, YOLO, and Whisper rather than assuming the LLM is always best.
- Evaluate latency, cost, accuracy, and scale for each modality.
- Review generated images for artifacts before showing them to customers.
- Use direct audio or video Q&A when it removes unnecessary processing steps.
- Write prompts that explicitly restrict summaries to source content when hallucination would be harmful.
