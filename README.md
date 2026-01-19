# ResNet Paper Reproduction (2015)

## Introduction
This project reproduces the original ResNet architecture as described in the paper.

## Architecture Summary
| Stage       | Layer    | Type             | Filters | Kernel / Stride | Output Size    |
| ----------- | -------- | ---------------- | ------- | --------------- | -------------- |
| Input       | Input    | Input            | –       | –               | 224 × 224 × 3  |
| Stem        | Conv1    | Conv + BN + ReLU | 64      | 7×7 / 2         | 112 × 112 × 64 |
|             | Pool1    | MaxPool          | –       | 3×3 / 2         | 56 × 56 × 64   |
| **Stage 1** | Block1   | BasicBlock ×2    | 64      | 3×3 / 1         | 56 × 56 × 64   |
| **Stage 2** | Block2_1 | BasicBlock       | 128     | 3×3 / 2         | 28 × 28 × 128  |
|             | Block2_2 | BasicBlock       | 128     | 3×3 / 1         | 28 × 28 × 128  |
| **Stage 3** | Block3_1 | BasicBlock       | 256     | 3×3 / 2         | 14 × 14 × 256  |
|             | Block3_2 | BasicBlock       | 256     | 3×3 / 1         | 14 × 14 × 256  |
| **Stage 4** | Block4_1 | BasicBlock       | 512     | 3×3 / 2         | 7 × 7 × 512    |
|             | Block4_2 | BasicBlock       | 512     | 3×3 / 1         | 7 × 7 × 512    |
| Head        | GAP      | Global Avg Pool  | –       | –               | 512            |
| Output      | FC       | Dense            | 200     | –               | 200            |


### BasicBlock Structure
| Order | Layer    | Details                      |
| ----- | -------- | ---------------------------- |
| 1     | Conv     | 3×3, stride = s, filters = F |
| 2     | BN       | Batch Normalization          |
| 3     | ReLU     | Activation                   |
| 4     | Conv     | 3×3, stride = 1, filters = F |
| 5     | BN       | **γ initialized to 0**       |
| 6     | Shortcut | Identity or 1×1 Conv + BN    |
| 7     | Add      | Residual addition            |
| 8     | ReLU     | Activation                   |




## Dataset
tiny-imagenet-200 is used as the dataset in this reproduction. Validation set is used as test set, as the actual test set is not labelled. Each image is resized from 64×64 to 256*256 and randomly crop an 224*224 image from it with a 50% chance of horizontal flip.

## Reproduced Results
| Metric            | Value |
| ----------------- | ----- |
| Training accuracy | 85.46% |
| Test accuracy     | 54.72% |

## Discussion
This network has many 3 * 3 convs, which is VGG-style. Compared with VGG11 and VGG16 we implemented earlier, we found that ResNet converges in fewer epochs(30 vs 40), which is expected, as residual connection enables preserving gradient magnitude and preventing vanishing gradients.

We also found that residual connection significantly improves early training, as signals can skip through connections and avoid early noisy gradients. On the contrary, we found that both VGG network are really hard to train at the beginning and we need to use BN to fix this problem.

## References
He, K., Zhang, X., Ren, S., & Sun, J. (2016). Deep residual learning for image recognition. In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR) (pp. 770–778). IEEE. https://doi.org/10.1109/CVPR.2016.90

Tiny ImageNet Dataset
Wu, J., Zhang, J., Xie, Y., & others. (2017).
Tiny ImageNet Visual Recognition Challenge.
Stanford University.
https://tiny-imagenet.herokuapp.com/
