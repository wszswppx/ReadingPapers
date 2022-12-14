# ReadingPapers
读论文过程中的记录。

## SRDF
### 2.2 Differentiable Rendering  
Another line of works has explored differentiable rendering approaches. 
Many of these works are used for novel view rendering applications however several also consider 3D shape geometry reconstruction, 
often as part of the new image generation process. 
They build on a rendering that is differentiable and henceforth enable shape model optimization by differentiating the discrepancy between generated and observed images. 
These methods were originally applied to various shape representations including meshes [34, 26, 19, 49], 
volumetric grids [64, 14, 14, 25, 77, 44] or even point clouds [24, 21, 43]. 
In association with deep learning, new neural implicit representations have also emerged. 
Their continuous nature and light memory requirements are attractive and they have been successfully applied to different tasks: 3D reconstruction [56, 57, 68, 38, 51, 48] or geometry and appearance representations [16, 63, 39, 46, 61]. 
Most of these methods solve for 3D shape inference and require 3D supervision, however 
recent works combine implicit representations with differentiable renderering and solve therefore for shape optimization with 2D image supervision. 
They roughly belong to two categories depending on the shape representation they consider.  

**Volume-based methods** use a volumetric renderer with 3D samples and estimate for each sample a density as well as a color conditioned on a viewing direction. Colors and densities of the samples are integrated along viewing rays to obtain image colors that can be compared with the observed pixel colors. The pioneer work of Mildenhall et al. [40] opened up this research area with impressive results on novel view synthesis. The quality of the associated geometry as encoded with densities is however not perfect and often noisy. Several works have followed that target generalization to new data [75, 22], dynamic scenes [52, 33] or propose new formulations based on Signed Distance Functions to improve the estimated geometry [66, 71]. [9](<i>Improving neural implicit surfaces geometry with patch warping</i>) also proposes a finetuning strategy based on image warpings to take advantage of high-frequency texture. A main limitation of methods based on neural volumetric rendering lies in the optimization time complexity which plagues the shape modeling process. To address this limitation various strategies have been investigated: a divide-and-conquer strategy [53], more efficient sampling [4], traditional volumetric representations to directly optimize densities and colors inside octrees [74], or voxel grids [73] and efficient multi-resolution hash tables [41]. See [11] for more details. These strategies dramatically decrease the optimization time while maintaining very good results for novel view rendering. However the geometry is still imprecise as a result of the non-explicit encoding with densities.  

**Surface-based methods** [35, 72, 45, 29] address this issue with a surface renderer that estimate 3D locations where viewing rays enter the surface and their colors. By making the shape surface explicit, these approaches obtain usually better geometries. Nevertheless, they are more prone to local minima during the optimization since gradients are only computed near the estimated surface as opposed to volumetric strategies. An interesting hybrid approach [47], proposes to combine the advantages of both volumetric and surface rendering and shows good surface reconstructions.  

Following volumetric methods we use a volumetric signed ray distance representation which we parameterize with depths along viewing rays, hence making the surface explicit while keeping the benefit of better distributed gradients with a volumetric representation. To optimize this shape representation we introduce an unsupervised differentiable volumetric criterion that, in contrast to differentiable rendering approaches, does not require color estimation.  

