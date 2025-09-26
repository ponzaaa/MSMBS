# General Information
This is the GitHub repository of Group 4 

# Assignment 1: Metabolic Modeling (E. coli Core)
 In this assignment, we work with the E. coli core metabolic model using COBRApy in Python. Our goal is to see how gene expression data can affect the model by using it as a proxy for the maximum activity of certain reactions.  
We first visualize the gene expression data on the E. coli core map and then use it to set new flux bounds for the reactions. After building the constrained model, we run Flux Variability Analysis to find the possible flux ranges for all reactions. Lastly, we perform Flux Balance Analysis to calculate the maximum biomass production rate and figure out which reactions are acting as bottlenecks for growth.  
## Files

- `Assignment1_Notebook.ipynb` – main notebook (Exercises 1–4)
- `e_coli_core.json` 
- `e_coli_core_expression.csv` 



# Assignment 2: Epidemiological Model Parameter exploration
 In this assignment, we implement a SIRD function. We then use this to explore two different epidemiological scenarios.
 Our goal is to see how these parameters influence the outcome of an epidemic in terms of total infections, deaths and epidemic peak.
 At the end we analyse the effects of an intervention and discuss how effective such an intervention would be in real life.
 
## Files

- `Assignment2.ipynb` – main notebook (Exercises 1–3)



# Assignment 3: Network Biology
This assignment involves an analysis of cancer-causing mutations using Boolean networks, building upon the provided notebook. We model four distinct mutations in a cell's regulatory network, in addition to the normal network. Each mutation simulates a common cancer-related pathway disruption: a p53 knockout, which eliminates a key tumor suppressor; an MYC amplification which causes an oncogene to be always active; an MDM2 overexpression, which disrupts the p53 pathway; and a mutation that always results in the death of the cell. We perform a scenario analysis and test how the network responds to these conditions. Additionally, we identified all stable states the network can reach and determined the percentage of initial states that lead to a "cancer-like" outcome. This will allow for a comparative study of how each mutation changes the network's dynamics.

### What is the role of Feedback loops?
Feedback loops are regulatory circuits where the output of a process influences its own activity, either amplifying it (positive feedback) or dampening it (negative feedback).  These loops allow cells to dynamically adapt to internal and external changes. For example, in the MYC → MDM2 → p53 loop, MYC promotes MDM2, which suppresses p53 and keeps growth active. p53 can in turn regulate MYC and MDM2, forming a negative feedback loop that maintains controlled cell growth.

### What are the limitations?
First, the model does not consider all real world cell outcomes such as Senescence. By excluding alternative destinations, the model can only funnel initial states toward the 3 attractors proliferation, oncogenic growth and apoptosis, thus presenting an incomplete and potentially misleading picture of the cell's potential long-term behaviors.

The second limitation is the model’s binary setup and the logic operators it uses, which oversimplify biology. It does not consider continious concentration thresholds and gradual responses and therefore the cell either ignores stress or dies, without considering repair mechanisms or partial arrest that real cells use for actual outcome decisions. In addition, the AND, OR and NOT operators neccessarily reduce the models accuracy of the cells non-linear behaviour.

Lastly, a limitation related to the previous point, the model lacks nuanced temporal dynamics. Although it updates in discrete steps, it cannot represent variable durations, delays, or commitment points. This limits its ability to capture when a cell recovers, pauses, or irreversibly commits to arrest or death.

## Files

- `Assignment3.ipynb` – main notebook (Building and analysing a network with mutations)

---