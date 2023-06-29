## 🧠 to be continued... 🧠

#### src/ctransformers
- Experiment compiling and optimizing ctransformers for Graviton3 instances (ARM64)

#### src/ggml
- Experiment compiling and optimizing ggml for Graviton3 instances (ARM64)
- Starchat-beta running at 80ms/token on a c7g.4xlarge instance

#### src/jobgpt
- Fine-tuning pythia2.8B on amazon job postings
- Using p4d.24xlarge with smdistributed dataparallel
- HuggingFace `accelerate` & `peft`