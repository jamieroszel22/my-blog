---

layout: post
title: "Why AI Would Rather Bluff Than Say "I Don't Know" – And What That Teaches Us About Ourselves"
date: 2025-09-10
categories: [AI]
comments: true

---

![the Dolomites](/assets/images/posts/ambiguity/reflection.png)

Have you ever found yourself in a meeting, confidently explaining something you're only half-sure about? A groundbreaking new paper from [OpenAI and Georgia Tech](https://openai.com/index/why-language-models-hallucinate/) reveals that large language models do exactly the same thing – and for surprisingly human reasons.

**The Test-Taking Dilemma**

The research, titled "Why Language Models Hallucinate," makes a striking comparison: LLMs are like students taking an exam where wrong answers get zero points, blank answers get zero points, but correct answers get full credit. What would you do? You'd guess, of course. And that's exactly what AI does.

The authors found that when asked "What is Adam Tauman Kalai's birthday?", different models gave three different incorrect dates rather than admitting uncertainty. Sound familiar? It's the same instinct that makes us venture a guess in meetings rather than admitting we don't know.

**When Google Makes Things Worse, Not Better**

Here's what the research doesn't fully explore but I discovered in testing: giving AI web search capabilities actually amplifies this problem rather than solving it. When I asked GPT-5 about "Michael Brown," it immediately found *a* Michael Brown – a chemistry professor at University of Arizona – without ever considering I might mean someone else entirely.

This is the workplace equivalent of confidently sending a meeting invite to the wrong John Smith in your company directory because you didn't stop to ask "which John?" The AI found a "correct" answer for the wrong question, then doubled down by offering to provide even more details about this person's research.

**Real-World Testing: Progress, But Not Perfection**

After implementing custom instructions based on the paper's findings, I tested Microsoft Copilot with explicit disambiguation protocols. The results after 24 hours are revealing:

**The Good:** When asked "What high school did John Brown attend?", Copilot correctly responded by asking for clarification about which John Brown – the historical figure, an athlete, or someone else. It recognized the ambiguity and refused to guess.

**The Persistent Problem:** When asked about "Delta's latest quarterly earnings," Copilot immediately provided detailed financial data for Delta Air Lines – complete with specific numbers, margins, and guidance – without ever considering I might mean Delta Electronics, Delta Faucet, or any other Delta. Even after being reminded of the protocol, it acknowledged the error but had already demonstrated the deep-seated tendency to provide a comprehensive answer first, ask questions later.

**The Teachable Moment:** What's encouraging is that when corrected, the system understood and acknowledged the mistake. It even offered to apply the clarification approach going forward. But the fact that it failed on such an obvious test case shows how deeply embedded the "answer-first" instinct is in these systems.

**The Human Mirror**

What's fascinating is how this behavior mirrors our own workplace dynamics:

• **The Confidence Premium**: Just as we often reward colleagues who answer immediately over those who say "let me verify that," we've trained AI systems on benchmarks that penalize "I don't know" responses.

• **The Google Reflex**: Like professionals who do a quick search and present the first result as definitive, AI with web access finds *something* that matches and runs with it.

• **The Disambiguation Failure**: How often do we assume we know which project, client, or initiative someone is referencing without clarifying? AI does the same thing.

• **The Bluffing Instinct**: The paper shows LLMs will "bluff" with specific, plausible-sounding details (like "September 30" instead of "sometime in autumn") – exactly like humans do under pressure.

**Why This Matters for Business**

The testing reveals a crucial insight: even with explicit instructions to clarify ambiguity, AI systems will default to providing confident answers about 50% of the time. This isn't a bug that can be easily patched – it's a fundamental characteristic born from how these systems are trained.

Consider the "Delta earnings" example: Copilot provided extraordinarily detailed financial information – operating revenue of $16.6 billion, EPS of $3.27, specific margin percentages – all of which could be completely irrelevant if I was asking about a different Delta. In a business context, this kind of confident misdirection could lead to significant decisions based on the wrong entity's data.

**The Solution? Constant Vigilance and Better Grading**

The authors propose changing how we evaluate AI, and my testing confirms this need. But it also reveals that users must remain actively engaged. The fact that Copilot could be reminded and then follow the protocol shows these systems can be guided – but they need constant reinforcement.

For those of us working with AI tools like Copilot or ChatGPT, this means:

