---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---



# Report on **"Non-Determinism of "Deterministic" LLM Settings"**

## Purpose of the Event

* Explain how **Large Language Models (LLMs)** generate text by selecting the next **token**.
* Clarify why **Temperature = 0** does not always guarantee completely **deterministic** outputs.
* Analyze both technical and infrastructure-related causes of **non-determinism** in modern AI models.
* Share practical strategies for improving the consistency and reliability of **LLM** applications in production environments.
* Help **Developers** and **AI Engineers** build more robust and **production-ready** applications powered by **Large Language Models**.

---

## Speaker

* **Đức Đào** – Solution Architect, Cloud Kinetics

---

## Highlights of the Event

### How LLMs Select the Next Token

* Explained the text generation process of **Large Language Models**.
* During each generation step, the model:

  * Computes **logits** for the entire vocabulary.
  * Converts logits into probabilities using the **Softmax** function.
  * Selects the next **token** based on the predicted probabilities.
* This process repeats until the model completes the response.

---

### The Role of Temperature, Top-P, and Top-K

* Explained how **Temperature** influences text generation.
* Different **Temperature** settings produce different behaviors:

  * **Temperature > 1:** More diverse and creative outputs, but with higher randomness.
  * **Temperature = 1:** Balanced between creativity and consistency.
  * **Temperature close to 0:** More stable and predictable responses.
  * **Temperature = 0:** Theoretically always selects the token with the highest probability.
* Compared the roles of **Top-P** and **Top-K** in controlling the sampling process.

---

### Assumptions vs. Reality of Temperature = 0

* Many developers assume that **Temperature = 0** always produces identical outputs.
* This assumption is commonly used for:

  * **Structured Output**
  * **Regression Testing**
  * **Prompt Benchmarking**
  * **Production Reliability**
* However, real-world research demonstrates that this assumption is not always valid.

---

### Research on Non-Determinism

* Experiments were conducted using multiple models:

  * **GPT-3.5 Turbo**
  * **GPT-4o**
  * **Llama 3**
  * **Mixtral**
* Each experiment used:

  * The same **Prompt**
  * The same **Seed**
  * **Temperature = 0**
* The results showed that:

  * No model consistently generated identical outputs.
  * Prediction accuracy varied across executions.
  * Generated responses sometimes differed significantly despite identical configurations.

---

### Causes of Non-Determinism

#### Technical Causes

* **Floating-point arithmetic** on **GPUs** introduces tiny numerical differences.
* Parallel execution on **GPUs** can change the order of computations.
* Small variations in **logits** may result in different token selections.

#### Infrastructure Causes

* AI service providers commonly use **Inference Batching**.
* Multiple requests are processed together to maximize GPU utilization.
* Responses may be influenced by other requests within the same inference batch.

---

### Real-World Impact

* For applications requiring high reliability, such as:

  * **Healthcare**
  * **Legal**
  * **Finance**
* Assuming that **Temperature = 0** always produces deterministic outputs may introduce significant operational risks.

---

### Mitigation Strategies

* Execute multiple inference runs and apply **Majority Voting**.
* Utilize:

  * **JSON Mode**
  * **Function Calling**
  * **Structured Output**
* Deploy **Self-hosted LLMs** to gain greater control over the inference infrastructure.
* Design systems that can tolerate the inherent variability of **LLMs**.
* Implement mechanisms to handle uncertain or inconsistent responses.

---

### Best Practices

* Avoid always setting **Temperature = 0**.
* **Temperature = 0.1** often provides a better balance between consistency and robustness.
* Increase the **Repeat Penalty** to reduce repetitive responses.
* Always review the documentation of each model provider because implementation details vary across platforms.

---

## What I Learned

### AI & Large Language Models

* Understood how **LLMs** generate text one **token** at a time.
* Learned the roles of:

  * **Softmax**
  * **Temperature**
  * **Top-P**
  * **Top-K**
* Understood why **LLM** outputs may still vary even when using the same **Prompt**.

---

### Machine Learning Infrastructure

* Learned that **Floating-point arithmetic** on **GPUs** is not perfectly deterministic.
* Understood how **Inference Optimization** can directly affect model outputs.
* Recognized that **non-determinism** is an inherent characteristic of modern AI systems rather than a software bug.

---

### Production AI

* Learned that **LLMs** should not be assumed to always return identical responses.
* Understood the importance of designing AI systems that account for output variability.
* Recognized the critical roles of **Testing** and **Validation** before deploying AI applications to production.

---

### Prompt Engineering

* Learned that **Temperature = 0** is not always the optimal configuration.
* Understood how **Structured Output** improves response consistency.
* Learned to combine **Prompt Engineering** with model parameters for better performance.

---

## Applying the Knowledge

* Design chatbots capable of handling different outputs generated by **LLMs**.
* Use **Structured Output** when developing AI-powered APIs.
* Apply **Majority Voting** for tasks requiring higher reliability.
* Fine-tune **Temperature**, **Top-P**, and **Repeat Penalty** according to different use cases.
* Build AI testing workflows that evaluate multiple inference runs instead of relying on a single output.

---

## My Experience at the Event

Attending the **"Non-Determinism of "Deterministic" LLM Settings"** session gave me a much deeper understanding of how **Large Language Models** actually work. Before attending this talk, I believed that simply setting **Temperature = 0** would always produce identical outputs. However, the presentation demonstrated that this assumption is not always true in real-world production environments, where infrastructure, GPU computation, and inference optimization all contribute to output variability.

### Learning from the Speaker

* Gained a deeper understanding of how **LLMs** generate tokens.
* Learned about recent research on **non-determinism** in modern AI models.
* Better understood the practical limitations of deploying **Large Language Models** on cloud platforms.

---

### Technical Experience

* Observed how **Temperature**, **Top-P**, and **Top-K** influence generated responses.
* Learned why identical prompts can still produce different outputs.
* Explored the impact of **GPU computation**, **Floating-point Arithmetic**, and **Inference Batching** on model behavior.

---

### Practical Knowledge

* Learned strategies for designing more reliable AI systems.
* Understood the importance of **Structured Output**, **Majority Voting**, and **Guardrails** in production AI applications.
* Realized that successful AI deployment depends not only on prompt design but also on the underlying infrastructure and inference mechanisms.

---

### Key Takeaways

* **Temperature = 0** does not guarantee fully deterministic outputs.
* **Non-determinism** is an inherent characteristic of many modern **LLMs**.
* AI systems should be designed to tolerate output variability instead of attempting to eliminate it completely.
* Multiple rounds of testing and validation should be performed before deploying AI applications to production environments.

---

### Summary

> Overall, this session significantly changed my perspective on the reliability and consistency of **Large Language Models**. It provided valuable insights into the factors that influence AI inference and equipped me with practical knowledge for building more reliable **Generative AI** applications in real-world production environments.

