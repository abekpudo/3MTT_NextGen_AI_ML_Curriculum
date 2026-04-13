 Convolution & Pooling: Mathematical Foundations

 1. Introduction
Understanding the mathematical foundations of convolution and pooling operations is essential for designing effective CNN architectures, debugging model behavior, and optimizing performance. This lecture covers the mathematical operations, parameter calculations, receptive fields, and practical implementations.

 2. Convolution Mathematics

 2.1. Mathematical Definition
```
2D Convolution Operation:

Given:
- Input: I of size (H × W × C)
- Kernel: K of size (k × k × C × N)
- Output: O of size (H' × W' × N)

Formula:
O[i, j, n] = Σ Σ Σ I[i+m, j+n, c] × K[m, n, c, n]
            m n c

Where:
- m, n: Kernel spatial indices (0 to k-1)
- c: Input channel index (0 to C-1)
- n: Output channel/filter index (0 to N-1)

Key Parameters:
- Kernel Size (k): Spatial extent (typically 3, 5, 7)
- Stride (s): Step size (1, 2)
- Padding (p): Border pixels added

Output Dimensions:
H' = floor((H - k + 2p) / s) + 1
W' = floor((W - k + 2p) / s) + 1
```

 2.2. Complete Convolution Implementation
```python
import numpy as np
import torch
import torch.nn.functional as F
import matplotlib.pyplot as plt

class ConvolutionOperations:
    """Complete convolution implementation and visualization"""
    
    @staticmethod
    def manual_convolution2d(input_array, kernel, stride=1, padding=0):
        """
        Manual 2D convolution implementation
        
        Parameters:
        - input_array: 2D numpy array (H × W)
        - kernel: 2D numpy array (k × k)
        - stride: Step size
        - padding: Zero padding to add
        """
         Add padding
        if padding > 0:
            input_padded = np.pad(input_array, padding, mode='constant')
        else:
            input_padded = input_array
        
         Get dimensions
        h, w = input_padded.shape
        k_h, k_w = kernel.shape
        
         Calculate output dimensions
        out_h = (h - k_h) // stride + 1
        out_w = (w - k_w) // stride + 1
        
         Initialize output
        output = np.zeros((out_h, out_w))
        
         Perform convolution
        for i in range(out_h):
            for j in range(out_w):
                 Extract region
                region = input_padded[istride:istride+k_h, 
                                     jstride:jstride+k_w]
                 Element-wise multiplication and sum
                output[i, j] = np.sum(region  kernel)
        
        return output
    
    @staticmethod
    def demonstrate_convolution_steps():
        """Visualize convolution step by step"""
        
         Simple 5x5 input
        input_img = np.array([
            [1, 1, 1, 0, 0],
            [0, 1, 1, 1, 0],
            [0, 0, 1, 1, 1],
            [0, 0, 1, 1, 0],
            [0, 1, 1, 0, 0]
        ])
        
         Vertical edge detector
        kernel = np.array([
            [1, 0, -1],
            [1, 0, -1],
            [1, 0, -1]
        ])
        
        print("Input Image (5x5):")
        print(input_img)
        print("\nKernel (3x3) - Vertical Edge Detector:")
        print(kernel)
        
         Perform convolution
        result = ConvolutionOperations.manual_convolution2d(input_img, kernel)
        
        print("\nOutput (3x3):")
        print(result)
        print(f"\nOutput shape: {result.shape}")
        print(f"Receptive field: 3x3")
        print(f"Number of operations: {3333} = 27 multiplications + 27 additions")
        
        return input_img, kernel, result
    
    @staticmethod
    def demonstrate_padding_modes():
        """Show different padding modes"""
        
        input_img = np.ones((5, 5))
        kernel = np.ones((3, 3))
        
        padding_modes = {
            'valid': (0, 'No padding'),
            'same': (1, 'Zero padding to maintain size'),
            'full': (2, 'Maximum padding')
        }
        
        results = {}
        for mode, (pad, description) in padding_modes.items():
            output = ConvolutionOperations.manual_convolution2d(input_img, kernel, padding=pad)
            results[mode] = {
                'output_shape': output.shape,
                'description': description,
                'output': output
            }
            print(f"\n{mode.upper()} ({description}):")
            print(f"  Input: (5, 5) → Output: {output.shape}")
        
        return results

 Demonstrate
conv_ops = ConvolutionOperations()
input_img, kernel, result = conv_ops.demonstrate_convolution_steps()
padding_results = conv_ops.demonstrate_padding_modes()
```

 2.3. Common Convolutional Filters
