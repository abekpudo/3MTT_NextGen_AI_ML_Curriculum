 Exercise 1: Image Processing Fundamentals

 🎯 Objective
Master basic image processing operations using OpenCV and PIL.

---

 📋 Tasks

 Task 1: Image Loading and Display
Load images using OpenCV and PIL, convert between formats.

```python
import cv2
import numpy as np
from PIL import Image
import matplotlib.pyplot as plt

def load_and_compare(image_path):
    """
    Load image using different libraries and compare.
    
    Args:
        image_path (str): Path to image
        
    Returns:
        dict: Loaded images in different formats
    """
     OpenCV (BGR format)
    img_cv = cv2.imread(image_path)
    
     PIL (RGB format)
    img_pil = Image.open(image_path)
    img_pil_array = np.array(img_pil)
    
     Convert OpenCV to RGB
    img_cv_rgb = cv2.cvtColor(img_cv, cv2.COLOR_BGR2RGB)
    
    return {
        'opencv_bgr': img_cv,
        'opencv_rgb': img_cv_rgb,
        'pil_rgb': img_pil_array,
        'pil_image': img_pil
    }

def display_image_properties(img_dict):
    """
    Display properties of loaded images.
    
    Args:
        img_dict (dict): Dictionary of images
    """
    for name, img in img_dict.items():
        if isinstance(img, np.ndarray):
            print(f"{name}:")
            print(f"  Shape: {img.shape}")
            print(f"  Dtype: {img.dtype}")
            print(f"  Min/Max: {img.min()}/{img.max()}")
            print()

 Test (requires image file)
 images = load_and_compare('test_image.jpg')
 display_image_properties(images)

print("Task 1: Image loading and display")
print("Load images with OpenCV and PIL, compare formats")
```

---

 Task 2: Color Space Conversion
Convert images between color spaces.

```python
def color_space_conversions(image):
    """
    Convert image to different color spaces.
    
    Args:
        image (numpy.ndarray): Input image (BGR)
        
    Returns:
        dict: Images in different color spaces
    """
    conversions = {
        'BGR': image,
        'RGB': cv2.cvtColor(image, cv2.COLOR_BGR2RGB),
        'GRAY': cv2.cvtColor(image, cv2.COLOR_BGR2GRAY),
        'HSV': cv2.cvtColor(image, cv2.COLOR_BGR2HSV),
        'LAB': cv2.cvtColor(image, cv2.COLOR_BGR2LAB),
        'YUV': cv2.cvtColor(image, cv2.COLOR_BGR2YUV),
        'HLS': cv2.cvtColor(image, cv2.COLOR_BGR2HLS)
    }
    
    return conversions

def analyze_color_distribution(image, color_space='RGB'):
    """
    Analyze color distribution in image.
    
    Args:
        image (numpy.ndarray): Input image
        color_space (str): Color space name
        
    Returns:
        dict: Color statistics
    """
    if len(image.shape) == 3:
        channels = cv2.split(image)
        stats = {}
        
        channel_names = {
            'RGB': ['Red', 'Green', 'Blue'],
            'BGR': ['Blue', 'Green', 'Red'],
            'HSV': ['Hue', 'Saturation', 'Value'],
            'LAB': ['L', 'A', 'B'],
            'GRAY': ['Intensity']
        }
        
        names = channel_names.get(color_space, [f'Ch{i}' for i in range(len(channels))])
        
        for name, channel in zip(names, channels):
            stats[name] = {
                'mean': np.mean(channel),
                'std': np.std(channel),
                'min': np.min(channel),
                'max': np.max(channel)
            }
        
        return stats
    else:
        return {
            'Intensity': {
                'mean': np.mean(image),
                'std': np.std(image),
                'min': np.min(image),
                'max': np.max(image)
            }
        }

 Test
 bgr_image = cv2.imread('test.jpg')
 conversions = color_space_conversions(bgr_image)
 
 for space, img in conversions.items():
     stats = analyze_color_distribution(img, space)
     print(f"\n{space}:")
     for ch, values in stats.items():
         print(f"  {ch}: mean={values['mean']:.1f}, std={values['std']:.1f}")

print("\nTask 2: Color space conversions")
print("Convert between RGB, HSV, LAB, etc.")
```

---

 Task 3: Image Transformations
Apply geometric transformations to images.

