#  Learning Shape Abstractions by Assembling Volumetric Primitives
Shubham Tulsiani, Hao Su, Leonidas J. Guibas, Alexei A. Efros, Jitendra Malik. In CVPR, 2017.

[Project Page](https://shubhtuls.github.io/volumetricPrimitives/)

### 1) Demo
Please check out the [interactive notebook](demo/demo.ipynb) which shows how to compute the primitive based representation for an input shape. You'll need to - 
- Download the [pretrained models](https://people.eecs.berkeley.edu/~shubhtuls/cachedir/primitives/models.tar.gz) and place them in a folder 'cachedir'
- Install a working implementation of torch and itorch.

### 2) Training
We provide code to train the abstraction models on ShapeNet categories.

#### a) Preprocessing
We'll first need to preprocess the ShapeNet models to compute voxelizations required as inout as well as data required to implement the loss functions.
- Modify the path to ShapeNet dataset in the [startup file](preprocess/shapenet/startup.m)
- Specify the synsets of interest in the [preprocessing script](preprocess/shapenet/precomputeShapeData.m)
- Using Matlab, run the [preprocessing script](preprocess/shapenet/precomputeShapeData.m)

#### b) Learning
The training takes place in two stages. In the first we use all cuboids while biasing them to be small and then allow the netowrk to choose to use fewer cuboids. Sample scripts for the synset corresponding to chairs are below.
```
# Stage 1
cd experiments;
disp=0 gpu=1 nParts=20 nullReward=0 lossPower=2 shapeLrDecay=0.01 learningRate=0.001 gridSize=32 nSamplePoints=1000 synset=3001627 modelIter=2 useCubOnly=0 batchSizeVis=4 chamferLossWt=1 useBn=1 symLossWt=1 probLrDecay=0.0001 usePretrain=0 nSamplesChamfer=150 numTrainIter=20000 name=chairChamferSurf_null_small_init_prob0pt0001_shape0pt01 th cadAutoEncCuboids/primSelTsdfChamfer.lua
```

After the first network is trained, we allow the learning of primitive existence probabilities.
```
# Stage 2
cd experiments;
disp=0 gpu=1 nParts=20 nullReward=8e-5 lossPower=2 shapeLrDecay=0.5 learningRate=0.001 gridSize=32 nSamplePoints=1000 synset=3001627 modelIter=2 useCubOnly=0 batchSizeVis=4 chamferLossWt=1 useBn=1 symLossWt=1 probLrDecay=0.2 usePretrain=1 nSamplesChamfer=150 numTrainIter=30000 name=chairChamferSurf_null_small_ft_prob0pt2_shape0pt5_null8em5 th cadAutoEncCuboids/primSelTsdfChamfer.lua
```