```python
class CommonFilters:
    """Common image processing filters/kernels"""
    
    @staticmethod
    def get_all_filters():
        """Return dictionary of common filters"""
        
        return {
            'identity': {
                'kernel': np.array([[0, 0, 0],
                                   [0, 1, 0],
                                   [0, 0, 0]]),
                'description': 'Returns original image'
            },
            'box_blur': {
                'kernel': np.ones((3, 3)) / 9,
                'description': 'Simple averaging filter'
            },
            'gaussian_blur': {
                'kernel': np.array([[1, 2, 1],
                                   [2, 4, 2],
                                   [1, 2, 1]]) / 16,
                'description': 'Weighted averaging, smoother than box blur'
            },
            'sharpen': {
                'kernel': np.array([[0, -1, 0],
                                   [-1, 5, -1],
                                   [0, -1, 0]]),
                'description': 'Enhances edges'
            },
            'sobel_vertical': {
                'kernel': np.array([[-1, 0, 1],
                                   [-2, 0, 2],
                                   [-1, 0, 1]]),
                'description': 'Detects vertical edges'
            },
            'sobel_horizontal': {
                'kernel': np.array([[-1, -2, -1],
                                   [0, 0, 0],
                                   [1, 2, 1]]),
                'description': 'Detects horizontal edges'
            },
            'laplacian': {
                'kernel': np.array([[0, 1, 0],
                                   [1, -4, 1],
                                   [0, 1, 0]]),
                'description': 'Detects edges in all directions'
            },
            'emboss': {
                'kernel': np.array([[-2, -1, 0],
                                   [-1, 1, 1],
                                   [0, 1, 2]]),
                'description': 'Creates embossed effect'
            }
        }
    
    @staticmethod
    def apply_filters_to_image(image_path):
        """Apply all filters to an image"""
        import cv2
        
         Load image
        img = cv2.imread(image_path, cv2.IMREAD_GRAYSCALE)
        
        filters = CommonFilters.get_all_filters()
        results = {}
        
        for name, filter_info in filters.items():
            kernel = filter_info['kernel']
            result = ConvolutionOperations.manual_convolution2d(img, kernel)
            results[name] = {
                'result': result,
                'description': filter_info['description']
            }
        
        return results

 Usage
filters = CommonFilters.get_all_filters()
for name, info in filters.items():
    print(f"{name}: {info['description']}")
```

 3. Pooling Operations

 3.1. Mathematical Definitions
```
Pooling Operations:

1. MAX POOLING
   For window W of size (k × k):
   output = max(W)
   
   Properties:
   - Preserves strongest activation
   - Translation invariance
   - Reduces spatial dimensions
   - No learnable parameters

2. AVERAGE POOLING
   For window W of size (k × k):
   output = mean(W) = (1/k²) Σ Σ W[i,j]
                       i j
   
   Properties:
   - Smooths feature maps
   - Preserves background info
   - Less aggressive downsampling

3. GLOBAL AVERAGE POOLING (GAP)
   For entire feature map F of size (H × W):
   output = (1/HW) Σ Σ F[i,j]
                  i j
   
   Properties:
   - Reduces each channel to single value
   - Eliminates need for FC layers
   - Reduces overfitting
   - Maintains spatial information

Output Size Calculations:
- Max/Avg Pool: H' = H/k, W' = W/k (stride = kernel_size)
- Global Pool: Output size = (channels × 1 × 1)
```

 3.2. Complete Pooling Implementation
