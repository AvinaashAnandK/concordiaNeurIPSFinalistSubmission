# Concordia Competition Agent: Challenges and Solutions

This repository contains the code for an agent designed to compete in the Concordia social simulation competition. The development process involved overcoming numerous challenges related to information asymmetry, resource constraints, scenario diversity, and the complex nature of social interactions. This document outlines these challenges and the solutions implemented to address them.

## I. Core Challenges

### 1. Information Asymmetry and Dynamic Inference

**Challenge:** Agents in the Concordia competition begin with severely limited knowledge. They lack information about the environment, other agents' identities, goals, and capabilities, as well as the overall scenario. This necessitates a strategy of continuous inference and belief updating throughout the simulation.

**Implication:** The agent's underlying Large Language Model (LLM) must dynamically infer missing information and update its understanding as the game progresses. This is computationally expensive and requires careful management of the limited API calls and token counts available.

**Refinement:** The core of this challenge lies not just in *inferring* information but in *prioritizing* what to infer given the resource constraints. The agent needs a strategy to determine which unknowns are most critical to resolve at each stage of the simulation.

**Solution:** The agent employs a structured information framework (detailed below) to organize and prioritize information needs. It focuses on inferring information most relevant to the current situation and the agent's immediate goals, updating its beliefs iteratively.

### 2. Resource Constraints (API Calls and Tokens)

**Challenge:** The competition imposes strict limits on the agent's computational resources: 1000 API calls with a 7k token limit per call. This severely restricts the LLM's ability to process information and make decisions.

**Implication:** Every LLM call must be strategically justified. Continuous, exhaustive inference is impossible, demanding a highly efficient use of resources.

**Refinement:** The core issue is *optimization under scarcity*. The agent needs a framework to maximize the value derived from each API call, balancing the need for information with the cost of obtaining it.

**Solution:** The agent utilizes a structured information framework to minimize redundant queries and prioritize the most valuable information. It also employs a custom-designed action component (`ConcatActComponent`) that efficiently integrates information from various sources, reducing the need for multiple API calls.

### 3. Scenario Diversity and Hidden Scenarios

**Challenge:** The competition features a wide range of scenarios, potentially spanning economic, social, and abstract domains. Furthermore, the final competition scenarios are unknown during the development phase.

**Implication:** Developing a single, hard-coded strategy is unlikely to succeed. The agent must be adaptable and capable of generalizing across different scenario types.

**Refinement:** The challenge is to design a *meta-strategy* or a set of guiding principles that are robust to scenario variations. This requires identifying common underlying structures or decision-making patterns across different game types.

**Solution:** The agent's strategy is built upon a foundation of decision theories (e.g., 'gameTheo_boundRat', 'prospect_theory'). These theories provide a general framework for decision-making that can be applied across different scenarios. The agent's behavior is guided by these principles rather than scenario-specific rules.

## II. Strategic Challenges

### 4. Meta-Strategy Development Using Decision Theories

**Challenge:** Integrating decision theories into the agent's policy proved effective, but the specific implementation and level of detail significantly impacted performance.

**Implication:** Decision theories provide a valuable framework for guiding the LLM's behavior. However, finding the optimal level of abstraction when applying these theories is crucial.

**Refinement:** The challenge is to find the *optimal level of abstraction* when applying decision theories. More detailed, nuanced instructions (as seen in the "big" variant) are beneficial but must be balanced against resource constraints.

**Solution:** The agent uses the 'gameTheo_boundRat' decision theory in the "big" variant configuration. This provides a detailed framework for decision-making, incorporating concepts like bounded rationality and game theory. The implementation of this theory is carefully crafted within the prompt to guide the LLM effectively.

## III. Implementation Challenges

### 5. Custom Action Component vs. Default

**Challenge:** The default action component provided in the Concordia framework was insufficient for effectively utilizing inferred context and the guidance provided by the decision theory.

**Implication:** The action generation process is crucial and requires careful design to ensure the LLM can leverage all available information.

