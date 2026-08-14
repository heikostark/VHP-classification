# VHP-Classification

**Reconstruction and Classification of Connective Tissue Distribution in Humans**

A comprehensive collection of processing scripts for analyzing and classifying connective tissue distribution using the Visible Human Project (VHP) dataset.

## Overview

**VHP-Classification** is a project dedicated to the detailed analysis, reconstruction, and classification of connective tissue distribution patterns in human anatomical specimens. Using high-resolution cross-sectional imaging data from the Visible Human Project, this repository contains automated processing scripts that segment, analyze, and quantify connective tissue characteristics.

The project combines medical imaging analysis, tissue segmentation, quantitative morphology, and tensor field computation to create a comprehensive classification of connective tissue properties throughout the human body.

## Project Context

### Visible Human Project (VHP)

The Visible Human Project is a landmark initiative by the U.S. National Institutes of Health (NIH) that created complete, anatomically detailed, three-dimensional representations of normal adult human male and female bodies. The project involved acquiring high-resolution cross-sectional imaging data through:

- **CT (Computed Tomography)** - For skeletal and dense tissue visualization
- **MRI (Magnetic Resonance Imaging)** - For soft tissue and neural tissue visualization
- **Cryosectioning** - High-resolution physical cross-sections of the whole body at 1mm intervals
- **Color Photography** - High-resolution digital color images of cryosection surfaces

### Research Focus

This project specifically focuses on:
- **Connective tissue characterization** - Analyzing fibrous tissue distribution
- **Spatial organization** - Understanding how connective tissue is organized throughout the body
- **Quantitative analysis** - Measuring tissue thickness, distribution, and properties
- **Tensor field computation** - Representing directional properties of tissue organization
- **Sex-based differences** - Comparing male and female connective tissue patterns

## Repository Contents

### Processing Scripts

All scripts are written for the **imagexd** image processing framework and follow a hierarchical processing pipeline:

```
Raw VHP Image Data
        ↓
[convert.run] - Segmentation & Color Splitting
        ↓
[male-patch.run / female-patch.run] - Gap Filling
        ↓
[mass.run] - Segment Component Quantification
        ↓
[t.run] - Connective Tissue Thickness Calculation
        ↓
[c2.run] - Tensor Field Computation
        ↓
[e1.run] - Eigenvalue/Eigenvector Analysis
        ↓
Final Classification & Analysis
```

#### Script Descriptions

##### 1. **convert.run** - Image Segmentation and Color Processing

**Purpose:** Converts raw VHP imaging data and segments connective tissue based on color information.

**Key Operations:**
- Load raw 24-bit color image data (4096 x 2700 pixels)
- Peak/mean normalization for local blue channel
- Color balance adjustment using midtone correction
- Outlier removal to reduce noise
- Color-point based segmentation:
  - Gray (background/artifacts)
  - Red (connective tissue)
  - Light red (transitional tissue)
  - Green (specific tissue type)
  - Blue (another tissue component)
  - Black (empty/no tissue)
- Channel splitting into individual grayscale images
- RGB channel combination for enhanced contrast
- 8-bit conversion for downstream processing

**Input:** Raw RGB image files
**Output:** 
- Cleaned and balanced images
- Channel-separated grayscale images
- Combined RGB segmentation
- 8-bit processed image

**Typical Processing:**
```
Input: 1200.rgb, 1400.rgb, 1600.rgb, ..., 2800.rgb
         (VHP cryosection images)
Output: 1200-8bit.png, 1400-8bit.png, ... (segmented)
```

##### 2. **male-patch.run** & **female-patch.run** - Gap Filling

**Purpose:** Fills segmentation gaps caused by image artifacts, tissue boundaries, or processing artifacts.

**Key Operations:**
- Morphological operations for gap closure
- Interpolation across segmentation boundaries
- Artifact removal and boundary smoothing
- Sex-specific processing parameters:
  - Male anatomy considerations
  - Female anatomy considerations

**Input:** Segmented 8-bit images from convert.run
**Output:** Gap-filled binary or grayscale segmentation