```python
class PoolingOperations:
    """Comprehensive pooling implementations"""
    
    @staticmethod
    def max_pooling2d(input_array, kernel_size=2, stride=None):
        """
        Manual max pooling implementation
        
        Parameters:
        - input_array: 2D numpy array (H × W) or 3D (H × W × C)
        - kernel_size: Pooling window size
        - stride: Step size (default = kernel_size)
        """
        if stride is None:
            stride = kernel_size
        
         Handle multi-channel input
        if len(input_array.shape) == 3:
            h, w, c = input_array.shape
            output = np.zeros((h // stride, w // stride, c))
            
            for ch in range(c):
                for i in range(0, h - kernel_size + 1, stride):
                    for j in range(0, w - kernel_size + 1, stride):
                        window = input_array[i:i+kernel_size, j:j+kernel_size, ch]
                        output[i//stride, j//stride, ch] = np.max(window)
        else:
            h, w = input_array.shape
            output = np.zeros((h // stride, w // stride))
            
            for i in range(0, h - kernel_size + 1, stride):
                for j in range(0, w - kernel_size + 1, stride):
                    window = input_array[i:i+kernel_size, j:j+kernel_size]
                    output[i//stride, j//stride] = np.max(window)
        
        return output
    
    @staticmethod
    def average_pooling2d(input_array, kernel_size=2, stride=None):
        """Manual average pooling implementation"""
        if stride is None:
            stride = kernel_size
        
        h, w = input_array.shape[:2]
        output_shape = (h // stride, w // stride)
        
        if len(input_array.shape) == 3:
            output_shape += (input_array.shape[2],)
        
        output = np.zeros(output_shape)
        
        for i in range(0, h - kernel_size + 1, stride):
            for j in range(0, w - kernel_size + 1, stride):
                if len(input_array.shape) == 3:
                    window = input_array[i:i+kernel_size, j:j+kernel_size, :]
                    output[i//stride, j//stride, :] = np.mean(window, axis=(0, 1))
                else:
                    window = input_array[i:i+kernel_size, j:j+kernel_size]
                    output[i//stride, j//stride] = np.mean(window)
        
        return output
    
    @staticmethod
    def global_average_pooling(input_array):
        """
        Global average pooling
        Reduces each channel to a single value
        """
        if len(input_array.shape) == 3:
            return np.mean(input_array, axis=(0, 1))
        else:
            return np.mean(input_array)
    
    @staticmethod
    def demonstrate_pooling_comparison():
        """Compare different pooling operations"""
        
         Sample feature map
        feature_map = np.array([
            [1, 3, 2, 4],
            [5, 6, 1, 2],
            [3, 2, 7, 8],
            [1, 4, 3, 2]
        ])
        
        print("Original Feature Map (4×4):")
        print(feature_map)
        print()
        
         Max pooling
        max_pooled = PoolingOperations.max_pooling2d(feature_map, kernel_size=2)
        print("Max Pooling (2×2) → (2×2):")
        print(max_pooled)
        print()
        
         Average pooling
        avg_pooled = PoolingOperations.average_pooling2d(feature_map, kernel_size=2)
        print("Average Pooling (2×2) → (2×2):")
        print(avg_pooled)
        print()
        
         Global average pooling
        global_avg = PoolingOperations.global_average_pooling(feature_map)
        print(f"Global Average Pooling: {global_avg:.2f}")

 Demonstrate pooling
pool_ops = PoolingOperations()
pool_ops.demonstrate_pooling_comparison()
```

 4. Receptive Field Analysis

 4.1. Receptive Field Calculations
```python
class ReceptiveFieldCalculator:
    """Calculate and visualize receptive fields"""
    
    @staticmethod
    def calculate_receptive_field(layers_config):
        """
        Calculate receptive field size through network
        
        layers_config: List of dicts with keys:
        - 'type': 'conv' or 'pool'
        - 'kernel_size': int
        - 'stride': int
        """
        rf_size = 1   Initial receptive field
        stride_product = 1   Cumulative stride
        
        rf_history = [{'layer': 'input', 'rf_size': 1, 'stride': 1}]
        
        for i, layer in enumerate(layers_config):
            if layer['type'] == 'conv':
                 Receptive field grows by (kernel_size - 1)  previous_stride
                rf_size += (layer['kernel_size'] - 1)  stride_product
                
            elif layer['type'] == 'pool':
                rf_size += (layer['kernel_size'] - 1)  stride_product
                stride_product = layer['stride']
            
            rf_history.append({
                'layer': f"{layer['type']}_{i+1}",
                'rf_size': rf_size,
                'stride': stride_product
            })
        
        return rf_history
    
    @staticmethod
    def visualize_receptive_field():
        """Visualize how receptive field grows"""
        
         VGG-like configuration
        vgg_config = [
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'pool', 'kernel_size': 2, 'stride': 2},
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'pool', 'kernel_size': 2, 'stride': 2},
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'conv', 'kernel_size': 3, 'stride': 1},
            {'type': 'pool', 'kernel_size': 2, 'stride': 2},
        ]
        
        rf_history = ReceptiveFieldCalculator.calculate_receptive_field(vgg_config)
        
        print("Receptive Field Growth (VGG-like Architecture):")
        print("="  50)
        for entry in rf_history:
            print(f"{entry['layer']:15} RF: {entry['rf_size']:3d}  Stride: {entry['stride']:3d}")
        
        return rf_history
    
    @staticmethod
    def effective_receptive_field_formula():
        """Display ERF formulas"""
        print("""
        Effective Receptive Field Formulas:
        
        1. FOR CONVOLUTION LAYER:
           RF_out = RF_in + (k - 1) × j_in
           j_out = j_in × s
           
           Where:
           - RF: Receptive field size
           - j: Jump (distance between adjacent features)
           - k: Kernel size
           - s: Stride
        
        2. FOR POOLING LAYER:
           RF_out = RF_in + (k - 1) × j_in
           j_out = j_in × s
        
        3. CUMULATIVE CALCULATION:
           RF = 1 + Σ (k_i - 1) × Π s_j
                    i       j<i
           
           Where we sum over all layers i
        """)

 Usage
rf_calc = ReceptiveFieldCalculator()
rf_history = rf_calc.visualize_receptive_field()
rf_calc.effective_receptive_field_formula()
```

 5. Parameter and FLOP Calculations

 5.1. Model Complexity Analysis
