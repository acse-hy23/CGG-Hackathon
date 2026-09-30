# Core Image Segmentation: Imperial GPS × CGG Seismic Hackathon

Team Domino's entry to the GPS × CGG Seismic Hackathon, a one-day event run by the Imperial College London Geophysics Society (GPS) and CGG on 16 March 2024. The event had two challenge tracks; **we won our track and placed 2nd overall.**

## The challenge

Our track was *Thin Section Quality Screening*. The organisers gave us photographs of core boxes, some taken under normal light and some under UV light. The tasks were to:

1. segment the individual core columns in each photograph,
2. tell whether the photograph was taken under normal or UV light,
3. assess image quality, and
4. find where plugs had been taken from the core.

## Approach

- **Segmentation.** Meta's Segment Anything (ViT-H) generates candidate masks, with the automatic mask generator tuned towards whole core columns rather than small fragments. Two rounds of k-means then pick the columns. The first runs on mask area and skips a mask that covers most of the image, since that one is the background. The second runs on bounding-box position and separates neighbouring columns. Each column is saved as its own RGBA image.
- **Normal vs UV light.** We compute the mean HSV values of each segmented column. UV photographs have much higher saturation and lower brightness, so a simple threshold on these two values separates them.
- **Plug detection.** A Hough circle transform finds the plug holes. We also tried fitting ellipses to contours, to catch plugs photographed at an angle.

## Results and limitations

- Segmentation isolated the core columns in the sample photographs, and the light-type rule labelled all eight segmented columns correctly (four UV, four normal). The thresholds were chosen by looking at these same images, and there was no held-out set.
- **Image quality is unfinished.** Laplacian variance, a common sharpness score, gave overlapping values for the good and poor images, so it could not separate them.
- Plug detection works on clear, circular plugs but misses elliptical ones.

## Repository contents

- `hackathon_q2.ipynb`: the organisers' challenge notebook, with our solution after the "Your turn!" section. Outputs are included.
- `Presentation.pptx`: our final presentation.
- `GPS_CGG_Seismic_Hackathon.pdf`: the event poster.

CGG provided the core images for the event, and they are not included here, so the notebook cannot be re-run as is.

## Setup

```shell
pip install git+https://github.com/facebookresearch/segment-anything.git
pip install opencv-python numpy pandas pillow matplotlib scikit-learn pycocotools onnxruntime onnx
```

Download the SAM ViT-H checkpoint (`sam_vit_h_4b8939.pth`) from the [Segment Anything repository](https://github.com/facebookresearch/segment-anything#model-checkpoints) into the notebook's directory.

## Team

Team Domino: Hao You and Oscar.

## License

The code is available under the MIT License. The images are copyright hy23 and CGG. See [LICENSE](LICENSE) for details.

## Acknowledgements

Thanks to CGG Data Hub for providing the data, and to the Imperial College London Geophysics Society for organising the event.
