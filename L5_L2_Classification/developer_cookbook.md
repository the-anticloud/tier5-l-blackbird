# Developer Cookbook — L_BLACKBIRD
**Stack:** Python 3.11, PyTorch 2.10+, ROS2, Blackbird dataset, PAX 27B, AIOSS_FORMAT
**Domain:** Blackbird: high-speed drone dynamics dataset and neural controller for Anticloud UAV

## Neural controller inference
```python
from l_blackbird import BlackbirdController

controller = BlackbirdController(
    checkpoint="./blackbird_controller_checkpoint/",
    pax_model="./pax-27b-q4.gguf",
    aioss_chain="./blackbird.aioss"
)

# Plan and execute mission
mission = controller.plan_mission(
    objective="Survey 500m x 500m area for heat signatures",
    pax_model="./pax-27b-q4.gguf"
)
print(f"Waypoints: {len(mission.waypoints)}, Duration: {mission.duration_s:.0f}s")

# Execute (in sim)
result = controller.execute_in_sim(mission)
print(f"Coverage: {result.area_covered_pct:.1%}, Collisions: {result.n_collisions}")
```

## AIOSS Chain Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```
