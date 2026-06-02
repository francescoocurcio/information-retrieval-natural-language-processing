# Logical-Mathematical Parameter Efficient Fine Tuning for Open Large Languge Models

🧑🏼‍🏫 This project focuses on the fine-tuning of Large Language Models (LLMs) for logical-mathematical tasks, addressing the challenge of "catastrophic forgetting". Through Parameter-Efficient Fine-Tuning (PEFT) techniques, particularly LoRA, models such as Gemma 2 2B, Llama 3.2 3B, and Qwen3 4B have been adapted and specialized in complex domains, maintaining a balance between general and specific skills.

💻 What has been made:

Dataset Origin
The data used ranges from the mathematical to the formal logical domain. Initially, extensive datasets such as OpenMathInstruct-2 and NuminaMath 1.5 were explored, before permanently converging on the MATH dataset (about 7,500 examples), chosen for its cleanliness and the absence of non-textual elements like graphs or unparsable LaTeX blocks. For the logical domain, the data comes from four sources: Common Sense QA (common sense inferences), LogicInference OA (propositional and first-order logic), MALLS v0 (translation between natural language and first-order logic - FOL), and FOLIO (logical reasoning on FOL premises).

Pre-analysis Phase
This phase involved an in-depth exploration of the datasets to understand the semantic similarities between the problems. Using models like MathBERT and S-BERT, embeddings of the mathematical questions were generated. Subsequently, dimensionality reduction (via PCA and UMAP) and a clustering algorithm (DBSCAN) were applied to identify latent clusters. The analysis revealed a high data density without clearly separable clusters, confirming the strong and complex interconnection of mathematical sub-topics (algebra, geometry, etc.).

Pre-processing Phase
In this phase, strict filters were applied to prepare the data for training. First, a rigid limit of 2048 tokens was imposed for the context window. The data was cleaned of invalid or non-textual elements (images, external links) and filtered to exclude text outliers that were too short or too long (beyond the 99th percentile). Targeted sampling was also performed to mitigate the strong imbalance of question categories and types. Finally, the records were transformed into a conversational format by applying the native chat templates of the respective LLMs.

Classification & Fine-Tuning Phase (LoRA)
To specialize the models without incurring expensive full fine-tuning and to prevent the complete overwriting of prior knowledge, the Low-Rank Adaptation (LoRA) technique was employed. Five separate adapters were configured and trained (rank r=8 or r=4), dedicated in isolation to each task/dataset. The models, quantized to 4 bits and optimized with AdamW, were then tested using lm-evaluation-harness, showing notable improvements in QA and logical inference tasks, while encountering intrinsic challenges related to the capacity of smaller models on purely mathematical problems.

Advanced Phases (Catastrophic Forgetting Mitigation)
To overcome the limits of isolated training and address catastrophic forgetting in multi-task scenarios, experimental architectural solutions were implemented. The main innovation is the development of an attention-based router with literal bias: the system analyzes the model's internal attention metrics (entropy, focus, block attention, and forward/backward ratio) combined with textual detection in the input to dynamically select and blend the LoRA adapters. Alternative techniques were also tested, such as CURLoRA and Reverse Distillation, an experiment aimed at transferring the weights of a LoRA adapter from a compact model (e.g., 2B) to a larger one (e.g., 9B) by scaling the matrices to preserve the extracted knowledge.