**Note:** Separate scripts account for anatomical differences between male and female specimens.

##### 3. **mass.run** - Segment Component Quantification

**Purpose:** Calculates quantitative measurements of segmented tissue components.

**Key Operations:**
- Volume calculation of identified tissue components
- Component size analysis
- Connectivity analysis
- Statistical distribution of tissue areas
- Generation of quantitative metrics for each segmented region

**Input:** Gap-filled segmentation images
**Output:** 
- Component-wise mass measurements
- Tissue distribution statistics
- Volumetric data for analysis

**Key Metrics:**
- Individual component volumes
- Tissue percentage distribution
- Component connectivity statistics

##### 4. **t.run** - Connective Tissue Thickness Calculation

**Purpose:** Computes the thickness of connective tissue at each location.

**Key Operations:**
- Distance transform computation
- Local thickness measurement using perpendicular distance
- Medial axis calculation
- Thickness quantification along tissue boundaries
- Statistical analysis of thickness distribution

**Input:** Mass measurements from mass.run
**Output:** 
- Thickness maps (3D thickness field)
- Thickness statistics per region
- Thickness distribution histograms

**Key Metrics:**
- Mean thickness
- Minimum/maximum thickness
- Thickness variance
- Thickness distribution profile

##### 5. **c2.run** - Tensor Field Computation

**Purpose:** Calculates second-order tensor fields representing directional properties of tissue organization.

**Key Operations:**
- 16-bit scalar image loading
- Connection method configuration (method 6: advanced connectivity)
- Thread-based parallel processing (up to 40 threads)
- Tensor computation with 8-neighborhood connectivity (x2)
- Full-scan mode optimization

**Input:** High-resolution segmented images (16-bit NIFTI format)
**Output:** Tensor field representation (3D tensor data)

**Configuration:**
```
Connection method: 6 (advanced)
Neighborhood: 8-connected (x2)
Threading: 40 threads (parallel processing)
Format: NIFTI compressed (.nii.gz)
```

**Key Computation:**
- Local structure tensor calculation
- Gradient-based orientation analysis
- Anisotropy measurement
- Directional preference quantification

##### 6. **e1.run** - Eigenvalue/Eigenvector Analysis

**Purpose:** Analyzes eigenvalues and eigenvectors of computed tensor fields to characterize tissue organization.

**Key Operations:**
- Eigenvalue decomposition of tensor fields
- Principal direction identification
- Anisotropy quantification (FA - Fractional Anisotropy)
- Tensor invariant computation
- Directionality analysis

**Input:** Tensor fields from c2.run
**Output:**
- Eigenvalue maps (λ₁, λ₂, λ₃)
- Eigenvector fields (principal directions)
- Anisotropy indices (FA, MD, RA)
- Directionality classifications

**Key Metrics:**
- Fractional Anisotropy (FA): Degree of directional preference
- Mean Diffusivity (MD): Average magnitude
- Relative Anisotropy (RA): Normalized directional strength
- Principal direction maps

### Reference Files

#### **imagexd.macro** - Command Reference

Complete reference documentation for imagexd macro language commands, including:
- Image processing operations
- Scalar/Vector/Tensor operations
- File I/O operations
- Conditional logic and loops
- Variable management
- Mathematical operations

### License File

**LICENSE** - GNU General Public License v3.0 (GPL-3.0)

All scripts and associated documentation are distributed under the GPL-3.0 license, allowing free use, modification, and distribution with attribution.

## Technical Details

### Software Requirements

#### Primary Tool: imagexd

**imagexd** is the core image and data processing framework used for all processing steps.

**Website:** https://stark-jena.de/research-interests/software/imagexd/

**Features:**
- Multi-format image I/O (PNG, NIFTI, RAW, and many others)
- Advanced image processing algorithms
- Tensor field computation
- Morphological operations
- Statistical analysis
- Macro scripting language for automation
- Multi-threaded processing
- Support for 8-bit, 16-bit, and 32-bit data

#### Input Data

