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

### Which mutation is most dangerous?
From the data we have gathered, mutation A, or the p53 Knockout (always OFF) seems the most dangerous. B and C are also highly oncogenic but A is even more due to the following reasons: 

scenario outcomes end in profileration/oncogenic states only(no apoptosis):
• healthy -> growth=1, death=0
• stressed (DNA_damage=1) -> growth=1, death=0 <- cancer-like
• oncogene hijacked -> growth=1, death=0

When we consider the attractors: A has 2 attractors and both are cancer-like (proliferation, oncogenic). 

Speed to malignant state: In the stressed scenario A reaches the cancer-like steady state in 4 steps, whereas B/C take 7. Faster convergence means less opportunity for recovery/repair.

Comparison to baseline: Normal has 3 attractors with the split being ≈ 50.0% Proliferation, 46.9% Apoptosis, 3.1% Oncogenic (so about ≈ 53.1% cancer-like overall). Mutation A removes the apoptotic ending entirely and leads to cancer-like situations only.


### What is the role of Feedback loops?
Feedback loops are regulatory circuits where the output of a process influences its own activity, either amplifying it (positive feedback) or dampening it (negative feedback).  These loops allow cells to dynamically adapt to internal and external changes. For example, in the MYC → MDM2 → p53 loop, MYC promotes MDM2, which suppresses p53 and keeps growth active. p53 can in turn regulate MYC and MDM2, forming a negative feedback loop that maintains controlled cell growth.

### What are the limitations?
First, the model does not consider all real world cell outcomes such as Senescence. By excluding alternative destinations, the model can only funnel initial states toward the 3 attractors proliferation, oncogenic growth and apoptosis, thus presenting an incomplete and potentially misleading picture of the cell's potential long-term behaviors.

The second limitation is the model’s binary setup and the logic operators it uses, which oversimplify biology. It does not consider continious concentration thresholds and gradual responses and therefore the cell either ignores stress or dies, without considering repair mechanisms or partial arrest that real cells use for actual outcome decisions. In addition, the AND, OR and NOT operators neccessarily reduce the models accuracy of the cells non-linear behaviour.

Lastly, a limitation related to the previous point, the model lacks nuanced temporal dynamics. Although it updates in discrete steps, it cannot represent variable durations, delays, or commitment points. This limits its ability to capture when a cell recovers, pauses, or irreversibly commits to arrest or death.

## Files

- `Assignment3.ipynb` – main notebook (Building and analysing a network with mutations)



# Assignment 4: Plant Systems Biology
This assignment is an investigation into a computational model of a plant-pathogen infection, where the pathogen facilitates its spread by secreting a chemical that degrades cell wall stability. We analyze and interpret the model's existing structure through simulation, code review, and pseudocode. 

### Task 1
![task1](Assignment4/Assignment4_task1.png)

Running the simulation we observe that the pathogen rapidly attacks the cell walls in the first hour and is able to weaken a lot of them.
We can see deformation on the infected cells due to a loss of sturctural integrity caused by the wall weaking chemicals. The pathogen is able to penetrate deeper into tissue.
After the 2nd hour the infection slows down but the pathogen is still steadily growing and infecfting more cells.

### Task 2

CellHouseKeeping is called on each cell at every step of the simulataion. It governs how the cells behave, for example how they grow and how they respond to chemichals. If cell is a pathogen (type 2) its target area is increased, simulating the faster expansion of a pathogen.
The function sets the base length of wall elements to 25 units. It also measures the level of pathogen chemicals in the cell and limits it's maximum effect. If the pathogen chemical level is too high stiffness is reduced to simulate weaker walls. Otherwise the stifness is kept at a default of 2.5.

### Task 3
![task3](Assignment4/Assignment4_task3.png)

### Task 4
Decrease:
The chemical stays localized near the pathogen and the gradient is steeper. There is very high C(0) next to the pathogen and it quickly drops a few cells away. The infection advances slowly and the soft/weak region is small. At the end only a small patch remains

Increase:
The chemical spreads much farther from the source. More distant cells recieve a low C(0) so more cells show some weakening earlier. Local concentration near the pathogen is lower and the nearest walls can be less damaged than in the decrease scenario but the area of compromised walls is larger and it progresses faster



### Task 5
We can add one new mechanism

S – defense signal produced by healthy cells when they detect the pathogen chemical C0

