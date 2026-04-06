# V8 Training Results

**Date**: 2026-04-06
**Dataset**: 1,626 training samples (1,397 original + 29 firestore + 200 gemini)
**Eval**: 34 eval_v2 images + 5 firestore images

## Model Configurations

### V7 Production (Current)
- **Hip**: yolo backend, ONNX (`api/weights/hip_model_v7.onnx`), crop=96, pad=1.5
  - Trained with: 1,397 samples, 100 epochs, lr=1e-3, nofilter, confidence_weights
- **Knee**: vitpose_b backend, ONNX (`api/weights/knee_model_v6.onnx`), crop=96, pad=1.5
  - Trained with: 1,397 samples, 50 epochs, lr=1e-3, nofilter

### V8 Gemini
- **Hip**: yolo backend, PyTorch (`checkpoints/hip_v8_gemini_best.pth`), crop=96, pad=1.5
  - Trained with: 1,713 samples (1,397 + 116 firestore@4x + 200 gemini), 100 epochs, lr=1e-3, nofilter, confidence_weights
- **Knee**: vitpose_b backend, PyTorch (`checkpoints/knee_v8_gemini_best.pth`), crop=96, pad=1.5
  - Trained with: 1,713 samples, 100 epochs, lr=1e-3, nofilter

### V8 General
- **Hip**: yolo backend, PyTorch (`checkpoints/hip_v8_general_best.pth`), crop=96, pad=1.5
  - Trained with: 1,626 samples (1,397 + 29 firestore@1x + 200 gemini), 150 epochs, lr=5e-4, nofilter, no confidence weights
- **Knee**: vitpose_b backend, PyTorch (`checkpoints/knee_v8_general_best.pth`), crop=96, pad=1.5
  - Trained with: 1,626 samples, 150 epochs, lr=5e-4, nofilter

## Results on eval_v2 (34 images)

| Model | Hip Error | Knee Error | Depth Accuracy |
|-------|-----------|------------|----------------|
| **V7 prod** | **0.915%** | 1.014% | **85.3%** |
| V8 gemini | 1.178% | **0.857%** | 82.4% |
| V8 general | 1.216% | 1.000% | 82.4% |

## Results on firestore (5 images, in-training)

| Model | Hip Error | Knee Error | Depth Accuracy |
|-------|-----------|------------|----------------|
| V7 prod | 1.687% | 0.973% | **100.0%** |
| V8 gemini | 1.576% | **0.724%** | 60.0% |
| V8 general | **1.464%** | 0.910% | 80.0% |

## Key Findings

1. **Knee V8 gemini is the best knee model ever** (0.857% vs 1.014% prod = 15.5% improvement)
2. **Hip V7 prod remains the best hip model** (0.915% vs 1.178% V8 gemini)
3. **No single V8 model beats V7 on both hip and knee**
4. The gemini synthetic data improves knee detection but hurts hip detection
5. V8 general (balanced approach with 1x weighting, lower LR) is on par with V7 for knee (1.000%) but still worse for hip

## Recommended Deployment

**Hybrid**: Keep V7 hip (0.915%) + Deploy V8 gemini knee (0.857%)

## New Data Added
- **Firestore batch**: 29 real production photos (from app users), deduplicated from 73 originals
- **Gemini batches**: 200 AI-labeled synthetic squat frames (98 bert + 102 fahim)
- Both batches converted with YOLO and vitpose_b keypoints

## Visualization

Open `docs/v8_comparison.html` in a browser to see per-image results with overlaid predictions.
