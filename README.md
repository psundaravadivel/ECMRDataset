# Coding
import pandas as pd
import numpy as np
import random

# Constants
NUM_NODES = 1000
NUM_REDUCERS = 10
REGIONS = ["US-East", "US-West", "Europe", "Asia", "Australia", "Africa"]
REDUCERS = [{"Reducer_ID": f"R{i+1}", "Region": random.choice(REGIONS)} for i in range(NUM_REDUCERS)]

# Helper function to simulate latency based on region difference
def estimate_latency(node_region, reducer_region):
    return random.randint(5, 50) if node_region == reducer_region else random.randint(60, 200)

# Generate Nodes
nodes = []
for i in range(NUM_NODES):
    node_id = f"N{i+1}"
    region = random.choice(REGIONS)
    data_size = round(np.random.uniform(100, 1000), 2)
    bandwidth = round(np.random.uniform(10, 100), 2)
    cpu_cores = random.randint(2, 32)
    memory_gb = random.randint(4, 256)

    # Match to nearest reducer with best latency and load
    reducer_scores = []
    for reducer in REDUCERS:
        latency = estimate_latency(region, reducer["Region"])
        score = 1 / (latency + 1e-6)  # Higher score = better
        reducer_scores.append((score, reducer))

    best_reducer = max(reducer_scores, key=lambda x: x[0])[1]

    nodes.append({
        "Node_ID": node_id,
        "Region": region,
        "Data_Size_MB": data_size,
        "Bandwidth_Mbps": bandwidth,
        "Latency_ms": estimate_latency(region, best_reducer["Region"]),
        "CPU_Cores": cpu_cores,
        "Memory_GB": memory_gb,
        "Assigned_Reducer_ID": best_reducer["Reducer_ID"],
        "Reducer_Location": best_reducer["Region"]
    })

# Create DataFrame
df = pd.DataFrame(nodes)

# Save to Excel
df.to_excel("geo_distributed_dataset.xlsx", index=False)

print("✅ Dataset created and saved to 'geo_distributed_dataset.xlsx'")