Right after infection, healthy cells that detect the pathogen chemical above a small threshold begin secreting the defense signal S. It then diffuses to neighboring cells to help them and decays gradually. In regions where S is present walls become both stiffer and less sensitive to C0 weakening effectso the infection advances more slowly. Changing the size of the effects (ALPHA_STIFF, BETA_C0) will determine how much it stops any infection

### So we have some paramenters that it operates with

    THRESHOLD_DETECT = 0.2     # C0 level that triggers S production
    
    K_SECRETE_S      = 0.05    # how fast an active cell adds S 
    
    D_S               = 0.02   # diffusion coefficient of S
    
    K_DECAY_S         = 0.01   # how much S decays per hour
    
    ALPHA_STIFF       = 0.5    # stiffness boost from S
    
    BETA_C0           = 0.4    # reduction of C0 weakening effect by S
    
    S_HALF            = 0.30   # S level that gives half of the defense effect

### this will be in CellHouseKeeping

we read the current local concentrations

    c = cell.C0
    
    s = cell.S

cells detect C0 high and make S

    if cell.type == HEALTHY and c > THRESHOLD_DETECT:

    cell.S += K_SECRETE_S * dt


Then stiffness is computed with defense and the weakening from C0 applies

    C_effect      = c                       //C effect
    S_effect      = HILL(s, S_HALF)        //S effect 
    C_effect_mod  = C_effect * (1 - BETA_C0 * S_effect)   //how strong is C weakening effect
    stiff_from_C  = stiffness_base * f_weak(C_effect_mod)  //weakening of the cell from C
    boost         = (1 + ALPHA_STIFF * S_effect)  //boosting of the cell from S
    cell.stiffness = stiff_from_C * boost    //final cell stiffness 



### then this will be in the transport step to spread it and dissapear

    Diffuse(C0, D_C) 
    
    Diffuse(S,  D_S)
    S -= K_DECAY_S * S * dt