```python
def apply_transformations(image):
    """
    Apply various geometric transformations.
    
    Args:
        image (numpy.ndarray): Input image
        
    Returns:
        dict: Transformed images
    """
    h, w = image.shape[:2]
    
    transformations = {}
    
     1. Resizing
    transformations['resized_half'] = cv2.resize(image, (w//2, h//2))
    transformations['resized_double'] = cv2.resize(image, (w2, h2))
    
     2. Rotation
    center = (w // 2, h // 2)
    rotation_matrix = cv2.getRotationMatrix2D(center, 45, 1.0)
    transformations['rotated_45'] = cv2.warpAffine(image, rotation_matrix, (w, h))
    
     3. Translation
    translation_matrix = np.float32([[1, 0, 100], [0, 1, 50]])
    transformations['translated'] = cv2.warpAffine(image, translation_matrix, (w, h))
    
     4. Scaling
    transformations['scaled_up'] = cv2.resize(image, None, fx=1.5, fy=1.5)
    transformations['scaled_down'] = cv2.resize(image, None, fx=0.5, fy=0.5)
    
     5. Flipping
    transformations['flipped_h'] = cv2.flip(image, 1)
    transformations['flipped_v'] = cv2.flip(image, 0)
    
     6. Cropping
    transformations['cropped'] = image[h//4:3h//4, w//4:3w//4]
    
    return transformations

def perspective_transform(image, src_points, dst_points):
    """
    Apply perspective transformation.
    
    Args:
        image (numpy.ndarray): Input image
        src_points (numpy.ndarray): Source points
        dst_points (numpy.ndarray): Destination points
        
    Returns:
        numpy.ndarray: Transformed image
    """
    h, w = image.shape[:2]
    
     Compute perspective transform matrix
    matrix = cv2.getPerspectiveTransform(src_points.astype(np.float32), 
                                          dst_points.astype(np.float32))
    
     Apply transformation
    result = cv2.warpPerspective(image, matrix, (w, h))
    
    return result

 Test
 image = cv2.imread('test.jpg')
 transforms = apply_transformations(image)
 
 print(f"Original shape: {image.shape}")
 for name, img in transforms.items():
     print(f"{name}: {img.shape}")

print("\nTask 3: Geometric transformations")
print("Resize, rotate, translate, flip, crop images")
```

---

 Task 4: Image Filtering
Apply various image filters.

```python
def apply_filters(image):
    """
    Apply various image filters.
    
    Args:
        image (numpy.ndarray): Input image
        
    Returns:
        dict: Filtered images
    """
    filters = {}
    
     1. Blurring
    filters['gaussian_blur'] = cv2.GaussianBlur(image, (5, 5), 0)
    filters['median_blur'] = cv2.medianBlur(image, 5)
    filters['bilateral_filter'] = cv2.bilateralFilter(image, 9, 75, 75)
    
     2. Sharpening
    kernel_sharpen = np.array([[-1, -1, -1],
                               [-1,  9, -1],
                               [-1, -1, -1]])
    filters['sharpened'] = cv2.filter2D(image, -1, kernel_sharpen)
    
     3. Edge detection
    gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
    filters['edges_sobel_x'] = cv2.Sobel(gray, cv2.CV_64F, 1, 0, ksize=3)
    filters['edges_sobel_y'] = cv2.Sobel(gray, cv2.CV_64F, 0, 1, ksize=3)
    filters['edges_laplacian'] = cv2.Laplacian(gray, cv2.CV_64F)
    
     4. Morphological operations
    kernel = np.ones((5, 5), np.uint8)
    filters['erosion'] = cv2.erode(image, kernel, iterations=1)
    filters['dilation'] = cv2.dilate(image, kernel, iterations=1)
    filters['opening'] = cv2.morphologyEx(image, cv2.MORPH_OPEN, kernel)
    filters['closing'] = cv2.morphologyEx(image, cv2.MORPH_CLOSE, kernel)
    
    return filters

def custom_filter(image, kernel):
    """
    Apply custom kernel filter.
    
    Args:
        image (numpy.ndarray): Input image
        kernel (numpy.ndarray): Filter kernel
        
    Returns:
        numpy.ndarray: Filtered image
    """
    return cv2.filter2D(image, -1, kernel)

 Common kernels
KERNELS = {
    'identity': np.array([[0, 0, 0], [0, 1, 0], [0, 0, 0]]),
    'edge_detection': np.array([[-1, -1, -1], [-1, 8, -1], [-1, -1, -1]]),
    'box_blur': np.ones((3, 3), np.float32) / 9.0,
    'gaussian_approx': np.array([[1, 2, 1], [2, 4, 2], [1, 2, 1]]) / 16.0,
    'unsharp_mask': np.array([[0, -1, 0], [-1, 5, -1], [0, -1, 0]])
}

 Test
 image = cv2.imread('test.jpg')
 filtered = apply_filters(image)
 
 print(f"Applied {len(filtered)} filters")
 for name in filtered.keys():
     print(f"  - {name}")

print("\nTask 4: Image filtering")
print("Apply blur, sharpen, edge detection, morphological operations")
```

