# Week 4: Model Comparison

Tested 4 AI models on 5 cybersecurity text samples to evaluate their suitability for the alert classification and triage component of our Cybersecurity Alert Analysis capstone project.

## Models Tested
- HF Sentiment (distilbert-sst-2)
- HF Zero-Shot (bart-large-mnli)
- HF NER (bert-large-NER)
- Groq Llama 3 8B

## Finding
Recommended Groq Llama 3 8B for the alert classification component because it provides accurate severity levels with plain-English reasoning, making alerts immediately actionable for security analysts.

See `report.md` for full analysis.
