# Deploy Guide — L_BLACKBIRD
**Tier:** TIER_5_WORLD_NEURO_EMBODIED | **Stack:** Python 3.11, PyTorch 2.10+, ROS2, Blackbird dataset, PAX 27B, AIOSS_FORMAT
**Air-gap capable after initial setup.**

## Prerequisites
Python 3.11+, PyTorch 2.10+, ROS2 Humble, Blackbird dataset (~100GB), T4 GPU.

## Environment
T4 GPU for neural controller inference. ROS2 Humble. 32GB RAM. Blackbird dataset on SSD.

## AIOSS Integration
```bash
aioss init --module L_BLACKBIRD --output ./l_blackbird.aioss
aioss append --chain ./l_blackbird.aioss --payload ./output.bin --module L_BLACKBIRD
aioss verify --chain ./l_blackbird.aioss
```

## Air-Gap Setup
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="L_BLACKBIRD",
    aioss_chain="./L_BLACKBIRD.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./L_BLACKBIRD.aioss --verbose
python -m L_BLACKBIRD.tests.smoke
```