---

 Task 5: Histogram Analysis
Analyze and manipulate image histograms.

```python
def analyze_histogram(image):
    """
    Analyze image histogram.
    
    Args:
        image (numpy.ndarray): Input image
        
    Returns:
        dict: Histogram data
    """
    histograms = {}
    
    if len(image.shape) == 3:
         Color image
        colors = ('b', 'g', 'r')
        for i, col in enumerate(colors):
            histr = cv2.calcHist([image], [i], None, [256], [0, 256])
            histograms[col] = histr
    else:
         Grayscale
        histograms['gray'] = cv2.calcHist([image], [0], None, [256], [0, 256])
    
    return histograms

def histogram_equalization(image):
    """
    Apply histogram equalization.
    
    Args:
        image (numpy.ndarray): Input image
        
    Returns:
        numpy.ndarray: Equalized image
    """
    if len(image.shape) == 3:
         Convert to LAB and equalize L channel
        lab = cv2.cvtColor(image, cv2.COLOR_BGR2LAB)
        l, a, b = cv2.split(lab)
        l_equalized = cv2.equalizeHist(l)
        lab_equalized = cv2.merge([l_equalized, a, b])
        result = cv2.cvtColor(lab_equalized, cv2.COLOR_LAB2BGR)
    else:
        result = cv2.equalizeHist(image)
    
    return result

def adaptive_histogram_equalization(image, clip_limit=2.0, grid_size=8):
    """
    Apply CLAHE (Contrast Limited Adaptive Histogram Equalization).
    
    Args:
        image (numpy.ndarray): Input image
        clip_limit (float): Clip limit
        grid_size (int): Grid size
        
    Returns:
        numpy.ndarray: Enhanced image
    """
    clahe = cv2.createCLAHE(clipLimit=clip_limit, tileGridSize=(grid_size, grid_size))
    
    if len(image.shape) == 3:
        lab = cv2.cvtColor(image, cv2.COLOR_BGR2LAB)
        l, a, b = cv2.split(lab)
        l_enhanced = clahe.apply(l)
        lab_enhanced = cv2.merge([l_enhanced, a, b])
        result = cv2.cvtColor(lab_enhanced, cv2.COLOR_LAB2BGR)
    else:
        result = clahe.apply(image)
    
    return result

 Test
 image = cv2.imread('test.jpg')
 hists = analyze_histogram(image)
 
 print("Histogram analysis complete")
 for channel, hist in hists.items():
     print(f"{channel}: mean={np.mean(hist):.1f}, max={np.max(hist):.0f}")

print("\nTask 5: Histogram analysis and equalization")
print("Analyze histograms, apply equalization and CLAHE")
```

---

 ✅ Completion Checklist

- [ ] Images loaded with OpenCV and PIL
- [ ] Color space conversions working
- [ ] Geometric transformations applied
- [ ] Image filters implemented
- [ ] Histogram analysis complete
- [ ] All functions documented
- [ ] Test cases pass

---

 🎯 Extension Challenge

Create complete image processing pipeline:

```python
class ImageProcessingPipeline:
    """
    Complete image processing pipeline.
    """
    def __init__(self):
        """Initialize pipeline."""
        self.operations = []
    
    def add_operation(self, operation, params=None):
        """
        Add operation to pipeline.
        
        Args:
            operation (str): Operation name
            params (dict): Operation parameters
        """
        self.operations.append((operation, params or {}))
    
    def process(self, image):
        """
        Process image through pipeline.
        
        Args:
            image (numpy.ndarray): Input image
            
        Returns:
            numpy.ndarray: Processed image
        """
         Your implementation
        pass
    
    def batch_process(self, images):
        """
        Process multiple images.
        
        Args:
            images (list): List of images
            
        Returns:
            list: Processed images
        """
         Your implementation
        pass
```
