What is a Bayesian Network?

A Bayesian Network is a data structure used to represent uncertainties and probabilistic relationships between variables. 
It is structured as a Directed Acyclic Graph (DAG) where:  Nodes represent random variables (e.g., whether someone has a gene). 
Edges (Arrows) represent direct conditional dependencies (e.g., parents passing genes to children).Every node in the network is assigned a Conditional Probability Table (CPT), which quantifies the exact probability of an event occurring based on the states of its parent nodes.  
How the Project Maps to the Network ArchitectureIn the project, you are given a CSV dataset representing a family tree. 
Your script dynamically treats this data as a network where each person has two specific random variables:Gene Node (0, 1, or 2): Represents the number of copies of a target mutated gene a person has.  
If a person has no parents listed: They are a "root node" and use a baseline, unconditional population probability (e.g., a 1% default chance of having two copies).  
If parents are listed: The node is conditionally dependent on the mother's and father's gene counts.Trait Node (True or False): Represents whether the physical condition (like hearing loss) is actively expressed. This node is strictly dependent on the person’s own Gene node.        

        [ Mother's Gene ]       [ Father's Gene ]
               \                       /
                \                     /
                 v                   v
                 [ Child's Gene Node ]
                           |
                           v
                 [ Child's Trait Node ]
The Mathematical Logic You ImplementTo program this network's inference engine in Python, you write functions to calculate joint probabilities and normalize them:  

1. Handling Mutation LogicWhen calculating how genes pass from parent to child, you have to account for a biological wildcard: mutations.If a parent has 1 copy of the gene, there is a $50\%$ chance they pass it on.If they have 2 copies, they pass it on $100\%$ of the time—unless it mutates ($1 - \text{mutation rate}$).If they have 0 copies, they pass it on $0\%$ of the time—unless the normal gene mutates into the target gene ($\text{mutation rate}$).
2. Computing Joint ProbabilityYou write a function to calculate the probability of a specific, massive global event—where every single family member simultaneously holds an exact gene count and trait state. Because of the structure of a Bayesian Network, you calculate this cumulative probability by multiplying the individual probabilities of every node given its parents:$$P(\text{System}) = \prod_{i=1}^{n} P(X_i \mid \text{Parents}(X_i))$$
3. Inference by Enumeration & NormalizationBecause real-world data is often incomplete (e.g., you might know a child's physical traits, but have no medical records for the grandparents), your AI has to infer the missing data.You implement Inference by Enumeration:The script loops through all mathematically possible combinations of hidden variables (the powerset of possibilities).  It filters out any combinations that contradict known data.It sums up the joint probabilities of the valid scenarios.Finally, you write a normalization function to scale the raw remaining values so that the final probability distribution of genes and traits for every individual sums up neatly to exactly $1$ ($100\%$).  