**VHP Cryosection Images:**
- Format: 24-bit RGB raw files (.rgb)
- Resolution: 4096 x 2700 pixels
- Spacing: 1mm intervals through body
- Color-coded tissue identification

**VHP Data Access:**
The Visible Human Project data is publicly available from:
- National Library of Medicine: https://www.nlm.nih.gov/research/visible/visible_human.html
- Dataset includes both male (Visible Human Male) and female (Visible Human Female) specimens

### Data Processing Pipeline

```
Step 1: Image Segmentation (convert.run)
├─ Load raw RGB images
├─ Color-based tissue classification
├─ Channel splitting and combination
└─ Output: Segmented 8-bit images

Step 2: Gap Filling (male-patch.run / female-patch.run)
├─ Sex-specific artifact removal
├─ Morphological closure
└─ Output: Cleaned segmentation

Step 3: Quantification (mass.run)
├─ Component analysis
├─ Volume calculation
└─ Output: Tissue measurements

Step 4: Thickness Analysis (t.run)
├─ Distance transform
├─ Thickness mapping
└─ Output: Thickness field

Step 5: Tensor Computation (c2.run)
├─ Connection analysis
├─ Tensor field generation
└─ Output: Second-order tensors

Step 6: Eigenanalysis (e1.run)
├─ Tensor decomposition
├─ Principal direction extraction
└─ Output: Eigenvalue/eigenvector maps
```

## Usage Guide

### Prerequisites

1. **Install imagexd** from https://stark-jena.de/research-interests/software/imagexd/
2. **Obtain VHP Data** from https://www.nlm.nih.gov/research/visible/visible_human.html
3. **Prepare input images** in raw RGB format

### Running the Processing Pipeline

#### Option 1: Process Individual Scripts

```bash
# Step 1: Segmentation
imagexd convert.run

# Step 2: Gap filling (choose male or female)
imagexd male-patch.run
# or
imagexd female-patch.run

# Step 3: Quantification
imagexd mass.run

# Step 4: Thickness calculation
imagexd t.run

# Step 5: Tensor field computation
imagexd c2.run

# Step 6: Eigenvalue analysis
imagexd e1.run
```

#### Option 2: Batch Processing

Modify the script file paths and run:

```bash
# Process entire dataset
imagexd convert.run > convert.log 2>&1
imagexd male-patch.run > male-patch.log 2>&1
# ... continue with other steps
```

### Customization

Each script can be modified for:

**Image Resolution:**
```
image.loadraw filename 0 4096 2700
// Change 4096 and 2700 to match your input image dimensions
```

**Color Thresholds:**
```
image.split.colorpoint <192,192,192> <255,0,0> ...
// Adjust RGB values for color-based segmentation
```

**Threading:**
```
thread.max := 40
// Adjust number of parallel threads for your system
```

**Connection Method:**
```
scalar.connection.method 6
// Change connectivity algorithm (1-6 available)
```

## Research Applications

### Anatomical Understanding

- Sex-based differences in connective tissue distribution
- Regional tissue thickness variation
- Tissue organization patterns
- Structural anisotropy analysis

### Medical Imaging

- Reference atlas creation
- Tissue segmentation algorithm validation
- 3D anatomical model generation
- Clinical correlation studies

### Biomechanical Modeling

- Tissue property characterization
- Mechanical behavior understanding
- Material property mapping
- FEBio simulation model preparation

### Educational Use

- Medical student anatomy training
- Anatomical visualization
- 3D anatomy understanding
- Cross-sectional anatomy review

## Output Data Formats

### Image Formats
- **PNG** - Standard 8-bit and 16-bit images
- **NIFTI (.nii.gz)** - Medical imaging format for 3D volumes
- **RAW** - Headerless binary format for custom applications

### Data Types
- **8-bit grayscale** - Segmentation and intermediate results
- **16-bit grayscale** - High-precision measurements
- **RGB/RGBA** - Multi-channel visualizations
- **Tensor fields** - 3x3 symmetric matrices per voxel
- **Eigenvalue/eigenvector data** - Decomposition results