```python
class ModelComplexityAnalyzer:
    """Analyze CNN model complexity"""
    
    @staticmethod
    def conv_layer_params(in_channels, out_channels, kernel_size, bias=True):
        """Calculate convolution layer parameters"""
        weights = in_channels  out_channels  kernel_size  kernel_size
        biases = out_channels if bias else 0
        return weights + biases
    
    @staticmethod
    def conv_layer_flops(h, w, in_channels, out_channels, kernel_size):
        """Calculate convolution layer FLOPs"""
         FLOPs = output_size  kernel_operations
        output_size = h  w  out_channels
        kernel_ops = in_channels  kernel_size  kernel_size
         Multiply-add counts as 2 FLOPs
        return output_size  kernel_ops  2
    
    @staticmethod
    def pooling_layer_flops(h, w, channels, kernel_size):
        """Calculate pooling layer FLOPs"""
         Comparisons for max pool, or additions for avg pool
        return h  w  channels  kernel_size  kernel_size
    
    @staticmethod
    def analyze_simple_cnn():
        """Analyze a simple CNN architecture"""
        
         Assume input: 224x224 RGB
        input_size = (224, 224, 3)
        
        layers = [
            {'name': 'conv1', 'type': 'conv', 'k': 3, 'in': 3, 'out': 64},
            {'name': 'pool1', 'type': 'pool', 'k': 2},
            {'name': 'conv2', 'type': 'conv', 'k': 3, 'in': 64, 'out': 128},
            {'name': 'pool2', 'type': 'pool', 'k': 2},
            {'name': 'conv3', 'type': 'conv', 'k': 3, 'in': 128, 'out': 256},
            {'name': 'pool3', 'type': 'pool', 'k': 2},
        ]
        
        print("CNN Architecture Analysis")
        print("="  70)
        print(f"{'Layer':<10} {'Type':<8} {'Params':<12} {'FLOPs':<15} {'Output':<15}")
        print("-"  70)
        
        h, w, c = input_size
        total_params = 0
        total_flops = 0
        
        for layer in layers:
            if layer['type'] == 'conv':
                params = ModelComplexityAnalyzer.conv_layer_params(
                    layer['in'], layer['out'], layer['k']
                )
                flops = ModelComplexityAnalyzer.conv_layer_flops(
                    h, w, layer['in'], layer['out'], layer['k']
                )
                
                 Update dimensions
                h = h   Same padding
                w = w
                c = layer['out']
                
                output_str = f"{h}×{w}×{c}"
                
            elif layer['type'] == 'pool':
                params = 0   No parameters
                flops = ModelComplexityAnalyzer.pooling_layer_flops(h, w, c, layer['k'])
                
                 Update dimensions
                h = h // 2
                w = w // 2
                
                output_str = f"{h}×{w}×{c}"
            
            total_params += params
            total_flops += flops
            
            print(f"{layer['name']:<10} {layer['type']:<8} {params:<12,} {flops:<15,} {output_str:<15}")
        
        print("-"  70)
        print(f"{'TOTAL':<10} {'':<8} {total_params:<12,} {total_flops:<15,}")
        print(f"\nModel Size: {total_params  4 / 1e6:.2f} MB (float32)")
        print(f"Compute: {total_flops / 1e9:.3f} GFLOPs per forward pass")

 Usage
ModelComplexityAnalyzer.analyze_simple_cnn()
```

 6. Best Practices Summary

 6.1. Convolution and Pooling Guidelines
```
✅ DO:
- Use 3×3 convolutions (most parameter-efficient receptive field)
- Apply 'same' padding to preserve spatial dimensions
- Use stride 2 in pooling for downsampling, not in conv
- Use global average pooling before final classifier
- Calculate receptive field for architecture decisions
- Consider FLOPs and memory for deployment targets

❌ DON'T:
- Use large kernels (5×5, 7×7) without good reason
- Mix different padding modes without documentation
- Use stride > 2 (causes information loss)
- Forget that pooling reduces spatial precision
- Ignore receptive field when designing for object sizes
```

 7. Resources and Further Learning

 7.1. Documentation
- [CS231n Convolution Guide](http://cs231n.github.io/convolutional-networks/)
- [A Guide to Convolution Arithmetic](https://arxiv.org/abs/1603.07285)
- [Receptive Field Calculator](https://fomoro.com/research/article/receptive-field-calculator-9)

 7.2. Key Takeaways
- Convolution applies learnable filters across spatial locations
- Pooling reduces spatial dimensions while preserving features
- Receptive field determines what each neuron "sees"
- Stride controls downsampling rate
- Parameter count depends on kernel size, channels, not input size

---

Next Steps: Implement custom convolution operations, experiment with different filter designs, and calculate receptive fields for your architectures.
