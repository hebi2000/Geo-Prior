# Geo-Prior
🌍 Geo-Gated Net: Geo-Prior Guided Multi-Modal Fusion for Remote Sensing Semantic Segmentation (Coming Soon)
Official PyTorch implementation of the paper: "Geo-Prior Guided Fine-Tuning: A Gated Multi-Modal Fusion Network for Few-Shot Remote Sensing Semantic Segmentation with Frozen Vision Transformers"This repository provides a lightweight, multi-modal, and high-performance semantic segmentation network tailored for high-resolution remote sensing imagery. By cleverly combining a frozen DINOv3 (ViT-Large) backbone with our proposed H-M3E (Hierarchical Multi-Modal Mixture-of-Experts) and SimpleUperHead, the model achieves state-of-the-art performance with remarkably low training costs.🔥 Highlights (Core Capabilities)🚀 Parameter-Efficient "Surgical" Tuning: We freeze the majority of the ViT-Large backbone to retain its robust universal priors and only unfreeze the head, tail (Layers 5, 23), normalization layers, and adapters. This drastically reduces GPU memory consumption and prevents overfitting in few-shot scenarios.🧠 Deep Multi-Modal Interaction (H-M3E): Replaces naive concatenation with a dynamic Reliability Router. It adaptively routes features through decoupled experts (LoRA-Opt, LoRA-Aux, Spatial Expert) and merges them via a Channel-Spatial Dense Fusion (CSDF) module.⚡ Lightweight & Efficient Head: We replace the heavy Mask2Former with a streamlined SimpleUperHead, significantly reducing inference latency and making deployment feasible without sacrificing accuracy.🔍 Full-Scale Perception: Integrates Spatial Gated Fusion in deep layers for semantic alignment, and Shallow Injection (Stride-4) from an Auxiliary CNN Expert to preserve high-frequency details like tiny boundaries and small objects.🏗️ Architecture Overview(Please upload your architecture diagram and replace the link above)Our model adopts a Dual-Stream + Interactive design paradigm:Optical Stream (Main): Powered by DINOv3 (ViT-Large). Processes RGB images.Auxiliary Stream (Side): A lightweight 3-layer CNN processes multi-modal auxiliary data (e.g., NDVI, SAR, DSM). It provides Shallow Injection for high-res details and Deep Gates/Features for semantic guidance.Deep Interaction: H-M3E Adapters are inserted into ViT layers 18, 20, and 22 for token-level multimodal mixing.Decoding: An asymmetric FPN combined with a SimpleUperHead fuses multi-scale features (P4, P8, P16, P32) for final pixel-level classification.📈 Main Results(Replace this with your actual experimental table. Below is a template based on your logs)Model ArchitectureBackboneParams (Trainable)mIoU (%)OA (%)Ditch (IoU)Road (IoU)Baseline (FPN)DINOv3-L~304 M29.8584.3914.1857.72FPN + H-M3EDINOv3-L~41.8 M42.2092.0429.8455.56Geo-Gated Net (Ours)DINOv3-L~54.1 M43.8893.1835.72 🚀58.99 🚀🛠️ Getting Started1. InstallationBash# Clone the repository
git clone https://github.com/YourUsername/Geo-Gated-Net.git
cd Geo-Gated-Net

# Create conda environment
conda create -n geo-gated python=3.10 -y
conda activate geo-gated

# Install PyTorch and dependencies
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118
pip install -r requirements.txt
2. Data PreparationYour dataset should be organized as follows. Auxiliary data (Aux) should be strictly pixel-aligned with the Optical (RGB) images.Plaintextdataset/
├── train/
│   ├── rgb/          # [H, W, 3] Optical Images
│   ├── aux/          # [H, W, C] Tri-spectral Aux (e.g., NDVI+Edge+Gray)
│   └── masks/        # [H, W] Ground Truth Labels
└── val/
    ├── rgb/
    ├── aux/
    └── masks/
3. TrainingWe provide training scripts utilizing our Head-to-Tail Surgical Tuning strategy.Bashpython train.py --config configs/geo_gated_dinov3.yaml --batch-size 4 --epochs 100
4. InferenceBashpython inference.py --model-path checkpoints/best_model.pth --input-rgb sample.jpg --input-aux sample_aux.tif
⚠️ Known Limitations & Future WorkWhile our model achieves leading accuracy, we transparently acknowledge the following limitations to inspire future research:Modality Imbalance: The main stream utilizes a massive ViT-Large, whereas the auxiliary expert is a shallow 3-layer CNN. Highly complex SAR textures might be under-represented.Alignment Sensitivity: SpatialGatedFusion relies on pixel-wise operations, making the model sensitive to raw data registration errors between RGB and Aux images.Fixed Resolution Constraint: Due to ViT's fixed patch size (16x16), the model requires specific input dimensions. Inference on arbitrary resolutions requires padding/interpolation, which may introduce edge artifacts.We welcome pull requests and collaborative efforts to address these engineering challenges!📖 CitationIf you find our work or code useful for your research, please consider citing our paper:代码段@article{YourName2026GeoGated,
  title={Geo-Prior Guided Fine-Tuning: A Gated Multi-Modal Fusion Network for Few-Shot Remote Sensing Semantic Segmentation with Frozen Vision Transformers},
  author={Your Name and Co-authors},
  journal={TBD (e.g., IEEE Transactions on Geoscience and Remote Sensing)},
  year={2026}
}
🤝 AcknowledgementsThis project is built upon the incredible works of DINOv3 and UperNet. We thank the authors for open-sourcing their codes.💡 README 设计巧思（写给您的建议）：Badges (徽章)：顶部的徽章能瞬间提升项目的专业感。"Surgical" Tuning (外科手术式微调)：把您文档里这个极具画面感的词直接翻译过来用作卖点，非常抓眼球，能让同行立刻明白您的训练策略。Known Limitations 的巧妙转化：把您原本列出的“缺点”放在 README 最后作为 Future Work 探讨，这是顶会大佬开源时非常喜欢的做法（例如 Meta 或 Google 的开源项目），显得极为自信且坦诚，能极大增加学术好感度。