### Analysis Outputs
- Volumetric measurements (mm³)
- Thickness maps (mm)
- Anisotropy indices (dimensionless)
- Direction vectors (normalized)
- Statistical summaries

## Performance Considerations

### Computational Requirements

**Typical System Specifications:**
- **CPU:** Modern multi-core processor (4+ cores recommended)
- **RAM:** 16 GB minimum, 32+ GB recommended
- **Storage:** 1-2 TB for full VHP dataset
- **GPU:** Optional (accelerated processing with CUDA support)

**Processing Times (Approximate):**
- Segmentation (convert.run): 2-5 minutes per image
- Gap filling (patch.run): 1-2 minutes per image
- Mass calculation (mass.run): 1 minute per image
- Thickness calculation (t.run): 2-3 minutes per image
- Tensor field (c2.run): 3-5 minutes per image
- Eigenanalysis (e1.run): 2-3 minutes per image

**Total Pipeline:** ~15-30 minutes per cryosection image

### Optimization Tips

1. **Use threaded processing:** Set `thread.max` to number of available cores
2. **Process multiple images:** Batch processing reduces overhead
3. **Use appropriate bit depth:** 8-bit for initial segmentation, 16-bit for final analysis
4. **Enable compression:** Use .nii.gz format to reduce file sizes
5. **Parallel execution:** Run multiple imagexd instances on different images

## Scientific Background

### Connective Tissue Structure

Connective tissue forms a continuous network throughout the human body, providing:
- **Support structure** for organs and tissues
- **Mechanical strength** and elasticity
- **Nutrient transport** through capillaries
- **Immune function** through cellular components
- **Regenerative capacity** for tissue repair

### Tissue Anisotropy

The tensor-based analysis reveals tissue anisotropy:
- **Isotropic regions:** Random fiber orientation (equal in all directions)
- **Anisotropic regions:** Preferred fiber orientation (directional preference)
- **Highly organized tissues:** Muscle, tendon, ligament (highly anisotropic)
- **Amorphous tissues:** Loose areolar tissue (nearly isotropic)

### Quantitative Metrics

**Fractional Anisotropy (FA):**
- Range: 0 (isotropic) to 1 (perfectly anisotropic)
- High FA: Highly organized tissue
- Low FA: Random/disorganized tissue

**Mean Anisotropy (MA):**
- Average directionality magnitude
- Indicates average structural organization

**Thickness Analysis:**
- Local tissue thickness measurement
- Reveals structural hierarchy
- Important for biomechanical properties

## Related Research

### Associated Projects

- **Cloud2:** Data visualization and geometric analysis tool
  - https://github.com/heikostark/Cloud2
  - Used for 3D visualization of results

- **imagexd:** Core image processing framework
  - https://stark-jena.de/research-interests/software/imagexd/
  - All processing executed through this tool

- **Gordon1966 FEBio Plugin:** Muscle modeling
  - https://github.com/heikostark/Gordon1966
  - Used for biomechanical applications

### References

**Visible Human Project:**
- Spitzer, V., Ackerman, M. J., Scherzinger, A. L., & Whitlock, D. (1996). "The visible human male: a technical report." Journal of the American Medical Informatics Association, 3(2), 118-130.

**Connective Tissue Research:**
- Detailed connective tissue analysis and classification
- Anatomical variation studies
- Biomechanical property relationships
- Sex-based differences in tissue organization

### Research Website

For more information on connective tissue research and measurements:
https://stark-jena.de/research-interests/measurement/connective-tissue/

## Documentation

### imagexd Command Reference

Complete macro language reference included in `imagexd.macro`:
- Image operations (load, save, transform, filter)
- Scalar operations (arithmetic, morphological)
- Vector operations (calculation, analysis)
- Tensor operations (computation, decomposition)
- Control structures (loops, conditionals)
- File I/O operations
- Variable management
- Function definitions

### File Format Specifications

**Raw RGB Format (.rgb):**
- 24-bit color (8 bits each for R, G, B)
- No header information
- Row-major byte ordering
- Dimensions: 4096 x 2700 pixels

