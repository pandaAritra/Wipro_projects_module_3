# Wipro_projects_module_3

AI-Based Automotive Review and Customer Sentiment Analysis
Project Report
Aritra Kumar Panda
Enrollment No: 12023002028078

1. Abstract
Automotive customer reviews inherently combine divergent opinions regarding distinct vehicle subsystems within a single passage. A consumer may praise battery endurance while simultaneously reporting significant dissatisfaction with aftersales support. Conventional document-level sentiment classification assigns a single composite score, obscuring granular engineering and operational insights.
This project implements a lightweight, rule-based Aspect-Based Sentiment Analysis (ABSA) system designed to parse unstructured automotive reviews. The system extracts mentions across predefined functional vehicle categories, isolates the corresponding contextual sentences, and determines categorical polarity (Positive, Negative, or Neutral) at both the aspect and document levels.
2. Problem Statement
Given unstructured natural language customer feedback for internal combustion engine (ICE) and electric vehicles (EVs), identify the core subsystems evaluated by the reviewer and determine the targeted sentiment polarity expressed toward each subsystem alongside the aggregate sentiment of the passage.
3. Objectives
Identify mentions across nine functional vehicle aspects from raw customer feedback.
Evaluate sentiment polarity independently for each detected subsystem rather than relying solely on global review polarity.
Compute an aggregate baseline sentiment score for the entire review.
Maintain execution speed, determinism, and explainability by avoiding resource-heavy external dependencies or parameter training.
Provide a unified function returning structured, machine-readable JSON output suitable for dashboard integration.
4. Methodology
4.1 Pipeline Architecture
The system employs deterministic regular expression patterns and targeted lexicon lookup tables using standard library components. The processing sequence is as follows:
Preprocessing: Normalizes input strings by converting characters to lowercase, replacing non-alphanumeric symbols with whitespace delimiters, and collapsing multi-space sequences.
Aspect Detection: Scans the text against mapped token arrays representing specific vehicle subsystems.
Sentence Extraction: Splits the review along terminal punctuation delimiters (., !, ?) and isolates the first sentence matching a detected aspect's keyword set.
Polarity Calculation: Evaluates matched sentences against curated positive and negative word sets using set intersection. The dominant polarity count dictates the aspect sentiment; ties or zero-matches are classified as Neutral.
Output Serialization: Compiles the processed review text, aggregate polarity score, and structured aspect-sentiment mappings into a standardized schema.
4.2 Aspect Keyword Lexicon
The taxonomy defines nine functional vehicle aspects paired with domain-specific keyword anchors:
Aspect PDF
Trigger Keywords PDF
Battery Range
battery, range, battery range, charging
Engine / Powertrain
engine, motor, power
Mileage
mileage, fuel economy, fuel consumption
Safety
safety, brake, brakes, airbag, abs
Comfort
comfort, comfortable, seat, seats, suspension
Service
service, maintenance, dealer, support
Infotainment
infotainment, screen, display, music, navigation
Price
price, cost, expensive, cheap, value
Charging Time
charging time, charge time, charging speed

4.3 Sentiment Polarity Lexicons
The polarity determination engine uses two disjoint lexicons:
Positive Lexicon ($S^+$): 16 qualitative tokens including excellent, great, smooth, efficient, reliable, love, good, amazing, responsive, and comfortable.
Negative Lexicon ($S^-$): 17 qualitative tokens including bad, poor, terrible, slow, long, expensive, problem, disappointing, noisy, and rough.
Sentence scoring uses the polarity decision rule:
$$\text{Polarity} = \begin{cases} \text{Positive}, & \text{if } \vert{}W \cap S^+\vert{} > \vert{}W \cap S^-\vert{} \\ \text{Negative}, & \text{if } \vert{}W \cap S^-\vert{} > \vert{}W \cap S^+\vert{} \\ \text{Neutral}, & \text{otherwise} \end{cases}$$
5. System Implementation
5.1 Core Modules
preprocess(text): Performs cleaning, case normalization, and punctuation replacement.
find_aspects(text): Scans input text and identifies present aspect classes.
sentence_for_aspect(text, keywords): Partitions text on punctuation boundaries and extracts the primary sentence associated with the target keyword set.
get_sentiment(text): Compares tokens against positive and negative lexicons to return polarity classifications.
analyze_review(text): Serves as the primary pipeline orchestrator, formatting and returning the composite output.
5.2 Structured Output Schema
JSON
{
  "processed_text": "the battery range is excellent but the service center support is terrible",
  "overall_sentiment": "Neutral",
  "aspects": [
    {"aspect": "Battery Range", "sentiment": "Positive"},
    {"aspect": "Service", "sentiment": "Negative"}
  ]
}

(Reference format based on standard ABSA data schema)
6. Empirical Test Results

Scraped Review Sample PDF
Document Polarity PDF
Extracted Aspect Polarities PDF
"The battery range is excellent. But the service center support is terrible."
Neutral
Battery Range: Positive
Service: Negative
"Charging time is very long and the price is expensive."
Negative
Battery Range: Negative
Price: Negative
Charging Time: Negative
"The seats are comfortable and the infotainment screen is amazing."
Positive
Comfort: Positive
Infotainment: Positive
"The car is okay."
Neutral
None detected

The system was evaluated against real-world scraped automotive reviews covering electric and internal combustion models:7. Observed Limitations
Absence of Negation Handling: Contextual modifiers like "not" or "never" are ignored, causing inverted phrases such as "the brakes are not good" to register as Positive due to the presence of the word "good".
Sub-Token Matching Collisions: Unbounded substring matching causes false positives (e.g., the token abs triggering the Safety aspect inside common words like "absolutely").
Category Overlap: Shared root tokens lead to duplicate tagging; reviews discussing charging time trigger both Charging Time and Battery Range due to the presence of the keyword charging.
Context-Independent Lexicon Weights: Polysemous terms such as long produce negative polarity, even when describing beneficial attributes like "long range".
First-Sentence Boundary Truncation: Analysis is limited to the first occurrence of an aspect keyword, omitting sentiment updates present in subsequent sentences discussing the same category.
8. Architectural Roadmap & Refinements
Regex Word Boundaries: Enforce \b token anchors and phrase-priority matching so compound phrases (charging time) take precedence over single tokens (charging), eliminating substring errors.
Negation Windows: Implement a polarity-reversal window scanning up to three tokens following negation markers (not, hardly, no, n't).
Compound Sentence Aggregation: Score every sentence containing aspect keywords and aggregate scores across the entire passage.
Pretrained Contextual Embeddings: Migrate to lightweight domain-specific transformers (such as RoBERTa or DeBERTa fine-tuned on automotive corpora) to resolve polysemy and complex sentence structures.
Interactive Visualization: Integrate the core ABSA engine with a Streamlit interface displaying subsystem-level positive/negative distributions.
9. Practical Applications
OEM Product Engineering: Identifies specific sub-assembly failure rates, ride-quality criticisms, and component defects directly from user feedback.
Competitive Intelligence: Allows direct comparative benchmarking between competing platforms across standardized categories like range efficiency, software stability, and ergonomics.
Service Network Monitoring: Tracks localized dealership feedback and repair-turnaround complaints.
10. Conclusion
This project provides a rule-based aspect-based sentiment analysis implementation tailored to automotive reviews. By decoupling aspect-level polarity from document-level scores, it exposes critical subsystem details that uniform sentiment scoring flattens. While rule-based lexicon systems require guardrails for negation and substring collisions, this implementation establishes a functional, resource-efficient foundation for automated automotive text mining.

