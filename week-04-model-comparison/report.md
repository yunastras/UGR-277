# Model Comparison Report — Week 4

**Name:** Gatsdaporn Sayananon
**Date:** March 16, 2026
**Capstone Project:** Cybersecurity Alert Analysis
**My Component:** Automated alert classification and triage

## Test Setup

**Input dataset:** 5 cybersecurity text samples covering:
- 2 clearly concerning/high-severity records (unauthorized login from Moscow, multiple failed SSH attempts from Beijing)
- 1 ambiguous/edge case record (phishing email with spoofed Amazon domain)
- 2 routine/benign records (firewall rule update, normal system resource utilization)

**Models tested:**
1. distilbert-base-uncased-finetuned-sst-2-english (sentiment)
2. facebook/bart-large-mnli (zero-shot classification)
3. dslim/bert-large-NER (named entity recognition)
4. Groq Llama 3 8B (LLM classification)

**Evaluation criteria:** label accuracy, confidence score, speed, ease of integration in n8n

## Results Summary

| Record | Sentiment | Zero-Shot | NER Entities | Groq |
|--------|-----------|-----------|-------------|------|
| 1. Unauthorized login from Moscow | NEGATIVE (0.9961) | possible anomaly (0.9021) | LOC: Moscow | HIGH |
| 2. Routine firewall rule update | NEGATIVE (0.9986) | routine activity (0.9961) | {} | INFORMATIONAL |
| 3. Phishing email with spoofed Amazon domain | NEGATIVE (0.9959) | possible anomaly (0.8049) | MISC: Amazon-related | HIGH |
| 4. Multiple failed SSH attempts from Beijing | NEGATIVE (0.9996) | possible anomaly (0.8571) | error | CLASSIFICATION: HIGH |
| 5. System resource utilization normal | NEGATIVE (0.9979) | routine activity (0.9713) | {} | INFORMATIONAL |

## Analysis

**Where models agreed:** Zero-Shot and Groq consistently agreed across all 5 records. Both correctly identified records 2 and 5 as routine/benign, and records 1, 3, and 4 as concerning or anomalous. This agreement across two very different model architectures (a zero-shot classifier vs. a large language model) gives confidence that these classifications are reliable.

**Where models disagreed:** The Sentiment model disagreed with all others by labeling every single record as NEGATIVE, including the two routine records. This is a fundamental mismatch — the sentiment model was trained on product and movie reviews, so any security alert text sounds "negative" regardless of whether it represents a real threat or routine maintenance.

**Most accurate model overall:** Groq's Llama 3 8B gave the most useful and accurate results. It provided severity levels (HIGH, CRITICAL, INFORMATIONAL) with detailed reasoning, making it the easiest for a security analyst to act on. Zero-Shot was a close second, correctly distinguishing threats from routine activity with high confidence scores.

**Fastest/most practical:** The HuggingFace models (Sentiment, Zero-Shot, NER) were slower due to cold-start times on the free tier, with Zero-Shot being the slowest. Groq was the fastest by far. For production use at scale, Groq's speed and output quality make it the most practical choice.

## Recommended Models for My Capstone Component

**Component:** Automated cybersecurity alert classification and triage

**Primary model:** Groq Llama 3 8B — provides severity classifications with reasoning in plain English, making it immediately actionable for security analysts without additional post-processing.

**Secondary model (if applicable):** facebook/bart-large-mnli (Zero-Shot) — useful as a lightweight, fast pre-filter to route alerts before sending to the more expensive LLM, reducing API costs at scale.

**Rejected models and why:**
- distilbert-sst-2 (Sentiment): Labels all security text as NEGATIVE regardless of actual threat level, providing no useful signal for triage. It was trained on consumer reviews, not security data.
- dslim/bert-large-NER: Useful for extracting specific entities (locations, organizations) but not suitable as a standalone classifier. It also failed on one record due to input formatting issues, and extracted false positives like "SS" from "SSH".

## Failure Cases and Limitations

The NER model failed on Record 4 ("Multiple failed SSH attempts from Beijing IP on Cloudflare production server") due to input formatting issues, returning an error instead of entities. More interestingly, on Record 4 it extracted "SS" from "SSH" as a MISC entity — a clear false positive caused by the model tokenizing the abbreviation incorrectly. This highlights a key limitation: NER models trained on general text struggle with technical abbreviations and jargon common in cybersecurity logs. In production, a domain-specific NER model fine-tuned on security data would be needed for reliable entity extraction.

The Sentiment model's complete failure to distinguish threat levels is also a significant limitation to document. All 5 records received NEGATIVE scores above 0.99, meaning the model has essentially no discriminative power for this domain. This is a reminder that model selection must be driven by domain fit, not just benchmark performance on general datasets.

## Next Steps

With more time, I would test security-specific models such as a fine-tuned BERT model trained on CVE descriptions or MITRE ATT&CK data. I would also increase the test dataset to at least 20 records to better evaluate edge cases, and test whether Groq's classifications remain consistent when the system prompt is varied. Additionally, I would explore confidence-based routing — using Zero-Shot scores to triage alerts before sending only ambiguous cases to the more expensive Groq API, which could significantly reduce costs in production.

## Reflection

The most surprising finding was how well the Zero-Shot model performed without any security-specific training. By simply providing candidate labels like "critical threat", "routine activity", and "possible anomaly", it correctly classified 4 out of 5 records with high confidence. This suggests that for early-stage prototyping, zero-shot classification can be a powerful and practical tool even in specialized domains. At the same time, Groq's detailed reasoning output showed that LLMs add value beyond just classification — the natural language explanations could help junior analysts understand why an alert was flagged, reducing the expertise barrier for security operations.