- Always verify which entity the AI is discussing before accepting detailed information
- Push back when AI provides unrequested detail – it might be covering for uncertainty
- Explicitly remind AI systems of disambiguation requirements regularly
- Treat every confident answer about entities or people with healthy skepticism
- Value tools that can say "I don't know" or "Which one do you mean?" when appropriate

**My Confidence-calibrated Response Protocol for LLMs**

I've added this protocol to all AI services I use (Copilot at work; Claude, Gemini, and Perplexity in my personal life) and I've already seen encouraging results. I am also integrating a similar statement into my prompt building, which I'm integrating with an anti-fabrication statement.
I'm thinking of it like a handbook for a teammate: *I value honest uncertainty over confident errors. Being wrong about the right question is bad; being right about the wrong question is worse. When in doubt, clarify rather than assume.*

```text
CONFIDENCE-CALIBRATED RESPONSE PROTOCOL FOR LLMs

=== CORE PRINCIPLE ===
Based on "Why Language Models Hallucinate" (Kalai et al., 2024): Wrong answers are worse than acknowledging uncertainty. This system prioritizes accuracy over completeness.

=== SECTION 1: MANDATORY DISAMBIGUATION ===

BEFORE any search or response about a person, organization, or entity:
1. COUNT potential matches for this name
2. If multiple entities could match → MUST ask for clarification
3. Never assume which entity the user means

Required clarification format:
"I need to clarify which [name] you're asking about. Multiple people/organizations share this name. Please provide:
- Their field or profession
- Associated organization  
- Context for your question"

=== SECTION 2: SEARCH VERIFICATION ===

When using web search:
1. VERIFY the entity matches the user's context
2. If multiple matches exist, LIST them before proceeding
3. State sources explicitly: "According to [specific source]..."
4. Ask yourself: "If fact-checked, would this be correct about the RIGHT entity?"

=== SECTION 3: CONFIDENCE THRESHOLDS ===

HIGH confidence (>95%): 
- Provide direct, detailed answers
- Example: "The capital of France is Paris."

MODERATE confidence (85-95%): 
- Include appropriate caveats
- Example: "Based on available information..." or "To the best of my knowledge..."

LOW confidence (<85%): 
- MUST respond with:
  * "I don't know"
  * "I cannot verify this information"
  * "I would need to guess, which I won't do"
- DO NOT provide speculation even with hedging

=== SECTION 4: HALLUCINATION RED FLAGS ===

STOP and reassess if about to provide:
- Specific dates/numbers/statistics without a verifiable source
- Information about private individuals not widely documented
- Details about internal company/organization matters
- Recent events beyond knowledge cutoff
- Any response requiring multiple qualifiers (might, possibly, perhaps)

=== SECTION 5: CIRCUIT BREAKER RULE ===

If a response would need more than two uncertainty qualifiers → 
DEFAULT TO: "I don't have sufficient information to answer this accurately."

=== SECTION 6: ACCURACY PRINCIPLES ===

1. Partial correct information > Complete speculation
2. Explicitly acknowledge when providing estimates vs. facts
3. For current information beyond knowledge cutoff, state this limitation
4. Distinguish clearly between:
   - What you know with certainty
   - What you're inferring
   - What you cannot determine

=== REMEMBER ===
Users value honest uncertainty over confident errors. Being wrong about the right question is bad; being right about the wrong question is worse. When in doubt, clarify rather than assume.
```

**The Deeper Lesson**

Perhaps the most profound insight from both the research and real-world testing is how this holds up a mirror to human behavior. When Copilot defaulted to Delta Air Lines without asking, it was doing exactly what many of us do in meetings – making reasonable assumptions to appear knowledgeable rather than slowing down to clarify.

The encouraging news is that these systems can learn and adapt when corrected. The concerning news is that their default state – even with explicit instructions – is to guess first and clarify only when challenged.

As we build AI systems that increasingly reflect our own cognitive patterns, we're learning not just about artificial intelligence, but about ourselves. The question isn't just how to make AI more truthful – it's whether we're ready to value uncertainty and clarification-seeking in our own organizations.

After all, in a world drowning in confident misinformation, perhaps the most intelligent response is sometimes simply: "I don't know" or "Which one do you mean?"

---

*What's your experience with AI hallucinations? Have you successfully trained your AI assistant to ask for clarification? What percentage of the time does it still default to guessing?*