**NIFTI Format (.nii.gz):**
- Neuroimaging Informatics Technology Initiative standard
- Includes header with spacing and orientation
- Gzip-compressed for storage efficiency
- 16-bit or 32-bit precision options

## Troubleshooting

### Common Issues

**Problem:** "File not found" errors
- **Solution:** Verify file paths and extensions match script expectations
- Check working directory: `pwd`
- Verify imagexd installation

**Problem:** Out of memory errors
- **Solution:** Process fewer images at once
- Reduce image resolution or bit depth
- Increase available RAM
- Use 64-bit imagexd version

**Problem:** Color segmentation not working
- **Solution:** Adjust color thresholds in convert.run
- Verify input image color profile
- Check image histogram for color ranges
- Test with smaller subset first

**Problem:** Gap filling not effective
- **Solution:** Adjust morphological operation parameters
- Use male-specific or female-specific version
- Consider manual post-processing
- Verify input segmentation quality

**Problem:** Slow processing
- **Solution:** Increase thread count if CPU allows
- Reduce floating-point precision if acceptable
- Process images in parallel batches
- Check for disk I/O bottlenecks

## Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Document your changes
4. Submit a pull request with detailed description

### Areas for Contribution

- Additional anatomical site-specific processing
- Improved gap-filling algorithms
- Enhanced tissue classification methods
- Validation against manual segmentation
- Performance optimizations
- Documentation improvements
- Additional example datasets

## Citation

If you use this work in your research, please cite:

```bibtex
@repository{vhp-classification,
  title={VHP-Classification: Reconstruction and Classification 
         of Connective Tissue Distribution in Humans},
  author={Stark, Heiko},
  year={2025},
  url={https://github.com/heikostark/VHP-classification}
}
```

**Associated Publication Keywords:**
- Visible Human Project
- Connective tissue analysis
- Medical image segmentation
- Tensor field analysis
- Anatomical variation
- Biomechanical properties

## License

This project is licensed under the **GNU General Public License v3.0** (GPL-3.0).

You are free to:
- ✅ Use the software for any purpose
- ✅ Study how it works
- ✅ Modify and improve it
- ✅ Share it with others

With the requirement to:
- ℹ️ Provide attribution to the original author
- ℹ️ Disclose source modifications
- ℹ️ Use the same license for derivative works

See LICENSE file for full details.

## Author and Attribution

**Heiko Stark**  
Institute of Zoology and Evolutionary Research
University of Jena

**Research Interests:**
- Medical image analysis
- Computational geometry
- Biomechanical simulation
- Tissue modeling
- Anatomical reconstruction

**Website:** https://stark-jena.de/  
**Research Page:** https://stark-jena.de/research-interests/measurement/connective-tissue/

## Acknowledgments

- **Visible Human Project:** NIH/National Library of Medicine
- **imagexd Development Team:** For the powerful image processing framework
- **Free and Open Source Community:** For foundational libraries and tools
- **Research Collaborators:** For anatomical expertise and validation

## References and Links

### Project Resources
- **VHP-Classification GitHub:** https://github.com/heikostark/VHP-classification
- **Research Website:** https://stark-jena.de/research-interests/measurement/connective-tissue/
- **imagexd Tool:** https://stark-jena.de/research-interests/software/imagexd/

### External Resources
- **Visible Human Project:** https://www.nlm.nih.gov/research/visible/visible_human.html
- **NIFTI Format Specification:** https://nifti.nimh.nih.gov/
- **Medical Imaging Informatics:** https://www.nlm.nih.gov/

### Related Tools
- **Cloud2 (Visualization):** https://github.com/heikostark/Cloud2
- **Gordon1966 (FEBio Plugin):** https://github.com/heikostark/Gordon1966

## Support and Feedback

For questions, bug reports, or feature suggestions:

1. **GitHub Issues:** https://github.com/heikostark/VHP-classification/issues
2. **Email:** Through GitHub profile
3. **Research Discussion:** https://stark-jena.de/

---

**Last Updated:** May 2026  
**Repository Status:** Active  
**License:** GPL-3.0  
**Maintained By:** Heiko Stark
