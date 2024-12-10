# Split Federated Learning

A research project on implementing and optimizing Split Federated Learning using ResNet10 architecture (modified ResNet architecture) on CIFAR-10 dataset.

## Overview
This project combines the benefits of Federated Learning and Split Learning to achieve optimal privacy and efficiency in distributed model training. The implementation focuses on dividing neural network models between client devices and a central server while optimizing for performance and resource usage.

## System Architecture

### Client Side
- Hardware: Raspberry Pi 4 Model B
- Specifications: ARM quad-core Cortex-A72, 8GB RAM

### Server Side
- Hardware: Intel i7 10th Gen CPU
- Specifications: 16GB RAM

## Implementation Details

### Model Architecture
- Base Model: ResNet10
- Dataset: CIFAR-10 (60,000 32x32 color images)
  - Training Set: 50,000 images
  - Testing Set: 10,000 images
- Framework: PyTorch

### Key Components

#### 1. Cut Layer
- Divides the model between client and server
- Initial layers process data locally
- Generates intermediate results for server processing

#### 2. Bottleneck Layer
- Compresses high-dimensional data
- Reduces overfitting
- Improves abstract representation learning
- Compression ratio: 4 (output channels = input channels / 4)

#### 3. Quantization
- Converts data from `torch.float32` to `torch.qint8`
- Optimizes model for resource-constrained devices
- Reduces communication overhead

## Results and Analysis

### Optimization Configurations Performance

| Config | Bottleneck | Quantization | Training Acc | Testing Acc | Send (MB) | Receive (MB) | Training Time (s) | Energy (Wh) |
|--------|------------|--------------|--------------|-------------|-----------|--------------|------------------|-------------|
| 1 | ❌ | ❌ | 52.98% | 50.60% | 781.77 | 782.29 | 12444.01 | 10.35 |
| 2 | ✓ | ❌ | 56.27% | 53.49% | 195.87 | 196.39 | 1977.18 | 1.64 |
| 3 | ❌ | ✓ | 53.92% | 51.14% | 195.86 | 196.39 | 1958.82 | 1.63 |
| 4 | ✓ | ✓ | 54.56% | 51.90% | 49.41 | 49.94 | 839.82 | 0.23 |

### Cut Layer Performance Analysis

| Cut Layer | Training Acc | Testing Acc | Send (MB) | Receive (MB) | Training Time (s) | Energy (Wh) |
|-----------|--------------|-------------|-----------|--------------|------------------|-------------|
| Layer 1 | 54.56% | 51.90% | 49.41 | 49.94 | 839.82 | 0.23 |
| Layer 2 | 44.72% | 43.74% | 25.98 | 26.51 | 980.37 | 0.81 |
| Layer 3 | 37.89% | 37.27% | 17.71 | 18.24 | 2183.60 | 1.81 |

### Extended Training Results (10 Epochs)
Cut Layer 1 with optimization achieved:
- Training Accuracy: 72.89%
- Testing Accuracy: 59.09%
- Data Transfer: ~494MB (Send), ~499MB (Receive)
- Training Time: 5611.96s
- Energy Consumption: 0.007 Wh

## Key Findings

1. **Optimal Configuration**: Cut-Layer 1 with Quantization and Bottleneck showed the best balance:
   - Low energy consumption (0.23 Wh)
   - Manageable memory usage (617.80 MB)
   - Reasonable accuracy levels

2. **Impact of Optimizations**:
   - Bottleneck compression significantly reduced data transfer size and energy consumption
   - Quantization further improved efficiency with minimal accuracy impact
   - Combined optimizations (Config 4) achieved best efficiency-performance trade-off

3. **Cut Layer Effects**:
   - Deeper cut-layers showed reduced accuracy but smaller data transfer sizes
   - Computational demand increased with deeper layers on client side
   - Layer 1 provided optimal balance when combined with optimizations
