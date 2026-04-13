 Week 4 Exercises: NLP Capstone Launchpad

 Objectives
Finalize your NLP model and prepare it for a professional demonstration.

 1. The Interface Challenge (Gradio)
- Use Gradio to create a demo for your model.
- If your model is a classifier, use the `Label` output component.
- If your model is a search engine, use the `Dataframe` or `HighlightedText` component.
- Goal: Make the UI self-explanatory for a non-technical user.

 2. Model "Diet" (Quantization)
- Use the `optimum` library or basic PyTorch tools to convert your model to 8-bit integers.
- Compare:
    - Model File Size (Before vs After).
    - Inference Time for a 50-word sentence.
    - Accuracy (did it drop significantly?).

 3. README Storytelling
- Write a 3-paragraph summary for your `README.md`.
- Paragraph 1: The human problem (e.g., "Language barriers in medical care").
- Paragraph 2: Your AI solution (e.g., "A translation model for medical terms").
- Paragraph 3: The proof (e.g., "Achieved a ROUGE score of 0.45").

 4. Failure Mode Analysis
- Intentionally find 3 "edge cases" where your model fails (e.g., extremely long sentences, heavy slang, or code-switching).
- Document these in a "Limitations" section of your final report.

 5. Final Pitch
- Prepare a 3-slide deck:
    1. Problem: Use a real-world example.
    2. Solution: Show your Gradio interface screenshot.
    3. Results: Compare your Transformer model to your baseline (TF-IDF).

---
