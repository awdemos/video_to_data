# Video to Data (V2D) Agent Guide

V2D is an end-to-end pipeline that converts human demonstration videos into simulation-ready assets and physics-grounded robot training data. The project is split into three independently runnable stages: ingestion, reconstruction, and robotic grounding.

## Repository Layout

- `video_ingestion_agent/` — LangGraph-driven agentic workflow that segments videos into a queryable action database.
- `reconstruction/` — 3D reconstruction stage: video → 3D data and simulation assets.
- `robotic_grounding/` — RL/policy training stage using Isaac Lab.
- `docs/` — documentation and figures.
- `README.md` — full quickstart and prerequisites.
- `SECURITY.md` — NVIDIA security reporting guidelines.

## Setup Commands

Each stage has its own Python environment. Start with the stage you need:

```bash
# Video ingestion agent
cd video_ingestion_agent
conda env create -f environment.yml  # or pip install -r requirements.txt
conda activate v2d_ingestion

# Reconstruction
cd reconstruction
# Follow the stage-specific README for Isaac Sim / SDFStudio / colmap setup

# Robotic grounding
cd robotic_grounding
# Uses Isaac Lab; see its README for conda env setup
```

## Run Commands

```bash
# Run the video ingestion agent on a demo video
cd video_ingestion_agent
python main.py --video /path/to/demo.mp4 --output ../data/ingested/

# Run reconstruction on ingested output
cd reconstruction
python run_reconstruction.py --input ../data/ingested/ --output ../data/reconstructed/

# Run robotic grounding / policy training
cd robotic_grounding
python train_policy.py --config configs/default.yaml
```

See each stage's README for exact arguments and example data.

## Test Commands

Tests are stage-specific. Look for `tests/` under each stage directory and run:

```bash
cd <stage>
pytest tests/
```

Add unit tests for any new data transformation or policy logic.

## Lint / Code Style

```bash
ruff check video_ingestion_agent/ reconstruction/ robotic_grounding/
black --check video_ingestion_agent/ reconstruction/ robotic_grounding/
```

## Key Conventions

- Each stage writes its artifacts to disk; pipeline boundaries are explicit.
- Use the existing directory structure per stage; shared utilities should go in a top-level `common/` package if created.
- Heavy dependencies (Isaac Sim, SDFStudio, PyTorch) are isolated per stage.

## Common Gotchas

- GPU memory requirements vary by stage; reconstruction and RL training need the most VRAM.
- Do not commit large assets or checkpoint files; use `docs/` links or DVC instead.
- NVIDIA security issues must be reported via the channels in `SECURITY.md`, not public issues.
