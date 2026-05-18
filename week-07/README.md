
# Week 7: RAG Security Knowledge Assistant — Evaluation Report
🔗 **Live Chatbot:** https://cloud.flowiseai.com/chatbot/87afca78-db6b-450a-af1c-501b59332d93

## 1. Setup Summary
- **LLM:** llama-3.3-70b-versatile via Groq
- **Embeddings:** sentence-transformers/all-MiniLM-L6-v2 via HuggingFace Inference API
- **Vector Store:** In-Memory Vector Store
- **Documents loaded:**
  - mitre-initial-access.txt (~2 pages)
  - mitre-credential-access.txt (~2 pages)
  - mitre-lateral-movement.txt (~2 pages)

## 2. Test Results
| # | Question | Used Documents? | Quality | Notes |
|---|----------|----------------|---------|-------|
| 1 | What are common techniques for credential access according to MITRE? | Yes | Partial | Chunks retrieved but LLM responded with "I'm not sure" instead of summarizing |
| 2 | How does phishing relate to initial access in the ATT&CK framework? | Yes | Good | Named specific threat actors (AppleJeus, Axiom, Kimsuky, GOLD SOUTHFIELD) from documents |
| 3 | What is lateral movement and what techniques do attackers use? | Yes | Partial | Relevant chunks retrieved but LLM did not synthesize into a full answer |
| 4 | What is the difference between spearphishing attachment and spearphishing link? | Yes | Good | Clear accurate answer distinguishing file attachment vs URL link |
| 5 | What tools do attackers use for OS credential dumping? | Yes | Partial | Chunks retrieved but LLM responded with "I'm not sure" |

## 3. Edge Case Observations
- **Unrelated question** ("What is the weather like today?"): Returned "I'm not 
  sure" and pulled document chunks — did not hallucinate weather information. 
  RAG correctly constrained the LLM to its knowledge base.
- **Topic not in documents** ("What are the latest CVEs from 2026?"): Also 
  returned "I'm not sure" — no fabricated CVEs generated. System stayed within 
  its knowledge base.
- **Technique not uploaded** ("What techniques do attackers use for defense 
  evasion?"): Returned "I'm not sure" — retrieved chunks from other categories 
  but did not hallucinate defense evasion techniques. System correctly admitted 
  it lacked specific information on this topic.

## 4. Settings Experiments
- Not completed for this submission.

## 5. Reflection
- **What surprised you about how RAG works?**
  The most surprising aspect was how the retrieval system consistently found 
  relevant document chunks even when the LLM failed to synthesize them into a 
  complete answer. This highlighted that RAG has two distinct failure points — 
  retrieval and generation — and they can fail independently. It was also 
  interesting to see how the system handled completely unrelated questions by 
  still returning document chunks rather than hallucinating, demonstrating how 
  RAG constrains LLM behavior.

- **How could you improve this chatbot for real-world use?**
  Several improvements would make this more production-ready. First, replacing 
  the in-memory vector store with a persistent database like Pinecone or Chroma 
  would eliminate the need to re-process documents on every load. Second, 
  expanding the knowledge base with more MITRE ATT&CK categories and NIST 
  framework documentation would improve coverage. Third, tuning the prompt 
  template in the Conversational Retrieval QA Chain to instruct the LLM to 
  always summarize retrieved chunks — rather than saying "I'm not sure" — would 
  improve response quality significantly.

- **How might you use RAG in your capstone project?**
  RAG would be highly valuable in a capstone project involving threat 
  intelligence or incident response. For example, a security analyst assistant 
  could use RAG to search through internal SOC runbooks, past incident reports, 
  and threat intelligence feeds to answer analyst questions in real time. This 
  would reduce the time analysts spend searching through documentation and 
  ensure responses are grounded in verified internal knowledge rather than 
  general LLM training data.