---
![Initial scenario](https://drive.google.com/file/d/1fQeFQlbHjtX4vM53U3VIJIEM9RvzXoAn/view?usp=sharing)

# Assignment 5: Neuroanatomically Constrained Modeling of the Visual System

This project implements and analyzes a neural network model of the human visual system. The model's architecture is not arbitrary; it is constrained by real-world anatomical data for cell density and structural connectivity, extracted from the EBRAINS human brain atlas.

---
## Phase 1: Anatomical Data Collection

The first and most critical phase was to gather anatomical data to serve as a blueprint for our model.

### Tools and Atlas Selection
We used the **`siibra`** Python library to programmatically access the **EBRAINS/HBP Human Brain Atlas**.

-   **Atlas:** We selected the `Multilevel Human Atlas`.
-   **Parcellation:** We chose the **`Julich-Brain Cytoarchitectonic Atlas (v3.1)`**. This parcellation is ideal because it defines brain regions based on their cellular micro-structure (cytoarchitecture), which is closely related to function. We chose the latest version available (v3.1) to ensure the most up-to-date map.

### Regions of Interest (ROIs)
Based on the canonical two-streams hypothesis of vision, we selected six key regions spanning the ventral ("what") and dorsal ("where/how") pathways:
-   **`hOc1` (V1):** Primary Visual Cortex, the entry point for visual information.
-   **`hOc2` (V2):** Secondary Visual Cortex, a crucial early processing stage.
-   **`hOc4v` (V4):** A mid-level ventral stream area, important for form and color.
-   **`hOc5` (MT):** A key dorsal stream area, specialized for motion processing.
-   **`FG1` / `FG2` (IT):** Regions in the Fusiform Gyrus, part of the Inferior Temporal cortex, the final stage of the ventral stream for object recognition.

### Data Extraction
For each region, we extracted two types of data:
1.  **Cell Density:** We queried for `CellDensityProfile` features derived from the BigBrain high-resolution histological dataset. After an iterative process, we determined the correct method was to access the `.data` attribute and calculate the mean of the first column, yielding an average density in `cells/0.1mm³`. This value would inform the relative size (number of channels) of each layer in our model.
2.  **Structural Connectivity:** We queried for `StreamlineCounts` from the Human Connectome Project (HCP) cohort. Our initial `KeyError` revealed that the connectivity matrix uses hemisphere-specific `Region` objects as keys. We developed a robust method to find the correct key for the 'left' hemisphere and extract the connectivity profile. We then filtered this profile to the top 5 *external* connections to create a manageable wiring diagram.

### Final Anatomical Blueprint
The automated `siibra` script produced the following JSON object, which serves as the final blueprint for our human model:
```json
{
    "hOc1": {
        "connectivity": {
            "Area hOc3v (LingG) left": 35125.04044117647,
            "Area hOc2 (V2, 18) left": 28349.60661764706,
            "Area hOc3d (Cuneus) left": 22377.98161764706,
            "Frontal-to-Occipital (GapMap) left": 18723.5,
            "Area hOc4v (LingG) left": 8105.621323529412
        },
        "cell_density": "84.90"
    },
    "hOc2": {
        "connectivity": {
            "Area hOc1 (V1, 17, CalcS) left": 28349.60661764706,
            "Area hOc3d (Cuneus) left": 14656.661764705883,
            "Area hOc3v (LingG) left": 2529.2904411764707,
            "Frontal-to-Occipital (GapMap) left": 1667.5330882352941,
            "Area hOc4lp (LOC) left": 1575.9779411764705
        },
        "cell_density": "80.67"
    },
    "hOc4v": {
        "connectivity": {
            "Area hOc3v (LingG) left": 21455.40073529412,
            "Area hOc1 (V1, 17, CalcS) left": 8105.621323529412,
            "Area FG1 (FusG) left": 6120.371323529412,
            "Area hOc4lp (LOC) left": 5248.761029411765,
            "Area FG2 (FusG) left": 4819.672794117647
        },
        "cell_density": "Not found"
    },
    "hOc5": {
        "connectivity": {
            "Area hOc4la (LOC) left": 2611.8970588235293,
            "Temporal-to-Parietal (GapMap) left": 2314.202205882353,
            "Area hOc4lp (LOC) left": 621.1102941176471,
            "Area FG2 (FusG) left": 569.5073529411765,
            "Area PGp (IPL) left": 367.95588235294116
        },
        "cell_density": "60.66"
    },
    "FG1": {
        "connectivity": {
            "Area MFG4 (MFG) left": 1819.5294117647059,
            "Frontal-I.1 (GapMap) left": 1094.4779411764705,
            "Area MFG2 (MFG) left": 492.59191176470586,
            "Area SFG4 (SFG) left": 424.51838235294116,
            "Area Fp1 (FPole) left": 409.3014705882353
        },
        "cell_density": "64.83"
    },
    "FG2": {
        "connectivity": {
            "Area FG4 (FusG) left": 6070.573529411765,
            "Temporal-to-Parietal (GapMap) left": 5776.150735294118,
            "Area hOc4la (LOC) left": 5071.735294117647,
            "Area hOc4v (LingG) left": 4819.672794117647,
            "Area FG3 (FusG) left": 3216.716911764706
        },
        "cell_density": "Not found"
    }
}
```

## Phase 2: Model Architecture and Implementation
The anatomical blueprint was translated into a PyTorch nn.Module.

- Layer Size: The **out_channels** of each convolutional layer was set to be proportional to the cell_density of the corresponding brain region. For regions where data was not found (e.g., `hOc4v`), a reasonable intermediate value was chosen.

- Network Wiring: The `forward()` method was constructed to mirror the connectivity data.

The primary connections (V1->V2, V2->V4, etc.) defined the main feedforward flow.

A key finding was the strong connection between the ventral stream (hOc4v) and the dorsal stream (hOc5/MT, via other LOC regions). This justified the implementation of a crosstalk connection, where the output of the V4 layer contributes to the dorsal stream's processing.

The `HumanVisualSystem` Model:
```python

import torch
import torch.nn as nn
import torch.nn.functional as F

class HumanVisualSystem(nn.Module):
    def __init__(self):
        super(HumanVisualSystem, self).__init__()
        # Layer channel sizes are inspired by cell density values
        self.v1 = nn.Conv2d(1, 85, 3, padding=1)
        self.v2 = nn.Conv2d(85, 80, 3, padding=1)
        self.pool = nn.MaxPool2d(2, 2)
        self.ventral_v4 = nn.Conv2d(80, 64, 3, padding=1) # Using 64 as a workaround for 'Not Found'
        self.ventral_it = nn.Conv2d(64, 65, 3, padding=1) # Based on FG1
        self.dorsal_mt = nn.Conv2d(80, 60, 3, padding=1)
        self.v4_to_mt_crosstalk = nn.Conv2d(64, 60, 1)
        self.ventral_classifier = nn.Linear(12740, 10)
        self.dorsal_classifier = nn.Linear(11760, 10)

    def forward(self, x):
        x = F.relu(self.v1(x))
        x = F.relu(self.v2(x))
        x = self.pool(x)
        
        # Ventral stream and crosstalk signal generation
        v4_output = F.relu(self.ventral_v4(x))
        it_output = F.relu(self.ventral_it(v4_output))
        it_output_flat = it_output.view(it_output.size(0), -1)
        ventral_prediction = self.ventral_classifier(it_output_flat)
        
        # Dorsal stream with crosstalk integration
        d_out = F.relu(self.dorsal_mt(x))
        v4_to_dorsal_signal = F.relu(self.v4_to_mt_crosstalk(v4_output))
        d_out = d_out + v4_to_dorsal_signal
        d_out_flat = d_out.view(d_out.size(0), -1)
        dorsal_prediction = self.dorsal_classifier(d_out_flat)
        
        final_prediction = (ventral_prediction + dorsal_prediction) / 2
        return final_prediction
```
---
## Phase 3: Model Training

-   **Dataset:** The model was trained on the **Fashion MNIST** dataset, a standard benchmark for image classification. For efficiency on a CPU, a subset of 6,000 training images and 1,000 test images was used.
-   **Training Parameters:**
    -   **Loss Function:** `nn.CrossEntropyLoss`
    -   **Optimizer:** `Adam`
    -   **Learning Rate:** `0.001`
    -   **Epochs:** `5`
    -   **Batch Size:** `64`
-   **Performance:** The model trained successfully, achieving a final accuracy of **~88%** on the test subset.

---
## Phase 4: Analysis of Results

After successfully training the neuroanatomically constrained model, we performed several analyses to understand its internal representations and functional organization. The results confirm that the model learned a hierarchical and categorical representation of the input data.

### Representational Similarity Analysis (RSA)

We first visualized the Representational Dissimilarity Matrix (RDM) for each individual layer. The plots show a clear progression:
-   **Early Layers (`v1`, `v2`):** Display complex, fine-grained patterns, reflecting dissimilarities based on low-level visual features like edges and textures.
-   **Deeper Layers (`v4`, `it`, `mt`):** Exhibit a prominent block-like structure. This indicates the emergence of categorical representations, where the model groups images of the same class as being highly similar.

Next, we compared these layer-specific RDMs to each other. The resulting similarity matrix reveals the network's hierarchical structure:

-   **Hierarchical Clustering:** The matrix shows a clear block-diagonal pattern. The early layers (`v1`, `v2`) have highly similar representations to each other (correlation ≈ 0.87), and the deeper layers (`v4`, `it`, `mt`) form another cluster of high similarity (correlations > 0.93). The similarity *between* these two blocks is lower (≈ 0.75).
-   **Conclusion:** This demonstrates a clear processing hierarchy. The network progressively transforms representations from low-level visual features into more abstract, categorical features as information flows from early to later stages.

### Functional Connectivity (FC)

We measured the correlation of activity between layers to understand how training shapes the network's functional interactions.

-   **Before Training:** The FC matrix shows a mix of positive and strong negative correlations. This pattern is largely unstructured, indicating random, uncoordinated activity between layers in the untrained network.
-   **After Training:** The connectivity pattern changes dramatically. All correlations become strongly positive. Notably, the connections between anatomically adjacent layers in our design (`v1-v2`, `v2-v4`, `v4-it`) are strengthened.
-   **Conclusion:** This shows that the training process tunes the network's functional dynamics to align with the structured, feedforward flow of information. The strong positive correlation between `v4` and `mt` after training also validates our anatomical design choice to include a crosstalk connection. Training pushed the network's *functional* connectivity to better reflect its underlying *structural* connectivity.

### t-SNE Visualization of Representational Space

To visualize the structure of the learned feature space, we applied t-SNE to the activations of the final ventral stream layer (`it`).

-   **Categorical Clustering:** The resulting 2D plot shows distinct clusters of data points corresponding to the 10 different classes of clothing (represented by different colors). While there is some minor overlap, the overall separation is very clear.
-   **Conclusion:** This visualization provides strong evidence that the model has learned a feature space where different object categories are well-separated. This underlying categorical structure is what enables the model to achieve high classification accuracy.

### Overall Conclusion

Our anatomically constrained model successfully learned to perform the image classification task with an accuracy of ~88% (on a subset). The analyses confirm that the model developed a hierarchical processing structure that was refined and organized through training.

Constraining the model with cell density and structural connectivity data resulted in a computationally "heavy" but functional network. This demonstrates a key trade-off between biological plausibility and pure engineering efficiency. 