**Refinement:** A custom action component is needed that seamlessly integrates the agent's understanding (scenario, self, others), the trusted advisor's guidance, and the chosen decision theory into a coherent and effective action.

**Solution:** The agent uses a custom-built `ConcatActComponent`. This component intelligently combines information from various modules, including the agent's understanding of the scenario, itself, and other participants, along with the guidance from the decision theory module. It is specifically designed to handle numeric information and perform threshold analysis.

### 6. Character Strategy for Enhanced Reasoning

**Challenge:**  To improve the LLM's reasoning and make the agent's behavior more interpretable, a mechanism was needed to encourage the LLM to articulate its strategic thinking.

**Implication:** Explicitly prompting the LLM to articulate its strategic thinking enhances its decision-making and provides insights into its internal processes.

**Refinement:** The challenge is to refine this mechanism to maximize its effectiveness, providing specific guidelines on what to include in the strategy, how to structure it, and how to connect it to the final action.

**Solution:** The agent's prompt includes a "character strategy" section. This section requires the LLM to outline its strategic thinking before generating an action. It includes 11 key considerations:
```
1. Key points from each input section.
2. Summary of the Trusted Advisor's Guidance.
3. Highlight of the most relevant recent observations.
4. Identification of important thresholds and the agent's position relative to them.
5. Explanation of strategy adjustments based on the specific experiment type.
6. Outline of the information sharing strategy.
7. Description of the planned approach for engaging with other participants.
8. Sketch of any proposals the agent plans to make.
9. Development of a counter-proposal.
10. Formulation of a suggestion.
11. Explicitly stating how each planned action connects to the character's motivation.
```
This detailed breakdown helps the LLM reason through its decisions and provides valuable insights for debugging and improvement.

### 7. Handling Numeric Information and Thresholds in `ConcatActComponent`

**Challenge:** The `ConcatActComponent` needs to process numeric information (prices, quantities, thresholds) effectively to make informed decisions, especially in scenarios involving transactions.

**Implication:** The component must extract, interpret, and utilize numeric data from various sources and perform calculations to guide actions.

**Solution:** The `ConcatActComponent` is designed to handle numeric information through:
*   **Context Assembly:** It gathers information from different components, including "Self Understanding" (containing aspiration levels, satisficing thresholds) and the "Trusted Advisor" (providing numeric targets or ranges).
*   **Additional Instructions:** The prompt includes specific instructions for "Threshold Analysis," guiding the LLM to identify numerical values, determine if they are minimums or maximums, and perform calculations.
*   **Character Strategy:** The "character strategy" section reinforces this by requiring the agent to identify important thresholds, its position relative to them, and to show calculations for any values.


## IV. Further Considerations

### 10. Structured Information Framework

**Observation:** A well-defined structure helps the LLM process and utilize information more effectively.

**Solution:** The agent employs a structured information framework with three main blocks:

*   Optimizing the update frequency for each block, and exploring ways to dynamically adjust the framework based on the current scenario.
*   Explicitly extracting and storing Numeric information (e.g. purchase price) to store them as states.



### 6. Performance Evaluation with Limited Feedback

**Challenge:** The lack of direct feedback beyond the overall baseline score makes it difficult to assess the effectiveness of specific strategies or prompt changes during development.

**Implication:** Debugging and fine-tuning the agent's behavior is challenging. Isolating the impact of individual decisions or prompt modifications is difficult without detailed feedback.

**Refinement:** The challenge is to develop *alternative evaluation methods* that do not rely on direct feedback.

**Solution:** The agent's development process focuses on point-in-time assessments and analyzing the LLM's reasoning process based on its output (e.g., the "final act component"). The "character strategy" section (described below) also aids in understanding the agent's decision-making process.


## Conclusion

The agent developed for the Concordia competition represents a significant effort to overcome the challenges inherent in this complex social simulation. By employing a structured information framework, a custom action component, a meta-strategy grounded in decision theory, and a detailed character strategy section, the agent demonstrates a sophisticated approach to decision-making in an environment of information asymmetry and resource constraints. The solutions implemented provide a solid foundation for further development and improvement.
